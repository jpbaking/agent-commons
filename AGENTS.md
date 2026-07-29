# Agent Commons repository instructions

This umbrella follows the
[Portable Agent Authoring](https://github.com/jpbaking/portable-agent-authoring)
guides.

- Treat each toolkit directory as an independent Git submodule. Changes inside
  one belong to that toolkit's own history; the umbrella later records its
  updated submodule commit.
- Keep universal skills authoritative in `.agents/skills/` inside each
  toolkit. Generated `.claude/skills/` mirrors must remain byte-identical, and
  `.clinerules/workflows/` files must remain thin invocation shims.
- Keep reusable user-global rule bodies in `rules/shared/`; host instruction
  files point to those bodies once rather than duplicating them.
- Update the toolkit README, `AGENT-INSTALL.md`, generated adapters, and this
  umbrella's family documentation whenever discovery or install behavior
  changes.
- Never edit a consuming project's ignore files during installation.
