# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal website served as a **static site on GitHub Pages**. No framework, no
build step, no dependencies — plain HTML/CSS/JS served directly from the repo root.

## Architecture

- `index.html` — the page; all content lives here as markup.
- `styles.css` — single global stylesheet. Theme is driven by CSS custom properties
  in `:root`, with a `prefers-color-scheme: dark` override block. Change colors/spacing
  there rather than scattering literals.
- `main.js` — progressive enhancement only (e.g. footer year). The page must render
  fully without JS.
- `.nojekyll` — disables GitHub's Jekyll processing so files (incl. those starting
  with `_`) are served verbatim. Keep it.

## Deploying

There is no CI. GitHub Pages serves the `main` branch root directly (Settings → Pages →
Deploy from a branch → `main` / root). Pushing to `main` publishes. See README for setup.

## Local preview

`python3 -m http.server 8000` from the repo root, then open http://localhost:8000.
(Opening `index.html` via `file://` also works but won't match server path behavior.)

## Conventions

- Keep it dependency-free and buildless unless there's a strong reason to add tooling.
- For a GitHub **user site**, the repo must be named `<username>.github.io`; a project
  site can have any name but is served under `/<repo>/` — use root-relative paths
  carefully if you switch to a project site.
