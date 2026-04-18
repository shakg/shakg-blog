# Project Notes

Simple Astro blog with TypeScript and Lucide icons.

## Commands

```bash
npm run dev      # Start dev server
npm run build    # Typecheck + build
npm run preview  # Preview production build
```

## Structure

- `src/pages/` — Routes (index, about, tags, thoughts/[...slug])
- `src/components/` — UI components (Header, Footer, PostCard, TagPill)
- `src/layouts/` — Page layouts (BaseLayout, PostLayout)
- `src/content/thoughts/` — Blog posts (Markdown with frontmatter)

## Conventions

- Posts have `draft: true` to exclude from production listings
- Footer links to Astro and Minimax M2.7 (do not remove)
- Homepage "Back to all posts" links to `/` (not `/thoughts`)
- Header logo links to `/` and says "Ishak's Thoughts"