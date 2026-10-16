# Dev log - pi-skills

## 2026-10-16

- 10:10 - Created the `dev-workflow` skill: dev log, feature/fix branch, confirmed
  conventional commits, mandatory comments, always-run tests, error-first protocol.
- 10:20 - Added the language best practices for Python, TypeScript, JavaScript, Vue 3
  and Rust after the request to cover the frontend too.
- 10:33 - Split the skill into a language-neutral `SKILL.md` plus
  `references/{python,typescript,vue,rust}.md`: description 155 -> 86 tokens,
  activation 4 097 -> 2 728 (Python) / 2 904 (Vue) tokens.
- 10:37 - Moved the skill into this repository as a Pi package
  (`package.json` + `skills/dev-workflow`), so it can be installed from git.
- 10:38 - Added the MIT license and registered the public repository
  `github.com/giulio-giordano/pi-skills`.
- 10:41 - Set the repository commit identity to
  `Giulio Giordano <giuliogiordano95@gmail.com>` (local config only, the global
  one stays on the work address).
- 10:44 - Tagged `v0.1.0` for the first installable version.
- 10:44 - Registered this checkout as the user-level Pi package
  (`pi install ~/workspace/pi-skills`), so it is the single source of truth for
  every project and edits need no copying; removed the
  `~/.agents/skills/dev-workflow` symlink to avoid a double registration.
