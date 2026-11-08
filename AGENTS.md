# Agent Commons repository instructions

This umbrella and every toolkit in it follow the
[Portable Agent Authoring](https://github.com/jpbaking/portable-agent-authoring)
guides; *Agent toolkit repositories* is the authoritative standard for toolkit
layout and install contracts. Family-specific additions are in
`docs/conventions.md`.

- Treat each toolkit directory as an independent Git submodule. Changes inside
  one belong to that toolkit's own history; the umbrella later records its
  updated submodule commit.
- Keep universal skills authoritative in `.agents/skills/` inside each
  toolkit. Generated `.claude/skills/` mirrors must remain byte-identical.
  Do not reintroduce `.clinerules/workflows/` shims.
- Keep reusable user-global rule bodies in `rules/shared/`; host instruction
  files point to those bodies once rather than duplicating them.
- Install contracts and skills acquire a toolkit's own files only with `git`
  or `gh`, never raw-file, archive, or release-asset downloads.
- Update the toolkit README, `AGENT-INSTALL.md`, generated mirrors, and this
  umbrella's family documentation (`README.md`, `AGENT-INSTALL.md`,
  `docs/`) whenever discovery or install behavior changes.
- Never edit a consuming project's ignore files during installation.
