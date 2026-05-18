# Multi-Repo 仓库设计

## 背景

AI Learning Garden 需要前后端独立部署,并让前端与后端的学习路径、职责边界更清晰。原架构选择 monorepo,是因为单人项目下设置更少、跨栈原子提交更省心。但当前更重视独立部署与职责分离,这个取舍需要调整。

当前仓库只包含 `docs/`,天然适合作为项目总控与文档仓;应用代码放入独立代码仓。

## 决策

采用三个仓库:

1. `ai-learning-garden` 或 `ai-learning-garden-docs`:项目总控与文档仓。
2. `ai-learning-garden-web`:Next.js 前端应用仓。
3. `ai-learning-garden-api`:Go 模块化单体后端仓。

这只是仓库与发布边界的拆分,不改变运行时架构:

- `web` 仍是一个 Next.js 应用。
- `api` 仍是一个 Go 模块化单体。
- PostgreSQL 与 S3 兼容对象存储仍由后端运行单元管理。
- 前后端只通过 `/api/v1` 下的 REST 接口通信。

## 仓库职责

### 文档仓

负责:

- 架构、路线图、规范、数据模型文档。
- 跨仓本地开发说明。
- API 契约引用与决策记录。
- 跨仓集成与 E2E 策略。

不放应用源码。

### Web 仓

负责:

- Next.js App Router 应用。
- community、workspace、studio、admin 四类 route group。
- `features/*`、`lib/api`、`runtime/`、通用组件、前端测试。
- Vercel 部署配置。
- 前端 CI:lint、typecheck、单元测试、API client 测试。

`web` 仓不得依赖后端源码。它只能通过配置的 API base URL 和已发布的 REST 契约访问后端。

### API 仓

负责:

- Go 模块化单体源码。
- identity、content、social、learning、relation、media、moderation、platform 等领域模块。
- 数据库迁移、SQL 查询、sqlc 配置、后端集成测试。
- 容器构建与后端部署配置。
- 后端 CI:gofmt/lint、depguard、测试、迁移校验、契约校验。

## API 契约

REST API 契约是两个代码仓之间的显式边界。

- 后端在首批接口稳定后维护 `openapi.yaml` 或等价契约文档。
- 前端浏览器侧调用使用 `NEXT_PUBLIC_API_BASE_URL`;RSC 或 server action 如需服务端调用,使用服务端专用 API base URL。
- 破坏性接口变更必须协调 `api` 与 `web` 两仓。
- M0 可以先用端点表记录契约;自动生成 client 等到接口 churn 降低后再引入。

## 本地开发

默认本地流程:

1. 从 `api` 仓启动 PostgreSQL 与对象存储。
2. 启动本地 API 服务。
3. 启动 `web` 应用,通过环境变量指向本地 API。

文档仓维护标准跨仓启动说明。根级 orchestrator 脚本可以后续添加,但 M0 不把它作为必需的平台层。

## CI 与测试

每个代码仓负责自己的快速检查:

- `web`:lint、typecheck、单元测试、API client 测试。
- `api`:lint、depguard、Go 测试、迁移校验、契约校验。

跨仓 E2E 测试默认推迟到 M2;除非 M0 需要极窄的 smoke test。引入时可以放在文档/总控仓,也可以放在 `web` 仓,以当时设置最简单为准。

## 影响

收益:

- 前端和后端可以独立部署。
- 后端学习路径从前端工作里分离出来。
- 仓库边界强化 REST 契约,减少源码级误耦合。

成本:

- 不再有单仓跨栈原子提交。
- API 兼容性需要显式纪律。
- 本地启动和 E2E 需要跨仓文档。

这些成本可以接受,因为项目当前优先级是独立部署和更清晰的前后端职责边界。
