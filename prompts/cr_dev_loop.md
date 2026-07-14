# Development Loop

## Overview

The development loop runs **Coding → Build → Test** as one cycle, repeating
until all completion criteria are met. Refer to the sub-phase prompts for details.

| Sub-phase | Reference |
|-----------|-----------|
| Coding | `prompts/cr_coding.md` |
| Build | `prompts/cr_build.md` |
| Test | `prompts/cr_test.md` |

---

## Loop Flow

1. Perform coding and update the **Coding** checkbox in `CR-XXXX_<title>/03_dev_loop.md` to `[x]`.
2. Run the build and record the result in `CR-XXXX_<title>/03_dev_loop.md`. Mark **Build** `[x]` on
   success.
3. Run the tests and record the result in `CR-XXXX_<title>/03_dev_loop.md`. Mark **Test** `[x]` on
   success.
4. If build or test fails, return to coding, fix the issue, and repeat the cycle.
5. When all checkboxes are complete, refer to `prompts/cr_close.md` to close
   the CR.

---

## Phase Transition Confirmation Rules

- Coding → Build: "Coding is complete. May we run `{{BUILD_COMMAND}}`?"
- Build → Test: "Build is complete. May we run `{{TEST_COMMAND}}`?"
- Test → CR Close: "Tests are complete. May we close CR-XXXX?"

Obtain approval at each confirmation before proceeding.

---

## CPA Perspective: Notes During the Development Loop

Follow the CPA coding checklist in `prompts/cr_coding.md` throughout the loop.
If a build error is caused by a Retro-dependency violation, revisit the layer
declaration in the Design section before modifying the code.

---
