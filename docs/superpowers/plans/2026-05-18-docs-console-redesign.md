# Docs Console Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Redesign the `learning-garden-web` skeleton into an OpenAI Developers-style docs console across all existing routes.

**Architecture:** Keep the current Next.js App Router structure and API/runtime boundaries. Refactor only the presentation layer: shared shell, docs-style components, page composition, and global visual tokens.

**Tech Stack:** Next.js 16, React 19, TypeScript strict, Tailwind CSS v4, lucide-react, pnpm, Vitest.

---

### Task 1: Shared Docs Shell

**Files:**
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/globals.css`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/components/app-shell.tsx`

- [ ] **Step 1: Replace global visual tokens**

Use a white/gray docs palette, remove the green background gradient, add clear focus states, and preserve responsive minimum width.

- [ ] **Step 2: Refactor `AppShell`**

Build the top header, primary nav, right-side search/action controls, and grouped sidebar. The shell must accept `activePath`, `children`, and optional `sidebarActive`.

- [ ] **Step 3: Verify shell compiles**

Run: `pnpm typecheck`

Expected: PASS.

### Task 2: Docs Components

**Files:**
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/components/docs-card-grid.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/components/docs-code-block.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/components/docs-hero-panel.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/components/docs-notice.tsx`
- Create: `/Users/raymond/Desktop/Projects/learning-garden-web/components/docs-page-header.tsx`

- [ ] **Step 1: Add reusable docs components**

Create focused presentational components for the repeated page structures: page header, large quickstart panel, code example, card grid, and notice panel.

- [ ] **Step 2: Keep components non-business-specific**

Do not import feature modules from these components.

- [ ] **Step 3: Verify lint**

Run: `pnpm lint`

Expected: PASS.

### Task 3: Redesign Routes

**Files:**
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/page.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(community)/community/page.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(workspace)/workspace/page.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(studio)/studio/page.tsx`
- Modify: `/Users/raymond/Desktop/Projects/learning-garden-web/app/(admin)/admin/page.tsx`

- [ ] **Step 1: Recompose `/`**

Create the overview page with `Learning Garden`, a developer-quickstart-style panel, build-path cards, and learning module cards.

- [ ] **Step 2: Recompose secondary routes**

Make `/community`, `/workspace`, `/studio`, and `/admin` use the same docs-console structure with route-specific copy and examples.

- [ ] **Step 3: Verify static checks**

Run: `pnpm lint && pnpm typecheck`

Expected: PASS.

### Task 4: Verification And Commit

**Files:**
- Modify only files changed by Tasks 1-3.

- [ ] **Step 1: Run complete command verification**

Run: `pnpm lint && pnpm typecheck && pnpm test:run && pnpm build && pnpm format`

Expected: PASS.

- [ ] **Step 2: Browser verify all routes**

Run the dev server and check `/`, `/community`, `/workspace`, `/studio`, and `/admin` at desktop and 390px mobile widths.

Expected: all pages render with no horizontal overflow and no console errors.

- [ ] **Step 3: Commit**

Run:

```bash
git add .
git commit -m "style(web): apply docs console redesign"
```
