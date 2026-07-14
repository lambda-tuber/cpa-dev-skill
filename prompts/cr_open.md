# CR Open Phase

## Steps

1. Refer to the PB folder's `PB-STATUS.md` and decide which CR to start.
2. ⚠️ **Confirmation required**: Show the CR list from `PB-STATUS.md` and ask:
   "Shall we start CR-XXXX?" — wait for approval before proceeding.
3. Update the CR list in `PB-STATUS.md` and set the CR status to **In Progress**.
4. Create the CR folder and its files using the structure and formats below.

---

## Naming Convention

### PB folder

```
backlog/PB-XXXX_<title>/
```

- Format: `PB-XXXX_<title>` (4-digit zero-padded ID + underscore + short title)
- Example: `backlog/PB-0005_CPADevSkillConstruction/`

### CR folder

```
backlog/PB-XXXX_<title>/CR-XXXX_<title>/
```

- Format: `CR-XXXX_<title>` (4-digit zero-padded ID + underscore + short title)
- Example: `backlog/PB-0005_CPADevSkillConstruction/CR-0016_PythonCPAAdapter/`

---

## CR Folder Structure (5-file standard)

Each CR is a **folder** containing the following files:

```
CR-XXXX_<title>/
  00_overview.md      ← CR-wide overview (always required)
  01_requirements.md  ← Requirements phase
  02_design.md        ← Design phase
  03_dev_loop.md      ← Development loop (coding / build / test)
  04_close.md         ← Completion checklist and work log (always required)
```

### Omission rules

- `00_overview.md` and `04_close.md` are **always created**.
- `02_design.md` and `03_dev_loop.md` may be omitted for documentation-only CRs
  (e.g. a CR that only updates prompt files or templates with no coding phase).

---

## `00_overview.md` Format

```markdown
# CR-XXXX: <Title>

## Status: In Progress

---

## Background

<!-- Why was this CR created? Reference the parent PB and any predecessor CRs. -->

---

## Purpose

<!-- One or two sentences describing the goal of this CR. -->

---

## Deliverables

| Deliverable | Path |
|-------------|------|
| (artifact 1) | `path/to/artifact` |

---

## References

| Resource | Path / URL |
|----------|------------|
| (reference 1) | |

---

## Completion Checklist

- [ ] (criterion 1)
- [ ] (criterion 2)
```

---

## `01_requirements.md` Format

```markdown
# CR-XXXX Requirements

## R-01: <Requirement title>

<!-- Describe the requirement in detail. -->

---

## R-02: <Requirement title>

<!-- Describe the requirement in detail. -->
```

---

## `02_design.md` Format

```markdown
# CR-XXXX Design

## CPA Layer Declaration

\`\`\`
Target layers:
  CoreModel        : (types / functions to add or change)
  ProjectedContext : (use cases / IO operations to add or change)
  ApplicationBase  : (configuration / service wiring to add or change)
  Interface/Boot   : (entry points / dependency injection to add or change)
\`\`\`

## Design Details

<!-- Record design decisions: directory layout, module structure, type definitions. -->

## Retro-dependency Check

- [ ] Dependencies flow outward → inward only
- [ ] No inner layer imports from an outer layer

## Erosion Check

- [ ] Semantic: No outer-layer concerns leak into ProjectedContext
- [ ] Ontological: New domain concepts are in CoreModel
```

---

## `03_dev_loop.md` Format

```markdown
# CR-XXXX Development Loop

## Iteration log

### Iteration 1

#### [ ] Coding

<!-- Record what was implemented. -->

#### [ ] Build (`{{BUILD_COMMAND}}`)

<!-- Record build result. -->

#### [ ] Test (`{{TEST_COMMAND}}`)

<!-- Record test result. -->
```

---

## `04_close.md` Format

```markdown
# CR-XXXX Close

## Completion Checklist

- [ ] All requirements (R-XX) delivered
- [ ] Build passes
- [ ] Tests pass

## Work Log

<!-- Summary of what was done, decisions made, and issues encountered. -->

## Retrospective

<!-- What went well, what to improve, carry-over items. -->
```

---
