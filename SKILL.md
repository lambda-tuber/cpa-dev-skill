# SKILL: Start CPA-compliant development for \<project\>

---

## How to Use This Skill

> **This section is addressed to the AI agent reading this file.**

You have received this `SKILL.md` as a file attachment in a chat message.
Your role is to initialize a CPA-compliant development environment in the
target project folder specified by the user.

**What you must do:**

1. Read **Before You Begin** below and ask the user the minimum required questions
   (at most 3). Do not ask for information you can infer or default.
2. Use your file tools (e.g. `pty-mcp-server` MCP tools) to copy and generate
   files directly inside the project folder. Do not just give instructions —
   actually create the files.
3. Follow **Steps 1–6** in order.
4. When done, verify the **Exit Conditions** using your file tools and report
   the result to the user.

**Assumed layout:**

```
<project>/               ← project folder specified by the user
├── skills/
│   └── cpa-dev-skill/   ← this repository, already cloned here
│       ├── SKILL.md     ← this file
│       ├── templates/
│       ├── prompts/
│       ├── adapters/
│       └── docs/
├── AGENTS.md            ← you will create this
├── docs/                ← you will create this
├── prompts/             ← you will create this
├── backlog/             ← you will create this
└── work/                ← you will create this (application / library development)
```

---

## Trigger

Use this skill when starting CPA-compliant iterative development on a new or
existing project. This skill initializes the repository structure needed for
human-AI collaborative development based on Core Projection Architecture (CPA).

---

## Preconditions

- The target project folder exists at the path specified by the user
  (e.g. `c:\work\prj_sample`).
- `cpa-dev-skill` is already cloned at `<project>/skills/cpa-dev-skill/`.
  All of `docs/`, `prompts/`, `templates/`, `adapters/` are present inside
  that clone.
- Claude Desktop is running with `pty-mcp-server` MCP configured and active.
- The language / toolchain for the project is known (or will be confirmed in
  **Before You Begin**).

---

## Before You Begin

Before starting the steps, confirm the following with the user.
Ask only what you cannot infer. Combine all questions into a single message.

### Confirm (ask if not already stated)

1. **Project folder path** — The absolute path to the project root
   (e.g. `c:\work\prj_sample`). If stated in the user's message, use it directly.

2. **Project name** — Defaults to the folder name (e.g. `prj_sample`).
   Propose the default and ask the user to confirm or correct it.

3. **Language / toolchain** — The primary programming language
   (e.g. `Haskell`, `Rust`, `Python`). If stated, use it directly.

### Infer automatically (do not ask)

Use the confirmed values above to fill these placeholders without asking:

| Placeholder | How to infer |
|-------------|-------------|
| `{{WORKSPACE_PATH}}` | Project folder path (from question 1) |
| `{{PROJECT_NAME}}` | Confirmed project name (from question 2) |
| `{{PROJECT_ROLE}}` | `"the developer of {{PROJECT_NAME}}"` |
| `{{TARGET_LANGUAGE}}` | Confirmed language (from question 3) |
| `{{BUILD_COMMAND}}` / `{{TEST_COMMAND}}` | Read from `adapters/<language>/README.md` |
| `{{PROJECT_SCALE}}` | Default to `small` — note that the user can change it later |
| `{{PROJECT_CATEGORY}}` | Default to `software` — note that the user can change it later |
| `{{TOOL_INVOCATION_POLICY}}` | Use the default text below |
| `{{CODEX_TOOL_NOTES}}` | Leave empty or omit |
| `{{CLAUDE_TOOL_NOTES}}` | Leave empty or omit |
| `{{COPILOT_TOOL_NOTES}}` | Leave empty or omit |

**Default `{{TOOL_INVOCATION_POLICY}}` text for Claude Desktop + pty-mcp-server:**

```
File system operations are performed via pty-mcp-server MCP tools
(pms-read-file, pms-write-file, pms-patch-file, pms-list-dir, pms-make-dir).
Shell commands are run via agent-proc-run / agent-proc-read / agent-proc-write.
Always confirm with the user before running destructive or irreversible operations.
Do not proceed autonomously to the next iteration phase without explicit user instruction.
```

**For `{{HOMEPAGE_URL}}` and `{{REPOSITORY_URL}}`:***
Leave as `(TBD)` and add a comment: `# Fill in after the repository is created`.

---

## Steps

### Step 1 — Fill templates/AGENTS.md

Copy `templates/AGENTS.md` from the skill folder into the project root as `AGENTS.md`,
then replace every `{{PLACEHOLDER}}` with the values confirmed in **Before You Begin**.

```
# Windows (PowerShell)
Copy-Item <project>\skills\cpa-dev-skill\templates\AGENTS.md <project>\AGENTS.md

# Unix / Git Bash
cp <project>/skills/cpa-dev-skill/templates/AGENTS.md <project>/AGENTS.md
```

Open the copied file and fill placeholders using the table in **Before You Begin**.

Fill the **CPA Convergence** section using the matching adapter:
`<project>/skills/cpa-dev-skill/adapters/<language>/AGENTS.md`

If the project also uses Claude Code or GitHub Copilot, copy the corresponding
tool-specific templates as well:

```
# PowerShell
Copy-Item <project>\skills\cpa-dev-skill\templates\CLAUDE.md <project>\CLAUDE.md
New-Item -ItemType Directory -Force <project>\.github
Copy-Item <project>\skills\cpa-dev-skill\templates\.github\copilot-instructions.md `
          <project>\.github\copilot-instructions.md

# Unix
cp <project>/skills/cpa-dev-skill/templates/CLAUDE.md <project>/CLAUDE.md
mkdir -p <project>/.github
cp <project>/skills/cpa-dev-skill/templates/.github/copilot-instructions.md \
   <project>/.github/copilot-instructions.md
```

### Step 2 — Copy shared CPA assets

Copy the shared documentation and prompt set into the project root.
The generated instruction files use repository-relative paths such as
`docs/...` and `prompts/...`.

Do not copy the `adapters/` directory into the target project by default.
Adapter content is used as source material while filling the CPA Convergence
section in `AGENTS.md`; the selected adapter guidance is merged into
`AGENTS.md` rather than kept as a separate project-local adapter folder.

```
# PowerShell
Copy-Item -Recurse <project>\skills\cpa-dev-skill\docs     <project>\docs
Copy-Item -Recurse <project>\skills\cpa-dev-skill\prompts  <project>\prompts

# Unix
cp -R <project>/skills/cpa-dev-skill/docs     <project>/docs
cp -R <project>/skills/cpa-dev-skill/prompts  <project>/prompts
```

### Step 3 — Initialize the backlog and work folder

Create the `work` folder (where application / library source code will be placed)
and copy only the root backlog templates into the project:

```
# PowerShell
New-Item -ItemType Directory -Force <project>\work
New-Item -ItemType Directory -Force <project>\backlog
Copy-Item <project>\skills\cpa-dev-skill\templates\backlog\BACKLOG.md   <project>\backlog\BACKLOG.md
Copy-Item <project>\skills\cpa-dev-skill\templates\backlog\STATUS.md    <project>\backlog\STATUS.md

# Unix
mkdir -p <project>/work <project>/backlog
cp <project>/skills/cpa-dev-skill/templates/backlog/BACKLOG.md   <project>/backlog/BACKLOG.md
cp <project>/skills/cpa-dev-skill/templates/backlog/STATUS.md    <project>/backlog/STATUS.md
```

> **Note:** The `work/` folder is where the actual application, middleware, or
> library source trees will be created during development. It is referenced in
> `AGENTS.md` under `# Project Folder`. Creating it now ensures the folder
> structure matches what AGENTS.md declares.
>
> PB folders are created later by the PB Open phase. CR files are created later
> by the CR Open phase. Initial backlog setup intentionally contains only
> `backlog/BACKLOG.md` and `backlog/STATUS.md`.

### Step 4 — Reference the CPA Core Vocabulary

Read `docs/cpa-core-vocabulary.md` and confirm that you (the AI agent) and the
user share the following minimum vocabulary before proceeding:

- **Core** — The discovered domain model: pure types, invariants, and value objects.
- **Projection** — A use-case-level view projected from Core; not an invention, but a selection.
- **Retro-dependency** — Dependencies must flow outward-to-inward only (Interface → ApplicationBase → ProjectedContext → Core).
- **Erosion** — Unintended leakage of outer-layer concerns into inner layers.

### Step 5 — Select or create an adapter

Check `<project>/skills/cpa-dev-skill/adapters/` for a folder matching the
target language.

- **If an adapter exists**: Read
  `<project>/skills/cpa-dev-skill/adapters/<language>/README.md` and
  `<project>/skills/cpa-dev-skill/adapters/<language>/AGENTS.md`, then merge
  the relevant CPA Convergence guidance into `AGENTS.md` (replacing the
  placeholder block).
- **If no adapter exists**: Draft the CPA Convergence section directly in
  `AGENTS.md` using `<project>/skills/cpa-dev-skill/adapters/README.md` as the
  authoring guide. Create a reusable adapter folder only when the user
  explicitly asks for one.

### Step 6 — Start the first PB iteration

Instruct yourself to read `prompts/pb_iteration.md` and ask the user whether to
open the first PB. When the user explicitly starts the PB Open phase, create the
PB folder and `PB-STATUS.md` from the backlog templates. When the user
explicitly starts the CR Open phase, create the corresponding `CR-XXXX.md`.

```
Read <project>/prompts/pb_iteration.md and prepare to open the first PB.
```

---

## Exit Conditions

After completing all steps, verify each item below using your file tools.
Report the full verification result to the user.

- [ ] `<project>/AGENTS.md` exists and contains no unfilled `{{PLACEHOLDER}}` values
      (except `{{HOMEPAGE_URL}}` and `{{REPOSITORY_URL}}` if intentionally left as `(TBD)`).
- [ ] `<project>/docs/` and `<project>/prompts/` exist.
- [ ] `<project>/adapters/` does not exist unless the user explicitly requested
      a project-local reusable adapter.
- [ ] `<project>/work/` exists.
- [ ] `<project>/backlog/BACKLOG.md` and `<project>/backlog/STATUS.md` exist.
- [ ] No PB folder or CR file was created during initialization unless the user
      explicitly instructed the PB Open or CR Open phase.
- [ ] You have loaded `<project>/AGENTS.md` into your context and read
      `<project>/prompts/pb_iteration.md`.

---

## References

- [CPA Core Vocabulary](docs/cpa-core-vocabulary.md)
- [CPA Project Profile](docs/cpa-project-profile.md)
- [AGENTS.md template](templates/AGENTS.md)
- [Adapters](adapters/README.md)
- [Backlog templates](templates/backlog/)
- [Core Projection Architecture](https://github.com/lambda-tuber/core-projection-architecture)
