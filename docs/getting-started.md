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
toolkit's install prompt (found in its README) into Antigravity CLI, Codex,
Claude Code, Gemini CLI, Cline, or OpenCode. The general shape:

```
Install <toolkit>. Using git with my existing credentials (do not download raw
files or archives), clone https://github.com/jpbaking/<toolkit> at tag <tag>
with --depth 1 into a new temporary directory outside any project. Then read
AGENT-INSTALL.md from that clone and follow it exactly. Merge with—never
blindly overwrite—any existing global instruction files, ask me which
harnesses to install for, and report every file you created or changed.
```

Your agent clones the toolkit with your own git credentials (so the same
prompt works for a fork, mirror, or enterprise GitHub host — substitute the
remote), executes the cloned contract, audits for skill-name collisions, and
copies the skills and rule into each selected harness's user-global discovery
paths. Use the release tag the toolkit's README pins (dropping `at tag <tag>`
installs the default branch as it is at clone time). The procedure is
idempotent — re-run to update — and each contract also documents uninstall.

### Installing several toolkits in one pass

For more than one toolkit, use the umbrella's own
[AGENT-INSTALL.md](../AGENT-INSTALL.md) instead of running the per-toolkit
prompts back to back — it acquires the pinned family once, surveys all
toolkits together, installs in a safe order, and gives you one validation and
report:

```
Install Agent Commons toolkits. Using git with my existing credentials (do not
download raw files or archives), clone
https://github.com/jpbaking/agent-commons at tag v4.0.0 with --depth 1
--recurse-submodules --shallow-submodules into a new temporary directory
outside any project. Then read AGENT-INSTALL.md from that clone and follow it
exactly. Ask me which toolkits and which harnesses before installing anything,
merge with—never blindly overwrite—any existing global instruction files, and
report every file you created or changed.
```

It asks you which of the five toolkits you want and which harnesses you
run; nothing is installed until you say so, and each toolkit's own
`AGENT-INSTALL.md` still governs its individual steps.

**Where things land:** every toolkit installs **user-global** —
`~/.agents/skills/` (Codex, Gemini CLI, Cline, OpenCode), `~/.claude/skills/`
(Claude Code), `~/.gemini/antigravity-cli/skills/` (Antigravity CLI), rule
bodies under `~/.agents/rules/`, and marker-guarded pointer blocks in your
global instruction files. Nothing is added to your repos, and no project
`.gitignore` is ever touched. What a project commits is truth written by the
skills in use: the `DOX.md` tree and root `AGENTS.md` / `CLAUDE.md` / `.gemini/settings.json` anchors
from `dox-init` / `dox-upgrade`, `.goal-ledger/` records, `compose-helper.*`
from the `compose-helper` skill, generated agentic tests. Project-level
install is available on explicit request.

## After installing

- Start a **new** session, then ask your agent to *"use the `<skill-name>`
  skill"*. Antigravity CLI, Claude Code, and Cline expose `/<skill-name>`;
  Codex uses a `$` skill selector; OpenCode uses `@<skill-name>`; in Gemini
  CLI, name the skill in the prompt. Implicit activation works when your
  request matches the skill's description.
- Toolkits are per-user, per-machine: teammates who want them install them
  for themselves, and their fresh clones work regardless — the committed
  root anchors steer any agent to `DOX.md` with zero tooling installed.
  Only project truth (`DOX.md` trees + anchors, `.goal-ledger/`,
  `compose-helper.*`, generated agentic tests) lives in the repo.
  Teams typically add a PR-checklist item that the DOX tree is current.
- Toolkits compose: use dox + goal-ledger + agentic-tests together; each owns
  only its own skill/rule names.
