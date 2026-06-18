# Woodworking Plans: how to add one

This repo is a tiny, zero-build static site that GitHub Pages serves at [christhoma.me/plans](https://www.christhoma.me/plans). Each plan is a self-contained HTML page in its own folder; a data-driven hub page lists them all. No framework, no build step, no dependencies. Just static files.

```
.
├── index.html          # the hub: lists every plan from a small array (edit this to register a plan)
├── README.md           # you are here
└── <slug>/
    └── index.html       # one plan: a self-contained page (its own CSS + inline SVG blueprints)
```

The hub and each plan are independent: adding a plan never requires touching another plan, and the hub only needs one new line.

---

## Add a new plan in two steps

### 1. Create the plan page

Make a new folder named with a URL-friendly **slug** (lowercase, hyphens) and put an `index.html` inside it:

```
<slug>/index.html
```

The fastest start is to **copy an existing plan and edit it**:

```bash
cp -R mud-kitchen <slug>
# then edit <slug>/index.html
```

`mud-kitchen/index.html` is the reference template. Keep these conventions so plans feel consistent:

- **Self-contained.** All CSS lives in a `<style>` block; no external fonts, scripts, or CDNs. The page must work offline and from `file://`.
- **One accent color** drives the look. Set `--accent` / `--accent2` in `:root` and reuse it. Pick a distinct color per plan.
- **Structure:** a `header.hero` (eyebrow + `<h1>` + one-line lead + spec `chips`), then numbered `<section>`s. Typical sections: Tools, Materials & cut list, Blueprints, Build steps, Safety, Finishing.
- **Footer** links back to the hub and home:
  ```html
  <a href="../">All woodworking plans</a> | <a href="/">christhoma.me</a>
  ```
- **Print-friendly.** Keep the `@media print` block so people can save a clean shop PDF.

### 2. Register it in the hub

Open `index.html` and add one object to the `PLANS` array near the bottom:

```js
const PLANS = [
  {
    slug:   "mud-kitchen",                 // must match the folder name
    icon:   "",                            // optional short mark shown on the card; leave blank for none
    title:  "The Toddler Mud Kitchen",
    blurb:  "A cheap, sturdy, smooth-sanded outdoor mud kitchen...",  // 1-2 sentences
    accent: "#a16207",                     // card bar color; match the plan page's --accent
    specs:  ["36 × 16 × 16\"", "~$40-$80", "Beginner", "A weekend"]   // 3-4 short chips
  },
  // add your new plan object here
];
```

| Field | Required | Notes |
|---|---|---|
| `slug` | yes | Must equal the folder name. The card links to `<slug>/`. |
| `icon` | no | Optional short mark shown on the card. Leave blank for none. |
| `title` | yes | Display name. |
| `blurb` | yes | One or two plain sentences. No HTML needed. |
| `accent` | yes | Hex color for the card's top bar; match the plan page's `--accent`. |
| `specs` | yes | 3-4 short strings (size, cost, difficulty, time). Rendered as pills. |

That's the whole authoring loop. The hub renders cards from this array on load; an empty array shows a friendly "no plans yet" state.

---

## Blueprints (inline SVG)

Blueprints are **hand-drawn inline `<svg>`**, not images. They stay razor-sharp at any zoom and print perfectly. The convention used in `mud-kitchen`:

- **Scale:** the grid is **1 inch per square** (a `<pattern id="grid10">` filling each drawing). State that in a caption.
- **Shared defs once:** a hidden `<svg>` at the top of `<body>` defines the grid pattern and the dimension arrowheads (`#arrowStart`, `#arrowEnd`); every figure references them by id.
- **Drawing classes** (in the `<style>` block) keep parts consistent:
  - `.woodtop`: worktop / panels. `.wood`: legs / posts (2×2). `.wood2`: rails / stretchers (1×4)
  - `.cutline`: a cutout. `.hidden`: dashed hidden/ghost parts. `.grnd`: ground line
  - `.dimL` / `.ext` / `.dimt`: dimension lines, extension lines, and dimension text
- Wrap each figure in `<figure class="card">` with a `<figcaption>` explaining what it shows.

If a plan needs photos instead, drop them in the plan's own folder (e.g. `<slug>/img/`) and reference them with relative paths.

---

## Test locally

The pages work straight from `file://`, but serving over HTTP exercises the real relative links:

```bash
cd /path/to/plans
python3 -m http.server 8000
# open http://localhost:8000/        (the hub)
# open http://localhost:8000/<slug>/ (your plan)
```

Check that: the hub shows your new card, the card links to the plan, the plan's blueprints render, and the footer links work.

---

## Publish

The site deploys from the `main` branch via GitHub Pages. Pushing is publishing.

```bash
git add <slug> index.html
git commit -m "Add <plan name> woodworking plan"
git push
```

Live within ~1 minute at:

- Hub: **https://www.christhoma.me/plans**
- Plan: **https://www.christhoma.me/plans/<slug>**

> The main site already links to `/plans`, so a new plan needs no change there. Only the hub array in this repo needs updating.
