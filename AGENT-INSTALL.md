# Agent Commons — agent-guided install (whole family)

You are an AI coding agent installing one or more Agent Commons toolkits.
Follow these steps exactly. This procedure is merge-aware — it never blindly
overwrites files the user already owns — and it is the only install path;
there are no install scripts.

This umbrella procedure does **not** replace the per-toolkit
`AGENT-INSTALL.md` files: each toolkit's own file stays authoritative for
its skills, rule, pointer block, and extras. What this file adds is the
family-level orchestration — an explicit scope confirmation, one source
acquisition, one collision audit across all toolkits, a safe install order,
and a single consolidated verification and report — so a user installing
several toolkits gets one coherent pass instead of seven disconnected ones.

Installing from this file is **opt-in per toolkit**. The user chooses which
of the seven to install (step 1); this procedure never installs the whole
family on its own initiative.

Every toolkit installs **user-global**: once per user, per machine, into
each harness's global discovery paths. The install never writes into a
consuming project — not even its `.gitignore`. Project-level adapters exist
only as an explicit per-toolkit opt-in (each toolkit's own file has that
section). What a project commits is truth written later by the skills
themselves: the `DOX.md` tree and root `AGENTS.md` / `CLAUDE.md` anchors,
`.goal-ledger/` records, `design/` copies, `compose-helper.*`, generated
agentic tests.

Source repository: `https://github.com/jpbaking/agent-commons` (submodules
pin known-good revisions of all seven toolkits). If the user named a fork or
a tag, substitute it below.

## The family

| Toolkit | Skills installed | Global rule file | Pointer-block marker | Extras beyond the standard copy |
| --- | --- | --- | --- | --- |
| [dox](https://github.com/jpbaking/dox) | `dox-init`, `dox-child`, `dox-audit`, `dox-fix`, `dox-remap`, `dox-upgrade` | `dox.md` | `<!-- dox:global-rule -->` | rule self-gates on a `DOX.md` root; project anchors are written later by `dox-init` / `dox-upgrade`, never by this install |
| [goal-ledger](https://github.com/jpbaking/goal-ledger) | `goal-ledger`, `goal-ledger-resume`, `goal-ledger-status`, `goal-ledger-abandon` | `goal-ledger.md` | `<!-- goal-ledger:global-rule -->` | rule self-gates on `.goal-ledger/` |
| [agentic-tests](https://github.com/jpbaking/agentic-tests) | `agentic-unit-test`, `agentic-mutation-check`, `agentic-refactor`, `agentic-test-update`, `agentic-test-clean`, `agentic-coverage-report` | *(none — skills only)* | *(none)* | no rule and no pointer block; do not invent one |
| [playwright-fieldkit](https://github.com/jpbaking/playwright-fieldkit) | `pw-playwright-fieldkit` (whole directory: SKILL.md, scripts, references) | `pw-playwright-fieldkit.md` | `<!-- playwright-fieldkit:global-rule -->` | generated Cline `/pw-*` workflow stubs; browser runtime setup |
| [compose-helper](https://github.com/jpbaking/compose-helper) | `compose-helper` | `compose-helper.md` | `<!-- compose-helper:global-rule -->` | Part B (the per-project wrapper script) is separate work — do it only when the user asks, in a project |
| [lazyway-io-design](https://github.com/jpbaking/lazyway-io-design) | `lazyway-io-design` (SKILL.md + `templates/`) | `lazyway-io-design.md` | `<!-- lazyway-io-design:global-rule -->` | kit library under `~/.agents/skills/lazyway-io-design/library/`; rule self-gates on a served `design/` folder |
| [cross-cli-handshake](https://github.com/jpbaking/cross-cli-handshake) | *(none — rules only)* | `cross-cli-handshake/core.md` + `delegates/{claude,codex,agy}.md` | `<!-- cross-cli-handshake:global-rule -->` | Claude Code, Codex, Antigravity only — no Cline, no Cursor, no project install; per-harness identity token; detects and replaces legacy hand-maintained policy copies |

## 1. Confirm scope — required, before anything else

**Never install the whole family by default.** "Install Agent Commons" is
not consent to install all seven toolkits; it is a request to start this
conversation. Do not copy a single file until the user has explicitly named
what they want.

Show the user the family table above, then ask:

1. **Which toolkits?** List all seven with their one-line purpose and let
   the user pick. Accept "all seven" only if the user says so themselves —
   never assume it, never pre-select it, and never treat silence, a hurried
   "yes", or "the usual" as a selection. The toolkits are fully independent;
   a two-toolkit install is as valid as a seven-toolkit one.
2. **Which harnesses?** Codex, Claude Code, Antigravity, Cline — ask which
   the user actually has. Cursor needs no separate copy (see step 5).
   Detecting an installed harness (`~/.codex/`, `~/.claude/`, `~/.gemini/`,
   `~/.cline/`) is useful evidence to *offer*, not permission to install.

Then read the resulting selection back — toolkits × harnesses — and get a
clear go-ahead before step 2. If the user's answer is ambiguous or partial,
ask again rather than resolving it in the permissive direction.

Anything outside that confirmed selection is out of scope for this run:
never widen it mid-install, never add "while I'm here" toolkits, and never
perform a toolkit's opt-in project-level install or per-project setup
(compose-helper Part B) unless the user asked for that too.

## 2. Acquire the sources once

Obtain the sources in a temporary directory (never inside a project). Two
equivalent options:

- **Pinned family (recommended):**
  `git clone --depth 1 --recurse-submodules https://github.com/jpbaking/agent-commons <tmp>/agent-commons`
  (add `--branch <tag>` if the user pinned an umbrella release). Each
  toolkit is then at `<tmp>/agent-commons/<toolkit>/`, at the revision this
  umbrella vouches for.
- **Latest per toolkit:** clone only the selected toolkits' own repos
  (`https://github.com/jpbaking/<toolkit>`) into `<tmp>/<toolkit>`, or
  download and extract their `archive/refs/heads/main.zip`, or use
  `gh repo clone jpbaking/<toolkit>`.

If the user has an existing Agent Commons clone and points you at it, use
it read-only as the staging area — but run `git submodule update --init`
first if any toolkit directory is empty, and do not delete it in step 7.

Record which revision each toolkit came from; the final report names it.

## 3. Survey before writing

Do this **once, across all selected toolkits**, before copying anything:

1. List the skill names from the table above for the selected toolkits and
   check each against every selected harness's global skills directory
   (`~/.agents/skills/`, `~/.claude/skills/`, `~/.gemini/config/skills/`,
   `~/.cline/skills/`) and, if you are inside a project, its
   `.agents/skills/`, `.claude/skills/`, `.cline/skills/`. A global skill
   **shadows or duplicates** a same-name project skill.
2. Note which of the pointer-block markers already exist in
   `~/.codex/AGENTS.md` and `~/.claude/CLAUDE.md`. Present markers mean a
   re-run/update, not a conflict.
3. Check for legacy artifacts of earlier install models: gitignored adapter
   blocks in consuming projects, `install.sh` / `install.ps1` left over from
   pre-agent-guided versions, and (for cross-cli-handshake) hand-maintained
   delegation policy outside the marker block. Report them; remove nothing
   without user approval.

Report the whole survey before proceeding. Collisions with skills the user
owns are the user's call, not yours.

## 4. Install in this order

Install the selected toolkits one at a time, following each toolkit's own
`AGENT-INSTALL.md` (`<staging>/<toolkit>/AGENT-INSTALL.md`) — skipping its
"acquire the sources" step, already done in step 2, and its per-toolkit
survey, already folded into step 3. Everything else in those files applies
verbatim.

Recommended order, with reasons:

1. **dox** — foundational; other toolkits' docs assume a governed project.
2. **goal-ledger**
3. **agentic-tests**
4. **playwright-fieldkit**
5. **compose-helper**
6. **lazyway-io-design**
7. **cross-cli-handshake** — last, because it is the only procedure that may
   legitimately stop and wait for the user (legacy-policy removal approval).
   Reaching it last means a declined answer does not strand the other six.

If any toolkit's step fails, stop that toolkit, keep what already succeeded,
and carry the failure into the final report — do not roll back the others.

## 5. Family-level rules that override nothing but bind everything

These hold no matter which toolkit's file you are following:

- **Byte-identical copies.** Every installed skill copy must match the
  canonical source and every other harness's copy. Cursor sees
  `~/.agents/skills/`, `~/.claude/skills/`, and `~/.codex/skills/` at once —
  byte-identical copies keep that benign; one edited copy surfaces as a
  duplicate or shadowed skill there first.
- **No `~/.cursor/skills/` copy.** Cursor discovers the shared paths
  natively. A separate copy is a duplicate.
- **Antigravity does not read `~/.agents/skills/`** (probe-verified
  negative). It needs its own `~/.gemini/config/skills/<name>/` copy.
- **Global instruction files are user-owned.** `~/.codex/AGENTS.md` and
  `~/.claude/CLAUDE.md` end up carrying up to six marker-guarded blocks
  after a full install. For each: if the marker is present, replace that
  block's body with the current text; otherwise append it once. Never
  reorder, merge across markers, or touch content outside them. If existing
  content cannot be merged cleanly, fail safe — report, do not overwrite.
- **Never write to `~/.codex/rules`** or any `.codex/rules` — that path
  holds command-execution policy, not guidance.
- **Never create or edit a project's `.gitignore`**, and never gitignore
  project truth (`DOX.md`, root anchors, `.goal-ledger/`, `design/`,
  `compose-helper.*`, generated agentic tests).
- **Cursor rules are app settings.** After installing, print every pointer
  block from the selected toolkits once, together, and ask the user to paste
  them into Cursor Settings → Rules — one paste for the whole family, not
  seven prompts.

## 6. Verify

Before reporting, confirm all of the following for the selected toolkits:

1. Every skill directory exists at every selected harness's global target
   and is byte-identical to its staging source.
2. Every rule file exists at `~/.agents/rules/<name>.md`,
   `~/.gemini/config/rules/<name>.md`, and the Cline Rules directory
   (`~/Cline/Rules/` on Linux, `~/Documents/Cline/Rules/` on macOS/Windows —
   if both exist, use the populated one), byte-identical to source.
3. `~/.codex/AGENTS.md` and `~/.claude/CLAUDE.md` each contain **exactly
   one** block per installed marker, with correct identity tokens for
   cross-cli-handshake (`CODEX` and `CLAUDE` respectively), and all
   pre-existing unrelated content intact.
4. Toolkit-specific extras from the table landed: the fieldkit's Cline
   workflow stubs and browser runtime, the lazyway kit library, the
   Antigravity `cross-cli-handshake.md` adapter with `always_on`
   frontmatter and identity `AGY`.
5. No skill name appears twice across a harness's overlapping discovery
   paths with differing content.

## 7. Clean up and report

Delete the staging directory (unless it was the user's own pre-existing
clone). Then report, in one message:

- every file created, changed, or intentionally left alone, grouped by
  toolkit;
- the source revision each toolkit was installed from;
- collisions and legacy artifacts found in step 3, and what was done about
  each;
- any toolkit that failed or was skipped, and why;
- the Cursor pointer blocks to paste, if the user uses Cursor.

Then state the next steps:

- Invoke a skill by asking for it by name — *"use the `dox-init` skill"*.
  Claude Code, Antigravity, Cline, and Cursor also expose `/<skill-name>`;
  Codex uses a `$` skill mention. Implicit activation works when the request
  matches a skill's description.
- The install is per-user, per-machine, and single-version: updating a
  toolkit changes it for every project at once. Re-run this procedure to
  update.
- In a project the user wants governed, start with `dox-init`. The other
  toolkits' rules stay inert until their project marker exists
  (`.goal-ledger/`, `compose-helper.sh` / `.ps1`, a served `design/`
  folder).
- Teammates who want these toolkits install them for themselves; their fresh
  clones still work, because the committed root anchors steer any agent to
  `DOX.md` with zero tooling installed.
