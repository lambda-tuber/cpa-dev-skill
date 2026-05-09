# SKILL: Start CPA-compliant development for \<project\>

## Trigger

Use this skill when starting CPA-compliant iterative development on a new or
existing project. This skill initializes the repository structure needed for
human-AI collaborative development based on Core Projection Architecture (CPA).

## Preconditions

- This repository (`cpa-dev-skill`) is available locally or cloned.
- The target project repository exists (or a new directory has been created).
- The language / toolchain for the project is known.
- An appropriate adapter exists in `adapters/<language>/`, or you will create one.

---

## Steps

### Step 1 — Fill templates/AGENTS.md

Copy `templates/AGENTS.md` into the root of the target project as `AGENTS.md`.
Open it and replace every `{{PLACEHOLDER}}` with project-specific values:

| Placeholder | Description |
|-------------|-------------|
| `{{PROJECT_NAME}}` | Name of the project (e.g. `my-library`) |
| `{{PROJECT_ROLE}}` | AI agent role description (e.g. `the developer of my-library`) |
| `{{HOMEPAGE_URL}}` | Project homepage or package registry URL |
| `{{REPOSITORY_URL}}` | GitHub / VCS repository URL |
| `{{TARGET_LANGUAGE}}` | Primary programming language (e.g. `haskell`, `rust`) |
| `{{WORKSPACE_PATH}}` | Absolute local path to the project workspace |
| `{{BUILD_COMMAND}}` | Build command (e.g. `cabal build`, `cargo build`) |
| `{{TEST_COMMAND}}` | Test command (e.g. `cabal test`, `cargo test`) |
| `{{PROJECT_SCALE}}` | Project scale: `small` / `medium` / `large`. See `docs/cpa-project-profile.md`. |
| `{{PROJECT_CATEGORY}}` | Project category: `system` / `software` / `other`. See `docs/cpa-project-profile.md`. |
| `{{TOOL_INVOCATION_POLICY}}` | Tool, shell, MCP, sandbox, and approval rules for the target agent environment. |
| `{{CODEX_TOOL_NOTES}}` | Optional Codex / agent-style tool notes. |
| `{{CLAUDE_TOOL_NOTES}}` | Optional Claude Code tool notes. |
| `{{COPILOT_TOOL_NOTES}}` | Optional GitHub Copilot tool notes. |

Fill the **CPA Convergence** section using the matching adapter:
`adapters/{{TARGET_LANGUAGE}}/AGENTS.md`

If the project also uses Claude Code or GitHub Copilot, copy the corresponding
tool-specific template as well:

```
cp templates/CLAUDE.md <project>/CLAUDE.md
mkdir -p <project>/.github
cp templates/.github/copilot-instructions.md <project>/.github/copilot-instructions.md
```

### Step 2 — Copy shared CPA assets

Copy the shared documentation, prompt set, and adapters into the target project.
The generated instruction files refer to these paths from the project root.

```
cp -R docs     <project>/docs
cp -R prompts  <project>/prompts
cp -R adapters <project>/adapters
```

### Step 3 — Initialize the backlog

Copy the contents of `templates/backlog/` into the target project:

```
cp templates/backlog/BACKLOG.md    <project>/backlog/BACKLOG.md
cp templates/backlog/STATUS.md     <project>/backlog/STATUS.md
```

For the first PB, create a PB folder and copy the per-PB templates:

```
mkdir <project>/backlog/PB-0001
cp templates/backlog/PB-STATUS.md  <project>/backlog/PB-0001/PB-STATUS.md
cp templates/backlog/CR-XXXX.md    <project>/backlog/PB-0001/CR-0001.md
```

### Step 4 — Reference the CPA Core Vocabulary

Open `docs/cpa-core-vocabulary.md` and confirm that the AI agent and human
collaborators share the following minimum vocabulary:

- **Core** — The discovered domain model: pure types, invariants, and value objects.
- **Projection** — A use-case-level view projected from Core; not an invention, but a selection.
- **Retro-dependency** — Dependencies must flow outward-to-inward only (Interface → ApplicationBase → ProjectedContext → Core).
- **Erosion** — Unintended leakage of outer-layer concerns into inner layers.

### Step 5 — Select or create an adapter

Check `adapters/` for a folder matching your target language.

- **If an adapter exists**: Read `adapters/<language>/README.md` and copy the
  CPA Convergence section into your `AGENTS.md`.
- **If no adapter exists**: Create `adapters/<language>/README.md` following the
  structure described in `adapters/README.md`, then fill your `AGENTS.md`
  CPA Convergence section accordingly.

### Step 6 — Start the first PB iteration

Open the AI agent session with `AGENTS.md` loaded.
Instruct the agent to read `prompts/pb_iteration.md` and begin the first PB.

```
Read prompts/pb_iteration.md and open PB-0001.
```

---

## Exit Conditions

This skill is complete when:

- [ ] `AGENTS.md` exists in the target project with all placeholders filled,
      including `PROJECT_SCALE`, `PROJECT_CATEGORY`, and `TOOL_INVOCATION_POLICY`.
- [ ] `docs/`, `prompts/`, and `adapters/` exist in the target project.
- [ ] `backlog/BACKLOG.md` and `backlog/STATUS.md` exist.
- [ ] At least one PB folder exists with `PB-STATUS.md` and one `CR-XXXX.md`.
- [ ] The AI agent session has loaded `AGENTS.md` and started the first PB.

---

## References

- [CPA Core Vocabulary](docs/cpa-core-vocabulary.md)
- [CPA Project Profile](docs/cpa-project-profile.md)
- [AGENTS.md template](templates/AGENTS.md)
- [Adapters](adapters/README.md)
- [Backlog templates](templates/backlog/)
- [Core Projection Architecture](https://github.com/lambda-tuber/core-projection-architecture)
