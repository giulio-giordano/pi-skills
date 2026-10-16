# pi-skills

Personal collection of [Pi](https://pi.dev) skills, packaged so it can be installed
from git (or npm) with `pi install`.

## Skills

### `dev-workflow`

House development workflow, enforced on every coding task in every project:

- **dev log** of the significant actions in the project's `wiki/DEV_LOG.md`;
- a **dedicated branch** proposed at the start of every feature or fix;
- **small conventional commits in English**, always confirmed by the user first,
  each proposal carrying the bulleted list of the modified files;
- **commented code** and strict, complete types;
- **small modules** - extract a file once it grows past ~300-400 lines;
- **tests always run**, written when missing;
- **errors shown before fixing**: stop, show the literal error, then diagnose
  together (your fix and mine, compared) before applying anything.

The language-specific rules live in separate files that are read **only when the
task needs them**, so a Python task never pays for the Rust rules:

| Work on | Read |
|---|---|
| Python, Django | `skills/dev-workflow/references/python.md` |
| TypeScript, JavaScript, Node, React | `skills/dev-workflow/references/typescript.md` |
| Vue 3, SFC, composables, Pinia | `skills/dev-workflow/references/vue.md` |
| Rust, Cargo, clippy, Tauri backend | `skills/dev-workflow/references/rust.md` |

## Install

```bash
pi install git:github.com/<owner>/pi-skills          # latest
pi install git:github.com/<owner>/pi-skills@v1       # pinned to a tag
pi install ./pi-skills                               # local checkout
```

Then run `/reload` in a running session. Force the skill with
`/skill:dev-workflow`.

> Installing from git **and** keeping a local checkout registered will register
> the skill twice (Pi keeps the first one and warns). Use one source at a time.

## Layout

```text
pi-skills/
├── package.json                    # Pi package manifest (pi.skills)
├── skills/
│   └── dev-workflow/
│       ├── SKILL.md                # always read when the skill activates
│       └── references/             # read on demand, one per language
│           ├── python.md
│           ├── typescript.md
│           ├── vue.md
│           └── rust.md
└── wiki/
    └── DEV_LOG.md                  # dev log for this repo
```

Token cost (measured with `o200k_base`, ±10% on other tokenizers):

| Item | Tokens |
|---|---|
| `description` (resident in the system prompt of every session) | 86 |
| `SKILL.md` body (read on activation) | 2 247 |
| `references/python.md` | 481 |
| `references/rust.md` | 587 |
| `references/typescript.md` | 604 |
| `references/vue.md` | 657 |

A typical activation costs 2 728 (Python) to 2 904 (Vue) tokens, against 4 097 for
a single-file version of the same content.

## Editing

- Entry point: `skills/dev-workflow/SKILL.md`. Keep it language-neutral; put
  language rules in `references/`.
- **Frontmatter gotcha**: the `description` is a plain YAML scalar, so it must
  not contain `": "` (a colon followed by a space) - that would turn it into a
  nested mapping and the skill would silently fail to load. Use ` - ` instead.
- `name` must match the skill directory name.
- Run `/reload` after editing to load the change in the current session.
