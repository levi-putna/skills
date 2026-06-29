# mermaid-diagrams

Create clear, professional Mermaid diagrams with consistent styling for technical documentation.

Use when you need architecture visuals, process flows, sequence diagrams, ER diagrams, or when an existing Mermaid chart needs to look more polished.

## What it does

The agent will:

- Pick the right diagram type for the content
- Apply a curated light theme (`%%{init}`) with soft slate lines instead of default black
- Keep diagrams readable (≤15 nodes, meaningful labels, semantic colours)
- Use `classDef` for reusable styling and modern syntax (`flowchart`, `stateDiagram-v2`)
- Add captions when embedding in markdown docs
- Split oversized diagrams into a focused series

See [SKILL.md](./SKILL.md) for the full workflow and quality checklist. See [examples.md](./examples.md) for before/after patterns.

## When to use it

Good fits:

- Drawing a system architecture or data flow for docs
- Improving an ugly default Mermaid export
- Adding diagrams during `technical-documentation` or `refining-docs` work
- Explaining a process, API interaction, or state machine in chat

Not for:

- Non-Mermaid charting (D3, Chart.js, matplotlib)
- Heavy PNG/SVG export pipelines — output source; use `mmdc` if export is needed

## Install

Uses [@levi-putna/agent-kit](https://github.com/levi-putna/agent-kit) via `npx` — no global install required.

### Global install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill mermaid-diagrams --global
```

### Project install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill mermaid-diagrams --project
```

### Manual install

| Agent | Path |
|---|---|
| Claude Code (global) | `~/.claude/skills/mermaid-diagrams/` |
| Cursor | `.cursor/skills/mermaid-diagrams/` |

Copy the entire `mermaid-diagrams` folder including `SKILL.md`.

## Example

**Prompt:** *"Draw a diagram showing how the web app calls the API and database."*

**Output:** A themed `flowchart LR` with subgraphs for Client and Backend, camelCase node IDs, labelled edges, green database node, and a caption — not a default black-line three-box chart.

## Uninstall

```sh
npx @levi-putna/agent-kit@latest remove mermaid-diagrams
```
