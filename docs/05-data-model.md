# 数据模型与数据库设计

数据库:**PostgreSQL**。本文给出 `identity` / `content` / `social` 三个模块的表设计,并简述 `learning` / `relation` / `media` / `moderation`。

## 1. 通用约定

- 主键统一 `uuid`(`gen_random_uuid()`),列名 `id`。
- 时间统一 `timestamptz`;创建/更新时间列 `created_at` / `updated_at`。
- 需要"软删除"的表用 `deleted_at timestamptz NULL`。
- **每个内容表必须有** `owner_id`(FK `users.id`)与 `visibility`。
- 枚举用 PostgreSQL `enum` 类型。
- 表归属哪个模块,就只由那个模块的 repository 访问;跨模块经 service 接口(见 [01-architecture.md](./01-architecture.md) 5.2)。

```sql
CREATE TYPE visibility AS ENUM ('private', 'public');
CREATE TYPE user_role AS ENUM ('user', 'admin');
```

---

## 2. identity 模块

### users

| 列 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid PK | |
| email | text UNIQUE NOT NULL | 登录标识 |
| password_hash | text NOT NULL | argon2id 哈希 |
| display_name | text NOT NULL | 昵称 |
| handle | text UNIQUE NOT NULL | 用户名,用于主页 URL,kebab-case |
| avatar_url | text NULL | 头像(对象存储 URL) |
| bio | text NULL | 简介 |
| role | user_role NOT NULL DEFAULT 'user' | 角色 |
| created_at | timestamptz NOT NULL DEFAULT now() | |
| updated_at | timestamptz NOT NULL DEFAULT now() | |

### sessions

会话用 Cookie + 服务端会话表(可撤销)。

| 列 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid PK | |
| user_id | uuid FK users.id NOT NULL | |
| token_hash | text NOT NULL | 会话令牌的哈希,明文只存在 Cookie |
| expires_at | timestamptz NOT NULL | |
| created_at | timestamptz NOT NULL DEFAULT now() | |
| last_seen_at | timestamptz NOT NULL DEFAULT now() | |

索引:`sessions(token_hash)`、`sessions(user_id)`。

---

## 3. content 模块

概念/论文/实验/路线/复盘共享"归属 + 可见性 + slug + 时间"。正文以 Markdown 文本存 `body`。

### concepts

| 列 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid PK | |
| owner_id | uuid FK users.id NOT NULL | 归属 |
| visibility | visibility NOT NULL DEFAULT 'private' | |
| slug | text NOT NULL | 在 owner 范围内唯一 |
| title | text NOT NULL | |
| summary | text NULL | 一句话摘要 |
| body | text NOT NULL DEFAULT '' | Markdown 正文(数学/代码/论文三栏) |
| stage | int NULL | 学习阶段 |
| week | int NULL | 学习周 |
| tags | text[] NOT NULL DEFAULT '{}' | |
| created_at / updated_at | timestamptz | |

约束:`UNIQUE(owner_id, slug)`。索引:`concepts(owner_id)`、`concepts(visibility)`。

### papers

| 列 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid PK | |
| owner_id | uuid FK users.id NOT NULL | |
| visibility | visibility NOT NULL DEFAULT 'private' | |
| slug | text NOT NULL | `UNIQUE(owner_id, slug)` |
| title | text NOT NULL | |
| authors | text[] NOT NULL DEFAULT '{}' | |
| year | int NULL | |
| url | text NULL | 原文链接 |
| body | text NOT NULL DEFAULT '' | 阅读笔记(固定模板,Markdown) |
| created_at / updated_at | timestamptz | |

### experiments

| 列 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid PK | |
| owner_id | uuid FK users.id NOT NULL | |
| visibility | visibility NOT NULL DEFAULT 'private' | |
| slug | text NOT NULL | `UNIQUE(owner_id, slug)` |
| title | text NOT NULL | |
| body | text NOT NULL DEFAULT '' | 说明与结论 |
| repo_url | text NULL | |
| config | jsonb NOT NULL DEFAULT '{}' | 超参/种子等 |
| metrics | jsonb NOT NULL DEFAULT '{}' | reward/loss 等指标 |
| week | int NULL | |
| created_at / updated_at | timestamptz | |

### journal_entries / roadmap

- `journal_entries`:`id, owner_id, visibility, kind('weekly'|'stage'), title, body, period_label, created_at, updated_at`。
- 路线图任务:`roadmap_tasks(id, owner_id, stage, week, title, sort_order)`;勾选状态属 `learning` 模块,不在此。

### 关系边表(归 relation 模块查询,content 模块写入)

作者只录单向边,反向链接由查询派生。

| 表 | 列 | 含义 |
| --- | --- | --- |
| concept_prereqs | concept_id, prereq_id | 概念的前置概念 |
| concept_papers | concept_id, paper_id | 概念关联论文 |
| concept_experiments | concept_id, experiment_id | 概念关联实验 |
| experiment_papers | experiment_id, paper_id | 实验关联论文 |

每张边表 PK 为两列联合;两个方向都建索引,以便正向与反向查询。被引用 id 的有效性在 `content` 的 service 层校验。

---

## 4. social 模块

`content` 不依赖 `social`;评论等通过"目标类型 + 目标 id"挂到内容上。

```sql
CREATE TYPE comment_target AS ENUM ('concept','paper','experiment','journal','discussion');
CREATE TYPE notification_kind AS ENUM ('comment','reply','follow','mention');
```

### comments

支持楼中楼(`parent_id` 自引用)。目标用多态引用(`target_type` + `target_id`)——刻意取舍:省去对每种内容建独立评论表,代价是无数据库级 FK,有效性在 service 层校验。

| 列 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid PK | |
| author_id | uuid FK users.id NOT NULL | |
| target_type | comment_target NOT NULL | 评论挂在什么上 |
| target_id | uuid NOT NULL | 目标 id |
| parent_id | uuid FK comments.id NULL | 回复的父评论 |
| body | text NOT NULL | |
| created_at / updated_at | timestamptz | |
| deleted_at | timestamptz NULL | 软删除 |

索引:`comments(target_type, target_id)`、`comments(author_id)`。

### discussions

社区讨论帖(独立于具体内容的话题)。其下的回复复用 `comments`,`target_type='discussion'`。

| 列 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid PK | |
| author_id | uuid FK users.id NOT NULL | |
| title | text NOT NULL | |
| body | text NOT NULL | |
| tags | text[] NOT NULL DEFAULT '{}' | |
| created_at / updated_at | timestamptz | |
| deleted_at | timestamptz NULL | |

### follows

用户关注用户。

| 列 | 类型 | 说明 |
| --- | --- | --- |
| follower_id | uuid FK users.id NOT NULL | 关注者 |
| followee_id | uuid FK users.id NOT NULL | 被关注者 |
| created_at | timestamptz NOT NULL DEFAULT now() | |

PK `(follower_id, followee_id)`;`CHECK (follower_id <> followee_id)`。索引两个方向。

### notifications

| 列 | 类型 | 说明 |
| --- | --- | --- |
| id | uuid PK | |
| user_id | uuid FK users.id NOT NULL | 接收者 |
| kind | notification_kind NOT NULL | |
| actor_id | uuid FK users.id NOT NULL | 触发者 |
| target_type | comment_target NULL | |
| target_id | uuid NULL | |
| read_at | timestamptz NULL | |
| created_at | timestamptz NOT NULL DEFAULT now() | |

### 动态流(feed)

**不建表。** 动态流是一次查询:取当前用户 `follows` 的人 + 自己,聚合其 `public` 内容与活动,按时间排序。先小范围下查询足够;真出现性能问题再考虑写扩散(fan-out)——这是预留的接缝,不提前做。

---

## 5. 其余模块(简述)

- **learning**(运行态平面,与 content 分离):
  - `concept_progress(user_id, concept_id, status, updated_at)`,`status ∈ {not_started, learning, implemented, reviewed}`,PK 两列。
  - `task_checks(user_id, task_id, checked, updated_at)`。
  - `review_cards(id, user_id, concept_id, front, back, ease, interval_days, due_date, last_reviewed_at)`——SM-2 状态。
- **media**:`assets(id, owner_id, kind, storage_key, filename, content_type, size, created_at)`;文件本体在对象存储,库里只存元数据。
- **moderation**(M6):`reports(id, reporter_id, target_type, target_id, reason, status, created_at)` 及管理操作日志。
- **relation**:无独立表,查询第 3 节的边表派生正反向链接。

---

## 6. 授权规则(落在各模块 service 层)

| 操作 | 规则 |
| --- | --- |
| 读内容 | `visibility='public'` 或 `owner_id = 当前用户` |
| 写/删内容 | 必须 `owner_id = 当前用户`(或 `role='admin'`) |
| 评论/讨论 | 须登录;只能删自己的(或 admin) |
| 关注 | 须登录;不能关注自己 |
| 管理操作 | 仅 `role='admin'` |

授权一律在 service 层判定,不在 handler、不在 repository(见 [04-conventions.md](./04-conventions.md) 4.2)。
