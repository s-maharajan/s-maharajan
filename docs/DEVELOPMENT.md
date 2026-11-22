# Profile page — development guide

The working guide for this repository: how to change the page, how to change the header, how
to check the result, and how to publish it.

- [Environment setup](#environment-setup)
- [Editing the page](#editing-the-page)
- [Editing the header](#editing-the-header)
- [Verification](#verification)
- [Publishing](#publishing)
- [Troubleshooting](#troubleshooting)
- [Common tasks](#common-tasks)

## Environment setup

There is nothing to install. The repository has no manifest, no lockfile and no build.

```sh
git clone https://github.com/s-maharajan/s-maharajan.git
cd s-maharajan
```

What is useful to have:

| Tool | For |
|---|---|
| A browser | Previewing the rendered page and the SVG. |
| `xmllint` (libxml2) | Checking that the SVG is well-formed XML. |
| `gitleaks` | The secret scan that CI runs. |

To see the page the way GitHub renders it, open the repository on GitHub. There is no local
preview that is exactly the same — GitHub sanitises the HTML, resolves the image through
its proxy and applies its own CSS — so the remote page is the reference.

## Editing the page

`README.md` is Markdown with a little HTML. The HTML is limited to what GitHub keeps.

**Safe to use:**

```html
<p align="center">…</p>
<h1 align="center">…</h1>
<img src="./assets/header.svg?v=3" alt="…" width="880" />
<strong>…</strong>
<sub>…</sub>
<br />
```

**Not safe:** `style` attributes, `<script>`, `on*` event handlers, external stylesheets,
external images, fonts and trackers. GitHub removes them, so a layout that depends on them
will not render.

Rules for the content:

- Keep it short. It is a landing page.
- Keep it free of personal identifiers: no address, no phone number, no employer, no client
  names, no internal system names. The account handle is the only identifier.
- Keep the alt text meaningful. Describe what the image shows, not what it is called.
- Keep every asset local. A relative path is served from this repository; a URL to another
  host is proxied and adds a third-party dependency.
- When you add a row to a Markdown table, remember that a pipe inside a code span still
  breaks the table: write `\|`.

## Editing the header

`assets/header.svg` is hand-authored. Read it before editing it — the comments in the file
explain the constraints, and [docs/PROFILE.md](PROFILE.md) explains the anatomy.

### What to keep

| Rule | Why |
|---|---|
| The `prefers-color-scheme: light` block overrides the dark defaults | If the colour query never fires, the card must still be legible. |
| `animation-fill-mode: backwards` on every animation | The content must be on screen even if the animation never runs. |
| One `prefers-reduced-motion` block that switches everything off | It is the only place motion is disabled, so a new animation must be added to it. |
| `role="img"`, the `aria-label` and the `<title>` | They are the accessible name of the image. |
| The four wire paths and the eight `offset-path` values identical | The packets travel the wires; CSS cannot reference a path by id. |
| The comment blocks | They are the only documentation the file has. |

### The workflow

1. Edit `assets/header.svg`.

2. Check it is well-formed XML:

   ```sh
   xmllint --noout assets/header.svg
   ```

3. Check the duplicated geometry:

   ```sh
   diff <(grep -o 'd="M[0-9]*,[0-9]* C[^"]*"' assets/header.svg \
             | sed 's/^d="//;s/"$//') \
        <(grep -o "offset-path:path('[^']*')" assets/header.svg \
             | sed "s/^offset-path:path('//;s/')\$//" \
             | awk '!seen[$0]++')
   ```

   No output means the wires and the packet paths still match. A mismatch is visible as a
   packet jumping or disappearing, and it should be fixed before publishing.

4. Preview it in a browser. Look at the dark rendering, then the light one, then with the
   operating system's "reduce motion" setting on. The card must be legible in all three.

5. **Bump the cache-buster in `README.md`.** The page refers to the header as
   `./assets/header.svg?v=3`. Increment the number with every change to the SVG. Without it,
   GitHub serves the cached copy and the change appears to have done nothing.

6. Commit the SVG and the README together.

### Changing the text in the image

The header carries its own words: the eyebrow, the headline, the subhead, the micro label and
the five node names. If you change what the headline says, check that it still fits inside
864 pixels of card width at 27 px, and update the `aria-label` and the `<title>` so they
still describe the diagram.

## Verification

There is no test suite and nothing to build. The checks are:

| Check | How |
|---|---|
| The page renders | Open the repository on GitHub after pushing. Read it as a stranger. |
| Every image resolves | Every `src` is a relative path that exists in the repository. |
| Every image has alt text | Every `<img>` has a non-empty `alt`. |
| The tables render | No unescaped `\|` inside a code span in a table cell. |
| The SVG is valid | `xmllint --noout assets/header.svg` |
| The geometry matches | The `diff` command above. |
| The header is legible | In the browser, in dark and light, with motion reduced. |
| No secrets | `gitleaks git . --log-opts="--all" --config .gitleaks.toml --redact --no-banner` |

The last one is the only automated check, and it is the one CI runs on every push.

## Publishing

The profile page is rendered from the default branch, `main`. A change is live as soon as it
is pushed.

```sh
git add README.md assets/header.svg
git commit -m "Clarify how the work gets done"
git push origin main
```

Then open the profile page and check it. Nothing in CI renders the page, so this step is the
only verification of the result.

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| The header did not change | The cache-buster was not bumped. | Change `?v=3` to `?v=4` in the README. |
| The header is not animating | The renderer does not support CSS animations in SVG, or reduced motion is on. | Check the `prefers-reduced-motion` block; the static state must still be complete. |
| A packet jumps or disappears | The wire path and the `offset-path` values have drifted apart. | Run the `diff` check and make them identical. |
| The card is white on white | The `prefers-color-scheme: light` override has been removed, or the dark defaults have been changed. | Restore the light block; dark must be the default. |
| Text disappears when motion is off | An animation is using `opacity: 0` as its base state instead of `fill-mode: backwards`. | Use `backwards`. |
| The page is plain but the source has HTML | GitHub removed the tags. `style` attributes and scripts do not survive. | Use the small set of attributes GitHub keeps. |
| A table has lost a column | A `\|` inside a code span. | Escape it as `\\|`. |
| An image is broken | The path is wrong, or the file is not committed. | Check the path against the repository, and check `git status`. |
| The page still shows the old version | GitHub's cache, or the change is not on `main`. | Push to `main` and hard-reload. |

## Common tasks

| Task | How |
|---|---|
| Change a paragraph | Edit `README.md`, preview on GitHub. |
| Change the header image | Edit `assets/header.svg`, validate, bump `?v=`, commit both. |
| Change the header wording | Edit the `<text>` elements, then the `aria-label` and `<title>`, then bump `?v=`. |
| Add a focus area | Add a row to the Focus areas table. Keep it to what you actually do. |
| Add a project | Add a row to Selected work. Only public repositories, and only ones that are live. |
| Validate the SVG | `xmllint --noout assets/header.svg` |
| Check the geometry | The `diff` command in [Editing the header](#editing-the-header). |
| Scan for secrets | `gitleaks git . --log-opts="--all" --config .gitleaks.toml --redact --no-banner` |
| See the history of the page | `git log --oneline -- README.md` |
