---
name: design
description: Design SCXML state machines from natural language descriptions. Creates, validates, and visualizes state machines in the VSCXML editor.
---

# SCXML Design Workflow

Guide the user through designing a W3C SCXML state machine.

## Steps

1. **Check status** — Call `scxml_status` to verify backends are running.

2. **Clarify requirements** — Ask about states, events, data variables, timers, and target language.

3. **Create the SCXML** using `scxml_create`:
   - Map requirements to states (atomic, compound, parallel, final)
   - Define transitions with events, guards, and actions
   - For timers: use structured action objects:
     ```json
     {"onEntry": [{"type": "send", "event": "timeout", "delay": "5s", "id": "timer1"}],
      "onExit": [{"type": "cancel", "sendid": "timer1"}]}
     ```

4. **Validate** with `scxml_validate` — fix any errors.

5. **Push to editor** with `editor_push_scxml`, then:
   - `editor_auto_layout` for clean arrangement
   - `editor_highlight` with color presets for visual clarity
   - `editor_add_image` with `visibleWhen` for state-dependent icons
   - `editor_add_note` for documentation

6. **Iterate** using targeted edits:
   - `editor_set_property` for single-element changes
   - `editor_clone_state` for duplicating compound states
   - `editor_add_transition` with `event` parameter
   - `editor_remove_element` to delete states/transitions
   - **Never push the full document for small changes**

7. **Save** with `editor_save_file`.

## Tips

- Always validate after changes
- Use `editor_get_scxml(includeTraces=false)` to inspect without trace bloat
- Use `editor_export_svg` to reason about layout (SVG has state IDs as parseable XML)
