# CR Iteration

During CR work, **always** update `CR-XXXX.md` at every phase transition to record
the current state. Delayed recording is not acceptable.

> ⚠️ **Important: Always obtain human confirmation before moving to the next phase.**
> Do not proceed autonomously. After recording the current state in the CR file,
> explicitly ask the human: "Phase XX is complete. May we proceed to phase YY?"
> and wait for approval before continuing.

## Phase Structure

| Phase | Reference |
|-------|-----------|
| CR Open | `prompts/cr_open.md` |
| Requirements | `prompts/cr_requirements.md` |
| Design | `prompts/cr_design.md` |
| Development Loop | `prompts/cr_dev_loop.md` (Coding / Build / Test) |
| CR Close | `prompts/cr_close.md` |

---

## Phase Transition Rules

### Requirements → Design
- ⚠️ **Confirmation required**: Record the requirements in the CR file, then ask:
  "Requirements are complete. May we proceed to the Design phase?"

### Design → Coding
- ⚠️ **Confirmation required**: Record the design in the CR file, then ask:
  "Design is complete. May we proceed to the Coding phase?"
- When coding is done, update the Coding checkbox in the CR file from `[ ]` to `[x]`.

### Coding → Build
- ⚠️ **Confirmation required**: Confirm the Coding checkbox is `[x]` in the CR
  file, then ask: "Coding is complete. May we run `{{BUILD_COMMAND}}`?"
- Record the build result (success / failure and error details) in the CR file.
- Mark the Build checkbox `[x]` only after a successful build.

### Build → Test
- ⚠️ **Confirmation required**: Confirm the Build checkbox is `[x]` in the CR
  file, then ask: "Build is complete. May we run `{{TEST_COMMAND}}`?"
- Record the test result (success / failure and details) in the CR file.
- Mark the Test checkbox `[x]` only after all tests pass.

### Test → CR Close
- Refer to `prompts/cr_close.md`.

---
