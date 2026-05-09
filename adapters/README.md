# adapters/

This directory contains language- and environment-specific CPA adapter documents.

## Purpose

Each adapter describes how to enforce CPA layer boundaries, naming conventions,
and Erosion prevention strategies for a specific language or toolchain.
Adapters are the mechanism by which the language-neutral CPA skill becomes
concrete and actionable for a given project environment.

## Structure

```
adapters/
├── haskell/          ← First-class adapter (CR-10)
│   ├── README.md     ← Haskell-specific CPA enforcement guide
│   └── AGENTS.md     ← Haskell-specific AGENTS.md convergence section
└── rust/             ← Future adapter (planned after CR-10)
```

## Adapter Document Contents

Each adapter `README.md` should describe:

1. **Layer-to-module mapping** — How CPA layers map to packages/modules in this language
2. **Retro-dependency enforcement** — How the language compiler/linter can detect violations
3. **Erosion prevention** — Patterns to keep CoreModel free of framework/IO dependencies
4. **Naming conventions** — Language-idiomatic naming aligned with CPA vocabulary
5. **Build/test commands** — Standard commands for this language (filling `{{BUILD_COMMAND}}` / `{{TEST_COMMAND}}`)
6. **Tool assumptions** — MCP/agent tool usage specific to this environment

## Available and Planned Adapters

| Priority | Language | Status | CR |
|----------|----------|--------|----|
| 1 | Haskell | Available | CR-10 |
| 2 | Rust | Future | TBD |

## Status

The Haskell adapter is available in `adapters/haskell/`.
Future adapters should follow the same structure.
