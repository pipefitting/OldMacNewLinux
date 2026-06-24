# Setting Up Automatic Maintenance

> **Document:** Automation setup (companion to the Intel Mac Linux guide)
> **Version:** 1.0 · **Updated:** 2026-06-24

This wires up a system that checks the guide's upstream sources on a schedule and,
when something changes, opens a pull request you review and merge. You stay out of
the loop until there's a one-click decision to make.

## What you get

- A weekly job (cloud-hosted, runs whether or not your Mac is on) that re-validates
  the guide against the t2linux wiki and the Fedora ISO releases.
- **No silent edits.** Changes arrive as a pull request with a plain-English summary
  of what upstream changed. You skim and merge; merging redeploys the live page.
- Automatic regeneration of the Markdown export so it never drifts from the HTML.

## Prerequisites

- The guide already in a GitHub repo with Pages enabled (see the *GitHub Pages*
  tutorial). The canonical file should be named `index.html`.
- An Anthropic API key (`https://console.anthropic.com`).
- Claude Code installed locally for the one-time setup (`npm install -g
  @anthropic-ai/claude-code`, Node 18+).
- Repo admin rights (needed to install a GitHub App and add secrets).

## Step 1 — Let Claude Code wire up the GitHub plumbing

The official action handles auth, the GitHub App, and the secret for you. From a
terminal in your repo:

```bash
claude
```

Then inside Claude Code:

```
/install-github-app
```

Follow the prompts. This installs the Claude GitHub App, stores your
`ANTHROPIC_API_KEY` as a repository secret, and drops an example workflow into
`.github/workflows/`. (Doing this through the installer is far less error-prone than
hand-writing the auth pieces.)

## Step 2 — Add the maintenance files

Copy these three files from the kit into your repo, preserving paths:

- `CLAUDE.md` → repo root (the agent's standing rules)
- `sources.md` → repo root (the upstream watch list)
- `.github/workflows/maintain.yml` → the scheduled job

Commit and push them.

## Step 3 — Pick your cadence

`maintain.yml` is set to run weekly (Mondays 13:00 UTC). Change the `cron` line if
you want a different rhythm — `https://crontab.guru` makes the syntax painless.
Weekly is a good default; the t2linux project and Fedora releases don't move daily.

## Step 4 — Test it once by hand

In the repo on GitHub: **Actions** tab → **Guide maintenance** → **Run workflow**.
Watch it run. The first run will likely either do nothing (if the guide is already
current) or open a small PR. Either is a healthy sign it works.

## How it behaves from then on

- **Most weeks:** nothing, or a tiny housekeeping PR. A quiet week is the system
  working correctly.
- **When upstream changes:** you get a PR like *"Upstream: Fedora T2 ISO 45.0,
  reworded Wi-Fi step,"* with a summary of what changed, which source, and the
  agent's confidence. GitHub emails you. You skim, and either merge (which
  redeploys the live page automatically) or close it.
- The agent edits only `index.html` and the generated `.md`, never the design, and
  never the live site directly.

## Cost and safety notes

- A weekly single-document check is cheap. Billing draws from your Anthropic
  account; for current specifics see the Claude Code docs, since billing details
  have been in flux. `--max-turns 40` in the workflow caps runaway runs.
- The PR gate is the safety mechanism — keep it. Do not add
  `--dangerously-skip-permissions` or auto-merge to this workflow. This guide wipes
  disks; a human should always eyeball changes to it before they go live.
- Keep `permissions` minimal (the workflow already does). The agent prepares a
  branch and a PR; it does not have, and does not need, the ability to publish on
  its own.

## Optional: a local alternative

If you'd rather run it on your own machine instead of the cloud, Claude Code's
headless mode (`claude -p "...run the CLAUDE.md maintenance task..."`) can be driven
by a `launchd` timer on macOS. It's simpler to reason about but only runs when the
machine is awake, and given the T2 sleep quirks that's a real caveat. The cloud
(GitHub Actions) path is the more reliable "set it and forget it."

---

*Setup reflects the `anthropics/claude-code-action@v1` action and Claude Code as of
mid-2026. If an input name or menu has moved, the `/install-github-app` flow and the
action's README are the current references.*
