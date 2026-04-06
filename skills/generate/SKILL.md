---
name: generate
description: Generate native code from SCXML state machines for Java, JavaScript, C#, C, Python, Go, and Structured Text.
---

# SCXML Code Generation Workflow

Generate production-ready transpiled code from an SCXML state machine.

## Prerequisites

Call `scxml_status` first to check which backends are running:
- **Generator**: needed for code generation and project scaffolding

## Steps

1. **Choose target** — ask the user which language:
   - `java` — Gradle project, Rhino/GraalJS ECMAScript
   - `js` / `javascript` — Node.js project, native JS engine
   - `csharp` — .NET project, Jint ECMAScript
   - `c` — CMake project, JerryScript ECMAScript (or bare-metal)
   - `python` — pip project, dukpy ECMAScript
   - `go` — Go module, Goja ECMAScript
   - `st` — IEC 61131-3 Structured Text for PLCs (CODESYS, TwinCAT, OpenPLC)

2. **Choose datamodel** (if not already set in the SCXML):
   - `ecmascript` — default, 100% W3C compliant across all targets
   - `null` — no variables, pure event-driven
   - `xpath` — Java only, XPath 2.0 expressions
   - `native-{target}` — type-safe language-native variables (e.g., `native-java`, `native-csharp`, `native-c`, `native-python`, `native-go`, `native-st`)

3. **Generate project** with `scxml_generate_project`:
   - Set `tracingMode: "full"` for trace recording in generated code
   - Response includes `buildCommand` and `runCommand`
   - All targets emit consistent traces including `action_execute` entries
   - For ST target: set `plcPlatform` option:
     - `codesys` (default) — 3rd edition with METHODs
     - `iec-strict` — no pragmas or access modifiers
     - `openplc` — 2nd edition with standalone FUNCTIONs (no METHODs)

4. **Build and run**:
   - Follow the `buildCommand` (e.g., `gradle build`, `dotnet build`, `cmake .. && make`)
   - Run with `runCommand` (e.g., `gradle run`, `dotnet run`, `./build/main`)
   - All generated mains support `--events ev1 ev2` for batch mode and `--verbose` for logging

5. **Compare traces** (if simulator trace exists):
   - `scxml_compare_traces(expectedName="simulator-trace", actualName="java-trace", normalize=true)`
   - `mode: "state-sequence"` for state-only comparison
   - `mode: "structural"` to ignore loop count differences

## Tips

- Use `scxml_generate` with `codeOnly: true` for just the state machine code (no project scaffolding)
- C target generates CMakeLists.txt automatically; bare-metal mode via `SCXML_PLATFORM_BARE_METAL`
- All executors auto-start the machine — no manual `sm.start()` needed
- Use `SendById(EVT_*)` for O(1) event dispatch (available on Java, Go, C#, Python, C)
- All targets provide introspection: `getAllEvents()`, `getEnabledEvents()`, `getEventsForState(id)`, `getEnabledEventsForState(id)`
