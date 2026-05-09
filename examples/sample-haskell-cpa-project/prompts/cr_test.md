# Test Phase

## Overview

The Test phase verifies that all tests pass after a successful build. Record the
test result in the **Development Loop** section of `CR-XXXX.md`.

---

## Test Steps

### Step 1 — Run the test command

Run the test command configured for this project:

```
{{TEST_COMMAND}}
```

Execute it in the project workspace: `{{WORKSPACE_PATH}}`.

Use the tool or shell available in your agent environment to run commands. Refer
to the **Tool / Agent Assumptions** section in `AGENTS.md` for the execution
method specific to this project.

### Step 2 — Capture and record output

- Capture the full test output.
- Record the result (success or failure with details) in the **Development Loop**
  section of `CR-XXXX.md`.

---

## Notes

- If tests fail, record the failure details in `CR-XXXX.md`, return to the Coding
  phase to fix the issue, and run the build and tests again.
- Run one command at a time. Do not chain multiple commands in a single
  invocation if your agent environment does not support it — check
  **Tool / Agent Assumptions** in `AGENTS.md`.
- Mark the Test checkbox `[x]` only after all tests pass.

---
