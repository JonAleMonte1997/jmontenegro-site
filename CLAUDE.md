# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev        # Dev server at localhost:4321
npm run build      # Build to ./dist/
npm run preview    # Preview production build
npm run astro check  # Type-check .astro files
```

No test suite is configured.

## Architecture

Personal site for Jonathan Montenegro (Backend Developer). Stack: **Astro 4 + Tailwind CSS 3 + MDX**, deployed on Vercel via GitHub push (no manual build step).

### Content Collections

Two Astro content collections defined in `src/content/config.ts`:

- `marcus-ai` — devlog entries for the MarcusAI project (`src/content/marcus-ai/*.md`)
- `aprendizaje` — AI learning modules (`src/content/aprendizaje/*.md`)

Both share the same frontmatter schema: `title`, `description`, `date` (required), `tags[]` (optional), `draft: boolean`.

Files with `draft: true` are filtered out at build time. Filenames become URL slugs.

### Layouts

- `BaseLayout.astro` — wraps every page; includes `<Header>`, `<Footer>`, Google Fonts (Inter, JetBrains Mono, Space Grotesk), `ViewTransitions`, and a scroll-animation observer that adds `.in-view` to elements with `[data-animate]`.
- `ModuleLayout.astro` — used for individual learning module pages (`/aprendizaje/[slug]`).

### Pages

| Route | File |
|---|---|
| `/` | `src/pages/index.astro` |
| `/sobre-mi` | `src/pages/sobre-mi.astro` |
| `/proyectos/marcus-ai` | `src/pages/proyectos/marcus-ai.astro` |
| `/aprendizaje` | `src/pages/aprendizaje/index.astro` |
| `/aprendizaje/[slug]` | `src/pages/aprendizaje/[slug].astro` |
| `/contacto` | `src/pages/contacto.astro` |

### Design tokens (Tailwind)

| Token | Value | Use |
|---|---|---|
| `bg` | `#0d1117` | Page background |
| `surface` | `#161b22` | Cards, borders |
| `text` | `#e6edf3` | Body text |
| `muted` | `#8b949e` | Secondary text |
| `accent` | `#00ff9d` | Electric green — highlights, hover states |
| `font-mono` | JetBrains Mono | Code labels, dates, section headings |
| `font-display` | Space Grotesk | `<h1>`–`<h4>` |

Global CSS utilities in `src/styles/global.css`: `.glow-accent`, `.hover-glow` (lift + glow on hover), `.scanlines-pattern`, `[data-animate]` scroll entrance animations (staggered with `data-animate-delay="1|2|3"`).

### Content authoring

All content is written in **Spanish**. New content files go in:

- `src/content/marcus-ai/YYYY-MM-DD-descripcion.md` — devlog entries
- `src/content/aprendizaje/kebab-case-slug.md` — learning modules

Images go in `public/images/` and are referenced as `/images/nombre.png`. Code blocks must include the language identifier. See `CONTENT_GUIDE.md` in the repo root for full frontmatter reference.
