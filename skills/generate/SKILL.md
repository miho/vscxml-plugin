---
name: generate
description: Generate native code from SCXML state machines for Java, JavaScript, C#, C, Python, Go, and Structured Text.
---

# SCXML Code Generation Workflow

Generate production-ready transpiled code from an SCXML state machine.

## Steps

1. **Choose target** — ask the user which language:
   - `java` — Gradle project, Rhino/GraalJS ECMAScript
   - `js` / `javascript` — Node.js project, native JS engine
   - `csharp` — .NET project, Jint ECMAScript
   - `c` — CMake project, JerryScript ECMAScript (or bare-metal)
   - `python` — pip project, dukpy ECMAScript
   - `go` — Go module, Goja ECMAScript
   - `st` — IEC 61131-3 Structured Text for PLCs

2. **Generate project** with `scxml_generate_project`:
   - Set `tracingMode: "full"` for trace recording in generated code
   - Response includes `buildCommand` and `runCommand`
   - All targets emit consistent traces including `action_execute` entries

3. **Build and run**:
   - Follow the `buildCommand` (e.g., `gradle build`, `dotnet build`, `cmake .. && make`)
   - Run with `runCommand` (e.g., `gradle run`, `dotnet run`, `./build/main`)

4. **Compare traces** (if simulator trace exists):
   - `scxml_compare_traces(expectedName="simulator-trace", actualName="java-trace", normalize=true)`
   - `mode: "state-sequence"` for state-only comparison
   - `mode: "structural"` to ignore loop count differences

## Tips

- Use `scxml_generate` with `codeOnly: true` for just the state machine code (no project scaffolding)
- C target now generates CMakeLists.txt automatically
- All executors auto-start the machine — no manual `sm.start()` needed
- Use `SendById(EVT_*)` for O(1) event dispatch (available on Java, Go, C#, Python, C)
