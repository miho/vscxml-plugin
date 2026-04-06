---
name: design
description: Create and visualize state machines from descriptions using the VSCXML MCP tools.
---

# SCXML Design Workflow

Guided workflow for designing, testing, and generating SCXML state machines.

## When to use

When the user asks to:
- Design a state machine from a description
- Create an SCXML file
- Generate code from a state machine concept
- Test state machine behavior
- Compare simulator vs generated code traces

## Prerequisites

Call `scxml_status` first to check which backends are running:
- **Generator**: needed for validate, inspect, create, generate
- **Simulator**: needed for simulation, traces
- **Editor**: needed for visual editing, screenshots, save/load

## Workflow

1. **Clarify requirements** -- Ask about states, events, data variables, and target language if not specified.

2. **Create the SCXML** using `scxml_create`:
   - Map the requirements to states (atomic, compound, parallel, final)
   - Define transitions with events, guards, and actions
   - Add data variables for state-dependent logic
   - For onentry/onexit with `<send>`, `<cancel>`, `<assign>`: use structured action objects:
     ```json
     {"onEntry": [{"type": "send", "event": "timer", "delay": "500ms", "id": "t1"}],
      "onExit": [{"type": "cancel", "sendid": "t1"}]}
     ```

3. **Validate** using `scxml_validate`:
   - Check for errors before proceeding
   - Fix any issues

4. **Inspect** using `scxml_inspect`:
   - Verify the structure matches the user's intent
   - Show the event list and variable summary
   - Confirm the hierarchy is correct

5. **Push to editor** (if connected) using `editor_push_scxml`:
   - Let the user see and review the visual diagram
   - Auto-layout: `editor_auto_layout`
   - Add images with `editor_add_image`:
     - Use `filePath` for large images (editor reads from disk, avoids base64 in MCP)
     - Use `name` for display in structure panel
     - Use `visibleWhen` for state-dependent visibility
     - Use `attachedTo` to nest under a parent state
   - Add explanatory notes with `editor_add_note`
   - Highlight key states with `editor_highlight` using presets: `"error"` (red), `"success"` (green), `"warning"` (yellow), `"info"` (blue)

6. **Simulate**:
   - Start with `scxml_sim_start(source="editor", connectEditor=true)` — fetches from editor, auto-reconnects for live highlights
   - **CRITICAL**: If the machine uses `<send delay="..."/>` for internal events, use `scxml_sim_timed_scenario` (NOT `scxml_sim_scenario`). Timed mode lets internal timer events fire between external events.
   - **NEVER send events externally that the machine generates internally** via delayed send — they'll fire twice
   - Only send external stimuli (button presses, sensor inputs)
   - Results include `enabledEvents` per step so you know which events are valid
   - **Invoke (child machines)**: Use `scxml_sim_send(event, invokeId="childId")` to send events directly to a specific invoked child machine.

7. **Save the trace** using `embedTrace` parameter on scenario tools (saves a round-trip):
   ```json
   {"events": [...], "embedTrace": {"name": "sim-demo", "description": "Full cycle"}}
   ```
   Or separately: `scxml_trace_embed(name="...", target="both")`

8. **Iterate** based on the user's feedback:
   - Use `editor_add_state(parentId="parentId", id="myState", label="My State")` to add child states
   - Use `editor_add_transition(source, target, event="go", cond="x > 0")` for transitions
   - Use `editor_set_property` for targeted edits (NOT full `push_scxml`)
   - Use `editor_set_image_property` to modify existing images (position, visibility, z-order)
   - Use `editor_clone_state` to duplicate compound states (remaps IDs, initial, label)
   - Use `editor_remove_element` to delete states or transitions
   - Use `editor_get_scxml(includeTraces=false)` to inspect without trace bloat
   - Use `editor_save_file` to save progress to disk

9. **Generate code** using `scxml_generate_project`:
   - Ask for the target language if not specified
   - Set `tracingMode: "full"` to enable trace recording in generated code
   - Response includes both `buildCommand` and `runCommand`
   - Available targets: java, js, csharp, c, python, go, st
   - All targets emit consistent traces including `action_execute` entries

10. **Export** for sharing and documentation:
    - `editor_export_svg` -- full diagram as parseable SVG (include in README, docs)
    - `editor_export_png` -- high-res image for presentations
    - `editor_export_player_html` -- interactive HTML demo anyone can open in a browser

11. **Compare traces** using `scxml_compare_traces`:
    - Use `normalize: true` for cross-runtime comparison (maps type aliases, filters verbosity)
    - `mode: "state-sequence"` -- compares only state enters/exits
    - `mode: "structural"` -- ignores loop repetition counts (e.g., 3 blinks vs 5 blinks = match)
    - Use `expectedName`/`actualName` to compare embedded traces by name (saves tokens)

## Common Pitfalls

- **Double events**: Internal `<send delay="..."/>` events + external sends of the same event = processed twice
- **Stale push**: After editing in the editor, use `editor_get_scxml` to get current content — don't assume the last pushed version is current
- **Trace loss**: Use `target="both"` on `scxml_trace_embed` to sync traces to the editor before saving
- **Full-doc push**: Prefer `editor_add_state(parentId=...)`, `editor_set_property`, `editor_clone_state`, `editor_remove_element` over `editor_push_scxml` for targeted edits
- **Building hierarchies**: Use `editor_add_state(parentId="parent", id="child", label="Child")` to build nested structures
- **Port conflicts**: If the editor can't start its API, check for stale processes. All components clean stale discovery files on startup.
