# Conventions

Every toolkit in this family follows the same contract. The full, verified
playbook is [MULTI-HARNESS-SUPPORT.md](../MULTI-HARNESS-SUPPORT.md); this page
is the working summary.

## Repository layout (per toolkit)

```text
skills/shared/<name>/SKILL.md   # canonical portable Agent Skills
  scripts/ references/ assets/  # optional bundled resources
rules/shared/<name>.md          # canonical always-on rule bodies
AGENT-INSTALL.md                # authoritative, merge-aware install contract
install.sh / install.ps1        # idempotent script installers
```

Canonical sources never live in a harness discovery directory. Shared
`SKILL.md` frontmatter uses only `name` and `description`; bodies are
imperative and harness-neutral (no `/`, `$`, `$ARGUMENTS`, `use_skill`, or
vendor-only frontmatter).

## Install targets (per consuming project)

| Artifact | Destination | Harness |
|---|---|---|
| Skills | `.agents/skills/<name>/` | Codex, Antigravity, current Cline |
| Skills (byte-identical copy) | `.claude/skills/<name>/` | Claude Code |
| Rules | `.agents/rules/`, `.claude/rules/`, `.clinerules/` | one copy each |
| Bridges | `AGENTS.md`, `CLAUDE.md` (`@AGENTS.md`), optionally `GEMINI.md` | merged, never overwritten |

Never write behavioral Markdown to `.codex/rules` (that path is Codex's
command-execution policy, not guidance).

## The gitignore contract

Installed skill/rule copies are **generated adapters** and are added to the
target's `.gitignore` under a marker line, e.g.:

```gitignore
# <Toolkit> installer-managed agent adapters (generated; do not edit or commit)
.agents/skills/<name>/
.claude/skills/<name>/
.agents/rules/<name>.md
...
```

What stays **committed**: the root bridges (`AGENTS.md`, `CLAUDE.md`,
`GEMINI.md`) and all user data the toolkit produces or manages —
`.goal-ledger/`, generated agent tests, a copied `design/` kit, `DOX.md`
trees.

Because adapters are gitignored, bridge text is always **conditional**:

> If `.agents/rules/<name>.md` exists, read and follow it. If it is missing
> (fresh clone — the adapters are gitignored), re-run the installer.

A fresh clone therefore works without the adapters and self-describes how to
regenerate them.

## Install paths

1. **Agent-guided (preferred)** — `AGENT-INSTALL.md` is the authoritative
   contract: survey for collisions (project, compatibility, and global skill
   paths), install, merge bridges, gitignore, validate byte-identical copies,
   report every change.
2. **Script (`install.sh` / `install.ps1`)** — same layout, idempotent,
   marker-guarded appends, `<TOOLKIT>_REPO` / `<TOOLKIT>_REF` overrides for
   forks and tags (plus `<TOOLKIT>_SOURCE` for local installs where
   supported).

## Invocation wording

Documentation says: *ask your agent to use the `<skill-name>` skill*.
Slash forms (`/<skill-name>` in Claude Code / Antigravity / Cline, `$` mention
in Codex) are host conveniences, never a requirement — descriptions must
support implicit activation.
