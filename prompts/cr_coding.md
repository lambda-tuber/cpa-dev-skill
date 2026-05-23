# Coding Phase

## Overview

In the Coding phase, implement according to the Design section of the CR.
From a CPA perspective, always be aware of **which layer each file and function
belongs to** while coding.

---

## Test Code Creation

Based on the test cases extracted in the Design phase, create test code in
parallel with the implementation.

- [ ] Referred to the test case list in the Design section and implemented the
      corresponding test cases.
- [ ] Confirmed that test code placement is consistent with CPA layer
      responsibilities:
  - CoreModel tests: pure function tests (no side effects / I/O).
  - ProjectedContext tests: effectful integration tests.
- [ ] Confirmed that all test cases are covered by test code.
- [ ] Test code exists before entering the Build phase (where tests are run).

---

## Coding Conventions

### Comment Language

- All code comments must be written in **English**.
- This applies to all comment styles in any language (line comments, block
  comments, doc comments, etc.).

---

## CPA Perspective: Coding Checklist

### 1. Pre-edit Confirmation

Before editing each file, confirm:

- [ ] Which CPA layer does this file / function belong to?
      (Cross-check with the layer declaration in the Design section.)
- [ ] Is the change within the responsibility of the declared layer?

### 2. Language-Specific Layer Check

Verify layer boundaries using the idioms of your target language.

> For language-specific checks (type stack, import graph, effect boundaries,
> linter rules, etc.), refer to `adapters/<language>/README.md` and the CPA
> Convergence section in `AGENTS.md`.

### 3. CoreModel Implementation Purity

When implementing code that belongs to CoreModel:

- [ ] Is the implementation side-effect-free (no external I/O, no framework
      calls)?
- [ ] Are there no external library dependencies mixed into CoreModel?
- [ ] Are the types environment- and framework-independent?

### 4. Greenfield: Starting from Skeleton

For new libraries or services:

- [ ] Created directories and files using the CPA skeleton structure decided in
      Design.
- [ ] Implementing CoreModel first, then progressing outward toward outer layers
      (inside-out order).

---
