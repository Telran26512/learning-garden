# Local User App Design

## Context

The server repository currently has no API implementation. The first user-facing version will therefore be a frontend-local product slice in `learning-garden-web`. It must keep the OpenAI Developers-inspired visual language already applied to the skeleton while making the user workflow usable.

This is a local-first prototype, not the final persistence architecture. It validates the product surface before the Go backend exists.

## Scope

Implement a localStorage-backed first version for:

- Registration, login, logout, session state, current user, and `user` / `admin` role.
- Route protection for `/workspace` and `/studio`.
- Workspace Roadmap with stage/week grouping, task check state, current task, and next task.
- Concept CRUD with title, slug, summary, body, tags, stage, week, and `private` / `public` visibility.
- Concept detail page with math, runnable-code stub, and paper section.
- Studio markdown editor with draft saving, preview, visibility setting, and resource association fields.
- Community page showing public concepts.
- Admin page restricted to admin users.

## Non-Goals

- No real backend calls for these workflows.
- No password security beyond local prototype storage.
- No real file upload.
- No real Pyodide loading; the code section uses an explicit unavailable-runtime state.
- No full Markdown/KaTeX parser in this pass.
- No social features, comments, follows, notifications, or moderation behavior.

## Local Data Model

Persist under a single localStorage key such as `learning-garden.local.v1`:

- `users`: local users with `id`, `email`, `password`, `displayName`, `handle`, `role`, timestamps.
- `session`: current `userId` and timestamp.
- `roadmapTasks`: seeded learning tasks with `stage`, `week`, `title`, `description`.
- `taskChecks`: map of `userId:taskId` to checked state.
- `concepts`: local concept records owned by a user.
- `drafts`: unsaved editor state per user.

Seed data:

- Admin account: `admin@garden.local` / `admin123`
- User account: `learner@garden.local` / `learn123`
- A small roadmap around math, machine learning, and reinforcement learning.
- One private concept and one public concept for quick visual feedback.

## UX

Keep the OpenAI Developers-style shell:

- Top white navigation.
- Minimal gray active states.
- Large centered or left-aligned content blocks.
- Rounded light-gray panels.
- Restrained cards.
- Code/Markdown examples as a visual anchor.

Pages:

- `/auth`: tabbed sign in / register panel.
- `/workspace`: route-protected workspace dashboard.
- `/studio`: route-protected concept editor and local concept list.
- `/workspace/concepts/[slug]`: concept detail.
- `/community`: public concept list.
- `/admin`: admin-only local shell.

When a protected page has no session, redirect to `/auth?next=<path>`.

## Validation

Automated tests should cover pure local data behavior:

- Register/login/logout/session.
- Duplicate email rejection.
- Roadmap task toggling.
- Concept create/update/delete.
- Visibility filtering for public concepts.

Manual browser checks must cover:

- Register, login, logout.
- Workspace access protection.
- Studio concept creation and editing.
- Concept detail page.
- Public concept appears in community after visibility is set to public.
- Admin page denies non-admin users.
