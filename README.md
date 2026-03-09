# Agentic Kit

A community-driven collection of **custom AI agents, workflows, and test suites** you can drop into any project.

This repo exists to **build, share, and catalogue** reusable agentic tools — custom agents with specific expertise, automated workflows that solve real problems, and test suites that keep everything reliable. Every piece is designed to be grabbed individually and used inside a [BMAD Method](https://github.com/bmad-code-org) project or standalone in any AI IDE.

> **This is not a BMAD tutorial.** BMAD is the runtime platform underneath. This repo is about the custom things we build on top — and how you use them.

---

## Getting started

### New project (no BMAD yet)

The fastest way is to clone this repo directly — it already has a full BMAD install plus all custom agents and workflows pre-wired:

```bash
git clone https://github.com/<your-org>/agentic-kit.git my-project
cd my-project

# Set your name
# Edit _bmad/bmm/config.yaml → user_name: YourName

# Open in Claude Code or OpenCode — all slash commands are ready to go
```

This gives you the full BMAD platform, every custom agent (Forge), every custom workflow (Epic Auto Runner), and all IDE slash commands in one shot. Start building your product from here.

<details>
<summary>Alternative: install BMAD separately and copy custom pieces in</summary>

If you want a clean project without the kit's extras (planned items, docs, contribution scaffolding), install BMAD on its own and cherry-pick what you need:

```bash
# 1. Create your project and install BMAD
mkdir my-project && cd my-project
git init
npx bmad-method
# Follow the prompts — pick your modules (bmm, cis, tea, etc.)

# 2. Clone agentic-kit and copy the custom pieces you want
git clone https://github.com/<your-org>/agentic-kit.git /tmp/agentic-kit

# Copy a custom agent (e.g. Forge)
cp -r /tmp/agentic-kit/_bmad/agents/toolkit-advisor/ ./_bmad/agents/
cp -r /tmp/agentic-kit/_bmad/_memory/toolkit-advisor-sidecar/ ./_bmad/_memory/

# Copy a custom workflow (e.g. Epic Auto Runner)
cp -r /tmp/agentic-kit/_bmad/bmm/workflows/4-implementation/epic-auto-runner/ \
      ./_bmad/bmm/workflows/4-implementation/

# Copy the slash commands for your IDE
# Claude Code:
cp /tmp/agentic-kit/.claude/commands/bmad-bmm-epic-auto-runner.md ./.claude/commands/
# OpenCode:
cp /tmp/agentic-kit/.opencode/commands/bmad-bmm-epic-auto-runner.md ./.opencode/commands/

# 3. Register in manifests
# Add agent row to _bmad/_config/agent-manifest.csv
# Add workflow row to _bmad/_config/workflow-manifest.csv

# 4. Set your name
# Edit _bmad/bmm/config.yaml → user_name: YourName
```

</details>

### Existing BMAD project

If you already have a `_bmad/` folder in your project, you just copy the custom pieces in:

```bash
# From the agentic-kit repo, copy what you need:

# Custom agent (Forge)
cp -r agentic-kit/_bmad/agents/toolkit-advisor/ your-project/_bmad/agents/
cp -r agentic-kit/_bmad/_memory/toolkit-advisor-sidecar/ your-project/_bmad/_memory/

# Custom workflow (Epic Auto Runner)
cp -r ~/repos/agentic-kit/_bmad/bmm/workflows/4-implementation/epic-auto-runner/ \
      _bmad/bmm/workflows/4-implementation/

# Slash commands — copy only the ones for the pieces you added
# Claude Code:
cp ~/repos/agentic-kit/.claude/commands/bmad-bmm-epic-auto-runner.md .claude/commands/
# OpenCode:
cp ~/repos/agentic-kit/.opencode/commands/bmad-bmm-epic-auto-runner.md .opencode/command/

# Register: add rows to your project's agent-manifest.csv and workflow-manifest.csv
```

That's it — no install step, no rebuild. The next time you open the project in your AI IDE, the new slash commands are available.

### Use a standalone agent outside BMAD

Some agents (like Forge) are designed to work without the full BMAD platform. You only need three things:

1. The agent file — `_bmad/agents/toolkit-advisor/toolkit-advisor.agent.yaml`
2. The sidecar memory — `_bmad/_memory/toolkit-advisor-sidecar/`
3. A minimal `config.yaml` with `user_name` and `communication_language`

Copy those into any project, wire up a slash command for your IDE, and it works.

---

## Custom agents

<details>
<summary><strong>Forge — Agentic Toolkit Advisor</strong> · Scans projects, recommends MCPs/skills/models · <code>Live</code></summary>

**What it is:** A standalone agent that scans any project, identifies your tech stack, and recommends the right MCPs, skills, AI models, and workflows — specific to what's actually in your codebase. It has persistent memory across sessions and tracks patterns across projects.

**When to use it:**
- Starting a new project and want to know which agentic tools fit your stack
- Evaluating which AI model to use for a specific agent use case
- Building a catalogue of vetted tools for your team or community
- Bridging recommendations into other BMAD agents (e.g. telling the Architect to adopt a specific MCP)

**How to use it:**

```
1. Activate Forge (via its slash command or agent config)
2. Forge greets you with context from your last session
3. Pick from the menu:

   [SP] Scan Project
        → Give it a project path
        → It reads your files, identifies stack, cross-checks memory
        → Returns 3-7 specific recommendations
        → "Want me to scaffold it? [y/n]" for each

   [RM] Recommend Model
        → Describe the agent use case and constraints (cost, latency, context)
        → It researches current benchmarks online
        → Returns top 2-3 candidates with honest tradeoff analysis

   [CE] Catalogue Entry
        → Add a vetted tool/MCP/skill to the community catalogue
        → Structured format with setup snippets ready for reuse

   [BI] BMAD Instruct
        → Generates directives for other BMAD agents to adopt recommendations
        → Presents instructions for your review before handoff

   [SS] Save Session
        → Persists everything to long-term memory
        → Next time you activate Forge, it remembers where you left off
```

**Location:** `_bmad/agents/toolkit-advisor/`  
**Memory:** `_bmad/_memory/toolkit-advisor-sidecar/` (persistent across sessions)  
**Docs:** [creation report](_bmad-output/bmb-creations/agent-completion-toolkit-advisor.md) | [validation report](_bmad-output/bmb-creations/validation-report-toolkit-advisor.md)  
**Status:** Live

</details>

---

## Custom workflows

<details>
<summary><strong>Epic Auto Runner — Autonomous Build Cycle</strong> · Batch-executes epics hands-off · <code>Live</code></summary>

**What it is:** A fully autonomous workflow that takes specified epics and runs the complete implementation cycle — story creation, dev implementation, code review, QA test generation, and retrospective — without manual intervention between steps. Each sub-step is executed by a dedicated BMAD agent (SM, Dev, QA) spawned with fresh context, and every successful step is committed via Git MCP.

**When to use it:**
- You have a sprint plan with stories ready and want to batch-execute them hands-off
- You trust the planning artefacts (PRD, architecture, stories) are solid and want to go straight to autonomous implementation
- Running overnight or background builds where you review results after

**How to use it:**

```
1. Run:  /bmad-bmm-epic-auto-runner
2. The workflow validates Git MCP is installed and sprint-status.yaml exists
3. You enter which epic numbers to process (e.g. "2 3")
4. It shows a run plan and you confirm with [C]
5. From here it's fully autonomous — no more input needed
6. Outputs land in _bmad-output/implementation-artifacts/
7. A final summary report shows successes and failures per story
```

**The pipeline per epic:**

```
For each story:  Create Story (SM) → Dev Story (Dev) → Code Review (Dev) → git commit each
Then per epic:   QA Tests (QA) → Retrospective (SM) → git commit each
On any failure:  Log error → skip to next story/epic → never block
```

See the **[full process flow with Mermaid chart, step details, and design decisions](_bmad-output/bmb-creations/workflows/autonomous-build-cycle/epic-auto-runner-process-flow.md)**.

**Location:** `_bmad/bmm/workflows/4-implementation/epic-auto-runner/`  
**Slash command:** `/bmad-bmm-epic-auto-runner`  
**Prerequisites:** Git MCP installed, `sprint-status.yaml` exists (run `/bmad-bmm-sprint-planning` first)  
**Docs:** [process flow & chart](_bmad-output/bmb-creations/workflows/autonomous-build-cycle/epic-auto-runner-process-flow.md) | [creation plan](_bmad-output/bmb-creations/workflows/autonomous-build-cycle/workflow-plan-autonomous-build-cycle.md) | [validation report](_bmad-output/bmb-creations/workflows/autonomous-build-cycle/validation-report-epic-auto-runner-2026-03-01.md)  
**Status:** Live

</details>

---

## Custom test suites (planned)

Test suites that validate agents and workflows behave correctly. None are built yet — this is where contributions are most needed.

<details>
<summary><strong>Agent compliance checker</strong> · Validates agent structure and required fields · <code>Planned</code></summary>

**What it would do:** Verify every agent has the required activation steps, persona block, menu structure, and config loading. Run it before merging a new agent PR.

</details>

<details>
<summary><strong>Workflow integrity validator</strong> · Checks paths, entry/exit points, templates · <code>Planned</code></summary>

**What it would do:** Check that workflows have proper entry/exit points, all referenced file paths exist, and templates are valid. Catch broken references before they hit a user.

</details>

<details>
<summary><strong>Agent regression suite</strong> · Catches behavioural regressions via known prompts · <code>Planned</code></summary>

**What it would do:** Feed known prompts to agents, verify outputs match expected structure and content. Catch behavioural regressions when agent files are edited.

</details>

<details>
<summary><strong>Cross-agent handoff tests</strong> · Validates artefact compatibility between agents · <code>Planned</code></summary>

**What it would do:** Validate that artefacts produced by one agent (e.g. a PRD from the PM) are consumable by the next agent in the pipeline (e.g. the Architect). Ensure the workflow chain doesn't break.

</details>

<details>
<summary><strong>Manifest consistency checker</strong> · Finds orphaned agents missing from manifests · <code>Planned</code></summary>

**What it would do:** Verify all agents and workflows on disk are registered in their manifest CSVs and have corresponding IDE slash commands. Catch "orphaned" agents that exist but aren't wired up.

</details>

---

## Project structure

```
agentic-kit/
│
├── _bmad/
│   ├── agents/                    ← CUSTOM stand-alone agents
│   │   └── toolkit-advisor/       ← Forge: scans projects, recommends tools
│   │
│   ├── _config/                   ← Manifests for all agents, workflows, tasks
│   ├── _memory/                   ← Persistent sidecar knowledge per agent
│   │   └── toolkit-advisor-sidecar/
│   │
│   ├── core/                      ← [BMAD platform] Core runtime
│   ├── bmm/                       ← [BMAD platform] Build Methodology Module
│   │   └── workflows/
│   │       └── 4-implementation/
│   │           └── epic-auto-runner/   ← CUSTOM workflow
│   ├── bmb/                       ← [BMAD platform] Builder tools
│   ├── cis/                       ← [BMAD platform] Creative Intelligence Suite
│   └── tea/                       ← [BMAD platform] Test Architecture Enterprise
│
├── _bmad-output/                  ← All generated artefacts
│   ├── planning-artifacts/
│   ├── implementation-artifacts/
│   ├── test-artifacts/
│   └── bmb-creations/
│
├── .claude/commands/              ← Slash commands (Claude Code)
├── .opencode/                     ← OpenCode agents + commands
└── docs/
```

---

## Contributing

This repo is community-driven. The whole system is plain text — no compilation, no dependencies.

### What we're looking for

| Priority | Type | Description |
|---|---|---|
| **High** | Custom agents | New specialist agents — the main value of this repo |
| **High** | Custom test suites | Validation tools for agents and workflows |
| **Medium** | Custom workflows | Automated multi-step processes |
| **Medium** | IDE integrations | Support for Cursor, Windsurf, Zed |
| **Welcome** | Bug fixes / docs | Improvements to existing pieces |

### Contribution checklist

- [ ] Agent/workflow file following the patterns above
- [ ] Slash command in `.claude/commands/` and `.opencode/agents/` or `.opencode/commands/`
- [ ] Row added to the relevant manifest CSV (`agent-manifest.csv` or `workflow-manifest.csv`)
- [ ] Validation passes (`/bmad-bmb-validate-agent` or `/bmad-bmb-validate-workflow`)
- [ ] Description added to this README under the appropriate custom section

---

<details>
<summary><strong>BMAD platform reference (click to expand)</strong></summary>

The BMAD platform provides the runtime foundation. It ships with built-in agents and workflows that cover a standard product lifecycle. You don't need to modify them — they're here as the base layer.

**Built-in agents:** BMad Master, Analyst (Mary), PM (John), UX Designer (Sally), Architect (Winston), Scrum Master (Bob), Developer (Amelia), QA (Quinn), Quick Flow (Barry), Tech Writer (Paige), Agent Builder (Bond), Module Builder (Morgan), Workflow Builder (Wendy), Brainstorming Coach (Carson), Creative Problem Solver (Dr. Quinn), Design Thinking Coach (Maya), Innovation Strategist (Victor), Presentation Master (Caravaggio), Storyteller (Sophia), Test Architect (Murat)

**Built-in workflow phases:** Analysis → Planning → Solutioning → Implementation (+ Quick Flow shortcut)

**Supported IDEs:** Claude Code (`.claude/commands/`), OpenCode (`.opencode/agents/` + `.opencode/commands/`)

**Install BMAD:** `npx bmad-method`

**Modules included:**
- `core` + `bmm` — BMAD Core & Build Methodology (built-in, v6.0.4)
- `bmb` — [bmad-builder](https://github.com/bmad-code-org/bmad-builder) (v0.1.6)
- `cis` — [Creative Intelligence Suite](https://github.com/bmad-code-org/bmad-module-creative-intelligence-suite) (v0.1.8)
- `tea` — [Test Architecture Enterprise](https://github.com/bmad-code-org/bmad-method-test-architecture-enterprise) (v1.4.1)

</details>

---

## License

TBD
