# PB Iteration

## Overview

A PB (Product Backlog) iteration is the cycle that takes a single PB from open
to close. A PB represents a unit of desired development work, and is broken down
into one or more CRs (Change Requests) for execution.

## Phase Structure

| Phase | Reference |
|-------|-----------|
| PB Open | `prompts/pb_open.md` |
| CR Open | `prompts/cr_open.md` |
| CR Iteration | `prompts/cr_iteration.md` |
| PB Close | `prompts/pb_close.md` |

---

## CPA Perspective: Layer Overview for the PB

Before starting a PB, identify which CPA layers it primarily affects.
This informs the granularity of CR decomposition and the packages / modules each
CR will touch.

### CPA 4-Layer Table

| CPA Layer | Responsibility |
|-----------|---------------|
| CoreModel | Discovered domain model: pure types, invariants, value objects. No IO, no frameworks. |
| ProjectedContext | Use-case logic projected from Core. Handles external effects at the boundary. |
| ApplicationBase | Runtime configuration, service wiring, non-functional concerns. |
| Interface/Boot | Entry points, dependency injection, external tool integration. |

> For language-specific layer mapping (module names, package layout, enforcement
> mechanism), refer to `adapters/<language>/README.md` and the CPA Convergence
> section in `AGENTS.md`.

### Overview Checklist

- [ ] Which CPA layers does this PB primarily affect? (multiple allowed)
- [ ] Will new domain types or pure functions be added to CoreModel?
- [ ] Are new modules or packages required?
- [ ] Is fractal scale needed, or does a single-library structure suffice?

### Scale Selection Guide

| Scale | Structure | Rough indicator |
|-------|-----------|-----------------|
| Small | Single-library CPA 4-layer structure (module split) | 1–3 CRs |
| Medium | Multi-package CPA-compliant split | Multiple CRs, new package(s) |
| Large | Fractal 7-package structure (cpa-fractal equivalent) | Long-running, multi-team |

---
