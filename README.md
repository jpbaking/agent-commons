# Agent Commons

**One canonical source, every coding-agent harness.**

Agent Commons is an umbrella workspace for a family of reusable AI-agent
toolkits — portable [Agent Skills](https://agentskills.io/), always-on rules,
and a merge-aware agent install contract that works identically across **OpenAI Codex**,
**Anthropic Claude Code**, **Google Antigravity**, **Cline**, and **Cursor**
(Cursor discovers the shared global skill copies natively — no separate
install target).

Every toolkit here follows the same contract, defined in
[MULTI-HARNESS-SUPPORT.md](./MULTI-HARNESS-SUPPORT.md) (the authoritative
playbook) and summarized in [docs/conventions.md](./docs/conventions.md):

- canonical sources live in `skills/shared/` and `rules/shared/`, never in a
  harness's discovery directory;
- installs are **agent-guided only** — each repo ships an `AGENT-INSTALL.md`
  your agent follows (clone / zip / `gh`, then copy); there are no install
  scripts;
- every toolkit installs **user-global**; installs never write into
  consuming projects (not even `.gitignore`). Projects commit only truth —
  `DOX.md` trees, the root `AGENTS.md` / `CLAUDE.md` anchors carrying the
  DOX shim (created by `dox-init` / `dox-upgrade`), `.goal-ledger/` records,
  `design/` copies, `compose-helper.*`, generated agentic tests.

## The toolkits

| Submodule | What it gives your agent |
|---|---|
| [dox](https://github.com/jpbaking/dox) | A `DOX.md` documentation-tree framework: precise project context, walked root-to-leaf before every edit |
| [goal-ledger](https://github.com/jpbaking/goal-ledger) | A git-tracked execution record for multi-phase work: crash recovery, handoff, branch isolation, safe squash |
| [agentic-tests](https://github.com/jpbaking/agentic-tests) | A disciplined test engineer: behavior-locking unit tests, mutation checks, frozen-suite refactoring |
| [playwright-fieldkit](https://github.com/jpbaking/playwright-fieldkit) | Hands and eyes on the web: crawl, debug, record, and test live sites via deterministic Playwright scripts |
| [compose-helper](https://github.com/jpbaking/compose-helper) | A safe `docker compose` wrapper plus the rules that stop agents from wiping volumes or hanging on logs |
| [lazyway-io-design](https://github.com/jpbaking/lazyway-io-design) | The lazyway.io design system as tokens, classes, and a skill that keeps generated UI on-brand |

Each submodule is an independent repository with its own releases; this
umbrella pins known-good revisions of all six.

## Quick start

Clone with submodules:

```sh
git clone --recurse-submodules https://github.com/jpbaking/agent-commons.git
# or, in an existing clone:
git submodule update --init
```

Then install any toolkit into a project by pasting its README's agent prompt
into your coding agent — see
[docs/getting-started.md](./docs/getting-started.md).

## Layout

```text
MULTI-HARNESS-SUPPORT.md   # the authoritative multi-harness playbook
README.md                  # this file
docs/                      # umbrella user guides
<toolkit>/                 # six submodules, one per toolkit
```

## Documentation

- [Getting started](./docs/getting-started.md) — cloning, updating, and
  installing toolkits into your projects
- [Conventions](./docs/conventions.md) — the shared layout, install, and
  gitignore contract every toolkit follows
- [Authoring](./docs/authoring.md) — adding a new toolkit to the family
