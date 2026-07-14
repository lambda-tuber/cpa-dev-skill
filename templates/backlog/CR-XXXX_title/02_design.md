# CR-XXXX Design

## CPA Layer Declaration

```
Target layers:
  CoreModel        : (types / functions to add or change)
  ProjectedContext : (use cases / IO operations to add or change)
  ApplicationBase  : (configuration / service wiring to add or change)
  Interface/Boot   : (entry points / dependency injection to add or change)
```

---

## Design Details

<!-- Record design decisions: directory layout, module structure, type definitions. -->

---

## Retro-dependency Check

- [ ] Dependencies flow outward → inward only
      (Interface/Boot → ApplicationBase → ProjectedContext → CoreModel)
- [ ] No inner layer imports from an outer layer
- [ ] Import directions of new functions/types are correct

## Erosion Check

- [ ] Semantic: No outer-layer concerns leak into the ProjectedContext type stack
- [ ] Ontological: New domain concepts are placed in CoreModel with no framework dependencies
