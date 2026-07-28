# Getting started

## Cloning the umbrella

```sh
git clone --recurse-submodules https://github.com/jpbaking/agent-commons.git agent-commons
cd agent-commons
```

Already cloned without submodules? Populate them:

```sh
git submodule update --init
```

## Keeping toolkits up to date

Each submodule is pinned to a known-good commit. To pull every toolkit's
latest `main` and record the new pins:

```sh
git submodule update --remote --merge
git add -A && git commit -m "Bump toolkit pins"
```

To update a single toolkit, `cd` into it, `git pull`, then commit the pin
change at the umbrella root.

## Installing a toolkit

A clone of Agent Commons is never required on the machine doing the install
— each toolkit installs from its own GitHub repo.

Installs are **agent-guided only** (there are no install scripts). Paste the
toolkit's install prompt (found in its README) into Codex, Claude Code,
Antigravity, or Cline (Cursor picks the skills up automatically through the
shared `~/.agents/skills/` and `~/.claude/skills/` copies). The general
shape:

```
Fetch https://raw.githubusercontent.com/jpbaking/<toolkit>/main/AGENT-INSTALL.md and follow its instructions exactly to install <toolkit>. Merge with — never blindly overwrite — any existing AGENTS.md, CLAUDE.md, rule, or ignore files, and report every file you created or changed.
```

The agent acquires the toolkit sources itself (`git clone`, a repo/release
zip download, or `gh`) into a temporary directory, audits for skill-name
collisions, and copies the skills and rules into each harness's discovery
path. The procedure is idempotent — re-run to update.

### Installing several toolkits in one pass

For more than one toolkit, use the umbrella's own
[AGENT-INSTALL.md](../AGENT-INSTALL.md) instead of running the per-toolkit
prompts back to back — it acquires the pinned family once, audits skill-name
collisions across all toolkits together, installs in a safe order, and gives
you one verification and report:

```
Fetch https://raw.githubusercontent.com/jpbaking/agent-commons/main/AGENT-INSTALL.md and follow its instructions exactly. Ask me which toolkits and which harnesses before installing anything, and report every file you created or changed.
```

It asks you which of the seven toolkits you want and which harnesses you
run; nothing is installed until you say so, and each toolkit's own
`AGENT-INSTALL.md` still governs its individual steps.

**Where things land:** every toolkit installs **user-global** (your
`~/.agents`, `~/.claude`, `~/.gemini`, `~/.cline` discovery paths) — nothing
is added to your repos, and no project `.gitignore` is ever touched. What a
project commits is truth written by the skills in use: the `DOX.md` tree and
root `AGENTS.md` / `CLAUDE.md` anchors from `dox-init` / `dox-upgrade`,
`.goal-ledger/` records, `design/` copies, `compose-helper.*`, generated
agentic tests. Project-level adapter install is available on explicit
request.

## After installing

- Ask your agent to *"use the `<skill-name>` skill"*. Claude Code,
  Antigravity, Cline, and Cursor also expose `/<skill-name>`; Codex uses a `$` skill
  mention. Implicit activation works when your request matches the skill's
  description.
- Toolkits are per-user, per-machine: teammates who want them install them
  for themselves, and their fresh clones work regardless — the committed
  root anchors steer any agent to `DOX.md` with zero tooling installed.
  Only project truth (`DOX.md` trees + anchors, `.goal-ledger/`, `design/`
  artifacts, `compose-helper.*`, generated agentic tests) lives in the repo.
  Teams typically add a PR-checklist item that the DOX tree is current.
- Toolkits compose: use dox + goal-ledger + agentic-tests together; each owns
  only its own skill/rule names.
