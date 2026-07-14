# PB Open Phase

## Steps

1. Refer to `backlog/BACKLOG.md` and decide which PB to start.
2. ⚠️ **Confirmation required**: Summarize the BACKLOG.md content and the chosen
   PB, then ask: "Shall we start PB-XXXX?" — wait for approval before proceeding.
3. Update `backlog/STATUS.md` and set the PB status to **In Progress**.
4. Create the PB folder using the naming convention below.
5. Create `PB-STATUS.md` inside the PB folder. Decompose the PB requirements
   from BACKLOG.md into CRs and list them.
   Fill in the **Work Folder** table using the following rules:
   - Use `PROJECT_ROOT` (defined as `{{WORKSPACE_PATH}}` in `AGENTS.md` under `# Project Folder`) as the base.
   - Write paths in `PROJECT_ROOT/...` notation, or use relative paths from the project root.
   - Always include at minimum:
     | Target | Path |
     |--------|------|
     | Deliverable (work) | `PROJECT_ROOT/work/<sub-folder>` |
     | Backlog | `PROJECT_ROOT/backlog/PB-XXXX` |
   - Add additional rows for any other folders relevant to this PB
     (e.g. `mcp\`, `docs\`, a specific library sub-folder under `work\`).

## PB Folder Naming Convention

PB folders include a short title after the ID:

```
backlog/PB-XXXX_<title>/
```

- Format: `PB-XXXX_<title>` (4-digit zero-padded ID + underscore + short title)
- Example: `backlog/PB-0005_CPADevSkillConstruction/`
- The title should be concise (no spaces; use CamelCase or underscores).
- CR folders inside the PB folder follow the same convention: `CR-XXXX_<title>/`.
  See `prompts/cr_open.md` for the CR folder structure.

---

## PB-STATUS.md Format
```markdown
# PB-XXXX STATUS
## Work Folder
| Target | Path |
|--------|------|
| Deliverable (work) | `PROJECT_ROOT/work/<sub-folder>` |
| Backlog | `PROJECT_ROOT/backlog/PB-XXXX` |
## Overview
| CR ID | Title | Status | Notes |
|-------|-------|--------|-------|
| CR-01 | ...   | TODO   |       |
```

---

## CPA Perspective: Layer Impact Overview at PB Open

When opening a PB, record the following CPA perspective in the Overview section
of `PB-STATUS.md`.

### Project Profile Check

Confirm the project profile declared in `AGENTS.md` before decomposing into CRs.
See `docs/cpa-project-profile.md` for definitions and selection guidance.

**Scale** — confirm which applies and that it matches `AGENTS.md` `PROJECT_SCALE`:

| Scale | When to choose |
|-------|---------------|
| `small` | No non-functional concerns in ApplicationBase (no state machine, no logging, no async pipeline) |
| `medium` | ApplicationBase requires at least one of: state machine, structured logging, streaming / async pipeline |
| `large` | Multiple independently composable packages; Retro-dependency must be enforced at build-system level |

**Category** — confirm which applies and that it matches `AGENTS.md` `PROJECT_CATEGORY`:

| Category | When to choose |
|----------|---------------|
| `system` | Primary deliverable is environment configuration, IaC, or CI/CD pipeline |
| `software` | Primary deliverable is source code (library, application, or service) |
| `other` | Primary deliverable is documents, skill files, templates, or a composite of the above |

> If the PB's requirements reveal a mismatch with the current `AGENTS.md` profile,
> update `AGENTS.md` **before** creating any CRs.

### What to Record in PB-STATUS.md Overview

1. **Primary affected CPA layers**
   - Which of CoreModel / ProjectedContext / ApplicationBase / Interface/Boot are
     the main targets for change?
   - For language-specific layer mapping, refer to the CPA Convergence section in
     `AGENTS.md` and `adapters/<language>/README.md`.

2. **Scale and Category confirmation**
   - Confirmed scale: `small` / `medium` / `large`
   - Confirmed category: `system` / `software` / `other`
   - Matches `AGENTS.md` declarations: yes / no (if no, update `AGENTS.md` first)

3. **CoreModel impact**
   - Will new domain types or pure functions be introduced?
   - If yes, place the CoreModel CR first in the CR list.

### CR Decomposition Policy

- One CR = one primary layer change, as a baseline.
- Changes spanning multiple layers should be split by layer
  (CoreModel change → ProjectedContext change → etc.).
- CRs that include CoreModel changes should be executed before other-layer CRs.

---
