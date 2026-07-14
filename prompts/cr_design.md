# Design Phase

## Overview

The Design phase defines **how to build or change** the structure identified in
Requirements. From a CPA perspective, explicitly declaring layer ownership,
dependency direction, and Erosion checks is the primary responsibility of this
phase. Record all design decisions in the **Design** section of `CR-XXXX.md`.

## Steps

1. Use the affected layers identified in Requirements as the basis for design.
2. Apply the CPA perspectives below and record the design in
   `CR-XXXX_<title>/02_design.md`.
3. ⚠️ **Confirmation required**: Ask "Design is complete. May we proceed to the
   Coding phase?" and wait for approval before continuing.

---

## Test Design

In the Design phase, define the test strategy alongside the implementation
design, and record it in the **Design** section of `CR-XXXX.md`.

### 1. Test Strategy

- State what will be tested and at what granularity (unit, integration, E2E, etc.).
- If any scope is explicitly excluded from testing, state the reason.

### 2. Test Case Extraction

- Produce a list of test cases corresponding to the changes or additions designed.
- Each test case must specify: **target feature**, **input condition**, and **expected result**.

### 3. Test Perspectives

- Classify cases by perspective: normal path, error path, boundary values, etc.
- If perspectives differ by CPA layer (e.g. CoreModel → pure function tests,
  ProjectedContext → effectful integration tests), note that explicitly.

---

## External Dependency Policy

When using external libraries or packages, apply the following principles:

**✅ Acceptable**
- Libraries providing general-purpose data structures or algorithms.
- Standard or near-standard libraries with stable APIs that do not depend on a
  specific problem domain.

**❌ Avoid**
- Functions designed as internal implementation details of another library.
- Reuse justified only by "it is public" — the correct criterion is
  **what the function was designed for**.
- External dependencies whose policy changes could propagate into your library's
  behavior.

> **DRY and dependency boundaries**
> DRY encourages reuse, but reuse presupposes that the component was designed
> for your use case. Reusing internal parts of a library built for a different
> purpose is not DRY — it is an inappropriate dependency.

> For language-specific library and package conventions, see
> `adapters/<language>/README.md`.

---

## CPA Perspective

### 1. CPA Layer Declaration

Explicitly state which CPA layer each change belongs to.

```
Layers changed:
  CoreModel        : (list of types / pure functions added or changed)
  ProjectedContext : (list of use cases / effect-boundary logic added or changed)
  ApplicationBase  : (list of configuration / service wiring added or changed)
  Interface/Boot   : (list of entry points / dependency injection added or changed)
```

> For language-specific layer names and module conventions, refer to the CPA
> Convergence section in `AGENTS.md` and `adapters/<language>/README.md`.

### 2. Retro-dependency Principle Check

Confirm that dependencies flow strictly outward-to-inward.

- [ ] Dependency direction follows: Interface/Boot → ApplicationBase →
      ProjectedContext → CoreModel.
- [ ] Confirmed that inner layers (CoreModel / ProjectedContext) do not depend on
      outer layers.
- [ ] Confirmed that import directions of newly added functions and types are
      correct.

> The enforcement mechanism (compiler error, linter, or convention) depends on
> your language and toolchain. See `adapters/<language>/README.md` for details.

### 3. Erosion Check

#### Semantic Configuration (effect boundary erosion)

- [ ] Confirmed that outer-layer concerns have not leaked into the effect /
      context boundary of ProjectedContext.
- [ ] Confirmed that the effect boundary (e.g. IO, async, or monad stack in your
      language's idiom) does not reach into CoreModel.

> For language-specific effect / context boundary patterns, see
> `adapters/<language>/README.md`.

#### Ontological Configuration (domain concept misplacement)

- [ ] If new domain concepts are introduced, are they designed as CoreModel
      types / functions?
- [ ] Confirmed that CoreModel types carry no framework or library dependencies.

### 4. Greenfield: Initial Skeleton Design

If building a new library or service from scratch, state explicitly:

- Verify the declared project profile in `AGENTS.md` before choosing a structure:
  - `PROJECT_SCALE` (`small` / `medium` / `large`) determines the CPA construction pattern.
  - `PROJECT_CATEGORY` (`system` / `software` / `other`) determines how CPA layers
    are interpreted and validated.
  - See `docs/cpa-project-profile.md` for definitions and the scale selection guide.
- The CPA structure adopted (single-library 4-layer / multi-package / fractal
  7-package) — must be consistent with `PROJECT_SCALE`.
- Naming policy for packages / modules at each layer.
- Initial set of types to place in CoreModel.

> For language-specific skeleton starting points, see
> `adapters/<language>/README.md`.

---
