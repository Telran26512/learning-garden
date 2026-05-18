# 技术选型

## 1. 选型原则

1. **低运维优先。** 单人长期项目,不能有需要持续照看的组件。先小范围,不为规模化提前设计。
2. **够用即可,预留接缝。** 当前服务"自己 + 朋友"的体量;但架构(模块化单体、领域边界)为将来开放留好接缝。
3. **后端是对口职业的学习投入。** Go 选型对齐 RL Infra 方向,后端本身作为作品集的一部分。
4. **内容可移植。** 内容入库,但有导出接口,不锁定。

## 2. 前端 web

| 领域 | 选型 | 理由 | 备选 / 未选 |
| --- | --- | --- | --- |
| 框架 | **Next.js 16(App Router + RSC)** | 公开页服务端渲染、内容上传后即时可见;route group 天然支持按角色分区 | Astro(交互弱);纯 SPA(SSR 体验差) |
| 语言 | **TypeScript(strict)** | 实体/接口类型保证 | JS(放弃类型) |
| UI | **React 19** | 随 Next.js | — |
| 样式 | **Tailwind CSS v4** | 无运行时成本、开发快 | CSS Modules |
| 基础组件 | **Radix UI primitives**(按需) | 无样式、可访问性好 | 整套组件库(过重) |
| 客户端状态 | **Zustand**(仅会话与学习进度) | 全局可变状态少,Zustand 足够轻 | Redux(过重) |
| 内容正文 | **Markdown/MDX 渲染** | 内容以 Markdown 文本存于后端,前端渲染 | — |
| 数学公式 | **KaTeX**(`remark-math` + `rehype-katex`) | 快、SSR 友好 | MathJax(慢) |
| 代码高亮 | **Shiki** | 高质量、可服务端渲染 | Prism |
| 浏览器内跑 Python | **Pyodide**(CDN 懒加载) | 唯一能在浏览器跑 numpy 的方案;封装在 `runtime/` 后 | 远端执行(违反低运维) |

## 3. 后端 api

| 领域 | 选型 | 理由 | 备选 / 未选 |
| --- | --- | --- | --- |
| 语言 | **Go** | 对齐 RL Infra 职业方向;单二进制、运维轻;从 Java 转入平滑;后端本身作为作品集 | Java/Spring(上线更快但 career ROI 低);Python(LabCraft 已用,此处要换) |
| 架构 | **模块化单体(modular monolith)** | 先小范围下微服务是过度工程;模块化单体简单且预留拆分接缝 | 微服务(过度);传统单体(边界不清) |
| HTTP 路由 | **chi** | 轻量、惯用 `net/http`、中间件清晰;适合学习 Go 本身 | gin/echo(更"框架化",黑盒多) |
| 数据库访问 | **sqlc**(由 SQL 生成类型安全代码)+ **pgx** 驱动 | 写真实 SQL、编译期类型安全、无 ORM 魔法;利于真正掌握 Go 与 SQL | GORM(ORM 魔法重,学不到底层) |
| 数据库迁移 | **goose** 或 **golang-migrate** | 版本化迁移,纳入 CI | 手工改库(不可接受) |
| 认证 | **会话 + Cookie**(密码用 argon2id 哈希) | 可服务端撤销、实现简单;OAuth 作为后续接缝 | 纯 JWT(撤销麻烦) |
| 模块边界强制 | **depguard**(ESLint 之于前端的等价物) | 在 CI 强制领域模块依赖 DAG,架构红线工具化 | 仅靠自觉(必腐化) |

## 4. 存储与基础设施

| 领域 | 选型 | 理由 |
| --- | --- | --- |
| 数据库 | **PostgreSQL** | 关系/JSONB/数组/全文检索一站满足;关系索引、社交关系都适合关系库 |
| 对象存储 | **S3 兼容服务**(如 Cloudflare R2;本地开发用 MinIO) | 文件不入库;封装在 `media` 模块后,provider 可换 |
| 前端部署 | **Vercel** | Next.js 一等公民,Push 即部署 |
| 后端部署 | **单 Go 二进制容器**,部署到轻量容器平台(Fly.io / Railway / VPS) | Go 镜像小、启动快、内存省 |
| 托管数据库 | **Neon / Supabase Postgres 等托管 PG** | 不自己运维数据库 |

## 5. 工程化(前后端通用)

| 领域 | 选型 |
| --- | --- |
| 单元/集成测试 | 前端 **Vitest**;后端 Go 原生 `testing` |
| E2E(后期) | **Playwright**,只覆盖关键路径 |
| 代码质量 | 前端 **ESLint + Prettier**(含分层 import 边界);后端 `gofmt` + `golangci-lint` + `depguard` |
| 包管理 | 前端 **pnpm**;后端 Go modules |
| 仓库形态 | **Multi-repo**:`docs` 总控仓 + `web` 前端仓 + `api` 后端仓,支持前后端独立部署与职责分离 |
| API 风格 | **REST**,版本前缀 `/api/v1` |

仓库拆分后的归属:

- `learning-garden-web`:Next.js 应用、前端 CI、Vercel 部署。
- `learning-garden-server`:Go 模块化单体、数据库迁移、REST 契约、容器部署。
- `learning-garden`:架构文档、路线图、规范、跨仓联调说明。

代价是失去单仓原子提交,需要通过显式 REST 契约与跨仓联调文档控制变更。

## 6. 明确不采用

| 不采用 | 原因 |
| --- | --- |
| 微服务 / Kubernetes | "先小范围"下是巨大过度工程。模块化单体已留好拆分接缝。 |
| GraphQL | REST 对本项目足够;少一层复杂度。 |
| 重 ORM(GORM 等) | 与"用后端真正学好 Go/SQL"的目标冲突。 |
| 独立的 admin 应用 | 项目需要后台管理系统,但不需要独立 admin 应用。后台管理系统放在同一个 Next.js 应用的 `(admin)` 路由组中,由管理员角色、路由鉴权和后端管理接口隔离。 |
| CMS(Contentful / Sanity) | 内容要可导出、可迁移,且后端是刻意的学习载体。 |
| 实时协同编辑、自建训练仪表盘实时设施 | 远期愿景;当前架构不为其设计。 |
| 消息队列 / 缓存中间件(Redis 等) | 当前体量无需;真有需要时再作为 `platform` 层的接缝引入。 |

## 7. 已知风险与对策

| 风险 | 对策 |
| --- | --- |
| Pyodide 体积大、跑不了 PyTorch/RL | 懒加载;架构区分 `<RunnablePython>`(numpy)与 `<PaperCode>`(框架代码挂 Colab) |
| Go 是新语言,有学习成本 | 后端领域(CRUD/社交)成熟易懂,是优质的"第一个 Go 项目";视作对口职业的学习投入 |
| 完整社区工程量大 | 严守 M0→M6 分期:地基先行,社区功能 M4 才建;受 20% 时间预算约束 |
| 多用户授权易出漏洞 | 授权判定集中在各模块 service 层,统一以 `owner_id` + `visibility` 判定,辅以测试覆盖 |
| 跨仓 API 契约漂移 | `api` 仓维护 REST 契约;`web` 只通过契约和 API base URL 访问后端;破坏性变更需协调发布 |
| 内容入库失去 git 可移植 | `content` 模块提供导出接口(全量导出 Markdown/JSON);文件存对象存储 |
