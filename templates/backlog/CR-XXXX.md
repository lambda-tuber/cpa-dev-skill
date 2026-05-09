# CR-XXXX: (Title)

## Status: TODO

---

## Current State

<!-- Record progress dates and phase transitions here. -->

---

## Background

<!-- Describe why this CR was created. Reference the parent PB and any predecessor CRs. -->

---

## Purpose

<!-- One or two sentences describing the goal of this CR. -->

---

## Requirements

### Scope

<!-- List the artifacts or behaviors this CR must deliver. -->

### Out of Scope

<!-- Explicitly list what this CR does NOT cover. -->

### CPA Layer Check

| CPA Layer | Affected? | Change Summary |
|-----------|-----------|----------------|
| CoreModel | | |
| ProjectedContext | | |
| ApplicationBase | | |
| Interface/Boot | | |

### CoreModel Impact

- [ ] Does this CR introduce new domain types?
- [ ] Can it be realized within the existing CoreModel, or is extension required?
- [ ] Can the logic be implemented as pure functions (no IO leakage)?

### Scope Confirmation

- [ ] Does this CR complete independently, or does it depend on other CRs?
- [ ] Is the change confined to a single library/package, or does it span multiple?
- [ ] Are new files, modules, or packages required?

### CPA Skeleton Decision

- [ ] Start from a CPA-compliant skeleton?
- [ ] Select the appropriate scale configuration (single-library 4-layer / multi-package / fractal-7)?

### Completion Criteria for Requirements Phase

- [ ] (criterion 1)
- [ ] (criterion 2)

---

## Design

<!-- Record design decisions: directory layout, module structure, type definitions, layer declarations. -->

### CPA Layer Declaration

```
Target layers:
  CoreModel        : (types / functions to add or change)
  ProjectedContext : (use cases / IO operations to add or change)
  ApplicationBase  : (configuration / service wiring to add or change)
  Interface/Boot   : (entry points / dependency injection to add or change)
```

### Retro-dependency Check

- [ ] Dependencies flow outward → inward only (Interface/Boot → ApplicationBase → ProjectedContext → CoreModel)
- [ ] No inner layer imports from an outer layer
- [ ] Import directions of new functions/types are correct

### Erosion Check

- [ ] Semantic: No outer-layer concerns leak into the ProjectedContext type stack
- [ ] Ontological: New domain concepts belong to CoreModel; no framework dependencies mixed in

---

## Development Loop

### [ ] Implementation
### [ ] Build
### [ ] Test

---

## Completion Checklist

- [ ] (item 1)
- [ ] (item 2)

---
