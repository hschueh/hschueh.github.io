# Projects Portfolio & Intro Pages Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a portfolio page at `/projects/` plus five project intro pages, linked from the homepage.

**Architecture:** Plain static HTML pages in a new `projects/` directory, sharing the existing `/styles.css` (with small additions: breadcrumb nav, card grid, project-hero tweaks). No build step, no new JS, root-relative paths throughout.

**Tech Stack:** HTML + CSS only. Local verification via `python3 -m http.server`.

**Spec:** `docs/superpowers/specs/2026-07-12-projects-portfolio-design.md`

## Global Constraints

- No build step, no dependencies, no binary assets.
- Pages must render fully without JS (`/main.js` only fills the footer year).
- Root-relative paths: `/styles.css`, `/main.js`, `/projects/...`.
- Reuse existing CSS custom properties so dark mode works without new rules.
- All five project repos are private — no source-code links on any page.
- All page copy in English (translate TrashTalker's Chinese README content).

## Shared HTML skeleton

Every new page uses this head block (only `<title>` and `<meta name="description">` differ per page — each task shows its full file, this is for orientation):

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>PAGE TITLE — HoSo Chueh</title>
  <meta name="description" content="PAGE DESCRIPTION" />

  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=JetBrains+Mono:wght@400;500&family=Inter:wght@400;500&display=swap" rel="stylesheet" />

  <link rel="stylesheet" href="/styles.css" />
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><polyline points='10,80 35,60 55,68 75,30 90,18' fill='none' stroke='%2300a878' stroke-width='10' stroke-linecap='round' stroke-linejoin='round'/></svg>" />
</head>
```

And this footer + script, identical to the homepage:

```html
    <footer>
      <p>© <span id="year"></span> HoSo Chueh · built plainly, hosted on GitHub Pages</p>
    </footer>
  </main>

  <script src="/main.js"></script>
</body>
</html>
```

---

### Task 1: Stylesheet additions

**Files:**
- Modify: `styles.css` (append before the trailing `/* ---------- Responsive ---------- */` comment, i.e. at end of file)

**Interfaces:**
- Produces CSS classes used by later tasks: `.crumbs` (breadcrumb nav on sub-pages), `.proj-hero` (project page header), `.card-grid` / `.card` / `.card .ext` (portfolio cards), `.more` (homepage "see all" link).

- [ ] **Step 1: Append the new styles**

Append to the end of `styles.css`:

```css
/* ---------- Sub-pages ---------- */

.crumbs {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
  font-family: var(--mono);
  font-size: 0.78rem;
  margin-bottom: clamp(2rem, 5vw, 3rem);
}

.proj-hero { margin-bottom: clamp(2.5rem, 6vw, 3.5rem); }
.proj-hero h1 { font-size: clamp(2.1rem, 6vw, 3rem); }
.proj-hero .tagline { max-width: 52ch; }
.proj-hero .skills { margin-top: 1.25rem; }

.more {
  font-family: var(--mono);
  font-size: 0.78rem;
  margin: 1.1rem 0 0;
}

/* ---------- Portfolio cards ---------- */

.card-grid {
  list-style: none;
  padding: 0;
  margin: 0;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(15rem, 1fr));
  gap: 1rem;
}

.card {
  display: block;
  height: 100%;
  background: var(--panel);
  border: 1px solid var(--rule);
  border-radius: 8px;
  padding: 1.1rem 1.2rem;
  color: var(--ink);
  transition: border-color 0.15s ease;
}
.card:hover {
  border-color: var(--signal);
  text-decoration: none;
}
.card .proj-tag { display: block; margin: 0.15rem 0 0.4rem; }
.card p { margin: 0; color: var(--muted); font-size: 0.92rem; }
.card .ext {
  display: block;
  margin-top: 0.5rem;
  font-family: var(--mono);
  font-size: 0.68rem;
  color: var(--muted);
}
```

Note: `.proj-tag` already sets `margin-left: 0.7rem`; inside cards the tag sits on its own line, and the `.card .proj-tag` rule's `margin: 0.15rem 0 0.4rem` shorthand resets that left margin.

- [ ] **Step 2: Verify the stylesheet still parses**

Run: `python3 -c "css=open('styles.css').read(); assert css.count('{')==css.count('}'), 'unbalanced braces'; print('ok')"`
Expected: `ok`

- [ ] **Step 3: Commit**

```bash
git add styles.css
git commit -m "feat: styles for portfolio cards and project sub-pages"
```

---

### Task 2: Portfolio index page

**Files:**
- Create: `projects/index.html`

**Interfaces:**
- Consumes: `.crumbs`, `.card-grid`, `.card`, `.card .ext` from Task 1.
- Produces: links to `/projects/ggai.html`, `/projects/hudder.html`, `/projects/offtrack.html`, `/projects/pawsitive-track.html`, `/projects/trashtalker.html` — Tasks 3–7 must create exactly these filenames.

- [ ] **Step 1: Create `projects/index.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Projects — HoSo Chueh</title>
  <meta name="description" content="Side projects by HoSo Chueh — mobile apps, AI-driven games, and on-device tools." />

  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=JetBrains+Mono:wght@400;500&family=Inter:wght@400;500&display=swap" rel="stylesheet" />

  <link rel="stylesheet" href="/styles.css" />
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><polyline points='10,80 35,60 55,68 75,30 90,18' fill='none' stroke='%2300a878' stroke-width='10' stroke-linecap='round' stroke-linejoin='round'/></svg>" />
</head>
<body>
  <main>
    <nav class="crumbs">
      <a href="/">← HoSo Chueh</a>
    </nav>

    <header class="hero proj-hero">
      <p class="eyebrow">Portfolio</p>
      <h1>Projects</h1>
      <p class="tagline">Side projects built after hours — mobile apps, AI-driven
        games, and on-device tools. Most are personal prototypes; a couple made
        it to the Play Store.</p>
    </header>

    <section>
      <h2>Active Projects</h2>
      <ul class="card-grid">
        <li>
          <a class="card" href="/projects/pawsitive-track.html">
            <span class="proj-name">Pawsitive Track</span>
            <span class="proj-tag">React Native</span>
            <p>Track dog walks — live GPS routes, per-dog event logging,
               background tracking, and GPX export.</p>
          </a>
        </li>
        <li>
          <a class="card" href="/projects/trashtalker.html">
            <span class="proj-name">TrashTalker</span>
            <span class="proj-tag">Kotlin · sherpa-onnx</span>
            <p>Fully offline Traditional Chinese voice typing for Android —
               a floating mic and a voice keyboard, no audio ever leaves the device.</p>
          </a>
        </li>
        <li>
          <a class="card" href="/projects/ggai.html">
            <span class="proj-name">GGAI</span>
            <span class="proj-tag">Turborepo · AI SDK</span>
            <p>An AI-native game monorepo: LLM-driven text adventures and
               life-sims with Discord and web UIs.</p>
          </a>
        </li>
        <li>
          <a class="card" href="/projects/offtrack.html">
            <span class="proj-name">OffTrack</span>
            <span class="proj-tag">React Native · MapLibre</span>
            <p>View-only hiking trail viewer — import GPX routes and take
               fully offline maps on the trail.</p>
          </a>
        </li>
        <li>
          <a class="card" href="/projects/hudder.html">
            <span class="proj-name">Hudder</span>
            <span class="proj-tag">Kotlin · Compose</span>
            <p>Turns an Android phone into a windshield heads-up display by
               mirroring the app in the other split-screen pane.</p>
          </a>
        </li>
      </ul>
    </section>

    <section>
      <h2>Published Apps</h2>
      <ul class="card-grid">
        <li>
          <a class="card" href="https://play.google.com/store/apps/details?id=com.gonnaggstudio.oh_tai_gi">
            <span class="proj-name">Oh Tai Gi</span>
            <span class="proj-tag">Flutter</span>
            <p>Taiwanese vocabulary learning app. 10,000+ downloads.</p>
            <span class="ext">Google Play ↗</span>
          </a>
        </li>
        <li>
          <a class="card" href="https://play.google.com/store/apps/details?id=com.gonnaggstudio.threed2048">
            <span class="proj-name">2048 3D</span>
            <span class="proj-tag">Unity</span>
            <p>A 2048 variation reimagined in 3D.</p>
            <span class="ext">Google Play ↗</span>
          </a>
        </li>
      </ul>
    </section>

    <footer>
      <p>© <span id="year"></span> HoSo Chueh · built plainly, hosted on GitHub Pages</p>
    </footer>
  </main>

  <script src="/main.js"></script>
</body>
</html>
```

- [ ] **Step 2: Verify it serves**

Start the preview server if not already running: `python3 -m http.server 8000 &` (from repo root).

Run: `curl -s http://localhost:8000/projects/ | grep -o '<title>[^<]*</title>'`
Expected: `<title>Projects — HoSo Chueh</title>`

- [ ] **Step 3: Commit**

```bash
git add projects/index.html
git commit -m "feat: portfolio index page at /projects/"
```

---

### Task 3: GGAI intro page

**Files:**
- Create: `projects/ggai.html`

**Interfaces:**
- Consumes: `.crumbs`, `.proj-hero` from Task 1; linked from `/projects/` (Task 2).

- [ ] **Step 1: Create `projects/ggai.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>GGAI — HoSo Chueh</title>
  <meta name="description" content="GGAI — an AI-native game monorepo: LLM-driven text adventures and life-sims with Discord and web UIs." />

  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=JetBrains+Mono:wght@400;500&family=Inter:wght@400;500&display=swap" rel="stylesheet" />

  <link rel="stylesheet" href="/styles.css" />
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><polyline points='10,80 35,60 55,68 75,30 90,18' fill='none' stroke='%2300a878' stroke-width='10' stroke-linecap='round' stroke-linejoin='round'/></svg>" />
</head>
<body>
  <main>
    <nav class="crumbs">
      <a href="/projects/">← All projects</a>
      <a href="/">Home</a>
    </nav>

    <header class="hero proj-hero">
      <p class="eyebrow">Side project · in development</p>
      <h1>GGAI</h1>
      <p class="tagline">An AI-native game monorepo: LLM-driven text adventures
        and life-sims with Discord and web UIs.</p>
      <ul class="skills">
        <li>TypeScript</li><li>Node.js</li><li>Turborepo</li>
        <li>AI SDK</li><li>Zod</li><li>Next.js</li><li>Discord</li>
      </ul>
    </header>

    <section>
      <h2>What it is</h2>
      <p>
        GGAI is a workspace of AI-driven games and the shared libraries that power
        them. It currently hosts two games, both played in Traditional Chinese:
        <strong>GGAI MUD</strong>, a single-player turn-based text adventure with an
        LLM-driven narrative that you can play from Discord or a web UI, and
        <strong>Mountain Road Monkey</strong>, a 30-day Taiwanese motorcycle life-sim
        presented as a phone UI in Next.js, complete with LLM-narrated mountain
        runs and LINE-style messages.
      </p>
    </section>

    <section>
      <h2>How it's built</h2>
      <p>
        The monorepo is managed with Turborepo: games depend on shared packages for
        structured logging and an AI layer that wraps the AI SDK, loads prompts, and
        parses LLM output through Zod schemas so the games can trust what the model
        returns. A few deliberate constraints keep it simple — no database (game
        state lives as markdown and JSON on the file system), strict TypeScript with
        ES modules everywhere, and models served through OpenRouter so each game
        picks its own model via config.
      </p>
      <p>
        Each game deploys independently to a VPS under PM2, so shipping one game
        never touches the other.
      </p>
    </section>

    <footer>
      <p>© <span id="year"></span> HoSo Chueh · built plainly, hosted on GitHub Pages</p>
    </footer>
  </main>

  <script src="/main.js"></script>
</body>
</html>
```

- [ ] **Step 2: Verify it serves**

Run: `curl -s http://localhost:8000/projects/ggai.html | grep -o '<title>[^<]*</title>'`
Expected: `<title>GGAI — HoSo Chueh</title>`

- [ ] **Step 3: Commit**

```bash
git add projects/ggai.html
git commit -m "feat: GGAI project intro page"
```

---

### Task 4: Hudder intro page

**Files:**
- Create: `projects/hudder.html`

**Interfaces:**
- Consumes: `.crumbs`, `.proj-hero` from Task 1; linked from `/projects/` (Task 2).

- [ ] **Step 1: Create `projects/hudder.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Hudder — HoSo Chueh</title>
  <meta name="description" content="Hudder — an Android prototype that turns a phone into a windshield heads-up display by mirroring a split-screen app." />

  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=JetBrains+Mono:wght@400;500&family=Inter:wght@400;500&display=swap" rel="stylesheet" />

  <link rel="stylesheet" href="/styles.css" />
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><polyline points='10,80 35,60 55,68 75,30 90,18' fill='none' stroke='%2300a878' stroke-width='10' stroke-linecap='round' stroke-linejoin='round'/></svg>" />
</head>
<body>
  <main>
    <nav class="crumbs">
      <a href="/projects/">← All projects</a>
      <a href="/">Home</a>
    </nav>

    <header class="hero proj-hero">
      <p class="eyebrow">Side project · experimental prototype</p>
      <h1>Hudder</h1>
      <p class="tagline">Turns an Android phone or tablet into a simple
        heads-up display mirror for windshield reflection.</p>
      <ul class="skills">
        <li>Kotlin</li><li>Jetpack Compose</li><li>MediaProjection</li>
        <li>Foreground service</li><li>Material 3</li>
      </ul>
    </header>

    <section>
      <h2>What it is</h2>
      <p>
        Put the phone on the dashboard, open a navigation app in one split-screen
        pane and Hudder in the other, and start capture: Hudder grabs the full
        display, crops out the opposite pane, and renders it mirrored so its
        reflection reads correctly on the windshield. Options cover horizontal
        flip, 180° rotation, a dim backdrop, and which pane to mirror.
      </p>
    </section>

    <section>
      <h2>How it works</h2>
      <p>
        The app tracks its own window bounds and whether Android is in multi-window
        mode. A foreground service receives frames from a
        <code>MediaProjection</code>-based capture pipeline, maps the app window
        into captured-screen coordinates, and crops the opposite split-screen pane.
        A debug mode can show the raw captured frame with the computed crop
        rectangle overlaid — split-screen geometry is fiddly, and seeing the math
        beats guessing at it.
      </p>
      <p>
        It's an experimental driving-adjacent prototype, not a production
        automotive system: frame processing is deliberately throttled, and
        DRM-protected apps capture as black frames.
      </p>
    </section>

    <footer>
      <p>© <span id="year"></span> HoSo Chueh · built plainly, hosted on GitHub Pages</p>
    </footer>
  </main>

  <script src="/main.js"></script>
</body>
</html>
```

- [ ] **Step 2: Verify it serves**

Run: `curl -s http://localhost:8000/projects/hudder.html | grep -o '<title>[^<]*</title>'`
Expected: `<title>Hudder — HoSo Chueh</title>`

- [ ] **Step 3: Commit**

```bash
git add projects/hudder.html
git commit -m "feat: Hudder project intro page"
```

---

### Task 5: OffTrack intro page

**Files:**
- Create: `projects/offtrack.html`

**Interfaces:**
- Consumes: `.crumbs`, `.proj-hero` from Task 1; linked from `/projects/` (Task 2).

- [ ] **Step 1: Create `projects/offtrack.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>OffTrack — HoSo Chueh</title>
  <meta name="description" content="OffTrack — a view-only hiking trail viewer for Android with GPX import and automatic offline maps." />

  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=JetBrains+Mono:wght@400;500&family=Inter:wght@400;500&display=swap" rel="stylesheet" />

  <link rel="stylesheet" href="/styles.css" />
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><polyline points='10,80 35,60 55,68 75,30 90,18' fill='none' stroke='%2300a878' stroke-width='10' stroke-linecap='round' stroke-linejoin='round'/></svg>" />
</head>
<body>
  <main>
    <nav class="crumbs">
      <a href="/projects/">← All projects</a>
      <a href="/">Home</a>
    </nav>

    <header class="hero proj-hero">
      <p class="eyebrow">Side project · personal tool</p>
      <h1>OffTrack</h1>
      <p class="tagline">A view-only hiking trail viewer for Android —
        import GPX routes and take fully offline maps on the trail.</p>
      <ul class="skills">
        <li>React Native</li><li>TypeScript</li><li>Zustand</li>
        <li>MapLibre</li><li>OpenStreetMap</li>
      </ul>
    </header>

    <section>
      <h2>What it is</h2>
      <p>
        Import a GPX file — say, a route exported from AllTrails — and OffTrack
        draws it on an OSM-based map with distance and elevation stats and an
        elevation profile. It's deliberately view-only: no walk recording, no
        accounts, no remote database. Trails are just files on the device, a GPX
        per trail plus a JSON index.
      </p>
    </section>

    <section>
      <h2>Offline by default</h2>
      <p>
        Opening a trail automatically downloads an offline tile pack covering the
        route's bounding box (padded ~10%, zoom levels 8–15) from OpenFreeMap via
        MapLibre. Trails with a downloaded pack show an "offline" badge and render
        fully without a network connection — which is the point, since the best
        trails rarely come with reception. Failed downloads retry the next time
        the trail is opened.
      </p>
      <p>
        GPX files can come in through an import button, Android's share sheet, or
        "open with" from a file manager — all three converge on the same pipeline:
        parse, validate, compute stats, copy into app storage.
      </p>
    </section>

    <footer>
      <p>© <span id="year"></span> HoSo Chueh · built plainly, hosted on GitHub Pages</p>
    </footer>
  </main>

  <script src="/main.js"></script>
</body>
</html>
```

- [ ] **Step 2: Verify it serves**

Run: `curl -s http://localhost:8000/projects/offtrack.html | grep -o '<title>[^<]*</title>'`
Expected: `<title>OffTrack — HoSo Chueh</title>`

- [ ] **Step 3: Commit**

```bash
git add projects/offtrack.html
git commit -m "feat: OffTrack project intro page"
```

---

### Task 6: Pawsitive Track intro page

**Files:**
- Create: `projects/pawsitive-track.html`

**Interfaces:**
- Consumes: `.crumbs`, `.proj-hero` from Task 1; linked from `/projects/` (Task 2) and the homepage (Task 8).

- [ ] **Step 1: Create `projects/pawsitive-track.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Pawsitive Track — HoSo Chueh</title>
  <meta name="description" content="Pawsitive Track — a React Native app for tracking dog walks: GPS routes, per-dog event logging, and GPX export." />

  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=JetBrains+Mono:wght@400;500&family=Inter:wght@400;500&display=swap" rel="stylesheet" />

  <link rel="stylesheet" href="/styles.css" />
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><polyline points='10,80 35,60 55,68 75,30 90,18' fill='none' stroke='%2300a878' stroke-width='10' stroke-linecap='round' stroke-linejoin='round'/></svg>" />
</head>
<body>
  <main>
    <nav class="crumbs">
      <a href="/projects/">← All projects</a>
      <a href="/">Home</a>
    </nav>

    <header class="hero proj-hero">
      <p class="eyebrow">Side project · in development</p>
      <h1>Pawsitive Track</h1>
      <p class="tagline">Every walk tells a tail. 🐾 A React Native app for
        tracking dog walks — GPS routes, walk events, and GPX export.</p>
      <ul class="skills">
        <li>React Native</li><li>React 19</li><li>TypeScript</li>
        <li>Zustand</li><li>iOS + Android</li><li>i18next</li>
      </ul>
    </header>

    <section>
      <h2>What it is</h2>
      <p>
        Start a walk and Pawsitive Track records the route live on a map — and,
        because dog walks are about the dogs, it logs per-dog events along the
        way: pee, poop (with consistency, for the diligent), photos, encounters,
        incidents, water breaks. Finished walks land in a history you can review,
        export, and share as GPX, or turn into a shareable photo with the route,
        stats, and a watermark overlaid.
      </p>
    </section>

    <section>
      <h2>The details that matter</h2>
      <p>
        Tracking keeps running when the app is backgrounded, and a crash-recovery
        path detects interrupted walks on restart and offers to resume. Start a
        walk near a familiar spot and it suggests the same dogs as last time.
        Walks sync to Apple Health and Android Health Connect, themes follow the
        system, and the whole app is localized in English and Traditional Chinese.
      </p>
      <p>
        Under the hood: Zustand stores persisted through AsyncStorage, a small
        state machine for the tracking lifecycle
        (idle → tracking ⇄ paused → finished), and GPX files as the storage
        format — the walk history is just parsed GPX.
      </p>
    </section>

    <footer>
      <p>© <span id="year"></span> HoSo Chueh · built plainly, hosted on GitHub Pages</p>
    </footer>
  </main>

  <script src="/main.js"></script>
</body>
</html>
```

- [ ] **Step 2: Verify it serves**

Run: `curl -s http://localhost:8000/projects/pawsitive-track.html | grep -o '<title>[^<]*</title>'`
Expected: `<title>Pawsitive Track — HoSo Chueh</title>`

- [ ] **Step 3: Commit**

```bash
git add projects/pawsitive-track.html
git commit -m "feat: Pawsitive Track project intro page"
```

---

### Task 7: TrashTalker intro page

**Files:**
- Create: `projects/trashtalker.html`

**Interfaces:**
- Consumes: `.crumbs`, `.proj-hero` from Task 1; linked from `/projects/` (Task 2).

- [ ] **Step 1: Create `projects/trashtalker.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>TrashTalker — HoSo Chueh</title>
  <meta name="description" content="TrashTalker — fully offline Traditional Chinese voice typing for Android: hold to talk, release to get text in any app." />

  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=JetBrains+Mono:wght@400;500&family=Inter:wght@400;500&display=swap" rel="stylesheet" />

  <link rel="stylesheet" href="/styles.css" />
  <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><polyline points='10,80 35,60 55,68 75,30 90,18' fill='none' stroke='%2300a878' stroke-width='10' stroke-linecap='round' stroke-linejoin='round'/></svg>" />
</head>
<body>
  <main>
    <nav class="crumbs">
      <a href="/projects/">← All projects</a>
      <a href="/">Home</a>
    </nav>

    <header class="hero proj-hero">
      <p class="eyebrow">Side project · on-device app</p>
      <h1>TrashTalker</h1>
      <p class="tagline">Fully offline Traditional Chinese voice typing for
        Android — hold to talk, release to get text, in any app. Audio never
        leaves the device.</p>
      <ul class="skills">
        <li>Kotlin</li><li>Jetpack Compose</li><li>sherpa-onnx</li>
        <li>OpenCC</li><li>Accessibility service</li><li>IME</li>
      </ul>
    </header>

    <section>
      <h2>What it is</h2>
      <p>
        TrashTalker is local voice input for Android: hold to record, release, and
        the recognized Traditional Chinese text is typed straight into whatever
        input field you're in. Recognition runs entirely on-device through
        sherpa-onnx, so speech never leaves the phone. It works from three
        surfaces — an in-app playground for testing, a floating mic bubble that
        rides on top of any app via an accessibility service, and a full voice
        keyboard registered as a system input method.
      </p>
    </section>

    <section>
      <h2>How it works</h2>
      <p>
        Three switchable ASR models are supported — SenseVoice (fast, multilingual,
        punctuated), Paraformer (strongest Mandarin accuracy), and Whisper Small
        (99 languages) — downloaded at runtime with resumable downloads and SHA-256
        verification, which slimmed the APK from 397 MB to 157 MB. Every model's
        output passes through OpenCC so Simplified Chinese always comes out
        Traditional. A dictation state machine coordinates recording, recognition,
        and text injection across the bubble and keyboard, backed by a foreground
        recording service.
      </p>
      <p>
        Inspired by <a href="https://github.com/Jeffrey0117/SpeakSlow">SpeakSlow</a>,
        a desktop local Chinese voice-input tool — TrashTalker is an independent
        Android take on the same idea.
      </p>
    </section>

    <footer>
      <p>© <span id="year"></span> HoSo Chueh · built plainly, hosted on GitHub Pages</p>
    </footer>
  </main>

  <script src="/main.js"></script>
</body>
</html>
```

- [ ] **Step 2: Verify it serves**

Run: `curl -s http://localhost:8000/projects/trashtalker.html | grep -o '<title>[^<]*</title>'`
Expected: `<title>TrashTalker — HoSo Chueh</title>`

- [ ] **Step 3: Commit**

```bash
git add projects/trashtalker.html
git commit -m "feat: TrashTalker project intro page"
```

---

### Task 8: Homepage links to the portfolio

**Files:**
- Modify: `index.html` (Side Projects section, lines ~81–107)

**Interfaces:**
- Consumes: `/projects/` (Task 2), `/projects/pawsitive-track.html` (Task 6), `/projects/ggai.html` (Task 3), `.more` class (Task 1).

- [ ] **Step 1: Link Pawsitive Track and GGAI names to their detail pages**

In `index.html`, change:

```html
          <span class="proj-name">Pawsitive Track</span>
```

to:

```html
          <a class="proj-name" href="/projects/pawsitive-track.html">Pawsitive Track</a>
```

and change:

```html
          <span class="proj-name">GGAI</span>
```

to:

```html
          <a class="proj-name" href="/projects/ggai.html">GGAI</a>
```

- [ ] **Step 2: Add the "See all projects" link**

In `index.html`, immediately after the closing `</ul>` of the `projects` list (before that section's `</section>`), add:

```html
      <p class="more"><a href="/projects/">See all projects →</a></p>
```

- [ ] **Step 3: Verify all internal links resolve**

Run (from repo root, server still on port 8000):

```bash
for p in /projects/ /projects/ggai.html /projects/hudder.html /projects/offtrack.html /projects/pawsitive-track.html /projects/trashtalker.html /; do
  echo "$p $(curl -s -o /dev/null -w '%{http_code}' http://localhost:8000$p)"
done
```

Expected: every line ends with `200`.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: link homepage to projects portfolio"
```
