# VSCXML Plugin for Claude Code

Professional W3C SCXML state machine editor, simulator, and code generator — directly in Claude Code.

## Installation

### From Marketplace

```bash
/plugin add-marketplace https://github.com/miho/vscxml-marketplace
/plugin install vscxml
```

### From Local Path (Development)

```bash
claude --plugin-dir /path/to/vscxml-plugin
```

## Skills

| Command | Description |
|---------|-------------|
| `/vscxml:design` | Create and visualize state machines from descriptions |
| `/vscxml:simulate` | Test with event injection, tracing, live highlights |
| `/vscxml:generate` | Generate code for Java, JS, C#, C, Python, Go, ST |
| `/vscxml:trace` | Record, compare, and replay execution traces |

Skills are auto-invoked by Claude when the task matches (e.g., "generate Go code" triggers `vscxml:generate`). You can also invoke them explicitly with the slash commands above.

## MCP Tools

The plugin bundles `@vscxml/mcp` which provides 50 tools:

- **Design**: `scxml_create`, `scxml_validate`, `scxml_inspect`
- **Simulation**: `scxml_sim_start`, `scxml_sim_scenario`, `scxml_sim_timed_scenario`
- **Traces**: `scxml_trace_embed`, `scxml_trace_play`, `scxml_compare_traces`
- **Generation**: `scxml_generate`, `scxml_generate_project`, `scxml_list_targets`
- **Editor**: `editor_push_scxml`, `editor_add_state`, `editor_add_transition`, `editor_highlight`, `editor_export_svg`, and more

## Prerequisites

- **Node.js 18+** — required to run the MCP server (`npx @vscxml/mcp`). Install from [nodejs.org](https://nodejs.org).

The plugin also connects to VSCXML backends running locally:

- **VSCXML-Editor** — visual diagram editor (port 48623)
- **VSCXML-Simulator** — interactive simulation server (port 48621)
- **VSCXML-Generator-CLI** — code generation server (port 48620)

Start them before using the plugin. Check status with `scxml_status`.

## Links

- [VSCXML Website](https://vscxml.com)
- [MCP Package](https://www.npmjs.com/package/@vscxml/mcp)
- [GitHub](https://github.com/miho/vscxml)
