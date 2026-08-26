# WebMCP Research

## What is WebMCP?
WebMCP is a proposed web standard that lets websites expose structured tools for AI agents. Instead of agents scraping the DOM and guessing, sites declare tools with names, descriptions, and JSON schemas — agents call them directly.

## Two APIs

### Imperative API (JavaScript)
```js
await document.modelContext.registerTool({
  name: 'toggle_layer',
  description: 'Control pizza layers.',
  inputSchema: {
    type: 'object',
    properties: {
      layer: { type: 'string', enum: ['sauce', 'cheese'] },
      action: { type: 'string', enum: ['add', 'remove', 'toggle'] }
    },
    required: ['layer']
  },
  execute: async ({ layer, action }) => {
    return `Performed ${action || 'toggle'} on layer: ${layer}`;
  }
});
```

### Declarative API (HTML attributes)
Annotate standard HTML forms with `data-mcp-tool`, `data-mcp-tool-name`, `data-mcp-tool-description` etc.

## Key Concepts
- **Tool Registration**: `document.modelContext.registerTool()`
- **Tool Discovery**: `document.modelContext.getTools()`
- **Tool Execution**: `document.modelContext.executeTool(tool, inputJson)`
- **Events**: `toolchange` event for when tools change
- **Unregister**: AbortController to remove tools
- **Cross-origin**: iframe support via `allow="tools"` and `exposedTo`

## Chrome Support
- Origin trial from Chrome 149
- Chrome flag: `chrome://flags/#enable-webmcp-testing` (local dev)
- Available in ChatGPT in-app browser and Chrome with WebMCP enabled

## Hackathon Details
- **Prize**: $35K + Codex Micros + ChatGPT Pro subs
- **Deadline**: Sep 3, 2026 1:00pm PDT
- **Platforms**: Deploy on ChatGPT Sites, Cloudflare, Vercel, Render, Netlify, Shopify, or any platform
- **Judging Criteria**:
  1. WebMCP Leverage — how thoroughly used
  2. Execution — working, coherent product
  3. Potential Impact — solves a real problem
  4. Creativity & Ambition — novel, different

## Showcase Examples (from OpenAI)
- Margin Editor — local note-taking for agent collaboration
- Fieldwork // 12 — beat machine with Codex
- WanderNote — trip planning itinerary
- Sunday Table — meal planning
- Paperie — greeting card design
- Webroom — photo editing
- Verdant Market — grocery shopping with agent cart
- Crossword Desk — crossword building
- Codex Modeling Studio — 3D modeling
- Cubecade — puzzle cube with agent controls

## Requirements for Submission
- [ ] Live URL working in ChatGPT in-app browser or Chrome w/ WebMCP
- [ ] WebMCP tools verified working
- [ ] Demo video with audio narration
- [ ] Public GitHub repo
- [ ] Open-source license file
- [ ] Project description (why WebMCP fits)
- [ ] Team members added
- [ ] Submission not draft

## Deployment Options
- Render, Cloudflare, Vercel, Shopify, Netlify (hackathon sponsors)
- Any deployment platform works

## TypeScript Types
Use `webmcp-types` npm package for TypeScript typings.
