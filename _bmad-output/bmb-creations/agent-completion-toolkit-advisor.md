## Agent Creation Complete! 🎉

### Agent Summary

- **Name:** Forge (toolkit-advisor)
- **Type:** Stand-alone agent with sidecar (persistent memory)
- **Purpose:** Identify the delta between a developer's current setup and what the modern AI/agentic ecosystem makes possible — recommending and scaffolding the right skills, MCPs, agents, workflows, and models for a specific stack
- **Status:** ✅ Ready for installation

### File Locations

- **Agent Config:** `_bmad/agents/toolkit-advisor/toolkit-advisor.agent.yaml`
- **Sidecar Memory:** `_bmad/_memory/toolkit-advisor-sidecar/memories.md`
- **Sidecar Instructions:** `_bmad/_memory/toolkit-advisor-sidecar/instructions.md`
- **Manifest Entry:** `_bmad/_config/agent-manifest.csv`

### Commands

| Code | Command | Description |
|------|---------|-------------|
| `[SP]` | Scan Project | Scan a project and recommend skills, MCPs, agents & workflows |
| `[RM]` | Recommend Model | Research and recommend the best AI model for an agent use case |
| `[CE]` | Catalogue Entry | Add a curated tool, skill or MCP entry to the catalogue repo |
| `[BI]` | BMAD Instruct | Instruct BMAD agents to adopt Forge recommendations |
| `[SS]` | Save Session | Save session findings and patterns to memory |

### Installation

Package your agent as a standalone module with a `module.yaml` file.

**Module structure:**
```
my-custom-stuff/
├── module.yaml              ← include: code, name, version
├── agents/
│   └── toolkit-advisor/
│       └── toolkit-advisor.agent.yaml
```

The sidecar folder is installed separately into `_bmad/_memory/toolkit-advisor-sidecar/` by the BMAD installer.

See: https://github.com/bmad-code-org/BMAD-METHOD/blob/main/docs/modules/bmb-bmad-builder/custom-content-installation.md#standalone-content-agents-workflows-tasks-tools-templates-prompts

### Quick Start

1. Create a module folder (e.g., `my-agentic-kit/`)
2. Add `module.yaml` with `code`, `name`, `version`
3. Copy `_bmad/agents/toolkit-advisor/` into `agents/` in your module folder
4. Install via BMAD installer (new project) or "Modify BMAD Installation" (existing)
5. Start Forge and try: `[SP]` to scan your project

### Creation Date

2026-03-01

### Workflow Status

✅ Agent creation workflow completed successfully.
