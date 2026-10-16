---
name: dev-workflow
description: House dev workflow - dev log in the project wiki, a dedicated branch for each feature or fix, small conventional commits confirmed by the user with the list of changed files, commented code with strict types, small modules, tests always run, errors shown before fixing. Use when writing, refactoring, testing, committing or debugging Python, TypeScript, JavaScript, Vue or Rust code, and when starting a feature or a fix.
---

# Development workflow

Apply these rules on **every** coding task, in **every** project (this skill is
installed at user level). They are not optional and they override the default
"just write the code" behaviour. Reply to the user in **Italian** (their working
language), even though these instructions are in English.

## 0. Load the language reference first

The language-specific rules live in separate files. **Before writing any code**,
read the one reference that matches the work, relative to this skill directory:

| Work on | Read |
|---|---|
| Python, Django | `references/python.md` |
| TypeScript, JavaScript, Node, React | `references/typescript.md` |
| Vue 3, SFC, composables, Pinia | `references/vue.md` |
| Rust, Cargo, clippy, Tauri backend | `references/rust.md` |

Read **only** the reference you need (both languages when the task crosses a
frontend/backend boundary). Say in your report which reference you used.

Universal type rules that hold in every language, before reading anything:

- Every function, parameter, return value and domain structure is **typed**; no
  escape hatch used to dodge the type system (no `any`, no implicit `Any`, no
  `unwrap()`/cast just to silence a complaint).
- Types are **narrow** (enums/discriminated unions/newtypes over strings and
  booleans), and untrusted input is validated at the boundary.
- Doc comments on every module, class and public function (§3 below).
- Data is treated as immutable unless mutation is the point.

## 1. Log the significant actions

Keep a short running log of what matters, so the work is auditable later.

- File: **`wiki/DEV_LOG.md`** inside the project (the `wiki/` folder is the
  preferred home for project documentation).
  - If the project has no `wiki/` folder, use `docs/DEV_LOG.md`; if that does not
    exist either, **create `wiki/DEV_LOG.md`** (and its folder).
  - If the project documents a different log location (`AGENTS.md`, `README.md`,
    `.pi/`), that wins.
- Create the file on the first logged action; if it exists, **append**, never
  rewrite. Keep entries grouped under a `## YYYY-MM-DD` heading:

  ```markdown
  ## 2026-05-14
  - 14:32 - Fixed retry loop in `deverse/services.py:88` (request id was regenerated on retry).
  - 14:51 - Added `deverse_tests/tests/test_retry.py` covering the idempotency rule.
  ```

- Log: created/deleted files, refactors, schema or migration changes, config or
  dependency changes, bug fixes with their root cause, test additions, decisions
  taken and rejected alternatives.
- Do **not** log: file reads, searches, formatting, typos, intermediate failures
  of the same command.
- Keep it dense - one line each, past tense, no narrative. Mention in your final
  summary that the log was updated; commit it with the change it documents, but
  only after the usual confirmation.

## 2. Branch first, then small confirmed commits

### 2.1 Propose a branch at the start of a feature or a fix

As soon as the user asks for a **feature**, a **fix**, or any non-trivial change,
propose a dedicated branch **before** editing files, and wait for confirmation.

- Naming: `<type>/<short-slug>`, kebab-case, English, <= 50 characters, where
  `<type>` matches the work: `feat/`, `fix/`, `refactor/`, `chore/`, `docs/`,
  `test/`, `perf/`. Example: `fix/deverse-retry-request-id`.
- With the name, always state the **base branch** and why (integration branch vs
  release branch), e.g. `git switch -c fix/x develop`.
- If the project already has a branch convention (check `git branch -a`), follow
  it instead of inventing one.
- Trivial work (typo, comment, one-line config) does not need a branch - say so
  and stay on the current one.
- Never create, switch, delete, merge or push a branch without confirmation.

### 2.2 Commits

- Commit **only when the change is one coherent unit** (a fix, a feature, a
  refactor, a test addition). Never bundle unrelated work.
- **Best practices**, always: English, imperative mood, conventional-commit
  style `<type>(<scope>): <summary>` (`<type>` = feat|fix|refactor|test|docs|
  chore|perf), subject <= 72 characters, no trailing period, blank line, then a
  body explaining **why** and what was rejected when that is not obvious, plus
  `Refs: #123` / `Closes: #123` when an issue exists. No "WIP", no "fix stuff",
  no update-everything commits, no mixing formatting with behaviour. Example:

  ```
  fix(deverse): reuse request id when retrying a command

  The retry path generated a fresh requestId, so a retransmitted command was
  rejected as a duplicate. Store the id with the pending command and reuse it.

  Closes: #42
  ```

- **NEVER run `git commit` (or `git add`) before an explicit confirmation from
  the user.** Propose, then wait. One confirmation covers one commit.
- The proposal must contain, always:
  1. the proposed commit message;
  2. a **bulleted list of the modified files**, each with its status and a few
     words on what changed - from `git status --short` and `git diff --stat`,
     e.g. `M` `deverse/services.py` - reuse the stored request id on retry;
  3. one line on why this is a coherent unit (or why it is being split).
- Stage **explicit paths**, never `git add -A` / `git add .` blindly, and never
  stage files you did not touch.
- Never `git commit --amend`, `git rebase`, force-push, or `git push` without a
  separate explicit request.
- If the working tree contains unrelated pending changes, say so and propose
  committing only your own files.

## 3. Comment and document the code

- Every module, class, public function, and every public surface (Vue props and
  events, Rust public items, exported TS types) gets a doc comment: **what it
  promises**, the contract, the error cases.
- Comments explain **why** - the decision, the invariant, the workaround - with a
  link to the protocol/domain doc when relevant (e.g. "the same `requestId` must
  be reused on retry, see wiki/DEVERSE.md"). The exact doc-comment syntax per
  language is in the matching reference file.
- Keep comments in sync with the code - a stale comment is a bug, delete it.
- Never leave commented-out code, `TODO` without an owner/reason, or debug prints
  (`print`, `console.log`, `println!`) in a committed path.
- A file should be understandable by someone reading only that file.

## 4. Use the project's toolchain, never an invented one

Read the project configuration and use **its** tools: the linter, formatter and
type-checker it already declares (details per language in the references). If the
expected tool is missing, say so and propose adding it **asking first** (it is a
dependency change). Skipping a check the project genuinely does not support is
allowed, but must be stated explicitly in your report.

## 5. Simple, readable, modular

- **Maximum effort, minimum machinery**: solve the problem directly. No
  premature abstraction, no speculative generality, no unnecessary layers,
  patterns, or dependencies.
- Prefer clear names and straight-line code over clever one-liners.
- **Extract a module when a file grows past roughly 300-400 lines (200-300 for a
  Vue SFC) or starts mixing concerns.** One responsibility per file; when a
  single class accumulates unrelated work, split it.
- Follow the project's existing layout instead of imposing a new one; the
  per-language layout conventions are in the references.

## 6. Always test

- Run the relevant tests **after every change**, before reporting anything as
  done - the project's own command for that language (see the reference).
- If a test for the new or changed behaviour does not exist, **write it**:
  success path, edge cases, and the bug you just fixed (a regression test).
- Never claim something works because it "should" - show the command and its
  result. If you cannot run tests, say so explicitly and explain why.

## 7. Error protocol: stop, show, discuss

When something fails (test, build, migration, runtime, lint, type-check, clippy):

1. **Stop.** Do not attempt a silent fix or a blind retry in a loop.
2. **Show the user the error**: the exact failing command and the literal error
   text / traceback / compiler diagnostic (trimmed to the relevant frames). Make
   it visible - do not summarise it into "it failed".
3. **Wait for the user to look at it.**
4. Then produce a **joint diagnosis**: the user proposes their fix, you propose
   yours, and you compare them (root cause, symptoms explained, side effects,
   alternative explanations) before either is applied.
5. Only after agreeing on the cause, apply the fix - and then go back to §6.

Never hide an error, never paper over it with a fallback that masks the real
failure, and never let a red result scroll past unnoticed.

## 8. Report format

Close each work step with, in Italian:

- the proposed branch and base branch (when the work is new);
- which language reference you loaded;
- what was done and why (bullet points);
- tests, type-check and lint commands run, with their outcome;
- files touched;
- the proposed commit (when a coherent unit is ready), with the file list, and a
  request to confirm;
- anything blocked or awaiting the user's decision.
