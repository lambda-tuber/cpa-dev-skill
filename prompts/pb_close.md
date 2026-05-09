# PB Close Phase

## Steps

1. Confirm in the PB folder's `PB-STATUS.md` that all CRs have status **Done**.
2. Update `backlog/STATUS.md` and set the PB status to **Done**.
3. Review all acceptance criteria checkboxes in `backlog/BACKLOG.md` and confirm
   the PB is complete.

---

## CPA Perspective: Retrospective at Close

When closing a PB, perform the following CPA retrospective and record the results
in `PB-STATUS.md`.

### Retrospective Checklist

- [ ] Reviewed the list of types and functions added to CoreModel during this PB.
- [ ] Confirmed that layer dependency direction follows the Retro-dependency
      Principle (outward-to-inward only: Interface → ApplicationBase →
      ProjectedContext → CoreModel).
- [ ] Confirmed that no Erosion has occurred (no external effects, configuration,
      or framework dependencies have leaked into CoreModel).
- [ ] If new packages were added, confirmed that their CPA layer position is clear.

### Feedback Record

If this PB revealed gaps or improvement opportunities in the CPA skill prompts,
record them in `PB-STATUS.md`. These notes feed future improvements to `prompts/`.

```markdown
## CPA Skills Feedback (recorded at PB close)
- (observations and improvement proposals)
```

---
