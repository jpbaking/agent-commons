# Universal Agent Support Playbook

Verified against current official documentation on 2026-07-19.

This guide tells coding agents how to create or migrate reusable instructions for these preferred harnesses:

- OpenAI Codex
- Anthropic Claude Code
- Google Antigravity (`agy`)
- Cline

The goal is semantic portability: maintain one authoritative workflow or rule, then install the smallest adapters each harness needs. Do not pretend that discovery paths, command prefixes, rule metadata, or dedicated workflow formats are universal.

## The portable architecture

Use these layers:

1. **Persistent project guidance:** put the shared contract in a normal Markdown source such as `DOX.md`, and bridge harness entry files to it.
2. **On-demand procedures and workflows:** author them as Agent Skills using a portable `SKILL.md`.
3. **Host discovery adapters:** copy the shared skill or rule into each harness's documented discovery location.
4. **Permissions and enforcement:** keep them host-specific. Permission formats are not portable rules.

The portable user instruction is:

> Use the `skill-name` skill.

Do not use `/skill-name` as the universal spelling. Explicit invocation differs by harness.

## Compatibility matrix

| Harness | Persistent guidance / rules | Project skills | Explicit skill invocation | Dedicated workflow behavior |
| --- | --- | --- | --- | --- |
| Codex | `AGENTS.md` hierarchy | `.agents/skills/<name>/SKILL.md` | Type `$` and select the skill, or browse with `/skills` | Author reusable workflows as skills |
| Claude Code | `CLAUDE.md`, `.claude/CLAUDE.md`, `.claude/rules/*.md` | `.claude/skills/<name>/SKILL.md` | `/skill-name` | Claude-specific workflow/command features exist; do not use them as the shared source |
| Google Antigravity | `.agents/rules/*.md`; `AGENTS.md` is also recognized by the CLI | `.agents/skills/<name>/SKILL.md` | `/skill-name`, `/skills`, name mention, or implicit selection | UI-managed Markdown workflows use slash commands; migrate reusable cross-agent procedures to skills |
| Cline | `AGENTS.md`, `.clinerules`, `.clinerules/*.md`, or `.cline/rules/*.md` | Current Cline supports `.agents/skills`; its public skills page also documents `.cline/skills`, `.clinerules/skills`, and `.claude/skills` | `/skill-name` or implicit selection | `.clinerules/workflows` is Cline-specific; prefer skills for shared procedures |

Important distinctions:

- Codex `.codex/rules/*.rules` files control which shell commands may run. They are not Markdown behavioral guidance. Never copy Claude, Antigravity, Cline, AGENTS, or DOX instructions there.
- Claude Code reads `CLAUDE.md`, not `AGENTS.md`, but officially supports `@AGENTS.md` imports.
- `.agents/skills` is the common project location for Codex, Antigravity, and current Cline. Claude Code still needs `.claude/skills`.
- Slash autocomplete is a host UI feature, not part of the Agent Skills standard.

### Discovery collisions and ignore files

Multi-harness repositories can contain overlapping discovery roots. In
particular, Cline may recognize compatibility locations that another selected
harness also requires, such as `.claude/skills`, while the shared Cline adapter
lives in `.agents/skills`. Treat this as a discovery-collision problem even when
the files are byte-identical.

Before installing, build a per-harness map from each skill `name` to every path
that harness can discover. Apply these rules:

- Prefer one physical discovery path per harness and skill name.
- When two selected harnesses require separate paths and one harness can see
  both, keep the adapters byte-identical and verify that the overlapping harness
  resolves the name to exactly one visible skill. Do not assume deduplication or
  precedence without a current source check or harness smoke test.
- Inspect global and project skill locations. A global skill with the same name
  can override or shadow the project adapter even when the repository contains
  only one copy.
- Do not use `.clineignore`, `.gitignore`, `.geminiignore`, or a similar file as
  a skill-deduplication mechanism unless current documentation or released
  source explicitly says the skill registry honors it. File/context exclusion
  and skill discovery are separate behaviors.
- Preserve user-owned ignore files. If an ignore rule makes the canonical skill
  or rule path unreadable to a harness, warn with the exact matching pattern and
  require the user to review it; never silently remove the rule.
- Apply the same collision audit to persistent guidance. Do not both auto-load a
  rule adapter and import the same body through a root instruction file unless
  the harness is verified to collapse that duplication.

## Recommended repository layout

Keep one canonical source tree outside all auto-discovery directories, then let an installer copy it into place:

```text
skills/
  shared/
    my-workflow/
      SKILL.md
      scripts/        # optional
      references/     # optional
      assets/         # optional
rules/
  shared/
    project-guidance.md
AGENTS.md
CLAUDE.md
install.sh
install.ps1
```

Recommended bridge files when the corresponding auto-discovered rule adapter is
not installed:

`AGENTS.md`:

```markdown
This project keeps its shared agent instructions in `DOX.md`. Read `DOX.md` in this directory and follow it.
```

`CLAUDE.md`:

```markdown
@AGENTS.md
```

The installer should produce this project-local configuration:

```text
.agents/
  skills/<name>/SKILL.md       # Codex + Antigravity + current Cline
  rules/project-guidance.md    # Antigravity
.claude/
  skills/<name>/SKILL.md       # Claude Code
  rules/project-guidance.md    # Claude Code
.clinerules/
  project-guidance.md          # Cline
AGENTS.md                       # Codex + Antigravity CLI + Cline
CLAUDE.md                       # Claude Code bridge when the rule is not already auto-discovered
```

Do not keep independently edited copies in these destinations. They are generated adapters; the shared source is authoritative.

## Decide what kind of artifact to create

Use this decision order:

1. **Is it a repeatable task with steps and a result?** Create a skill. This is also the universal replacement for a vendor workflow or custom slash command.
2. **Must it guide nearly every task in the repository?** Put the durable contract in the shared project guidance and bridge it through the harness rule files.
3. **Does it apply only to certain paths?** Keep the semantic rule shared, but implement path activation separately in each host adapter. Path-rule metadata is not portable.
4. **Does it allow, deny, or sandbox commands/tools?** Keep it host-specific. Do not describe a permission policy as a universal rule.
5. **Does it need live external tools or data?** The skill may be shared, but MCP/plugin configuration and authentication remain host-specific adapters.

## Author a universal skill

Create `skills/shared/<skill-name>/SKILL.md` with this minimum shape:

```markdown
---
name: skill-name
description: State what this does and the requests or situations that should trigger it.
---

# Skill title

Follow these steps:

1. Read the required project context.
2. Perform the task.
3. Run the relevant validation.
4. Report the result and anything intentionally left unresolved.
```

Apply all of these portability rules:

- Use only `name` and `description` in shared YAML frontmatter.
- Make `name` lowercase kebab-case and exactly match the directory name.
- Keep the description at or below 1,024 characters because that is Cline's documented limit.
- Put every trigger and “when to use” clue in `description`; agents select skills from that metadata.
- Write the body in imperative, agent-neutral language.
- Refer to another skill as “the `other-skill` skill,” not `/other-skill`, `$other-skill`, or a harness tool call.
- Do not mention Cline's `use_skill` tool or any harness-internal skill-loading tool.
- Do not use host-only frontmatter such as Claude's `allowed-tools`, `context`, `agent`, `disable-model-invocation`, or argument fields in the shared source.
- Do not depend on host-only placeholders such as `$ARGUMENTS`, positional substitutions, dynamic command interpolation, or session variables.
- Request required inputs in normal prose. The agent can read them from the user's prompt or ask for missing information.
- Put detailed optional material in `references/`, deterministic helpers in `scripts/`, and output templates in `assets/`.
- Keep relative resource links anchored to the skill directory.
- Avoid assuming a shell, operating system, package manager, model, or tool name unless it is genuinely required. When it is required, state the precondition and provide safe alternatives where practical.
- Keep one skill focused on one job. Split unrelated procedures.

### Explicit invocation wording

Documentation and installer output should say:

> Ask your agent to use the `skill-name` skill.

Optionally add a host-specific reference table:

- Codex: type `$` and select `skill-name`, or use `/skills`.
- Claude Code: `/skill-name`.
- Antigravity: `/skill-name`, `/skills`, or mention the skill name.
- Cline: `/skill-name`.

Never make successful use depend on slash autocomplete. Clear descriptions must allow implicit selection, and plain-language name mention must remain the fallback.

## Convert a workflow or slash command into a universal skill

Use this migration procedure for Claude commands/workflows, Antigravity workflows, Cline workflows, or any Markdown slash command:

1. Identify the workflow's user-visible purpose, trigger phrases, required inputs, ordered steps, side effects, validation, and final output.
2. Create `skills/shared/<name>/SKILL.md`.
3. Move the purpose and all trigger phrases into `description`.
4. Move the ordered procedure into the skill body.
5. Replace special argument variables with plain instructions such as “Use the path supplied by the user; ask if it is missing.”
6. Replace nested slash-command calls with semantic skill references such as “Run the `audit-project` skill first.”
7. Replace harness tool names with outcomes. For example, write “search the repository” rather than “call `search_files`.”
8. Move reusable executable logic into `scripts/` only when deterministic execution is necessary.
9. Remove vendor-only frontmatter and dynamic interpolation from the shared source.
10. Copy the completed skill into `.agents/skills/<name>/` and `.claude/skills/<name>/`.
11. Test implicit activation and explicit selection separately in every supported harness.
12. Retire the old vendor workflow after confirming there is no remaining caller. Do not leave two editable sources of truth.

If a vendor workflow performs behavior that Agent Skills cannot express portably, split it:

- shared decision-making and procedure in the skill;
- host-specific automation in a clearly labeled adapter or script;
- a shared skill step that describes the required outcome without assuming the adapter implementation.

## Create or migrate universal rules

There is no universal rule directory or universal rule-frontmatter schema. Share the Markdown meaning, not the container.

Use this pattern:

1. Put the authoritative project contract in `DOX.md`, `docs/agent-guidance.md`, or another normal Markdown file.
2. Make `AGENTS.md` point to that source for Codex, Antigravity CLI, and Cline.
3. Make `CLAUDE.md` import `AGENTS.md` with `@AGENTS.md` only when Claude does
   not already receive the same semantic body through `.claude/rules`; avoid an
   import plus an auto-discovered duplicate.
4. If stronger host discovery is wanted, copy a concise shared rule body to:
   - `.agents/rules/<name>.md` for Antigravity;
   - `.claude/rules/<name>.md` for Claude Code;
   - `.clinerules/<name>.md` for Cline.
5. Keep path patterns, activation modes, priority, and other metadata in the individual adapters, not the shared body.
6. Never write behavioral Markdown to `.codex/rules`.

When migrating existing rules:

- From `CLAUDE.md` or `.claude/rules`: extract the host-neutral rule into the shared source; keep Claude-only imports, path metadata, and behavior in the Claude adapter.
- From `.agents/rules`: extract the rule body; keep Antigravity activation mode and glob configuration in the Antigravity adapter.
- From `.clinerules` or `.cline/rules`: extract the rule body; keep Cline path frontmatter and toggles in the Cline adapter.
- From `AGENTS.md`: preserve Codex's hierarchical scope. If the shared source is elsewhere, replace duplicated framework text with a short pointer only after verifying that every preferred harness still reaches it.
- From `.codex/rules`: do not migrate it as prompt guidance. Translate command permissions separately for each host only if the user explicitly wants equivalent enforcement.

### Preserve existing instructions

Installers and migration agents must not blindly overwrite root instruction files:

- If `AGENTS.md` is missing, create the minimal bridge when a selected harness
  needs it to reach the shared guidance.
- If it already reaches the shared guidance, leave it unchanged.
- If it contains unrelated instructions, add the bridge once and preserve the existing content.
- If it appears to be a legacy framework document, classify and migrate it explicitly; never replace it with a shim first.
- Apply the same preservation logic to `CLAUDE.md`; do not add a bridge when an
  auto-discovered `.claude/rules` adapter already provides the same guidance.
- Overwrite only clearly DOX-owned/generated adapter files, and document that policy.

## Installer requirements

A universal installer should be project-scoped by default and idempotent.

It should:

- copy every shared skill to both `.agents/skills/<name>/` and `.claude/skills/<name>/`;
- copy shared behavioral rule adapters to `.agents/rules/`, `.claude/rules/`, and `.clinerules/` when those adapters are part of the project;
- create missing `AGENTS.md` and `CLAUDE.md` bridges only when the selected
  harness would not otherwise receive the same semantic guidance;
- preserve and warn about existing root instruction files rather than overwrite them;
- overwrite only generated files with stable, documented ownership;
- accept a repository and revision override for testing forks and release tags;
- fail immediately when a download or write fails;
- print plain-language next steps plus the host-specific invocation syntax;
- work when re-run to update an existing installation;
- inspect every selected harness's overlapping project and global discovery
  paths, report same-name collisions, and verify installed adapter copies match;
- preserve ignore files and warn when they hide a required canonical path;
- avoid a universal global mode unless it can safely merge every harness's existing global instruction file.

Do not install the same skill simultaneously into `.agents/skills` and `.cline/skills` by default. Current Cline can discover `.agents/skills`, and duplicate discovery can expose two skills with the same name. Offer an explicit legacy-Cline compatibility option only when needed.

## Validation checklist

Run all applicable checks before declaring a migration complete.

### Shared skill validation

- [ ] Every skill directory contains `SKILL.md`.
- [ ] YAML parses successfully.
- [ ] Frontmatter contains only `name` and `description`.
- [ ] `name` exactly matches the directory and uses lowercase kebab-case.
- [ ] `description` is non-empty, trigger-oriented, and no longer than 1,024 characters.
- [ ] The body is imperative and under 500 lines.
- [ ] No shared body assumes `/`, `$`, `@`, `use_skill`, or another harness invocation mechanism.
- [ ] No host-only substitutions or frontmatter remain.
- [ ] Every referenced script, reference, or asset exists at the stated relative path.
- [ ] Side effects, stopping conditions, validation, and reporting expectations are explicit.

### Adapter validation

- [ ] `.agents/skills` and `.claude/skills` copies match the canonical source byte-for-byte.
- [ ] `AGENTS.md` reaches the shared project guidance.
- [ ] Claude reaches the shared guidance through exactly one of `CLAUDE.md` or
  an auto-discovered `.claude/rules` adapter.
- [ ] Antigravity, Claude, and Cline rule adapters contain the same semantic rule body.
- [ ] No behavioral instruction was placed in `.codex/rules`.
- [ ] Existing unrelated root instructions survived migration.
- [ ] No duplicate skill with the same name is exposed to one harness.
- [ ] Every selected harness's skill-name-to-path map has been inspected,
  including compatibility and global paths.
- [ ] Required copies visible through overlapping discovery roots are
  byte-identical and the harness resolves them to one skill entry.
- [ ] Ignore files were preserved, are not being used as an unverified registry
  filter, and do not hide the canonical adapter path.
- [ ] Persistent guidance reaches each harness once without duplicate automatic
  loading plus an equivalent import.

### Harness smoke tests

For each harness, start a fresh session in the project and test:

1. Ask a natural-language request matching the skill description; confirm implicit selection or correct behavior.
2. Invoke or select the skill explicitly using that harness's supported UI.
3. List discovered skills and confirm each installed skill name appears exactly
   once and resolves to the intended project adapter.
4. Confirm it reads the shared project guidance exactly once before editing.
5. Confirm it follows the same workflow and produces the same required result.
6. Confirm its rule adapter is loaded.
7. Confirm missing required input causes a question or clean stop rather than guessing.

## Common migration failures

- **Assuming slash commands are universal:** Codex uses `$` skill mentions.
- **Calling a vendor workflow a universal workflow:** the portable artifact is the skill; vendor workflows are adapters at most.
- **Using `.agents/skills` for Claude Code:** Claude needs `.claude/skills`.
- **Treating `.codex/rules` as Codex prompt rules:** it is an execution-policy format.
- **Copying Claude-only frontmatter into the shared skill:** other agents may reject or ignore it.
- **Depending on `$ARGUMENTS` or dynamic command interpolation:** those substitutions are not portable.
- **Duplicating skill sources:** later edits drift and agents may discover two copies.
- **Treating byte-identical adapters as proof of deduplication:** a harness may
  still expose or load both paths; inspect its resolved skill list.
- **Using an ignore file to hide duplicate skills:** ignore files commonly govern
  file/context access and may not filter the skill registry. Verify before relying
  on one, and never rewrite a user-owned ignore rule silently.
- **Overwriting `AGENTS.md` or `CLAUDE.md`:** existing project instructions may be destroyed.
- **Forcing one path-scoping schema across agents:** activation metadata differs; share only the rule body.
- **Keeping the old slash workflow active indefinitely:** users cannot tell which source is authoritative.

## Current official references

Re-check these before making broad migrations because discovery paths and UI behavior can change:

- Codex skills and invocation: [Build skills](https://learn.chatgpt.com/docs/build-skills)
- Codex project instructions: [AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
- Codex command execution policies: [Rules](https://learn.chatgpt.com/docs/agent-configuration/rules)
- Claude Code skills and slash invocation: [Extend Claude with skills](https://code.claude.com/docs/en/slash-commands)
- Claude Code instructions, imports, and rules: [How Claude remembers your project](https://code.claude.com/docs/en/memory)
- Antigravity Agent Skills: [Skills](https://antigravity.google/docs/skills)
- Antigravity rules and workflows: [Rules and Workflows](https://antigravity.google/docs/rules-workflows)
- Antigravity CLI skill browser: [CLI reference](https://antigravity.google/docs/cli-reference)
- Cline Agent Skills: [Skills](https://docs.cline.bot/customization/skills)
- Cline rules: [Cline Rules](https://docs.cline.bot/customization/cline-rules)
- Cline skill slash commands: [Using Commands](https://docs.cline.bot/core-workflows/using-commands)
- Cline's current `.agents/skills` adoption: [Cline changelog](https://github.com/cline/cline/blob/main/CHANGELOG.md)
- Cross-agent `.agents/skills` convention: [GitHub CLI `gh skill install`](https://cli.github.com/manual/gh_skill_install)

When official pages disagree with current released source, document the discrepancy and choose the least surprising behavior. Prefer a reversible adapter over changing the canonical shared content.
