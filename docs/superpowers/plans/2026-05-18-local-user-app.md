# Local User App Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the first localStorage-backed user-facing product slice for auth, workspace, concepts, studio, community, and admin restrictions.

**Architecture:** Keep the current Next.js App Router and docs-console visual shell. Add a local client-side domain layer under `lib/local-garden/`, then build client feature components that read/write localStorage and render inside existing route pages.

**Tech Stack:** Next.js 16, React 19, TypeScript strict, Tailwind CSS v4, localStorage, Vitest.

---

### Task 1: Local Domain And Tests

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/lib/local-garden/types.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/lib/local-garden/seed.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/lib/local-garden/store.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/tests/local-garden/store.test.ts`

- [ ] **Step 1: Write failing tests**

Cover registration, login, logout, session lookup, roadmap task toggling, concept CRUD, and public concept filtering.

- [ ] **Step 2: Verify RED**

Run: `pnpm test:run tests/local-garden/store.test.ts`

Expected: FAIL because `lib/local-garden/*` does not exist.

- [ ] **Step 3: Implement local data layer**

Add typed state, seed data, localStorage adapter, and pure functions for auth, roadmap, and concepts.

- [ ] **Step 4: Verify GREEN**

Run: `pnpm test:run tests/local-garden/store.test.ts`

Expected: PASS.

### Task 2: Auth And Session UI

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/auth/page.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/auth/auth-panel.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/auth/session-controls.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/components/app-shell.tsx`

- [ ] **Step 1: Implement `/auth`**

Create a docs-style local sign in / register page with demo account hints.

- [ ] **Step 2: Add session controls to shell**

Show current user and role when logged in, otherwise show a sign-in link. Add logout.

- [ ] **Step 3: Verify**

Run: `pnpm lint && pnpm typecheck`

Expected: PASS.

### Task 3: Workspace

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/workspace/workspace-client.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(workspace)/workspace/page.tsx`

- [ ] **Step 1: Add route protection**

Client-side redirect unauthenticated users to `/auth?next=/workspace`.

- [ ] **Step 2: Add Roadmap workflow**

Render stage/week grouped tasks, checkboxes, current task, next task, and current user summary.

- [ ] **Step 3: Verify**

Run: `pnpm lint && pnpm typecheck`

Expected: PASS.

### Task 4: Studio And Concepts

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/studio/studio-client.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/concepts/concept-detail-client.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(workspace)/workspace/concepts/[slug]/page.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(studio)/studio/page.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(community)/community/page.tsx`

- [ ] **Step 1: Add Studio route protection**

Redirect unauthenticated users to `/auth?next=/studio`.

- [ ] **Step 2: Add concept CRUD**

Implement create, edit, delete, draft save, preview, tags, stage, week, slug, summary, body, visibility, and resource association fields.

- [ ] **Step 3: Add concept detail**

Render math, runnable-code unavailable-runtime state, and paper sections from a local concept.

- [ ] **Step 4: Add public community list**

Show `public` concepts from all users.

- [ ] **Step 5: Verify**

Run: `pnpm lint && pnpm typecheck && pnpm test:run`

Expected: PASS.

### Task 5: Admin Restriction And Final Verification

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/admin/admin-client.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(admin)/admin/page.tsx`

- [ ] **Step 1: Restrict admin page**

Show admin shell only for `role='admin'`. Show a no-access panel for signed-in non-admin users and redirect guests to auth.

- [ ] **Step 2: Full verification**

Run: `pnpm lint && pnpm typecheck && pnpm test:run && pnpm build && pnpm format`

Expected: PASS.

- [ ] **Step 3: Browser verification**

Verify auth, workspace, studio, concept detail, community public concepts, logout, and admin restriction.

- [ ] **Step 4: Commit**

Run:

```bash
git add .
git commit -m "feat(web): add local user app workflows"
```
