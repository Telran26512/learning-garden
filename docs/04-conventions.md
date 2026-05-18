# 开发规范

规范服务于一个目标:让架构红线(见 [01-architecture.md](./01-architecture.md))被工具自动守护,而不是靠记忆。

## 1. 仓库结构

采用 multi-repo,当前仓库作为项目总控与文档仓,`web` 与 `api` 分别独立成代码仓。拆仓是为了前后端独立部署、职责分离和学习路径清晰;系统架构仍保持一个 Next.js 应用 + 一个 Go 模块化单体。

### 1.1 项目总控 / 文档仓

```text
learning-garden/
├── docs/
│   ├── 00-overview.md
│   ├── 01-architecture.md
│   ├── 02-tech-stack.md
│   ├── 03-roadmap.md
│   ├── 04-conventions.md
│   └── 05-data-model.md
└── README.md                     # 跨仓入口、本地联调说明索引
```

职责:维护架构、路线图、规范、数据模型、跨仓联调说明和 API 契约引用。不放应用源码。

### 1.2 `learning-garden-web`

```text
learning-garden-web/
├── app/
│   ├── (community)/              # 社区:发现、浏览他人、讨论、用户主页
│   ├── (workspace)/              # 登录后:我的学习空间(8 个学习模块)
│   ├── (studio)/                 # 创作:上传/编辑自己的内容
│   └── (admin)/                  # 管理员:审核(M6)
├── features/                     # L4 各功能模块,一模块一目录,互不 import
├── lib/api/                      # L3 唯一与后端通信的客户端层,按后端领域组织
├── runtime/                      # L2 Pyodide 等能力,接口隔离
├── components/                   # 跨模块通用展示组件(无业务逻辑)
├── tests/
└── README.md                     # 前端启动、环境变量、部署说明
```

`web` 仓只通过 REST 契约和 API base URL 访问后端,不得依赖 `api` 仓源码。浏览器侧 API 地址使用 `NEXT_PUBLIC_API_BASE_URL`;服务端组件或 server action 如需直接调用后端,使用服务端专用环境变量。

### 1.3 `learning-garden-server`

```text
learning-garden-server/
├── cmd/server/                   # 入口 main
├── internal/
│   ├── identity/                 # 每个领域模块内含 handler/service/repository/domain
│   ├── content/
│   ├── social/
│   ├── learning/
│   ├── relation/
│   ├── media/
│   ├── moderation/
│   └── platform/                 # 共享:db、config、middleware、httperr、log
├── migrations/                   # 数据库迁移(版本化)
├── queries/                      # sqlc 的 SQL 源文件
├── api/                          # REST 契约,如 openapi.yaml 或端点文档
└── README.md                     # 后端启动、数据库、对象存储、部署说明
```

## 2. 分层与模块边界(架构红线,工具强制)

### 2.1 web —— ESLint import 边界

| 位置 | 允许 import | 禁止 |
| --- | --- | --- |
| `features/*` (L4) | `lib/api`、`components`、`runtime` | **其他 `features/*`**;直接发 HTTP |
| `lib/api` (L3) | 仅类型 | `features`、`app` |
| `runtime` (L2) | 自包含 | `features` |

最关键:`features/concepts` 不得 import `features/papers`——模块互不认识,跨模块跳转只用 `next/link` + URL。

### 2.2 api —— `depguard` 模块依赖 DAG

领域模块只能按 [01-architecture.md](./01-architecture.md) 第 5.1 节的 DAG 互相依赖:

- 允许:`social → content`、`content → identity`、`learning → content` 等(单向)。
- 禁止:逆向依赖、成环;**禁止 import 其他模块的 `repository` 或 `domain` 包**——跨模块只能调对方 `service` 暴露的接口。
- `platform` 可被所有模块依赖,自身不依赖任何领域模块。

违反规则 → CI 失败。

## 3. 命名规范

| 对象 | 规范 | 示例 |
| --- | --- | --- |
| 前端文件/目录 | kebab-case | `concept-card.tsx` |
| React 组件 | 文件 kebab-case,组件名 PascalCase | `RunnablePython` |
| Go 包名 | 全小写、单词 | `identity`、`content` |
| Go 文件 | snake_case | `concept_service.go` |
| Go 导出标识符 | PascalCase;非导出 camelCase | `ConceptService` |
| 数据库表 | 复数 snake_case | `concepts`、`comment_threads` |
| 数据库列 | snake_case | `owner_id`、`created_at` |
| 内容 `slug` | kebab-case,在 owner 范围内唯一 | `linear-regression` |
| REST 路径 | 复数名词、kebab-case | `/api/v1/concepts` |

## 4. 代码规范

### 4.1 前端

- **TypeScript strict**,禁用 `any`。
- **RSC 默认**:组件默认 Server Component,仅在需交互时加 `'use client'` 并下推到叶子。
- 鉴权在路由层((studio)/(admin) route group)统一拦截,不在组件里散判。
- 不写"以后可能用到"的代码(YAGNI);三处重复才考虑抽象。

### 4.2 后端 Go

- 每个领域模块内分 `handler` / `service` / `repository` / `domain` 四层,依赖单向向下。
- **授权判定一律在 `service` 层**:任何写操作先校验 `owner_id`;任何读操作按 `visibility` 过滤。handler 不做、repository 不做。
- repository 用 `sqlc` 生成的类型安全代码;SQL 写在 `queries/`。
- 错误处理:领域错误在 `platform` 定义统一类型,handler 统一转 HTTP 状态码。
- 不在领域模块里 import 别的模块的 repository/domain(见 2.2)。

## 5. 内容与数据规范

- 内容正文以 Markdown 存储;数学用 KaTeX 语法;可在浏览器跑的 numpy 代码用 `<RunnablePython>`,框架代码用 `<PaperCode>`。
- 想沉淀为复习卡的片段用 `::card` 标记包裹,由 `learning` 模块抽取。
- **每个内容表必须有** `owner_id`、`visibility`、`created_at`、`updated_at`。
- 数据库变更一律走 `migrations/` 版本化迁移,禁止手工改库。
- 详见 [05-data-model.md](./05-data-model.md)。

## 6. Git 规范

- **Conventional Commits**:`feat:` `fix:` `docs:` `refactor:` `chore:` `test:`,带范围,如 `feat(content): 概念 CRUD`。
- 一次提交只做一件事。
- 每个仓库的 `main` 都是主干;较大功能用 `feat/<名称>` 短分支,完成即合并。
- 范围用模块名:`identity` / `content` / `social` / `web` 等,便于区分"建站工时"与"学习产出"。
- 跨仓变更不得依赖原子提交。破坏 REST 契约时,先在 `api` 仓更新契约并记录迁移方式,再协调 `web` 仓适配。

## 7. 测试规范

- **后端 service 层**:必须有单元测试,重点覆盖**授权逻辑**(非 owner 不能改、private 不外泄)与 `learning` 的 SM-2 算法。
- **后端 repository 层**:对真实测试库跑集成测试。
- **前端 `lib/api`**:对 mock 后端测。
- **E2E**(Playwright,M2 后):只覆盖关键路径——注册登录、创建并浏览内容、运行代码、完成复习。
- 不写脆弱的 UI 快照测试。

## 8. CI 检查项

每个代码仓拥有自己的 CI:

- `web` 仓: `lint`(含 import 边界)+ `typecheck` + 前端测试 + `lib/api` mock 后端测试。
- `api` 仓: `gofmt` / `golangci-lint` + `depguard`(模块 DAG)+ `go test` + 数据库迁移校验 + REST 契约校验。
- 文档仓:Markdown 链接与结构检查(可后置),并维护跨仓联调说明。

任一代码仓 CI 失败都阻止对应仓库合并——这是架构红线不腐化的保证。跨仓 E2E 测试 M2 后再引入,优先覆盖注册登录、创建并浏览内容、运行代码、完成复习。
