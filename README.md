# Agentic Kit

A community-driven collection of **custom AI agents, workflows, and test suites** you can drop into any project.

This repo exists to **build, share, and catalogue** reusable agentic tools — custom agents with specific expertise, automated workflows that solve real problems, and test suites that keep everything reliable. Every piece is designed to be grabbed individually and used inside a [BMAD Method](https://github.com/bmad-code-org) project or standalone in any AI IDE.

> **This is not a BMAD tutorial.** BMAD is the runtime platform underneath. This repo is about the custom things we build on top — and how you use them.

---

## Custom agents

### Forge — Agentic Toolkit Advisor

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

---

## Custom workflows

### Epic Auto Runner — Autonomous Build Cycle

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

---

## Custom test suites (planned)

Test suites that validate agents and workflows behave correctly. None are built yet — this is where contributions are most needed.

### Agent compliance checker

**What it would do:** Verify every agent has the required activation steps, persona block, menu structure, and config loading. Run it before merging a new agent PR.

### Workflow integrity validator

**What it would do:** Check that workflows have proper entry/exit points, all referenced file paths exist, and templates are valid. Catch broken references before they hit a user.

### Agent regression suite

**What it would do:** Feed known prompts to agents, verify outputs match expected structure and content. Catch behavioural regressions when agent files are edited.

### Cross-agent handoff tests

**What it would do:** Validate that artefacts produced by one agent (e.g. a PRD from the PM) are consumable by the next agent in the pipeline (e.g. the Architect). Ensure the workflow chain doesn't break.

### Manifest consistency checker

**What it would do:** Verify all agents and workflows on disk are registered in their manifest CSVs and have corresponding IDE slash commands. Catch "orphaned" agents that exist but aren't wired up.

---

## Planned custom agents

These don't exist yet — they're what we want to build next:

| Agent | What it would do | When to use it | Context |
|---|---|---|---|
| **Security Auditor** | Scan code for vulnerabilities, review auth patterns, check dependency CVEs | Before releases, during code review, or on-demand security check | BMAD project or standalone |
| **DevOps Advisor** | Recommend CI/CD setup, Docker configs, deployment strategies per stack | Setting up infrastructure for a new project or evaluating current setup | BMAD project or standalone |
| **Data Pipeline Architect** | Design ETL flows, recommend data tools, model schema evolution | When adding data processing to a project | BMAD project or standalone |
| **API Design Reviewer** | Validate REST/GraphQL API design against standards, suggest improvements | During architecture phase or before API goes public | BMAD project or standalone |
| **Dependency Auditor** | Analyze package.json/requirements.txt for bloat, outdated deps, conflicts | Regular maintenance, pre-release checks | Standalone |
| **Migration Planner** | Plan framework upgrades, language migrations, database schema changes | When upgrading major versions or switching tech | BMAD project |

## Planned custom workflows

| Workflow | What it would automate | When to use it |
|---|---|---|
| **Greenfield Bootstrapper** | From product brief → scaffold a full project (repo, CI, framework, DB) in one shot | Starting a brand new project from planning artefacts |
| **PR Review Pipeline** | Pull a GitHub PR, run code review + security scan + test coverage check, post summary | On every PR, automated quality gate |
| **Dependency Upgrade Cycle** | Scan deps → create upgrade plan → apply upgrades → run tests → report | Monthly maintenance or before major releases |
| **Multi-Repo Sync** | Propagate architecture decisions across multiple related repositories | Monorepo-style consistency across separate repos |
| **Release Checklist** | Validate all stories complete, tests passing, docs updated, changelog written | Before every release |

**Want to build any of these?** See [Adding a custom agent](#adding-a-custom-agent) or [Adding a custom workflow](#adding-a-custom-workflow) below.

---

## How to use the kit

### How agents and workflows run

Everything runs through **slash commands** in your AI IDE. Type `/` and pick one — no build step, no server.

There are two invocation styles:

**Activate an agent** (interactive, menu-driven) — the agent greets you, shows a menu, and you pick what to do conversationally. It stays in character until you dismiss it.

**Run a workflow directly** (one-shot) — no persona, no menu. It jumps straight into the guided steps.

Both do the same thing under the hood:

```
Slash command (.claude/commands/ or .opencode/)
    │
    ▼
Loads the agent or workflow file (.md or .yaml) from _bmad/
    │
    ▼
The LLM reads the instructions and follows them step by step
    │
    ▼
Outputs are saved to _bmad-output/
```

### Install options

**Option A: Cherry-pick into an existing BMAD project**

```bash
# Copy a custom agent into your project
cp -r agentic-kit/_bmad/agents/toolkit-advisor/ your-project/_bmad/agents/

# Copy the slash command too (Claude Code)
cp agentic-kit/.claude/commands/bmad-agent-toolkit-advisor.md your-project/.claude/commands/

# Add to your project's agent-manifest.csv
```

**Option B: Clone as a standalone workspace**

```bash
git clone https://github.com/<your-org>/agentic-kit.git
cd agentic-kit

# Edit _bmad/bmm/config.yaml → user_name: YourName
# Open in Claude Code or OpenCode and start using slash commands
```

**Option C: Use standalone agents outside BMAD**

Some agents (like Forge) work independently. Copy the agent file, its sidecar memory folder, and a config.yaml into any project — no full BMAD install needed.

---

## Adding a custom agent

This is the main way to contribute. You can use the BMAD Builder tools or create files manually.

### Using the BMAD Builder (recommended)

```
1. Open this repo in Claude Code
2. Run:  /bmad-agent-bmb-agent-builder
3. Pick: [CA] Create a new BMAD agent
4. Follow the guided workflow — it handles structure, compliance, and validation
5. Your new agent lands in _bmad-output/bmb-creations/
6. Move it to the right location and register it
```

### Manual creation

Every agent needs these pieces:

**1. Agent file** — `_bmad/agents/<your-agent>/` or `_bmad/<module>/agents/<your-agent>.md`

```markdown
---
name: "my-agent"
description: "What this agent does"
---

You must fully embody this agent's persona and follow all activation instructions...

<agent id="my-agent" name="AgentName" title="Agent Title" icon="🔧">
  <activation critical="MANDATORY">
    <step n="1">Load persona from this current agent file</step>
    <step n="2">Load and read config.yaml, store session variables</step>
    <step n="3">Remember: user's name is {user_name}</step>
    <step n="4">Show greeting, display menu</step>
    <step n="5">STOP and WAIT for user input</step>
  </activation>

  <persona>
    <role>Your agent's role</role>
    <identity>Background and expertise</identity>
    <communication_style>How they talk</communication_style>
    <principles>What they believe</principles>
  </persona>

  <menu>
    <item cmd="MH">[MH] Redisplay Menu Help</item>
    <item cmd="CH">[CH] Chat with the Agent</item>
    <!-- your custom menu items -->
    <item cmd="DA">[DA] Dismiss Agent</item>
  </menu>
</agent>
```

**2. Slash command** — `.claude/commands/bmad-agent-<name>.md` (and `.opencode/agents/` for OpenCode)

```markdown
---
name: 'my-agent'
description: 'my-agent agent'
---

<agent-activation CRITICAL="TRUE">
1. LOAD the FULL agent file from {project-root}/_bmad/agents/<your-agent>.md
2. READ its entire contents
3. FOLLOW every step in the <activation> section precisely
4. DISPLAY the welcome/greeting as instructed
5. PRESENT the numbered menu
6. WAIT for user input before proceeding
</agent-activation>
```

**3. Register** — add a row to `_bmad/_config/agent-manifest.csv`

**4. Validate** — run `/bmad-bmb-validate-agent` to check compliance

### Optional: sidecar memory

For agents that need persistent state (like Forge), create a sidecar folder:

```
_bmad/_memory/<agent-name>-sidecar/
  instructions.md     ← operating protocols loaded on every activation
  memories.md          ← long-term brain, updated via save-session
```

---

## Adding a custom workflow

### Using the BMAD Builder

```
1. Run:  /bmad-agent-bmb-workflow-builder
2. Pick: create-workflow
3. Follow the guided workflow
4. Move the output to the right location
```

### Manual creation

A workflow is a `.md` or `.yaml` file in `_bmad/<module>/workflows/<workflow-name>/`:

- **`.md` workflows** — direct instructions the agent reads and executes step by step
- **`.yaml` workflows** — structured configs processed by the BMAD workflow engine (`_bmad/core/tasks/workflow.xml`)

Register in `_bmad/_config/workflow-manifest.csv` and create a slash command in `.claude/commands/`.

---

## Adding a custom test suite

Test suites live in `_bmad-output/test-artifacts/` (outputs) and their definitions should live alongside the thing they test. The general pattern:

1. **Define what "correct" looks like** — expected structure, required fields, naming conventions
2. **Write a validation workflow** that checks those constraints (can be a BMAD workflow or a standalone script)
3. **Register it** so it can be invoked via slash command
4. **Document** when to run it (on PR, after agent creation, before release, etc.)

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
