# Conventions

Every toolkit in this family follows the external
[Portable Agent Authoring](https://github.com/jpbaking/portable-agent-authoring)
guides. They are authoritative; this page does not restate them:

- [Agent toolkit repositories](https://github.com/jpbaking/portable-agent-authoring/blob/main/agent-toolkit-repositories.md)
  — repository layout, the agent-guided `AGENT-INSTALL.md` contract
  (git-only acquisition, survey, install, rule pointers, legacy cleanup,
  validate, report, update, uninstall), global destinations, marker-guarded
  blocks, the README install prompt, security posture, testing, and the
  release checklist. Start new contracts from its
  [template](https://github.com/jpbaking/portable-agent-authoring/blob/main/templates/AGENT-INSTALL.md).
- [Universal skills, rules, and workflows](https://github.com/jpbaking/portable-agent-authoring/blob/main/universal-agent-authoring.md)
  — skill portability rules, the `AGENTS.md` / `CLAUDE.md` /
  `.gemini/settings.json` / `.claude/skills` layout, and per-harness details
  for `agy`, Codex, Claude Code, Gemini CLI, Cline, and OpenCode.

Re-check those guides before broad changes; harness discovery paths change
faster than this family does.

## Family-specific additions

These go beyond, or pin down choices left open by, the external guides.

- **Supported harnesses** are exactly the six the guides cover. Pi and
  generated `.clinerules/workflows/` shims are retired: contracts report an
  old Pi pointer block, `~/.cline/skills/` copy, or Cline workflow shim as a
  legacy artifact and remove it only with approval.
- **Contributor guidance** lives in each toolkit's `AGENTS.md`, including the
  dogfooding-collision warning. DOX is the exception: its root `AGENTS.md` is
  also the shim it ships, so contributor notes sit below the shim line.
- **Bundled files over network fetches.** A file a skill writes into a
  project is bundled in that skill's `assets/` (DOX's framework `DOX.md`,
  compose-helper's wrapper scripts) and kept byte-identical to its repository
  source. A skill that must track the latest release (`dox-upgrade`, the
  `dox-audit` version check) uses a shallow, sparse `git clone`, never raw
  HTTP.
- **Project truth is written by skills, never by installs:** `DOX.md` trees
  and the root `AGENTS.md` / `CLAUDE.md` / `.gemini/settings.json` anchors (`dox-init`,
  `dox-upgrade`), `.goal-ledger/` records, `compose-helper.sh` / `.ps1` /
  `.env` (the `compose-helper` skill), and generated agentic tests. These are
  committed and must never be gitignored.
- **Hands off `.gitignore`.** Neither installs nor skills edit a consuming
  project's ignore files. If an ignore rule hides something a harness needs,
  report the exact pattern and let the user decide.
- **Self-gating rules.** Each global rule gates on project state or a narrow
  request intent (a DOX root, `.goal-ledger/`, a compose-helper wrapper, or
  live-site testing). Its pointer block summarizes the behavior so it works
  even if the body is never opened.
- **Offer-based cross-references.** Where docs reference an optional toolkit,
  the wording is: *"If the `<name>` skills are not available, they are an
  optional user-global install — offer to install them and proceed only if
  the user agrees."*
- **Invocation wording.** Documentation says *ask your agent to use the
  `<skill-name>` skill*; host shortcuts (`/<skill>` in `agy`, Claude Code, and
  Cline; `$` in Codex; `@<skill>` in OpenCode) are conveniences only.

## Adding a toolkit to the family

1. Build the toolkit to the
   [Agent toolkit repositories](https://github.com/jpbaking/portable-agent-authoring/blob/main/agent-toolkit-repositories.md)
   guide and its checklist, plus the additions above. Automate what you can
   (goal-ledger's `tests/` and CI mirror check, and playwright-fieldkit's
   `npm test` self-test, are the reference examples).
2. Register it as a submodule:

   ```sh
   git submodule add https://github.com/jpbaking/<toolkit>.git <toolkit>
   git commit -m "Add <toolkit> submodule"
   ```

3. Add a row to the README's toolkit table **and** to the family table in the
   umbrella [AGENT-INSTALL.md](../AGENT-INSTALL.md) — skills, global rule
   file, pointer-block marker, and anything non-standard about its install —
   plus a position in that file's install order. A toolkit missing from that
   table is invisible to the family install.
4. When the toolkit is release-worthy, tag it in its own repository
   (`gh release create vX.Y.Z …`).

**Naming gotcha:** keep the local folder, the GitHub repository, and every URL
in docs and contracts on the **same canonical name** from day one. GitHub
redirects renamed repositories, so mismatches keep working silently until they
surface in release URLs and CI paths.
