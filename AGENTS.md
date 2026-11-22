# Agent Instructions

## What this repository is

The GitHub profile README for the account `s-maharajan`. GitHub renders `README.md` from the
default branch of this repository directly on the profile page, so it is a landing page, not
a project.

It holds exactly two things: a Markdown page, and one animated SVG that the page shows at the
top. There is no application, no build, no dependency manifest and no deployment.

The thing that surprises newcomers: **the header SVG is hand-authored.** It has no generator
and no build step. Its animations are CSS inside a `<style>` element in the SVG, and the
geometry of the wires is written twice — once as `<path>` elements and once as `offset-path`
values on the moving packets — because CSS inside a standalone SVG cannot reference a path by
id. Those two copies have to be kept identical by hand.

## Read first

1. [README.md](README.md) — the page itself.
2. [docs/PROFILE.md](docs/PROFILE.md) — how the page and the header are built, and how to
   regenerate the header.
3. `assets/header.svg` — the header. Read it before editing it; the comments explain the
   constraints.
4. [docs/DEVELOPMENT.md](docs/DEVELOPMENT.md) — the editing workflow, including how to
   preview the page and how to publish a change.

## Setup

There is nothing to install. The repository has no manifest, no lockfile and no build.

What is useful to have:

| Tool | For |
|---|---|
| A browser | Previewing the rendered page, including the SVG. |
| `gitleaks` | The secret scan that CI runs. |

## Commands that must pass

```sh
# The secret scan, as CI runs it
gitleaks git . --log-opts="--all" --config .gitleaks.toml --redact --no-banner
```

That is the whole automated check. There is no lint, no type check and no test suite,
because there is no code.

Everything else is a manual check, and it is the important one:

- Open `README.md` on GitHub and read it as a stranger would.
- Confirm every image has meaningful alt text and that its path resolves.
- Confirm the header still animates and that the page is legible with motion disabled.

## Architecture rules

- **The page is self-contained.** No external images, fonts, stylesheets, scripts, trackers or
  analytics. Every asset is in this repository and referenced by a relative path. A profile
  page that depends on a third-party host is a page that breaks for some readers.
- **Every image needs alt text that describes the image, not the filename.** The current
  header's alt text describes what the diagram shows, which is the right register.
- **The header is referenced with a cache-buster.** The README uses
  `./assets/header.svg?v=3`. When the SVG changes, bump the number. Without that, GitHub's
  image cache serves the old file and the change appears to have done nothing.
- **The SVG stays accessible.** It carries `role="img"`, an `aria-label` and a `<title>`. Keep
  them, and keep the `aria-label` in step with the words drawn inside the image.
- **Dark is the default in the SVG, light is the override.** The comment in the stylesheet
  says why: if the colour query never fires, the card is still legible instead of
  white-on-white. Do not invert that logic.
- **Motion is an enhancement, never the thing that reveals the content.** The animations use
  `animation-fill-mode: backwards` so the elements are on screen before the animation starts,
  and the `prefers-reduced-motion` block switches everything off in one place. Both are
  load-bearing; do not delete either.
- **The wire geometry is written twice and must stay identical.** The four `<path>` elements
  and the four `offset-path` values on the packet circles describe the same curves. A change
  to one must be made to the other.
- **The page carries no personal identifiers beyond the account handle.** No address, no
  phone number, no employer, no client names, no internal system names.
- **The page stays short.** It is a landing page. If a section grows past a screen, it wants
  to be its own page, not this one.

## Traps

- **The cache-buster is easy to forget.** Changing `assets/header.svg` and not bumping `?v=`
  in the README leaves the old header on the profile. This is the single most common way for
  a change here to look like it did nothing.
- **The SVG comment refers to `tools/check_header.py`, which is not in this repository.** The
  duplicate-geometry check it describes is therefore not automated here. The two copies of the
  wire geometry have to be compared by eye.
- **GitHub sanitises the HTML in a README.** Presentational attributes such as `style` are
  removed, and scripts and event handlers are stripped. The safe set is plain elements with
  `align`, `width`, `height`, `src` and `alt` — which is what the page uses. A layout that
  depends on inline CSS will not survive.
- **A pipe inside a code span still breaks a Markdown table.** In a table cell, write `\|`
  rather than `|`.
- **The SVG is served as an image, not inlined.** It cannot reference the page's styles, and
  the page cannot style it. Keep every visual decision inside `assets/header.svg`.
- **Images are proxied.** A relative path is served from the repository; an absolute URL to
  another host is proxied and adds a third-party dependency to the page. The house rule is
  to keep every asset local.
- **Nothing in CI renders the page.** A broken image path, a malformed table or a broken HTML
  tag will not be caught by any check. Open the page on GitHub before you push.
- **The secret scan walks the full history on every push.** Keep real credentials out of
  commits, and keep the history free of them.
- **The profile page shows `main` only.** A change on a branch is invisible until it is
  merged.

## Boundaries

- **Keep the repository to what a profile page needs:** `README.md`, `AGENTS.md`, `docs/` and
  `assets/header.svg`. Do not add an application, a build, a dependency manifest or a
  deployment.
- **Never add a tracker, an analytics beacon, a font CDN or an external image.** The page
  must render identically for everyone.
- **Never add personal contact details, an employer, a client or an internal system name.**
  The account handle is the only identifier on this page.
- **Never edit a CI workflow** to make a check pass, and never add a scanner exclusion.
- **Do not reformat the whole README for a wording change.** The diff is the reviewable
  artefact.
- **Do not push to `main` unless you have been told to.**
- **Do not add a licence.** There is no `LICENSE` file in this repository.

## Commit and PR conventions

- Commit messages are short imperative sentences describing the outcome
  (`Enhance profile header motion`, `Add personal profile README and banner`).
- A pull request should say what changed on the page, not only which files were touched. The
  reader of the profile never sees the commit.
- If the header SVG changed, say so explicitly, and mention that the cache-buster was
  bumped — otherwise the change will look like it did not apply.
