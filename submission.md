# Submission

## Working Live URL
https://agentflow-hackathon.pages.dev (Cloudflare Pages)
https://agentflow-webmcp.netlify.app (Netlify mirror)

## Text Description
**AgentFlow** is a visual workflow builder where humans and AI agents co-create automation pipelines in real-time.

Users drag-and-drop nodes (API Call, Transform, Condition, Output, Delay) on an interactive React Flow canvas. AI agents interact with the workflow through **8 WebMCP tools** registered via `document.modelContext.registerTool()`:

1. **add_node** — Add workflow nodes by type and label
2. **connect_nodes** — Create directed edges between nodes
3. **execute_workflow** — Run the full workflow in topological order
4. **get_available_tools** — Discover all available agent tools
5. **get_node_details** — Inspect a specific node's config and connections
6. **update_node_config** — Modify node settings without recreating
7. **get_workflow_status** — Get current canvas state (nodes, edges, counts)
8. **validate_workflow** — Check for errors, missing connections, cycles

**Why WebMCP fits**: Workflow builders need structured agent interaction. Instead of agents scraping the DOM and guessing button locations, WebMCP lets them call tools with JSON schemas. An agent can programmatically build a workflow by calling `add_node` and `connect_nodes`, then execute it with `execute_workflow` — all through the browser's built-in tool API.

**What people + agents can do together**: A human sketches a workflow visually. The agent validates it, suggests improvements, fills in node configurations, and executes it. The human sees results in real-time on the canvas. This is human-AI collaboration at the workflow level — not just chatting, but building automation together.

## Demo Video
<!-- Record a 2-3 minute demo showing:
1. The canvas with nodes
2. Agent adding nodes via WebMCP tools (get_available_tools, add_node)
3. Connecting nodes (connect_nodes)
4. Executing the workflow (execute_workflow)
5. Showing tool log output on the right panel
-->

## GitHub Repo URL
<!-- Push to GitHub and paste URL here -->
