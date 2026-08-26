<p align="center">
  <img src="logo.png" width="120" alt="AgentFlow Logo" />
</p>

<h1 align="center">AgentFlow</h1>

<p align="center">
  <strong>Human × Agent Visual Workflow Builder</strong><br/>
  Co-create automation pipelines with AI agents in real-time via WebMCP.
</p>

<p align="center">
  <a href="https://agentflow-hackathon.pages.dev">Live Demo</a> ·
  <a href="#quick-start">Quick Start</a> ·
  <a href="#node-types">Node Types</a> ·
  <a href="#webmcp-tools">WebMCP Tools</a> ·
  <a href="#architecture">Architecture</a>
</p>

---

## What is AgentFlow?

AgentFlow is a browser-based visual workflow builder where **humans and AI agents co-create automation pipelines** on a shared canvas. It uses **WebMCP** (Model Context Protocol for browsers) to expose workflow tools that any compatible AI agent can call — adding nodes, wiring them together, and executing flows, all in real-time.

### Key Features

- **15 node types** — API calls, transforms, conditions, filters, loops, AI, webhooks, and more
- **Real execution engine** — workflows run for real in the browser (topological order, branch gating, error propagation)
- **WebMCP integration** — 8 tools exposed via `document.modelContext.registerTool()` for AI agent control
- **Smart grid placement** — nodes snap to a 280×90 grid with spiral search, non-disruptive wiring, and snap-to-grid drag
- **Live telemetry** — every tool call is logged with actor (human/agent), input, result, and timestamp
- **Dark instrument console theme** — warm charcoal, amber + cyan accents, Chakra Petch / Archivo / IBM Plex Mono fonts

---

## Quick Start

### Prerequisites

- [Node.js](https://nodejs.org/) 18+
- npm or pnpm

### Install & Run

```bash
cd agentflow/frontend
npm install
npm run dev
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

### Build for Production

```bash
npm run build
```

Output goes to `dist/`. Deploy to any static host (Cloudflare Pages, Vercel, Netlify, etc.).

---

## Node Types

| Type | Color | What it does | Key Config |
|------|-------|-------------|------------|
| **API Call** | 🔵 `#8f9fdd` | Fetch data from any REST endpoint | `url`, `method`, `body` |
| **Transform** | 🟡 `#e0b45c` | Reshape data (pick keys, count, expression) | `op`, `keys`, `expression` |
| **Condition** | 🩷 `#d98aa6` | Branch on true/false with labeled wires | `expression` |
| **Filter** | 🟠 `#e8a33d` | Pass/fail data based on predicate | `expression` |
| **Split** | 🟢 `#56cdbd` | Break arrays into batches or objects into pairs | `batchSize` |
| **Merge** | 🔵 `#7ec8e3` | Combine multiple inputs into one object | — |
| **Loop** | 🟣 `#c9a0dc` | Iterate over collections | `maxIterations` |
| **Code** | 🟢 `#a8d8a8` | Run arbitrary JavaScript on data | `code` |
| **Delay** | 🟣 `#ab97d4` | Wait N milliseconds | `ms` |
| **Webhook** | 🟠 `#f0a07a` | POST data to an external URL | `url`, `method` |
| **AI** | 🩷 `#ff6b9d` | Call OpenAI (or simulate without key) | `prompt`, `model`, `apiKey` |
| **Validator** | 🔵 `#7dd3fc` | Check data passes rules | `expression` |
| **Logger** | 🟤 `#d4a574` | Console log with level and message | `level`, `message` |
| **File** | 🔵 `#93c5fd` | Trigger file download (browser) | `operation`, `path` |
| **Output** | 🟢 `#6cc7ba` | Deliver results (console, download, webhook) | `kind`, `url`, `filename` |

---

## WebMCP Tools

AgentFlow exposes 8 tools via the browser's WebMCP API. Any compatible AI agent (Brave Leo, etc.) can call these to build and run workflows:

| Tool | Description | Inputs |
|------|-------------|--------|
| `add_node` | Add a workflow node to the canvas | `type`, `label`, `x?`, `y?` |
| `connect_nodes` | Wire two nodes together | `sourceNodeId`, `targetNodeId`, `label?` |
| `execute_workflow` | Run the entire workflow | `input?` |
| `get_available_tools` | Discover all 8 tools | — |
| `get_node_details` | Get info about a specific node | `nodeId` |
| `update_node_config` | Update a node's configuration | `nodeId`, `config` |
| `get_workflow_status` | Get current canvas state | — |
| `validate_workflow` | Check for errors and missing configs | — |

### Programmatic Access

Every tool is also available via `window.__agentflow`:

```javascript
// List all tools
window.__agentflow.listTools()

// Call a tool
await window.__agentflow.callTool('add_node', {
  type: 'api_call',
  label: 'fetch users',
})

await window.__agentflow.callTool('connect_nodes', {
  sourceNodeId: 'node_abc123',
  targetNodeId: 'node_def456',
})

await window.__agentflow.callTool('execute_workflow', {
  input: { page: 1 }
})
```

---

## Architecture

```
agentflow/
├── frontend/                  # React + TypeScript + Vite
│   ├── src/
│   │   ├── App.tsx            # Main app — ReactFlow canvas, wiring, viewport
│   │   ├── engine.ts          # Workflow execution engine (topological sort, branch gating)
│   │   ├── webmcp.ts          # WebMCP tool registration (8 tools)
│   │   ├── utils/
│   │   │   └── grid.ts        # Smart placement, snap-to-grid, wiring adjustments
│   │   ├── components/
│   │   │   ├── nodes/         # Module node component (shared for all 16 types)
│   │   │   ├── Sidebar.tsx    # Module palette, canvas list, example flow
│   │   │   ├── ConfigPanel.tsx # Per-type configuration UI
│   │   │   ├── ExecutionPanel.tsx # Run button, input/output
│   │   │   ├── ToolLog.tsx    # Telemetry log (agent + human actions)
│   │   │   ├── LabeledEdge.tsx # Custom edge with dark-themed labels
│   │   │   └── icons.tsx      # 16 SVG icons
│   │   ├── index.css          # Global styles, fonts
│   │   └── App.css            # Theme, node transitions, responsive
│   ├── index.html
│   └── package.json
├── landing/                   # Separate static landing page
│   └── index.html
└── README.md
```

### Engine

The execution engine runs workflows in-browser:

1. **Topological sort** — determines execution order from the DAG
2. **Branch gating** — condition nodes set `lastCondition`; downstream edges labeled `true`/`false` skip accordingly
3. **Per-node runners** — each of the 16 types has a dedicated async runner
4. **Error propagation** — faults block all downstream nodes (fail-fast)
5. **Cycle detection** — cycles are appended to the order and fault at execution

### Positioning System

Grid-based placement (`utils/grid.ts`) ensures clean, predictable layouts:

- **Grid**: 280px horizontal × 90px vertical pitch, origin at (80, 80)
- **Smart placement**: new nodes go right of selection or rightmost node, then spiral-search for open cell
- **Non-disruptive wiring**: connecting A→B only moves B if it's not already to the right
- **Snap-to-grid drag**: drops snap to grid; occupants pushed down by one row
- **fitView**: all nodes visible in frame after each add/wire

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| UI | React 19, ReactFlow 12 |
| Language | TypeScript 5 |
| Build | Vite 8 |
| Execution | Custom in-browser engine (topological sort) |
| Agent Integration | WebMCP (`document.modelContext.registerTool`) |
| Fonts | Chakra Petch, Archivo, IBM Plex Mono |
| Hosting | Cloudflare Pages |

---

## Browser Compatibility

Requires a browser with WebMCP support for agent integration:

- **Brave Nightly** with Leo AI — fully tested
- Any Chromium-based browser with WebMCP enabled

The app itself works in any modern browser; agent tools are simply unavailable without WebMCP.

---

## License

MIT
