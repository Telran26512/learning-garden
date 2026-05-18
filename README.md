# AI Learning Garden

Project control and documentation repository for **AI Learning Garden**, a multi-user AI learning community that connects math derivations, runnable code, and paper reading workflows.

This repository is the source of truth for architecture, roadmap, conventions, data model, requirements, and cross-repository coordination. Application code lives in separate repositories.

## Repository Family

| Repository | Role |
| --- | --- |
| [`learning-garden`](https://github.com/Telran26512/learning-garden) | Project control, architecture docs, requirements, roadmap, and conventions |
| [`learning-garden-web`](https://github.com/Telran26512/learning-garden-web) | Next.js frontend application |
| [`learning-garden-server`](https://github.com/Telran26512/learning-garden-server) | Go backend server and REST API |

## Document Map

Start with the core docs in this order:

1. [Project overview](docs/00-overview.md)
2. [Architecture](docs/01-architecture.md)
3. [Data model](docs/05-data-model.md)
4. [Tech stack](docs/02-tech-stack.md)
5. [Roadmap](docs/03-roadmap.md)
6. [Conventions](docs/04-conventions.md)

Supporting documents:

- [Requirements analysis](docs/requirements/ai-learning-garden-requirements-analysis.docx)
- [RL infrastructure learning plan](docs/references/rl-infrastructure-learning-plan-revised-zh.md)

## Repository Layout

```text
learning-garden/
|-- docs/
|   |-- 00-overview.md
|   |-- 01-architecture.md
|   |-- 02-tech-stack.md
|   |-- 03-roadmap.md
|   |-- 04-conventions.md
|   |-- 05-data-model.md
|   |-- requirements/
|   |   `-- ai-learning-garden-requirements-analysis.docx
|   `-- references/
|       `-- rl-infrastructure-learning-plan-revised-zh.md
|-- .gitattributes
|-- .gitignore
`-- README.md
```

## Documentation Rules

- Keep canonical architecture and engineering decisions in Markdown.
- Put binary deliverables such as Word documents under `docs/requirements/` or another purpose-specific subdirectory.
- Put reference material that is not part of the product architecture under `docs/references/`.
- Do not commit local agent workflow artifacts such as `docs/superpowers/`.
- Keep cross-repository coordination notes in this repository, not in the frontend or backend repositories.

## Development Boundary

The three repositories are independent deployable workspaces:

- `learning-garden-web` consumes the backend only through REST contracts and configured API base URLs.
- `learning-garden-server` owns authentication, authorization, persistence, migrations, and REST contracts.
- `learning-garden` owns project-level documentation and decisions.

Breaking API changes should be documented here or in `learning-garden-server` before the frontend adopts them.

## Git Conventions

Use Conventional Commits:

```text
docs(repo): reorganize project documentation
docs(project): add requirements analysis document
chore(repo): update git ignore rules
```

Use short-lived feature branches for larger changes. Keep `main` as the stable project documentation branch.
