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
```

Canonical sources never live in a harness discovery directory. Shared
`SKILL.md` frontmatter uses only `name` and `description`; bodies are
imperative and harness-neutral (no `/`, `$`, `$ARGUMENTS`, `use_skill`, or
vendor-only frontmatter).

## Install scopes

**Every toolkit installs user-global.** Skills, rules, and workflows live in
each harness's per-user discovery paths; installs never write into a
consuming project — not even its `.gitignore`. What lives in a project is
**project truth, committed**: `DOX.md` trees plus the root
`AGENTS.md`/`CLAUDE.md` anchors carrying the DOX shim (created or merged by
`dox-init`/`dox-upgrade`), `.goal-ledger/` records, copied `design/`
artifacts, `compose-helper.sh`/`.ps1`/`.env`, and generated agentic tests.
Truth is written by skills doing their job, never by installs.

Project-level adapter install remains an explicit opt-in (user request only)
for teams that want a toolkit pinned and shared in one repo.

Globally installed rules must **self-gate on project state** (e.g. only apply
when a DOX root, `compose-helper.env`, `.goal-ledger/`, or a served `design/`
folder exists in the project) so they stay inert in unrelated projects.

### Project-level targets (opt-in only)

| Artifact | Destination | Harness |
|---|---|---|
| Skills | `.agents/skills/<name>/` | Codex, Antigravity, current Cline |
| Skills (byte-identical copy) | `.claude/skills/<name>/` | Claude Code |
| Rules | `.agents/rules/`, `.claude/rules/`, `.clinerules/` | one copy each |

Opt-in installs copy adapters only. Root anchors (`AGENTS.md`, `CLAUDE.md`
with `@AGENTS.md`, optionally `GEMINI.md`) are owned by `dox-init` /
`dox-upgrade`, which merge the DOX shim and never overwrite existing
content.

### User-global targets (the default for every toolkit)

Verified 2026-07-20 (live probes on codex-cli 0.144.6 and Antigravity CLI
1.1.4; Claude Code and Cline paths from current official docs):

| Harness | Global skills | Global rules |
|---|---|---|
| Codex | `~/.agents/skills/<name>/` (also reads `~/.codex/skills/`; both probe-verified) | merge a pointer block into `~/.codex/AGENTS.md` |
| Claude Code | `~/.claude/skills/<name>/` | merge a pointer block into `~/.claude/CLAUDE.md` |
| Antigravity | `~/.gemini/config/skills/<name>/` (probe-verified; `~/.agents/skills/` is **not** discovered by the CLI) | `~/.gemini/config/rules/<name>.md` |
| Cline | `~/.cline/skills/<name>/` | `~/Cline/Rules/<name>.md` on Linux; `~/Documents/Cline/Rules/` on macOS/Windows |
| Cursor | no separate copy — discovers `~/.agents/skills/` (and `~/.claude/skills/` / `~/.codex/skills/` compat paths) natively | no global rules file: User Rules are app settings (paste the pointer block there once); reads committed project `AGENTS.md` natively |

All global skill copies stay byte-identical to the canonical source, exactly
like project adapters. Global instruction files (`~/.codex/AGENTS.md`,
`~/.claude/CLAUDE.md`) are user-owned: merge marker-guarded blocks, never
overwrite.

Never write behavioral Markdown to `.codex/rules` (that path is Codex's
command-execution policy, not guidance).

## The gitignore policy: hands off

Toolkits and installs **never touch a consuming project's `.gitignore`**.
Since all adapters are user-global, nothing generated lands in projects, so
there is nothing to ignore. Whether a team ignores or commits its agent
folders (`.claude/`, `.agents/`, `.clinerules/`, …) — or keeps its own
project-specific agent rules in committed root anchor files — is entirely
the project's own policy.

What must never be gitignored (project truth): the root anchors
(`AGENTS.md`, `CLAUDE.md`, `GEMINI.md` carrying the DOX shim), `DOX.md`
trees, `.goal-ledger/`, generated agent tests (`*.agentic.spec.*`), the
project's used `design/` artifacts, and
`compose-helper.sh` / `compose-helper.ps1` / `compose-helper.env`.

If an existing ignore rule hides something a harness needs, warn with the
exact matching pattern and let the user decide — never edit their ignore
files. Projects installed under the old contract may still carry stale
marker-guarded ignore blocks and gitignored project adapters; removing those
is a manual, user-approved cleanup.

Fresh clones need no regeneration step: the committed anchors steer any
agent to `DOX.md` with zero tooling installed. Where docs reference an
optional toolkit, the wording is **offer-based, not imperative**:

> If the `<name>` skills are not available, they are an optional user-global
> install — offer to install them and proceed only if the user agrees.

## Install path

Installs are **agent-guided only** — there are no install scripts.
`AGENT-INSTALL.md` is the authoritative, idempotent contract:

1. **Acquire the sources** — `git clone` the toolkit repo (a tag/ref or fork
   URL may be given for pinning), download the repo/release zip, or use
   `gh repo clone` / `gh release download`; work from a temporary directory,
   never inside the target project.
2. **Survey** for collisions (project, compatibility, and global skill
   paths) and existing root instruction/ignore files.
3. **Install by copying** from the acquired sources to the user-global
   targets (project-level only on explicit user request).
4. **Merge marker-guarded pointer blocks** into user-owned global
   instruction files, validate byte-identical copies, clean up the
   temporary clone, and report every change. Never touch a project's
   `.gitignore`.

Re-running the same procedure updates an existing install.

## Invocation wording

Documentation says: *ask your agent to use the `<skill-name>` skill*.
Slash forms (`/<skill-name>` in Claude Code / Antigravity / Cline, `$` mention
in Codex) are host conveniences, never a requirement — descriptions must
support implicit activation.
