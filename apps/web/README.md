# `@chess-coach/web` — Frontend

Next.js 16 (App Router) PWA. **Not yet scaffolded.**

## Target stack

- Next.js 16, React 19.2, TypeScript (strict)
- Tailwind CSS + shadcn/ui
- TanStack Query (server state)
- react-chessboard + chess.js
- stockfish.wasm in a Web Worker (wrapped behind a `ChessEngineService` interface)
- Serwist for PWA (manifest, icons, asset SW at MVP; full offline in V2)

## Bootstrap (when ready)

```bash
# from repo root
pnpm dlx create-next-app@latest apps/web --ts --app --tailwind --eslint --src-dir --import-alias "@/*"
```

Then wire the package name `@chess-coach/web` in its `package.json` and add `dev`/`build`/`lint`/`test`/`typecheck` scripts so Turborepo picks them up.

See `docs/02-architecture.md` and `docs/04-design-system.md` before writing code.
