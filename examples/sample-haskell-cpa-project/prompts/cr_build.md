# Build Phase

## Overview

The Build phase verifies that the code compiles (or is otherwise valid) after
coding. Record the build result in the **Development Loop** section of `CR-XXXX.md`.

---

## Build Steps

### Step 1 — Run the build command

Run the build command configured for this project:

```
{{BUILD_COMMAND}}
```

Execute it in the project workspace: `{{WORKSPACE_PATH}}`.

Use the tool or shell available in your agent environment to run commands. Refer
to the **Tool / Agent Assumptions** section in `AGENTS.md` for the execution
method specific to this project.

### Step 2 — Capture and record output

- Capture the full build output.
- Record the result (success or failure with error details) in the
  **Development Loop** section of `CR-XXXX.md`.

---

## Notes

- If the build fails, record the error in `CR-XXXX.md`, return to the Coding phase
  to fix the issue, and run the build again.
- Run one command at a time. Do not chain multiple commands in a single
  invocation if your agent environment does not support it — check
  **Tool / Agent Assumptions** in `AGENTS.md`.
- Mark the Build checkbox `[x]` only after a successful build.

---
