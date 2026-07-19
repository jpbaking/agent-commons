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
- Write `AGENT-INSTALL.md` first (survey → install → merge bridges →
  gitignore → validate → report); make the script installers implement the
  same layout. Copy an existing repo's installer as the starting point —
  per-file `fetch` for small toolkits (dox, lazyway), tarball staging for
  bundled ones (agentic-tests, playwright-fieldkit).
- README install section: the agent prompt first, scripts second.

## 2. Validate

Run the playbook's validation checklist (frontmatter, byte-identical adapter
copies, collision audit, smoke tests in each harness). Automate what you can —
goal-ledger's `tests/` (installer + validator unit tests) and
playwright-fieldkit's self-test are the reference examples. If the repo has
CI that runs shellcheck, keep the installers clean.

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
