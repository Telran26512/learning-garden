# Docs Console Redesign Design

## Context

The user wants the `learning-garden-web` skeleton unified into the same visual language as the provided OpenAI Developers documentation screenshot. The target is not a pixel clone. The target is a documentation-console style product surface: white header, left navigation, restrained gray surfaces, large readable content blocks, and code/content examples as focal points.

This redesign applies to the existing frontend routes:

- `/`
- `/community`
- `/workspace`
- `/studio`
- `/admin`

## Design Direction

Use the **Docs Console** approach:

- Persistent top navigation with the product wordmark, primary route links, a search control, and one black primary action.
- Left documentation sidebar with grouped navigation and a gray active item.
- Main content area with a large page title, a broad quickstart-style panel, and secondary bordered content blocks.
- Mostly black, white, and gray. Keep one low-saturation green accent for learning status and progress markers.
- No marketing hero treatment, no dashboard gradient, and no decorative card-heavy layout.

## Layout

`AppShell` becomes the shared docs-style shell:

- Header:
  - Brand: `AI Learning Garden`
  - Primary nav: Home, Community, Workspace, Studio, Admin
  - Right controls: search box, `Start learning` button, settings icon
- Sidebar:
  - `Get started`: Overview, Roadmap, Concepts
  - `Learning modules`: Math derivations, Runnable code, Papers, Review
  - `Community`: Public notes, Profiles, Discussions
  - Active route item uses a light gray background.
- Main:
  - Width constrained similarly to the reference.
  - Generous top spacing.
  - Page title appears before the first large content panel.
  - Major panels use light gray fill or 1px border, not heavy shadows.

On mobile, the shell remains simple:

- Header stacks naturally.
- Sidebar appears above content as a compact navigation block.
- No drawer or complex interactive navigation in this pass.

## Route Content

### `/`

Overview page for the learning platform:

- H1: `Learning Garden`
- Large quickstart panel:
  - Left: project quickstart copy and two actions.
  - Right: Markdown/code example showing math, runnable Python, and paper links.
- Secondary "Build paths" section for Workspace and Studio paths.
- "Models" analog becomes "Learning modules" with module cards.

### `/community`

Public learning content section:

- H1: `Community`
- Large intro panel explaining public notes and visibility.
- Build-path cards for Public notes, Profiles, and Discussions.

### `/workspace`

Personal learning workspace:

- H1: `Workspace`
- Quickstart panel for the current learning path.
- Right-side checklist-style example.
- Cards for Roadmap, Concepts, and Review.

### `/studio`

Content authoring section:

- H1: `Studio`
- Quickstart panel for writing one concept from Markdown to code to paper links.
- Cards for Markdown, KaTeX, Runnable Python, and Paper links.

### `/admin`

Restricted admin shell only:

- H1: `Admin`
- Restricted-section panel noting this is reserved for M6.
- Cards for Reports, Moderation, and Registration controls, all explicitly inactive.

## Components

Refactor the existing component layer rather than adding a parallel design system:

- Replace `AppShell` with docs shell layout.
- Add focused docs components:
  - `DocsHeroPanel`
  - `DocsCodeBlock`
  - `DocsCardGrid`
  - `DocsPageHeader`
  - `DocsNotice`
- Keep API and runtime code unchanged.
- Existing feature components may be simplified or replaced where they conflict with the docs-console layout.

## Visual Rules

- Background: `#ffffff` with subtle `#f7f7f8` panels.
- Text: near-black for headings, neutral gray for body.
- Borders: `#e5e5e5`.
- Active nav: `#ececec`.
- Primary action: black fill, white text.
- Radius: 10px for nav/search, 14px for large panels.
- Shadows: remove most shadows.
- Typography: system sans or Geist-like stack; clear hierarchy, no decorative display font.
- Icons: small, restrained, consistent stroke.
- No nested card structures.

## Testing And Verification

Fresh verification must include:

- `pnpm lint`
- `pnpm typecheck`
- `pnpm test:run`
- `pnpm build`
- `pnpm format`
- Browser verification of all five routes on desktop and 390px mobile viewport:
  - route renders
  - correct heading appears
  - no horizontal overflow
  - no console errors
