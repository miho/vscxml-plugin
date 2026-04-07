---
name: vscxml:simulate
description: Simulate and test SCXML state machines with real-time event injection, tracing, and live editor highlights.
---

# SCXML Simulation Workflow

Guide the user through simulating and testing a state machine.

## Prerequisites

Call `scxml_status` first to check which backends are running:
- **Simulator**: needed for simulation, traces, event injection
- **Editor**: needed for live state highlights and visual feedback

## Steps

1. **Start simulation** with `scxml_sim_start(source="editor", connectEditor=true)`:
   - Fetches SCXML from the editor (no resending needed)
   - Auto-reconnects editor for live state highlights

2. **Run events**:
   - **If the machine has `<send delay="..."/>`**: Use `scxml_sim_timed_scenario` — internal timer events fire between external events
   - **Otherwise**: Use `scxml_sim_scenario` for instant batch execution
   - Results include `enabledEvents` per step

3. **CRITICAL**: Never send events externally that the machine generates internally via `<send delay="..."/>` — they will fire twice. Only send external stimuli (button presses, sensor inputs).

4. **Record trace** — use `embedTrace` parameter directly on scenario tools:
   ```json
   {"events": [...], "embedTrace": {"name": "test-run", "description": "Happy path"}}
   ```

5. **Inspect state** with `scxml_sim_get_state` for active states, variables, enabled events.

6. **Reset** with `scxml_sim_reset` to restart from initial state.

## Tips

- Use `scxml_trace_play` to replay recorded traces with visual highlights in the editor
- Use `scxml_trace_list` to see all embedded traces
- Compare traces: `scxml_compare_traces(expectedName="sim", actualName="impl", normalize=true)`
