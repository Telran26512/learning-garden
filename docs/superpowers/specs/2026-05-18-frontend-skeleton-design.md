# Frontend Skeleton Design

## Context

`ai-learning-garden` is the project control and documentation repository. Application source belongs in the independent `learning-garden-web` repository, which already exists at `/Users/raymond/Desktop/Projects/learning-garden-web` and currently contains only the initial README.

The frontend skeleton must follow the documented multi-repo boundary: the web app talks to the backend only through REST `/api/v1` and environment-configured API base URLs. It must not add application source to the docs repository.

## Scope

Build the M0 frontend foundation for `learning-garden-web`:

- Next.js App Router application using TypeScript strict mode, React 19, Tailwind CSS v4, and pnpm.
- Route groups for `(community)`, `(workspace)`, `(studio)`, and `(admin)`, with concrete URL segments under each group.
- Layered directories: `features/`, `lib/api/`, `runtime/`, `components/`, and `tests/`.
- A small API client boundary for identity and concepts using `NEXT_PUBLIC_API_BASE_URL` / `SERVER_API_BASE_URL`.
- A browser runtime boundary with a placeholder Python runtime interface; Pyodide is not loaded in M0.
- Basic lint, typecheck, test, format, and build scripts.
- README and `.env.example` updates for local development.

## Non-Goals

- No backend implementation.
- No real authentication flow beyond frontend boundary placeholders.
- No social, review, graph, moderation, or Pyodide implementation.
- No generated API client; M0 uses a small hand-written client while backend endpoints are still unstable.

## Architecture

The app keeps the documented web layering:

- `app/`: route groups and route-level layout.
- `features/*`: feature-specific presentation modules. Feature modules do not import each other.
- `lib/api/`: the only frontend layer that calls backend REST endpoints.
- `runtime/`: browser-only runtime capability interfaces.
- `components/`: shared presentational UI without business ownership.

The initial pages are intentionally thin. They expose the product surface for the M0/M1 workflow while deferring business behavior to later vertical slices.

## Testing And Verification

Initial automated coverage focuses on the boundaries that should not regress:

- API URL resolution always points at `/api/v1` and respects environment base URLs.
- The API client handles JSON success and error responses consistently.
- The Python runtime placeholder fails clearly until Pyodide is explicitly wired.

Completion requires fresh runs of lint, typecheck, unit tests, production build, and browser verification against a local dev server.
