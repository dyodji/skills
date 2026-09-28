---
name: ee-provision
description: Provision or update a Clearstory Ephemeral Environment (EE) for one or more open PRs across host-ui / legacy-api / other Clearstory services, so a human can validate the change before merging. Trigger when the user asks to "spin up an EE", "stand up an ephemeral", "deploy this PR to an EE", "get me an EE URL for #N", or wants to validate a PR (or stack of PRs) end-to-end before merging into main. Also trigger when the user asks why a PR's "🔄 Update Ephemeral Deployments" check passed but no EE exists, because the answer is in this skill.
---

# Provision a Clearstory Ephemeral Environment

## Background: what an EE is and is NOT
- A Clearstory **EE (Ephemeral Environment)** is a Kubernetes namespace running a full Clearstory stack pinned to specific PR images, used for stakeholder validation before merging to main.
- **A single EE can host multiple PRs from different services** (e.g. host-ui PR + extracker PR). Use this when a feature spans repos.
- The GitHub Action `🔄 / Update Ephemeral Deployments` only **refreshes** deployments in already-provisioned EE namespaces using the new image tag. It does **NOT** create new EEs. A green check means "I scanned all EE namespaces and nothing was using this PR's image, so there was nothing to update." It does NOT mean "an EE is up and waiting." Always verify by checking for an EE URL or asking the user.
- Image tag convention: `gcr.io/extracker-dev-233506/<service>:pr-<NUMBER>` (e.g. `host-ui:pr-1200`).

## URLs and patterns
- **Provisioning UI:** https://ephemeral-ui.clearstory.dev: the self-service web app to list, create, and configure EEs
- **EE URL pattern (after provisioning):** `https://app-<ephemeral-name>.ephemeral.clearstory.dev` for the host-ui frontend; companion services follow `https://<service>-<ephemeral-name>.ephemeral.clearstory.dev` (e.g. `cn-api-`, `account-api-`, `legacy-`, `analytics-`, `integrations-`). Full env-var template lives in `k8s/ephemeral/.env` in host-ui.
- **EE namespace label** in the cluster: `ephemeral=true`. Existing names are creative (e.g. `cn-detail2`, `swift-eagle`, `mighty-tiger`); some are themed (e.g. `cn-*` for CN work). Repurposing an existing namespace is fine and faster than creating a fresh one.

## GitHub Stacks (cross-repo multi-PR)
When a feature spans multiple PRs in **different repos** (e.g. extracker PR + host-ui PR), GitHub's native **Stack** feature lets you link them so one combined image captures all the changes. Use this before provisioning the EE so you only need to point the EE at one PR per service, not manually overlay changes.

To create / check a stack: go to either PR on github.com, click **"Stacked pull requests"** in the right sidebar (or look for a "Stack" badge), and add the companion PR to the stack. The stack view shows which PR is the umbrella. Point the EE at that one's image for each service.

## How to drive it

### A. User drives the UI themselves
Just hand them the URL `https://ephemeral-ui.clearstory.dev` and the PR number(s). They'll configure and deploy. This is the default, and fastest if they're already logged in.

### B. Claude drives via Ephemeral MCP (preferred when available)
The local Ephemeral app exposes an MCP server at `http://127.0.0.1:9876/mcp`. When it's registered and the Orca app is open, tools like `env_create_from_pr` are available in the deferred tools list and load via `ToolSearch`.

If those tools do NOT appear in the deferred list, check `~/.claude.json`: the `ephemeral` entry **must** have `"type": "http"` alongside `"url"` and `"headers"`, exactly like the `whimsical` entry. Without it the MCP is skipped at session start. Fix it and ask the user to restart Claude Code to pick up the new tools.

### C. Claude drives via Chrome devtools (fallback when Ephemeral MCP unavailable)
Use the `mcp__plugin_chrome-devtools-mcp_chrome-devtools__*` tools (NOT the old `mcp__Claude_in_Chrome__*` prefix, because that prefix doesn't exist):
1. `mcp__plugin_chrome-devtools-mcp_chrome-devtools__navigate_page` → https://ephemeral-ui.clearstory.dev
2. `mcp__plugin_chrome-devtools-mcp_chrome-devtools__take_screenshot` to see what's rendered. Auth check first: if redirected to a login page, surface that to the user; do NOT try to log in for them.
3. The UI lists existing EEs and a "create new" / "configure" entry point. Navigate via `click` or form tools.
4. Configure the EE: pick (or create) the EE name, pin the host-ui image to `pr-<NUMBER>`, leave other services on `main` (or also pin if the user gave you cross-repo PRs).
5. Submit and wait for the deployment to come up. Read the resulting URL back and report it to the user.

Always confirm with the user before clicking destructive actions (e.g. tearing down an existing EE someone else might be using). Repurposing a namespace IS destructive to whoever was using it, so surface the existing image tag and ask first.

## When NOT to use this skill
- The user is just looking at PR CI status (no validation intent). That's `gh pr checks`, not an EE.
- The user says "merge it": do not stand up an EE; merge per their instruction.
- The user is looking at an *existing* EE URL: just hand it to them.

## Common gotchas
- "EE check is green but no EE exists": see the Background note. Re-read the workflow log; "No matching ephemeral deployments found to update" is the giveaway.
- Cross-repo PR sets: make sure each repo's PR has actually been built (image pushed) before pointing the EE at it. CI's `📦 Build` job pushing the image is the prerequisite.
- Long-running umbrella PRs: when a stack of PRs has been merging into an integration branch, point the EE at the integration branch's PR (the umbrella) so it picks up everything in one shot. Don't try to overlay individual PR images.
- **Ephemeral MCP missing from deferred tools**: missing `"type": "http"` in `~/.claude.json`; see section B above.
- **Wrong Chrome tool prefix**: the correct prefix is `mcp__plugin_chrome-devtools-mcp_chrome-devtools__*`, not `mcp__Claude_in_Chrome__*`.
