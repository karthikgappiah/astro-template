# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
pnpm dev          # Start dev server
pnpm build        # Production build (outputs to dist/)
pnpm preview      # Preview production build
pnpm lint         # ESLint (TypeScript/TSX files only)
pnpm format       # Prettier (TS, TSX, Astro files)
pnpm typecheck    # astro check (type-checks .astro + TS files)
```

There are no tests configured in this project.

## Architecture

This is an Astro 5 static site template with React islands, Tailwind CSS v4, and a shadcn-compatible component system.

**Key architectural decisions:**

- **Astro + React islands**: Pages and layouts are `.astro` files. React components live in `src/components/` and require `client:load` (or other client directives) to be interactive. Static markup in `.astro` files needs no directive.
- **Tailwind CSS v4 via Vite plugin**: Tailwind is loaded via `@tailwindcss/vite` in `astro.config.mjs`, not via PostCSS. The stylesheet entry point is `src/styles/global.css`, which imports Tailwind, `tw-animate-css`, and the shadcn design tokens via `shadcn/tailwind.css`.
- **Design tokens**: CSS custom properties (oklch-based colors, radius scale) are defined in `src/styles/global.css` and aliased into Tailwind's theme via `@theme inline`. Dark mode is class-based (`.dark` selector).
- **UI components**: `src/components/ui/` holds shadcn-style components built on `@base-ui/react` primitives (not Radix), styled with `cva` + `cn`.
- **Path alias**: `@/` maps to `src/` (configured in both `tsconfig.json` and resolved automatically by Astro).
- **`cn` utility**: `src/lib/utils.ts` exports `cn()` — `clsx` + `tailwind-merge`. Prettier is configured to sort Tailwind classes within `cn()` and `cva()` calls.

**Deployment**: The CI workflow (`.github/workflows/build-ghcr.yaml`) builds a Docker image on push to `main` and pushes it to GHCR. The Dockerfile builds with pnpm and serves the static `dist/` via nginx.

## Adding UI Components

New UI components should follow the pattern in `src/components/ui/button.tsx`: use `@base-ui/react` primitives, define variants with `cva`, and export from the component file. Icons come from `@phosphor-icons/react`.
