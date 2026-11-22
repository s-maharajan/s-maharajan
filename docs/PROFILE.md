# The profile page — layout and assets

How the GitHub profile page is put together, what the header SVG contains, and how to change
either of them without breaking the other.

GitHub renders the README of this repository, from the default branch, on the account's
profile page. That is the entire mechanism: there is no site, no build and no deployment. A
push to `main` changes the profile.

- [What is in the repository](#what-is-in-the-repository)
- [The page](#the-page)
- [The header SVG](#the-header-svg)
- [Regenerating the header](#regenerating-the-header)
- [Checking the duplicated geometry](#checking-the-duplicated-geometry)
- [Accessibility](#accessibility)
- [Constraints and trade-offs](#constraints-and-trade-offs)
- [Known limitations](#known-limitations)

## What is in the repository

| Path | Purpose |
|---|---|
| `README.md` | The page. Markdown with a small amount of HTML for the centred header image. |
| `assets/header.svg` | The animated header. Hand-authored; no generator. |
| `AGENTS.md` | The rules for changing anything here. |
| `docs/PROFILE.md` | This file. |
| `docs/DEVELOPMENT.md` | The editing workflow. |
| `SECURITY.md` | How to report a vulnerability. |

There is no `LICENSE` file, and no licence is asserted.

## The page

The page is a single Markdown document. GitHub renders it as Markdown, so the HTML that
appears in it is sanitised: scripts and event handlers are removed, and presentational
attributes such as `style` do not survive. The page therefore uses only what GitHub keeps —
`<p align="center">`, `<strong>`, `<br />`, `<sub>` and `<img>`.

In order:

| Section | What it does |
|---|---|
| Header image | The animated SVG, centred, at 880 px wide. |
| `# s-maharajan` | The page title, and the only identifier on the page. |
| Tagline block | The one-line description of the work, centred. |
| `---` | A rule that separates the masthead from the body. |
| What I do | The paragraph on what the work is. |
| Current focus | What the attention is on at the moment. |
| How the work gets done | The four-stage loop and what each stage produces. |
| Focus areas | The five areas of practice, as a table. |
| Selected work | Links to the two public projects. |
| Working together | What to send, and what to expect. |
| About this page | What this repository is, the documentation map, and the workflows. |
| Suggesting a change | How to propose an edit. |

The page is deliberately short. It is a landing page: a stranger should be able to read it
in under two minutes and come away with a clear picture of the work.

## The header SVG

`assets/header.svg` is a standalone SVG document: 880 × 320, with its own stylesheet, its
own animation, and no external references of any kind.

### Canvas and card

| Element | Value | Purpose |
|---|---|---|
| `viewBox` | `0 0 880 320` | The coordinate space everything is drawn in. |
| `width` / `height` | 880 / 320 | The intrinsic size. The README overrides the width with `width="880"`. |
| Card | `<rect x="8" y="8" width="864" height="304" rx="18">` | The rounded panel, filled with the `card` gradient and clipped by `cardClip`. |
| Grid | a 34 × 34 `pattern` | A faint background grid. |
| Clip | `clipPath#cardClip` | Keeps the sweep and the ambient glow inside the rounded card. |

### Gradients and filters

| `id` | Kind | Use |
|---|---|---|
| `card` | linear gradient | The panel background, from `#0a1020` to `#101c33` to `#0d1628` in dark mode. |
| `rule` | linear gradient | The horizontal rule under the headline. |
| `sweep` | linear gradient | The soft band that crosses the card on a loop. |
| `glow` | radial gradient | The halo around a moving packet. |
| `accent` | linear gradient | The blue-to-teal accent, `#38bdf8` to `#2dd4bf`. |
| `soft` | filter | A `feGaussianBlur` with `stdDeviation="3.2"`, used for the ambient glow. |

### Typography and colour

Every text element sets its own `font-family`, because a standalone SVG has no access to the
page's styles:

- `ui-monospace, SFMono-Regular, Menlo, Consolas, monospace` for the eyebrow, the micro
  labels and the node tags;
- `ui-sans-serif, system-ui, sans-serif` for the headline and the subhead.

The colour classes are `.eyebrow`, `.headline`, `.subhead`, `.micro`, `.tag-text`, `.name`,
`.node-face`, `.node-dot`, `.edge`, `.frame` and `.grid-line`.

**Dark is the default.** The `prefers-color-scheme: light` block overrides each of them. The
comment in the stylesheet records the reason: if the colour query never fires, the card is
still legible rather than white-on-white.

### Layout

| Element | Position |
|---|---|
| Status light | circle at 52, 53 |
| Eyebrow — `SYSTEMS THAT KEEP MOVING` | baseline at y 58, x 66 |
| Headline — `Integration engineering for work that cannot stop.` | baseline at y 94, x 48 |
| Subhead — `Platforms, migrations and agents built around clear, observable flow.` | baseline at y 121, x 49 |
| Accent rule | 210 × 2 at 48, 141 |
| Micro label — `FROM SIGNAL TO OUTCOME` | baseline at y 168, x 48 |
| Five nodes | centres at x 132, 276, 420, 564, 708; y 228 |
| Node labels | baseline at y 274 |

The nodes are, in order: **SOURCE**, **TRANSFORM**, **TRANSPORT**, **ORCHESTRATE**,
**TARGET**. The first three are outlined in blue or teal; the last, TARGET, is outlined in
amber `#fbbf24`.

### The wires

Four cubic Bézier curves connect the nodes:

```text
M132,228 C186,192 222,192 276,228
M276,228 C330,264 366,264 420,228
M420,228 C474,192 510,192 564,228
M564,228 C618,264 654,264 708,228
```

They are drawn first, so the node rings sit on top of them. The `.edge path` rule adds a
marching-ants dash that traces along them.

### The packets

Eight circles ride the wires: four large ones filled with the `glow` radial gradient, and
four small bright ones. Each animates along its wire with `offset-path`, staggered by 0.85 s
so they arrive in sequence.

This is where the duplication comes from. **CSS inside a standalone SVG cannot reference a
path by `id`**, so each packet's `offset-path` repeats the wire's geometry as a literal
`path('…')` value. The eight `offset-path` values are the four curves, each written twice —
once for the large packet and once for the small one.

### The animations

| Class | Animation | Duration | Purpose |
|---|---|---|---|
| `.rise` | `rise` | 0.85 s | The text and the node groups fade and lift into place. |
| `.draw` | `draw` | 1.1 s, 0.35 s delay | The accent rule draws itself out from the left. |
| `.packet` | `flow` | 3.4 s, looping | A packet travels its wire. |
| `.pulse` | `pulse` | 3.4 s | A ring expands as the inbound packet lands. |
| `.sweep` | `sweep` | 7 s | A soft band crosses the card. |
| `.ambient` | `drift` | 9 s | The blurred background shapes drift. |
| `.spark` | `spark` | 2.8 s | Small points twinkle. |
| `.edge path` | `trace` | 1.9 s | The dash pattern moves along the wires. |
| `.node-dot` | `core` | 3.4 s | The centre of each node pulses. |
| `.status-light` | `status` | 1.7 s | The light beside the eyebrow breathes. |

Two rules make this robust rather than fragile:

1. **`animation-fill-mode: backwards`, not a base `opacity: 0`.** If the animation never runs
   — an odd renderer, animation disabled, a static export — the content is still on screen.
   Motion is the enhancement; it is never the thing that reveals the content.
2. **One `prefers-reduced-motion` block switches everything off.** It sets `animation: none`
   on every class at once, pins the packets at 62% along their path, and leaves the pulse at a
   low opacity. Anything added to the stylesheet must be added to that block too.

## Regenerating the header

There is no generator. "Regenerating" the header means editing the SVG by hand and
publishing it.

1. **Edit `assets/header.svg`.** Keep the structure: the defs, the stylesheet, the card, the
   wires, the packets, the nodes, and the text groups. Keep the comments — they explain the
   constraints.

2. **Check it is well-formed XML.**

   ```sh
   xmllint --noout assets/header.svg
   ```

3. **Preview it.** Open the file in a browser. Check the dark rendering, then the light one,
   then turn the operating system's "reduce motion" setting on and look again.

4. **Check the duplicated geometry** — see below.

5. **Bump the cache-buster in the README.** The page refers to the header as
   `./assets/header.svg?v=3`. Change it to `?v=4`, and then to `?v=5`, and so on, every time
   the SVG changes. Without this, GitHub serves the cached copy and the change appears to
   have done nothing.

6. **Commit the SVG and the README together.** A commit with only one of them leaves the page
   showing the old header.

## Checking the duplicated geometry

The SVG's own comment says that `tools/check_header.py` asserts the wire paths and the
`offset-path` values never drift apart. **That script is not part of this repository**, so
the check is manual. This command does it:

```sh
diff <(grep -o 'd="M[0-9]*,[0-9]* C[^"]*"' assets/header.svg \
          | sed 's/^d="//;s/"$//') \
     <(grep -o "offset-path:path('[^']*')" assets/header.svg \
          | sed "s/^offset-path:path('//;s/')$//" \
          | awk '!seen[$0]++')
```

The left side is the four wire paths. The right side is the four distinct `offset-path`
values, in order. No output means they match.

Run it after any edit that touches the wires, the node positions, or the packets. A mismatch
is not fatal — the packets will simply travel a curve that is not there — but it is visible
as a packet jumping or vanishing.

## Accessibility

| Property | Value | Why |
|---|---|---|
| `role` | `img` | The SVG is an image, not a document. |
| `aria-label` | A sentence describing the diagram | The accessible name, read by assistive technology. |
| `<title>` | `Integration, Platform and Agentic Engineering` | The tooltip and the fallback title. |

The `aria-label` and the `<title>` should describe what the diagram actually shows, and
should be kept in step with the words drawn inside it. The README's `alt` attribute on the
`<img>` is the third layer, and it is the one a screen reader will announce when the SVG is
embedded as an image.

The animations are all disabled under `prefers-reduced-motion: reduce`, so nothing moves for
a reader who has asked for that.

## Constraints and trade-offs

- **No external references.** The SVG has no fonts, images or stylesheets to load. It is one
  file that renders identically everywhere, including on a locked-down network.
- **Dark by default, light by override.** Reversed, the card would be unreadable wherever
  the colour query failed to apply.
- **Motion as enhancement.** `fill-mode: backwards` rather than a base `opacity: 0`, so the
  content never depends on an animation running.
- **One place to switch motion off.** The `prefers-reduced-motion` block is a single list of
  selectors; adding an animation means adding it to that list.
- **The geometry is duplicated.** This is the cost of CSS `offset-path` in a standalone SVG.
  The alternative — SMIL `animateMotion` with `mpath` — is less well supported and harder to
  disable. The duplication is checked by hand instead.
- **The image is served through GitHub's image proxy.** A relative path is served from the
  repository; an absolute URL elsewhere would add a third-party dependency to the page.

## Known limitations

- **The geometry check is not automated.** `tools/check_header.py` is referenced in a comment
  in the SVG but is not present in this repository, and no equivalent exists. The check in
  this document has to be run by hand.
- **The cache-buster is manual.** Nothing reminds you to bump `?v=`. A forgotten bump is the
  most common way for a header change to look like it did nothing.
- **Nothing renders the page in CI.** The workflows are the secret scan and Snyk. A broken
  image path or a malformed table is not caught by any automated check.
- **The SVG cannot be themed from outside.** Because it is served as an image, the page has no
  way to restyle it, and it has no way to restyle the page.
- **The page is Markdown, not a template.** There is no way to reuse a section of it, and the
  length is governed by taste rather than by a build.
