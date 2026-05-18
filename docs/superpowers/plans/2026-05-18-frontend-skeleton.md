# Frontend Skeleton Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the initial `learning-garden-web` Next.js skeleton described by the project documentation.

**Architecture:** Create a single Next.js App Router app in the independent web repository. Keep routes, features, API access, runtime capabilities, and shared components in separate directories that match the docs.

**Tech Stack:** Next.js 16, React 19, TypeScript strict, Tailwind CSS v4, pnpm, ESLint, Prettier, Vitest.

---

### Task 1: Repository Foundation

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/package.json`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/tsconfig.json`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/next-env.d.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/next.config.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/postcss.config.mjs`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/eslint.config.mjs`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/prettier.config.mjs`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/vitest.config.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/vitest.setup.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/.gitignore`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/.env.example`

- [ ] **Step 1: Add package and tool configuration**

Create scripts for `dev`, `build`, `lint`, `typecheck`, `test`, `test:run`, and `format`.

- [ ] **Step 2: Install dependencies**

Run: `pnpm install`

- [ ] **Step 3: Confirm package setup**

Run: `pnpm exec tsc --version`

Expected: TypeScript prints a version and exits 0.

### Task 2: Boundary Tests First

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/tests/lib/api/config.test.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/tests/lib/api/http.test.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/tests/runtime/python-runtime.test.ts`

- [ ] **Step 1: Write failing tests**

Add tests for API URL resolution, API JSON/error handling, and the unavailable Python runtime.

- [ ] **Step 2: Run tests and verify RED**

Run: `pnpm test:run`

Expected: FAIL because `lib/api/*` and `runtime/python-runtime` do not exist yet.

### Task 3: API And Runtime Boundaries

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/lib/api/config.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/lib/api/http.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/lib/api/concepts.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/lib/api/identity.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/lib/api/index.ts`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/runtime/python-runtime.ts`

- [ ] **Step 1: Implement minimal API URL and HTTP client code**

The client must prefix relative paths with `/api/v1`, include credentials, parse JSON responses, and throw `ApiError` for non-2xx responses.

- [ ] **Step 2: Implement minimal concept and identity endpoint wrappers**

Expose typed wrappers for `GET /concepts`, `GET /auth/session`, `POST /auth/login`, and `POST /auth/logout`.

- [ ] **Step 3: Implement unavailable Python runtime placeholder**

Expose `PythonRuntime`, `PythonExecutionResult`, and `createUnavailablePythonRuntime()`.

- [ ] **Step 4: Run tests and verify GREEN**

Run: `pnpm test:run`

Expected: PASS.

### Task 4: App Skeleton

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/globals.css`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/layout.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/page.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(community)/community/page.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(workspace)/workspace/page.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(studio)/studio/page.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(admin)/admin/page.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/components/app-shell.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/components/status-pill.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/components/metric-strip.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/concepts/concept-card.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/identity/session-summary.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/workspace/roadmap-preview.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/features/studio/content-draft-panel.tsx`

- [ ] **Step 1: Add root layout and global styles**

Use Tailwind v4 via `@import "tailwindcss";` and keep the visual style utilitarian, responsive, and app-first.

- [ ] **Step 2: Add route surfaces**

Create thin pages for community, workspace, studio, and admin under route groups with concrete URL segments.

- [ ] **Step 3: Add focused feature and shared components**

Keep components small. Do not import one feature from another feature.

- [ ] **Step 4: Run static checks**

Run: `pnpm lint && pnpm typecheck`

Expected: PASS.

### Task 5: Documentation And Verification

**Files:**
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/README.md`

- [ ] **Step 1: Update README**

Replace "planned scaffold" wording with concrete install, run, structure, and verification instructions.

- [ ] **Step 2: Run complete verification**

Run: `pnpm lint && pnpm typecheck && pnpm test:run && pnpm build`

Expected: PASS.

- [ ] **Step 3: Browser verification**

Run the dev server, open the app locally, and verify desktop and mobile viewport rendering for the main page and route entries.

- [ ] **Step 4: Commit the web skeleton**

Run:

```bash
git add .
git commit -m "chore(repo): add next frontend skeleton"
```
