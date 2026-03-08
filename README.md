# Agentic Kit

A community-driven collection of **custom AI agents, workflows, and test suites** designed to be dropped into any project. Built on [BMAD Method v6](https://github.com/bmad-code-org) as the runtime platform, but the value lives in what gets created on top of it: purpose-built agents you can use inside a BMAD project or take standalone into any AI IDE.

> **The idea:** BMAD gives you the engine. This repo is the garage where we build custom parts — agents, workflows, test harnesses — that anyone can grab and use.

---

## Running custom agents & workflows

Everything runs through **slash commands** in your AI IDE. Type `/` and pick one — no build step, no server.

### Run a custom agent (interactive session)

Agents are persistent personas with a menu of capabilities. You activate one, it greets you, shows its menu, and you pick what to do conversationally.

**Claude Code** — type `/` in the chat and pick:

```
/bmad-agent-bmm-pm              ← activates John, the Product Manager
/bmad-agent-bmm-architect       ← activates Winston, the Architect
/bmad-agent-bmm-dev             ← activates Amelia, the Developer
/bmad-agent-tea-tea             ← activates Murat, the Test Architect
```

**OpenCode** — same pattern, agents are in `.opencode/agents/`.

Once active, the agent shows a **numbered menu**. Interact by:

- **Number** — `1`, `2`, `3` …
- **Short code** — `CP` (Create PRD), `DS` (Dev Story), `SP` (Scan Project) …
- **Fuzzy text** — "create prd", "sprint planning", "scan project" …
- **`/bmad-help`** — ask what to do next: `/bmad-help I have a SaaS idea`

The agent stays in character until you dismiss it with `DA`.

### Run a custom workflow directly (one-shot)

If you don't need the agent persona, run a workflow straight from a slash command. No menu, no greeting — it jumps into the guided steps immediately.

```
/bmad-bmm-create-prd            ← PRD creation workflow
/bmad-bmm-epic-auto-runner      ← autonomous build cycle (dev → review → QA → retro)
/bmad-bmm-sprint-planning       ← sprint planning
/bmad-brainstorming             ← brainstorming session
/bmad-bmm-quick-spec            ← rapid tech spec for small features
```

### What happens under the hood

Both methods do the same thing:

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

Agent mode wraps the workflow in a persona with a menu. Direct mode skips the persona.

### Example: using the Forge agent to scan a project

```
1. Type:  /bmad-agent-toolkit-advisor    (or activate Forge however configured)
2. Forge greets you, shows menu
3. Type:  SP                              (Scan Project)
4. Forge asks for the project path
5. It reads your files, identifies your stack, cross-checks its memory
6. Returns 3-7 specific recommendations with "Want me to scaffold it? [y/n]"
7. Type:  SS                              (Save Session — persists findings to memory)
```

### Example: running the Epic Auto Runner workflow

```
1. Type:  /bmad-bmm-epic-auto-runner
2. The workflow asks which epics to run
3. It autonomously executes: dev story → code review → QA tests → retrospective
4. Outputs land in _bmad-output/implementation-artifacts/
5. No manual intervention between steps
```

---

## Table of Contents

1. [Running custom agents & workflows](#running-custom-agents--workflows) ← you are here
2. [Custom agents](#custom-agents)
3. [Custom workflows](#custom-workflows)
4. [Custom test suites (planned)](#custom-test-suites)
5. [How to install the kit](#how-to-install-the-kit)
6. [Adding a custom agent](#adding-a-custom-agent)
7. [Adding a custom workflow](#adding-a-custom-workflow)
8. [Adding a custom test suite](#adding-a-custom-test-suite)
9. [Project structure](#project-structure)
10. [All slash commands reference](#all-slash-commands-reference)
11. [BMAD platform reference](#bmad-platform-reference)
12. [Contributing](#contributing)

---

## Custom agents

Custom agents are the primary output of this repo. They follow BMAD conventions so they slot into any BMAD project, but they can also work standalone in any AI IDE that supports slash commands.

### Forge — Agentic Toolkit Advisor

**Status:** Live  
**Location:** `_bmad/agents/toolkit-advisor/`  
**Persona:** Forge — a senior engineer embedded in the agentic AI ecosystem  
**Memory:** persistent across sessions (`_memory/toolkit-advisor-sidecar/`)

| Menu command | What it does |
|---|---|
| **Scan Project** `[SP]` | Reads your project's actual files, identifies your stack, and recommends 3-7 high-value MCPs, skills, agents, or workflows — always specific to what's in *your* codebase |
| **Recommend Model** `[RM]` | Researches current AI models for a specific agent use case — benchmarks, cost, context window, speed — with an honest take on hype vs. real value |
| **Catalogue Entry** `[CE]` | Adds a curated entry (MCP / Skill / Agent / Workflow / Model) to the community catalogue with setup snippets |
| **BMAD Instruct** `[BI]` | Generates actionable directives for other BMAD agents (Architect, Dev, etc.) to adopt Forge's recommendations |
| **Save Session** `[SS]` | Persists findings and patterns to long-term memory |

Forge remembers past sessions, tracks cross-project patterns, and greets you with context from where you left off.

### Planned custom agents

These don't exist yet — they're what we want to build:

| Agent idea | Purpose | Use context |
|---|---|---|
| **Security Auditor** | Scan code for vulnerabilities, review auth patterns, check dependency CVEs | BMAD project or standalone |
| **DevOps Advisor** | Recommend CI/CD setup, Docker configs, deployment strategies per stack | BMAD project or standalone |
| **Data Pipeline Architect** | Design ETL flows, recommend data tools, model schema evolution | BMAD project or standalone |
| **API Design Reviewer** | Validate REST/GraphQL API design against standards, suggest improvements | BMAD project or standalone |
| **Dependency Auditor** | Analyze package.json / requirements.txt for bloat, outdated deps, conflicts | Standalone |
| **Migration Planner** | Plan framework upgrades, language migrations, database schema changes | BMAD project |

**Want to build one?** See [Adding a custom agent](#adding-a-custom-agent).

---

## Custom workflows

Custom workflows automate multi-step processes that BMAD's built-in workflows don't cover.

### Epic Auto Runner

**Status:** Live  
**Location:** `_bmad/bmm/workflows/4-implementation/epic-auto-runner/`  
**Slash command:** `/bmad-bmm-epic-auto-runner`

Runs an autonomous build cycle for specified epics — dev, code-review, QA, and retrospective — without manual intervention between steps. This is the "hands-off" mode for implementation.

### Planned custom workflows

| Workflow idea | What it automates |
|---|---|
| **Greenfield Bootstrapper** | From product brief → scaffold a full project (repo, CI, framework, DB) in one shot |
| **PR Review Pipeline** | Pull a GitHub PR, run code review + security scan + test coverage check, post summary |
| **Dependency Upgrade Cycle** | Scan deps → create upgrade plan → apply upgrades → run tests → report |
| **Multi-Repo Sync** | Propagate architecture decisions across multiple related repositories |
| **Release Checklist** | Validate all stories complete, tests passing, docs updated, changelog written |

---

## Custom test suites

Test suites validate that agents and workflows behave correctly — both the custom ones in this repo and any you create for your own projects.

### Planned test suites

| Test suite idea | What it validates |
|---|---|
| **Agent compliance checker** | Every agent has required activation steps, persona block, menu structure, config loading |
| **Workflow integrity validator** | Workflows have proper entry/exit points, all referenced files exist, templates are valid |
| **Agent regression suite** | Feed known prompts to agents, verify outputs match expected structure and content |
| **Cross-agent handoff tests** | Validate that artefacts produced by one agent (e.g. PRD from PM) are consumable by the next (e.g. Architect) |
| **Manifest consistency checker** | All agents/workflows in file system are registered in manifests and have IDE slash commands |

---

## How to install the kit

### Option A: Use in an existing BMAD project

Cherry-pick the custom pieces you want:

```bash
# Copy a custom agent into your project
cp -r agentic-kit/_bmad/agents/toolkit-advisor/ your-project/_bmad/agents/

# Copy the slash command too (Claude Code)
cp agentic-kit/.claude/commands/bmad-agent-toolkit-advisor.md your-project/.claude/commands/

# Add to your project's agent-manifest.csv
```

### Option B: Use as a standalone workspace

Clone the full repo — all BMAD platform agents and custom agents are pre-wired:

```bash
git clone https://github.com/<your-org>/agentic-kit.git
cd agentic-kit

# Set your name in the configs
# Edit _bmad/bmm/config.yaml → user_name: YourName
# (and optionally the other module configs)

# Open in Claude Code or OpenCode and start using slash commands
```

### Option C: Use standalone agents outside BMAD

Some agents (like Forge) are designed to work independently. Copy the agent file, its sidecar memory folder, and a config.yaml into any project — no full BMAD install needed.

---

## Adding a custom agent

This is the main way to contribute. You can build agents using the BMAD Builder tools, or manually.

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
    <!-- ... -->
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
    <!-- your custom menu items with exec/workflow/action handlers -->
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
│   ├── agents/                    ← CUSTOM stand-alone agents (your creations)
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
│   │           └── epic-auto-runner/   ← CUSTOM workflow: autonomous build cycle
│   ├── bmb/                       ← [BMAD platform] Builder tools
│   ├── cis/                       ← [BMAD platform] Creative Intelligence Suite
│   └── tea/                       ← [BMAD platform] Test Architecture Enterprise
│
├── _bmad-output/                  ← All generated artefacts
│   ├── planning-artifacts/
│   ├── implementation-artifacts/
│   ├── test-artifacts/            ← Test suite outputs
│   └── bmb-creations/             ← Agents/workflows built with the Builder
│
├── .claude/commands/              ← 78 slash commands (Claude Code)
├── .opencode/                     ← OpenCode agents + commands
│   ├── agents/                    ← 20 agent activators
│   └── commands/                  ← 58 workflow commands
│
└── docs/                          ← Project documentation
```

---

## All slash commands reference

<details>
<summary><strong>Agent activations (click to expand)</strong></summary>

```
/bmad-agent-bmm-analyst              ← Mary, Business Analyst
/bmad-agent-bmm-pm                   ← John, Product Manager
/bmad-agent-bmm-ux-designer          ← Sally, UX Designer
/bmad-agent-bmm-architect            ← Winston, Architect
/bmad-agent-bmm-sm                   ← Bob, Scrum Master
/bmad-agent-bmm-dev                  ← Amelia, Developer
/bmad-agent-bmm-qa                   ← Quinn, QA Engineer
/bmad-agent-bmm-quick-flow-solo-dev  ← Barry, Quick Flow
/bmad-agent-bmm-tech-writer          ← Paige, Tech Writer
/bmad-agent-bmb-agent-builder        ← Bond, Agent Builder
/bmad-agent-bmb-module-builder       ← Morgan, Module Builder
/bmad-agent-bmb-workflow-builder     ← Wendy, Workflow Builder
/bmad-agent-cis-brainstorming-coach
/bmad-agent-cis-creative-problem-solver
/bmad-agent-cis-design-thinking-coach
/bmad-agent-cis-innovation-strategist
/bmad-agent-cis-presentation-master
/bmad-agent-cis-storyteller
/bmad-agent-tea-tea                  ← Murat, Test Architect
/bmad-agent-bmad-master              ← BMad Master (orchestrator)
```

</details>

<details>
<summary><strong>Direct workflow commands (click to expand)</strong></summary>

```
# Analysis
/bmad-bmm-create-product-brief
/bmad-bmm-market-research
/bmad-bmm-domain-research
/bmad-bmm-technical-research

# Planning
/bmad-bmm-create-prd
/bmad-bmm-validate-prd
/bmad-bmm-edit-prd
/bmad-bmm-create-ux-design

# Solutioning
/bmad-bmm-create-architecture
/bmad-bmm-create-epics-and-stories
/bmad-bmm-check-implementation-readiness

# Implementation
/bmad-bmm-sprint-planning
/bmad-bmm-create-story
/bmad-bmm-dev-story
/bmad-bmm-code-review
/bmad-bmm-sprint-status
/bmad-bmm-correct-course
/bmad-bmm-retrospective
/bmad-bmm-epic-auto-runner

# Quick Flow
/bmad-bmm-quick-spec
/bmad-bmm-quick-dev

# QA & Testing
/bmad-bmm-qa-generate-e2e-tests
/bmad-tea-testarch-framework
/bmad-tea-testarch-atdd
/bmad-tea-testarch-automate
/bmad-tea-testarch-ci
/bmad-tea-testarch-test-design
/bmad-tea-testarch-test-review
/bmad-tea-testarch-trace
/bmad-tea-testarch-nfr
/bmad-tea-teach-me-testing

# Documentation
/bmad-bmm-document-project
/bmad-bmm-generate-project-context

# Creative / Ideation
/bmad-brainstorming
/bmad-cis-design-thinking
/bmad-cis-innovation-strategy
/bmad-cis-problem-solving
/bmad-cis-storytelling

# Meta — build your own
/bmad-bmb-create-agent
/bmad-bmb-create-module
/bmad-bmb-create-workflow
/bmad-bmb-validate-agent
/bmad-bmb-validate-workflow
/bmad-bmb-validate-module

# Utility
/bmad-help
/bmad-party-mode
```

</details>

---

## BMAD platform reference

The BMAD platform ships these built-in agents and modules. They're included in this repo as the runtime foundation — you don't need to build or modify them to use the kit, but knowing what's there helps you decide what custom agents to create on top.

<details>
<summary><strong>Built-in agents (click to expand)</strong></summary>

| Module | Agent | Persona | Role |
|---|---|---|---|
| core | BMad Master 🧙 | BMad Master | Orchestrator, lists all tasks and workflows |
| bmm | Analyst 📊 | Mary | Market/domain/technical research, product briefs |
| bmm | Product Manager 📋 | John | PRD creation/validation, epics & stories |
| bmm | UX Designer 🎨 | Sally | UX design specifications |
| bmm | Architect 🏗️ | Winston | Technical architecture, solution design |
| bmm | Scrum Master 🏃 | Bob | Sprint planning, story prep, retrospectives |
| bmm | Developer 💻 | Amelia | TDD story implementation |
| bmm | QA Engineer 🧪 | Quinn | API & E2E test generation |
| bmm | Quick Flow Solo Dev 🚀 | Barry | Rapid spec + implementation |
| bmm | Tech Writer 📚 | Paige | Documentation, diagrams |
| bmb | Agent Builder 🤖 | Bond | Create, edit, validate BMAD agents |
| bmb | Module Builder 🏗️ | Morgan | Create, edit, validate BMAD modules |
| bmb | Workflow Builder 🔄 | Wendy | Create, edit, validate BMAD workflows |
| cis | Brainstorming Coach 🧠 | Carson | Guided ideation sessions |
| cis | Creative Problem Solver 🔬 | Dr. Quinn | TRIZ, Systems Thinking |
| cis | Design Thinking Coach 🎨 | Maya | Empathy mapping, prototyping |
| cis | Innovation Strategist ⚡ | Victor | Blue Ocean Strategy |
| cis | Presentation Master 🎨 | Caravaggio | Slide design, visual storytelling |
| cis | Storyteller 📖 | Sophia | Narrative strategy |
| tea | Test Architect 🧪 | Murat | Risk-based testing, ATDD, CI/CD, NFR assessment |

</details>

<details>
<summary><strong>Built-in workflow phases (click to expand)</strong></summary>

```
1. Analysis        → product brief, market/domain/technical research
2. Planning        → PRD creation/validation/editing, UX design
3. Solutioning     → architecture, epics & stories, implementation readiness
4. Implementation  → sprint planning, story creation, dev, code review, QA, retro
   Quick Flow      → quick-spec + quick-dev (solo dev shortcut)
```

Full workflow command list: type `/bmad-bmm-` in Claude Code and browse.

</details>

<details>
<summary><strong>What's custom vs. what's BMAD</strong></summary>

| Layer | Source | What it is |
|---|---|---|
| **BMAD Platform** | Installed via `npx bmad-method` | Core runtime, built-in agents, standard lifecycle workflows, builder tools |
| **Custom agents** | **This repo** | New specialist agents (e.g. Forge) — usable inside BMAD projects or standalone |
| **Custom workflows** | **This repo** | New automated processes (e.g. epic auto-runner) |
| **Custom test suites** | **This repo** | Validation harnesses, compliance checks, regression tests |

</details>

<details>
<summary><strong>Supported IDEs</strong></summary>

| IDE | How it works |
|---|---|
| **Claude Code** | Slash commands auto-loaded from `.claude/commands/` |
| **OpenCode** | Agents in `.opencode/agents/`, commands in `.opencode/commands/` |

</details>

---

## Contributing

This repo is community-driven. The whole system is plain text — no compilation, no dependencies.

### What we're looking for

| Priority | Contribution type | Description |
|---|---|---|
| **High** | Custom agents | New specialist agents for specific domains or workflows — the main value of this repo |
| **High** | Custom test suites | Validation tools that ensure agents and workflows work correctly |
| **Medium** | Custom workflows | Automated multi-step processes that solve real problems |
| **Medium** | IDE integrations | Support for Cursor, Windsurf, Zed, VS Code Copilot Chat |
| **Welcome** | Bug fixes | Fixes to existing agents or workflows |
| **Welcome** | Documentation | Improve this README, add usage examples, write guides |

### How to contribute

1. **Fork** the repo and create a branch: `git checkout -b feat/my-agent`
2. **Build your agent/workflow/test** — use the BMAD Builder tools (`/bmad-bmb-create-agent`, `/bmad-bmb-create-workflow`) or create files manually following the patterns above
3. **Validate** — run `/bmad-bmb-validate-agent` or `/bmad-bmb-validate-workflow` to check compliance
4. **Register** — add entries to the relevant manifest CSV and create slash commands for both `.claude/commands/` and `.opencode/commands/` (or `.opencode/agents/` for agents)
5. **Open a PR** — describe what the agent/workflow does, when to use it, and whether it's meant for BMAD projects, standalone use, or both

### Agent contribution checklist

- [ ] Agent `.md` file with proper activation, persona, and menu sections
- [ ] Slash command in `.claude/commands/` and `.opencode/agents/`
- [ ] Row added to `_bmad/_config/agent-manifest.csv`
- [ ] Sidecar memory folder if the agent needs persistent state
- [ ] Validation passes (`/bmad-bmb-validate-agent`)
- [ ] Brief description in this README under [Custom agents](#custom-agents)

### Ideas board

If you're looking for something to build, here's what would be immediately valuable:

- **Security Auditor agent** — scan code for vulnerabilities, review auth patterns
- **DevOps Advisor agent** — recommend CI/CD, Docker, deployment per stack
- **Manifest consistency test suite** — verify all files match their manifest registrations
- **Agent regression test suite** — feed known inputs, validate output structure
- **Cross-agent handoff tests** — ensure artefacts flow correctly between agents
- **Cursor/Windsurf IDE integration** — port the slash command pattern

---

## License

TBD

---

## Acknowledgements

Built on the [BMAD Method](https://github.com/bmad-code-org) platform (v6.0.4). BMAD modules included as the runtime foundation:

- `core` + `bmm` — BMAD Core & Build Methodology (built-in)
- `bmb` — [bmad-builder](https://github.com/bmad-code-org/bmad-builder)
- `cis` — [Creative Intelligence Suite](https://github.com/bmad-code-org/bmad-module-creative-intelligence-suite)
- `tea` — [Test Architecture Enterprise](https://github.com/bmad-code-org/bmad-method-test-architecture-enterprise)
