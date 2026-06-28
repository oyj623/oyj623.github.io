# Ong Yi Jun — Personal Portfolio

A single-page, self-contained portfolio site. **Everything lives in `index.html`** — all CSS is in one `<style>` block in the `<head>`, all JS is in one `<script>` block before `</body>`. No build step, no framework, no dependencies except two Google Fonts. It runs as a static file and is hosted on GitHub Pages.

---

## Files in the repo

| File | Purpose |
|---|---|
| `index.html` | The entire website (HTML + CSS + JS inline). |
| `Ong_Yi_Jun_Resume_Analytics_Data_Lead.pdf` | The résumé the download buttons link to (relative path `./`). |
| `README.md` | This file. |

> The download buttons point at `./Ong_Yi_Jun_Resume_Analytics_Data_Lead.pdf`. Keep that exact filename next to `index.html`, or update both `href`s in the hero and contact sections.

---

## Design language

**Vibe:** dark-first "developer / IDE / terminal" aesthetic. Deep charcoal-navy canvas, electric-cyan accent, crisp monospace labels, generous spacing, restrained motion. Senior and technical — *not* gamer-RGB. Light mode is a clean inverse of the same system.

### Typography
- **`JetBrains Mono`** — all labels, nav, code-style accents, metrics, section markers (`// 01. about`), buttons, chips. This font carries the "developer" tone — use it for anything that should read as code/label.
- **`IBM Plex Sans`** — body copy, headlines, paragraphs. Everything that's prose.
- Loaded via one Google Fonts `<link>` in `<head>`. Headlines are weight 600–700 with tight negative letter-spacing; labels are mono with positive letter-spacing.

### Color tokens (CSS custom properties)
All colors are CSS variables defined on `:root` (dark) and overridden under `[data-theme="light"]`. **Never hard-code a color — always use a token** so both themes stay in sync.

**Dark (default):**
```
--bg:#0a0f1c    --bg-2:#0d1426    --bg-3:#101a31    --surface:#0f1830
--text:#e7eef8  --text-dim:#9fb2cd  --text-mute:#6a7d9c
--accent:#22d3ee (electric cyan)   --accent-2:#5b9dff (electric blue)
--accent-soft:rgba(34,211,238,0.12)   --glow:rgba(34,211,238,0.35)
--border:rgba(120,165,225,0.15)       --border-strong:rgba(120,165,225,0.28)
--navy:#1F3864
```

**Light (`[data-theme="light"]`):**
```
--bg:#eef2f9   --bg-2:#ffffff   --bg-3:#e6ecf6
--text:#142440   --text-dim:#42587a
--accent:#0a93b3   --accent-2:#2563eb
```

**Token usage rules:**
- Page background → `--bg`. Cards/panels → `--bg-2`. Inset chips/sub-cards → `--bg-3`.
- Primary text → `--text`. Secondary → `--text-dim`. Captions/meta → `--text-mute`.
- Anything interactive, highlighted, or "live" → `--accent`. Hover wash → `--accent-soft`. Glow/shadow halo → `--glow`.
- Hairlines and card outlines → `--border`; stronger outlines / button borders → `--border-strong`.

### Spacing & shape
- Sections: `88px` top/bottom padding (`64px` on mobile), separated by a hairline `border-top` + `34px` top margin. Content is capped at `--maxw: 1140px` and centered via the `.wrap` class.
- Radii: cards `12–16px`, chips/pills `6–7px`, the nav capsule and toggle buttons use `100px` (fully rounded).
- Shadows are soft and downward: `--shadow` (`0 18px 50px -20px rgba(0,0,0,.7)` dark).

### Motion
- Subtle and purposeful only. Scroll-reveal (fade + 26px rise), blinking hero cursor, flowing dots along the architecture pipes, particle background, hover lifts.
- **All motion must respect `prefers-reduced-motion`.** There's a global `@media (prefers-reduced-motion: reduce)` block that kills animations and the particle canvas never starts. Any new animation must degrade the same way.

---

## Page structure (in order)

1. **Nav** — `<header class="nav">`. A **fixed, floating rounded capsule** centered at the top with backdrop blur. Brand + links + theme toggle. Active link gets a filled cyan pill via the `.on` class, driven by scroll-spy. Collapses to a hamburger + slide-in `.mobile-menu` below 880px.
2. **Hero** (`#top` / `.hero`) — mono tag, name `<h1>`, dual-track headline with a blinking cursor, sub-paragraph, meta row, CTA buttons, résumé download button. Sits over the particle canvas.
3. **About** (`#about`) — 3 short paragraphs + a stat card.
4. **Skills** (`#skills`) — 8 grouped cards of mono chips (Analytics, Big Data, Languages, Frameworks, Databases, AI/LLM, Consulting, Leadership).
5. **Architecture** (`#architecture`) — "A map of my knowledge": a responsive data-lakehouse pipeline (Sources → Ingestion → Staging → Lakehouse medallion → Serving) with a cross-cutting Governance bar. **Each layer is labelled with the real project built there.** Flowing-dot connectors animate between stages; stacks vertically on mobile.
6. **Projects** (`#projects`) — 6 cards rendered from the `projects` JS array; clicking opens a detail modal (description, metrics, tech stack).
7. **Experience** (`#experience`) — vertical timeline (Huawei Data Analyst → Intern → Research Assistant).
8. **Education & Certs** (`#education`) — degree panel + certifications list + the ACM publication card.
9. **Contact** (`#contact`) — mailto, LinkedIn, GitHub, résumé button. *(GitHub link currently uses a placeholder — see "Known placeholders".)*
10. **Footer** — copyright + "built with HTML / CSS / JS".

Each section uses the pattern: `.seclabel` (mono `// NN. name`) → `h2.sec` headline → `.lead` intro → content. Reuse this rhythm for any new section.

---

## JavaScript features (all in the one `<script>`)

| Feature | How it works |
|---|---|
| **Theme toggle** | Flips `data-theme` on `<html>` between `dark`/`light`. **In-memory only** — intentionally NOT persisted to localStorage. Also updates the `theme-color` meta and recolors the particle canvas. |
| **Particle background** | `<canvas id="bg-canvas">`, fixed behind content. Drifting node network that links nearby nodes and reacts to the cursor. Node count scales with viewport area; pauses when the tab is hidden; never starts under reduced-motion. Colors come from `--node` / `--line` tokens. |
| **Scroll reveal** | `IntersectionObserver` adds `.in` to any `.reveal` element when it enters view. Add `reveal` (plus optional `d1`/`d2`/`d3` stagger) to any new element to opt in. |
| **Project modals** | The `projects` array holds all card + modal data. Cards are generated in JS; clicking opens the shared modal. Esc closes, focus is trapped, and focus returns to the card on close. |
| **Scroll-spy** | A second `IntersectionObserver` watches each section and toggles `.on` on the matching `.navlinks a[data-spy="…"]`, highlighting the current section in the capsule. |
| **Mobile menu** | Hamburger toggles `.mobile-menu` + scrim; links close it on click. |

---

## How to make common changes (recipes for prompting an LLM)

- **Edit text / copy:** find the section by its `id` and edit the HTML directly. Content is hard-coded in the markup (except project cards).
- **Add or edit a project:** edit the `projects` array in the `<script>`. Each entry: `{ key, badge, featured?, title, blurb, stk, desc:[…], metrics:[{v,l}], stack:[…] }`. Cards and modals regenerate automatically.
- **Add a skill chip:** add a `<span class="chip">…</span>` inside the relevant `.skill-card .chips`.
- **Add a nav link / section:** add a `<section id="x" class="wrap">…</section>` following the seclabel→h2→lead pattern, then add `<a href="#x" data-spy="x">label</a>` to BOTH `.navlinks` and `.mobile-menu`. Scroll-spy and smooth-scroll work automatically.
- **Change the accent color:** edit `--accent`, `--accent-soft`, and `--glow` on `:root` (and the light overrides). Don't touch individual elements.
- **Change a résumé file:** update the `href` on the `.btn-resume` links in the hero and contact sections (and rename the PDF in the repo to match).

### Conventions to keep
- Use existing CSS tokens; don't introduce new hex values unless adding a token.
- Mono font = labels/code/metrics; sans = prose. Don't mix them up.
- Keep new motion behind `prefers-reduced-motion`.
- Keep it a single self-contained `index.html` — no external JS/CSS files, no frameworks, relative paths only (it must keep working on GitHub Pages).
- Maintain semantic HTML, `alt` text, keyboard access, and the SEO/Open Graph meta tags in `<head>`.

### ⚠️ Gotcha: `.wrap` specificity
Sections carry the `.wrap` class, which sets `padding:0 24px` and `margin:0 auto`. Because `.wrap` (a class) outranks the bare `section` type selector, **section-level vertical padding/margin must be set on the higher-specificity selector `main > section.wrap`**, not on `section{}` — otherwise `.wrap` silently zeroes it. This is already done; remember it if you touch section spacing.

---

## Known placeholders to fill in

- **GitHub URL:** the contact GitHub button and the `<head>` canonical / Open Graph URLs use `https://github.com/your-github-username` and `https://ongyijun.github.io/`. Replace with the real username/URL before publishing.

---

## Deploying to GitHub Pages

1. Create a repo named **`<username>.github.io`**.
2. Commit `index.html`, the résumé PDF, and this `README.md` to the **root** of the `main` branch.
3. Repo **Settings → Pages → Source: Deploy from a branch → Branch: `main` / `(root)` → Save**.
4. Live at `https://<username>.github.io/` within a minute or two.

---

**Built with HTML / CSS / JS.** No build tools required — open `index.html` in any browser to preview locally.
