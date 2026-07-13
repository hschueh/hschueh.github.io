# Projects Portfolio & Intro Pages — Design

Date: 2026-07-12
Status: Approved

## Goal

Add a portfolio sub-page at `/projects/` and a concise intro page for each active
side project in `~/dev`, integrated into the existing static GitHub Pages site
(no build step, no dependencies).

## Scope

Detail pages for the five projects with READMEs:

| Project | Source dir | Page |
|---|---|---|
| GGAI | `~/dev/ggai` | `projects/ggai.html` |
| Hudder | `~/dev/hudder` | `projects/hudder.html` |
| OffTrack | `~/dev/offtrack` | `projects/offtrack.html` |
| Pawsitive Track | `~/dev/pawsitive-track-app` | `projects/pawsitive-track.html` |
| TrashTalker | `~/dev/trashtalker` | `projects/trashtalker.html` |

Excluded: `cool-stuffs`, `howtonote` (no READMEs), `ggai-worktrees` (worktrees).
The two published apps already on the homepage — Oh Tai Gi and 2048 3D — appear
as portfolio cards linking directly to the Play Store, with no detail pages.

## Pages

### `projects/index.html` — portfolio

- Same visual language as the homepage: reuses `/styles.css`, same Google Fonts
  and inline SVG favicon.
- Header with page title and a link back to `/`.
- Card grid: each card shows project name, tech tag, one-line pitch. The five
  scoped projects link to their detail pages; Oh Tai Gi and 2048 3D link to the
  Play Store.

### `projects/<name>.html` — project intros

Concise, text-only pages containing:

- Top nav: "← All projects" (to `/projects/`) and a home link.
- Title, one-line pitch, tech-stack tags.
- 2–3 paragraphs: what it is, how it works, highlights — drawn from each
  project's README, translated to English where the README is in Chinese
  (TrashTalker). TrashTalker's page links to SpeakSlow as inspiration.
- All five repos are private, so no source links. Each page states status
  honestly (personal prototype / in development / on-device app, etc.).

## Homepage changes

- "Side Projects" section gets a "See all projects →" link to `/projects/`.
- Pawsitive Track and GGAI entries link to their new detail pages.
- Everything else unchanged.

## Styling

Small additions to `styles.css` only: card grid for the portfolio and a
detail-page header/nav treatment. Use existing CSS custom properties so the
`prefers-color-scheme: dark` theme works without extra rules.

## Constraints kept

- No build step, no JS requirement for content (`main.js` footer-year only).
- Root-relative paths (`/styles.css`, `/projects/...`), consistent with the site.
- No binary assets added to the repo (text-only pages).
