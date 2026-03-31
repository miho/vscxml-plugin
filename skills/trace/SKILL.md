---
name: trace
description: Record, compare, and replay execution traces for SCXML state machines. Verify behavior across simulator and generated code.
---

# SCXML Trace Workflow

Manage execution traces for verification and visual replay.

## Recording Traces

- **During simulation**: Use `embedTrace` parameter on scenario tools:
  ```json
  {"events": [...], "embedTrace": {"name": "demo", "description": "Full cycle"}}
  ```
- **After simulation**: `scxml_trace_embed(name="trace-name", target="both")` — `target="both"` syncs to the editor so traces survive file save.

## Comparing Traces

Use `scxml_compare_traces` to verify simulator vs generated code behavior:

- `normalize: true` — maps type aliases, filters verbosity differences
- `mode: "full"` — compares all entries
- `mode: "state-sequence"` — compares only state enters/exits (most common)
- `mode: "structural"` — collapses repeated loops, ignores repetition counts

Compare by name (avoids resending data):
```json
{"expectedName": "sim-trace", "actualName": "java-trace", "normalize": true, "mode": "state-sequence"}
```

## Replaying Traces

- `scxml_trace_play(name="trace", speed=2.0)` — plays back with live state highlights in the editor
- `scxml_trace_step(delta=1)` — step forward/backward through a trace
- `scxml_trace_list` — list all embedded traces
- `scxml_trace_get(name="trace")` — retrieve trace entries for inspection

## Tips

- All targets (Java, JS, C#, C, Python, Go) emit consistent trace formats
- Structural mode is ideal for comparing traces with timing-dependent loops (e.g., LED blinking at different speeds)
- Use `editor_get_scxml(includeTraces=false)` to get the SCXML without trace data (saves tokens)
