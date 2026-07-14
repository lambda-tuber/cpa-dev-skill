# Requirements Phase

## Overview

The Requirements phase defines **what to build or change** in this CR.
From a CPA perspective, identifying which layers are affected here forms the
foundation for all subsequent design and implementation work.

## Steps

1. Read the PB requirements in `backlog/BACKLOG.md` and the CR overview in
   `PB-STATUS.md`.
2. Write the requirement details in `CR-XXXX_<title>/01_requirements.md`.
3. Complete the CPA perspective checks below and record the results in
   `CR-XXXX_<title>/01_requirements.md`.
4. Update the CR status in `CR-XXXX_<title>/00_overview.md`.
5. ⚠️ **Confirmation required**: Ask "Requirements are complete. May we proceed to
   the Design phase?" and wait for approval before continuing.

---

## CPA Perspective Check

### 1. Identify Affected Layers

Identify which CPA layers this CR's changes affect.

| CPA Layer | Affected? | Summary of changes |
|-----------|-----------|-------------------|
| CoreModel | | |
| ProjectedContext | | |
| ApplicationBase | | |
| Interface/Boot | | |

> For language-specific layer mapping, refer to the CPA Convergence section in
> `AGENTS.md` and `adapters/<language>/README.md`.

### 2. CoreModel Impact Check

If CoreModel is affected, confirm the following:

- [ ] Will new domain model types / schemas / value objects be introduced?
  - If yes: state the name and purpose explicitly in the Requirements section
    (this is the Discovery Principle in practice).
- [ ] Can this be achieved within the existing CoreModel, or does CoreModel need
      to be extended?
- [ ] Can the logic be implemented as side-effect-free functions
      (no external I/O, no framework calls)?

### 3. Scope Check

- [ ] Does this CR complete on its own, or does it depend on other CRs?
- [ ] Is this change contained within a single library, or does it span multiple
      packages?
- [ ] Are new files, modules, or packages required?

### 4. Project Profile Consistency Check

Confirm that this CR's deliverables are consistent with the project profile
declared in `AGENTS.md`. See `docs/cpa-project-profile.md` for definitions.

- [ ] The CR's scale assumption matches `AGENTS.md` `PROJECT_SCALE`
      (`small` / `medium` / `large`).
- [ ] The CR's deliverable type is consistent with `AGENTS.md` `PROJECT_CATEGORY`
      (`system` / `software` / `other`).
- [ ] If a mismatch is found, escalate to the PB level and update `AGENTS.md`
      before proceeding.

### 5. Skeleton Selection (for greenfield development)

If building a new library or service from scratch, confirm:

- [ ] Will a CPA-compliant skeleton be used as the starting point?
  - See `adapters/<language>/README.md` for language-specific skeleton guidance.
- [ ] Has the appropriate scale been chosen, consistent with `PROJECT_SCALE` in
      `AGENTS.md`? See `docs/cpa-project-profile.md` for the scale selection guide.

---
