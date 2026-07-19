# Getting started

## Cloning the umbrella

```sh
git clone --recurse-submodules <this-repo-url> agent-commons
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

## Installing a toolkit into one of your projects

You never install *from* this umbrella — each toolkit installs from its own
GitHub repo, so a clone of Agent Commons is not required on the machine doing
the install.

**Preferred — agent-guided.** From your project's root, paste the toolkit's
install prompt (found in its README) into Codex, Claude Code, Antigravity, or
Cline. The general shape:

```
Fetch https://raw.githubusercontent.com/jpbaking/<toolkit>/main/AGENT-INSTALL.md and follow its instructions exactly to install <toolkit> into this project. Merge with — never blindly overwrite — any existing AGENTS.md, CLAUDE.md, rule, or ignore files, and report every file you created or changed.
```

The agent audits for skill-name collisions, merges with existing root
instruction files, installs the skills and rules into each harness's
discovery path, and gitignores the generated copies.

**Script fallback.** Every toolkit also ships `install.sh` / `install.ps1`:

```sh
curl -fsSL https://raw.githubusercontent.com/jpbaking/<toolkit>/main/install.sh | sh
```

Both paths are idempotent — re-run to update.

## After installing

- Ask your agent to *"use the `<skill-name>` skill"*. Claude Code,
  Antigravity, and Cline also expose `/<skill-name>`; Codex uses a `$` skill
  mention. Implicit activation works when your request matches the skill's
  description.
- The installed adapters are gitignored: a teammate's fresh clone won't have
  them. The committed `AGENTS.md` / `CLAUDE.md` bridges say so and degrade
  safely — re-running the install regenerates everything.
- Toolkits compose: a project can carry dox + goal-ledger + agentic-tests
  side by side; each owns only its own skill/rule names and gitignore block.
