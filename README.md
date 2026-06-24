# Personal Website

A barebone static personal website, hosted on GitHub Pages.

## Local preview

It's plain HTML/CSS/JS — open `index.html` directly, or serve it:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploy to GitHub Pages

1. Push this repo to GitHub (repo name `<username>.github.io` for a user site,
   or any name for a project site).
2. In the repo: **Settings → Pages → Build and deployment**.
3. Set **Source** to *Deploy from a branch*, branch `main`, folder `/ (root)`.
4. Save. The site goes live at `https://<username>.github.io/` (or
   `/<repo>/` for a project site) within a minute or two.

No build step. `.nojekyll` disables Jekyll so files are served as-is.
