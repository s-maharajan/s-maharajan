<p align="center">
  <img src="./assets/header.svg?v=3" alt="Integration, platform and agentic engineering: an animated diagram of messages flowing through a pipeline from source to target" width="880" />
</p>

# s-maharajan

<p align="center">
  <strong>Engineering the connective tissue of the enterprise.</strong><br />
  Integration · platforms · agentic engineering
</p>

<p align="center">
  <sub>Reliable systems are built from clear boundaries, observable flows, and thoughtful change.</sub>
</p>

---

## What I do

I help move large middleware estates without stopping the business. That means treating
migration as an engineering discipline: understand the landscape, make the dependencies
visible, and change one reliable seam at a time.

Most of the work sits in the space between the platform and the people who depend on it —
where the documentation is thinner than the risk, where a migration touches systems nobody
fully owns, and where the only safe pace is one you can explain.

## Current focus

Building practical CLI agents for the unglamorous, high-leverage work: discovery, mapping, and
repeatable delivery. Let the agents handle the evidence and the grunt work; leave judgement
with the people who own the system.

## How the work gets done

```text
discover → map → modernise → operate
```

| Stage | What it produces |
|---|---|
| Discover | An inventory of what is actually running, not what the diagram says. |
| Map | Dependencies and seams made explicit, so a change has a known blast radius. |
| Modernise | One reliable seam at a time, with the business running throughout. |
| Operate | The thing stays observable, owned and maintainable after the migration ends. |

## Focus areas

| Area | What it covers |
|---|---|
| Integration | Messaging, file transfer, APIs and events; the glue between systems that were never designed to meet. |
| Platforms | Shared runtime, deployment and the standards that keep dozens of teams moving in the same direction. |
| Migrations | Legacy estates — file transfer, ESB, brokered integrations — moved to cloud-native equivalents without a big-bang cutover. |
| Agentic engineering | CLI agents for discovery and mapping: evidence gathered by machine, judgement kept with the owning team. |
| DevSecOps | Supply-chain security, repository health, and the controls that make a platform safe to change. |

## Selected work

| Project | What it is |
|---|---|
| [`cs` — Copilot Sessions](https://github.com/s-maharajan/copilot-sessions) | A terminal app for browsing, searching, auditing and resuming GitHub Copilot CLI sessions. Read-only, fully local, no network calls. |
| [Personal site](https://s-maharajan.vercel.app) | Essays on integration, cloud and AI, and a section-by-section learning log. |

## Working together

The shortest way to start is a short description of the estate, the constraint you are
under, and what "done" would look like. Most useful first questions are: how many systems
touch this one, who owns each of them, and what happens if it stops.

## About this page

This repository is the GitHub profile README. It contains a Markdown page and one SVG — the
animated header at the top. There is no application here, so there is nothing to build,
deploy or test.

| File | What it covers |
|---|---|
| [README.md](README.md) | This page. |
| [AGENTS.md](AGENTS.md) | How to change the profile page and the header without breaking them. |
| [SECURITY.md](SECURITY.md) | How to report a vulnerability privately. |
| [docs/PROFILE.md](docs/PROFILE.md) | How the page is laid out, what the header SVG does, and how to regenerate it. |
| [docs/DEVELOPMENT.md](docs/DEVELOPMENT.md) | The editing workflow: previewing the page, keeping the assets consistent, and publishing. |

### What runs here

| Workflow | Trigger | What it does |
|---|---|---|
| [Secret scan](.github/workflows/secret-scan.yml) | push, pull request, manual, Mondays 06:00 UTC | Gitleaks 8.30.1 over the full history, from a checksum-verified download. |
| [Snyk](.github/workflows/snyk.yml) | push, pull request, manual, Mondays 06:23 UTC | Snyk Open Source and Snyk Code, failing on high and critical. Skips with a notice when `SNYK_TOKEN` is not set. |

There is no build workflow and no deployment workflow: GitHub renders the profile README
directly from `main`.

To check the page locally for a leaked credential:

```sh
gitleaks git . --log-opts="--all" --config .gitleaks.toml --redact --no-banner
```

## Suggesting a change

The page is small on purpose. Corrections, better wording and clearer explanations of the
work are welcome — open a pull request against `main` and say in one line what you are trying
to improve. Please keep the page self-contained: no external images, fonts, trackers or
scripts, so it renders the same for everyone, including on a locked-down network.
