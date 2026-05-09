# skill_init: Initialize CPA-compliant development

## Trigger

Use this prompt when a human or AI agent wants to start CPA-compliant iterative
development on a new or existing project using `cpa-dev-skill`.

---

## Preconditions

- `cpa-dev-skill` is available locally or cloned.
- The target project directory exists.
- The target language / toolchain is known.

---

## Steps

### Step 1 — Copy and fill `templates/AGENTS.md`

Copy `templates/AGENTS.md` from this repository into the root of the target
project as `AGENTS.md`. Replace every `{{PLACEHOLDER}}` with project-specific
values:

| Placeholder | Description |
|-------------|-------------|
| `{{PROJECT_NAME}}` | Project name |
| `{{PROJECT_ROLE}}` | AI agent role description |
| `{{HOMEPAGE_URL}}` | Project homepage or package registry URL |
| `{{REPOSITORY_URL}}` | VCS repository URL |
| `{{TARGET_LANGUAGE}}` | Primary programming language |
| `{{WORKSPACE_PATH}}` | Absolute local path to the project workspace |
| `{{BUILD_COMMAND}}` | Build command for the project |
| `{{TEST_COMMAND}}` | Test command for the project |
| `{{PROJECT_SCALE}}` | Project scale: `small` / `medium` / `large`. See `docs/cpa-project-profile.md`. |
| `{{PROJECT_CATEGORY}}` | Project category: `system` / `software` / `other`. See `docs/cpa-project-profile.md`. |
| `{{TOOL_INVOCATION_POLICY}}` | Tool, shell, MCP, sandbox, and approval rules for the target agent environment |
| `{{CODEX_TOOL_NOTES}}` | Optional Codex / agent-style tool notes |
| `{{CLAUDE_TOOL_NOTES}}` | Optional Claude Code tool notes |
| `{{COPILOT_TOOL_NOTES}}` | Optional GitHub Copilot tool notes |

Then fill the **CPA Convergence** section from the matching adapter:
`skills/cpa-dev-skill/adapters/<language>/AGENTS.md`

If the project also uses Claude Code or GitHub Copilot, copy the corresponding
tool-specific template:

```
cp templates/CLAUDE.md <project>/CLAUDE.md
mkdir -p <project>/.github
cp templates/.github/copilot-instructions.md <project>/.github/copilot-instructions.md
```

### Step 2 — Copy shared CPA assets

Copy `docs/` and `prompts/` into the target project so that
repository-relative references in `AGENTS.md`, `CLAUDE.md`, and Copilot
instructions resolve locally:

```
cp -R docs     <project>/docs
cp -R prompts  <project>/prompts
```

Do not copy `adapters/` into the target project by default. The selected
adapter is merged into the root `AGENTS.md` during initialization.

### Step 3 — Initialize the backlog

Create `backlog/` and copy only the root backlog templates into the target
project:

```
mkdir -p <project>/backlog
cp templates/backlog/BACKLOG.md    <project>/backlog/BACKLOG.md
cp templates/backlog/STATUS.md     <project>/backlog/STATUS.md
```

PB folders are created by the PB Open phase. CR files are created by the CR
Open phase.

### Step 4 — Confirm shared CPA vocabulary

Open `docs/cpa-core-vocabulary.md`. Confirm that both the human and AI agent
share these four terms before starting development:

| Term | One-line definition |
|------|---------------------|
| **Core** | Discovered domain model: pure types, invariants, value objects. No IO, no frameworks. |
| **Projection** | Use-case view projected from Core. Not invented — selected. |
| **Retro-dependency** | Dependencies flow outward-to-inward only. Never inward-to-outward. |
| **Erosion** | Unintended leakage of outer-layer concerns into inner layers. |

### Step 5 — Select or create an adapter

Check `skills/cpa-dev-skill/adapters/` for a folder matching the target
language.

- **Adapter exists** → Read `skills/cpa-dev-skill/adapters/<language>/README.md`
  and `skills/cpa-dev-skill/adapters/<language>/AGENTS.md`, then merge the CPA
  Convergence guidance into `AGENTS.md`.
- **No adapter yet** → Draft the CPA Convergence section directly in
  `AGENTS.md` using `skills/cpa-dev-skill/adapters/README.md` as the authoring
  guide. Create a reusable adapter folder only if the user explicitly asks.

### Step 6 — Open the AI agent session

Load `AGENTS.md` into the AI agent session. Then instruct the agent:

```
Read prompts/pb_iteration.md and prepare to open the first PB.
```

---

## Exit Conditions

- [ ] `AGENTS.md` exists in the target project with all placeholders filled,
      including `PROJECT_SCALE`, `PROJECT_CATEGORY`, and `TOOL_INVOCATION_POLICY`.
- [ ] `docs/` and `prompts/` exist in the target project.
- [ ] `adapters/` does not exist in the target project unless the user
      explicitly requested a project-local reusable adapter.
- [ ] `backlog/BACKLOG.md` and `backlog/STATUS.md` exist.
- [ ] No PB folder or CR file was created during initialization unless the user
      explicitly instructed the PB Open or CR Open phase.
- [ ] The AI agent has loaded `AGENTS.md` and read the PB iteration prompt.

---

## References

- [AGENTS.md template](../templates/AGENTS.md)
- [CPA Core Vocabulary](../docs/cpa-core-vocabulary.md)
- [CPA Project Profile](../docs/cpa-project-profile.md)
- [Adapters](../adapters/README.md)
- [Backlog templates](../templates/backlog/)
- [PB Iteration prompt](pb_iteration.md)
