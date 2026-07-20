# Authoring a new toolkit

Adding a new member to the family means shipping a repository that honors the
[conventions](./conventions.md) and registering it here as a submodule.

## 1. Build the toolkit

Start from the closest existing sibling and keep the shape:

- Author each procedure as a skill in `skills/shared/<name>/SKILL.md` —
  lowercase-kebab `name` matching the directory, a trigger-rich
  `description` (≤ 1,024 chars), an imperative harness-neutral body, and
  optional `scripts/`, `references/`, `assets/`.
- Put durable, always-on guidance in `rules/shared/<name>.md` — small, with a
  fresh-clone fallback sentence.
- Write `AGENT-INSTALL.md` as the sole install path (acquire sources via
  clone / zip / `gh` → survey → copy-install user-global → merge
  marker-guarded pointer blocks into global instruction files → validate →
  report). There are no script installers, and installs never write into a
  project — project truth is created by the skills during use.
- README install section: the agent prompt is the only install instruction.

## 2. Validate

Run the playbook's validation checklist (frontmatter, byte-identical adapter
copies, collision audit, smoke tests in each harness). Automate what you can —
goal-ledger's `tests/` (layout + validator unit tests) and
playwright-fieldkit's self-test are the reference examples.

## 3. Register the submodule

```sh
git submodule add https://github.com/jpbaking/<toolkit>.git <toolkit>
git commit -m "Add <toolkit> submodule"
```

Then add a row to the README's toolkit table and, when the toolkit is
release-worthy, tag it in its own repo (`gh release create vX.Y.Z …`).

## 4. Naming gotcha (learned the hard way)

Keep the local folder, the GitHub repo, and every URL in docs/installers on
the **same canonical name** from day one. GitHub redirects renamed repos, so
mismatches keep working silently — until they surface in release URLs and
CI paths.
