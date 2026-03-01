---
name: epic-auto-runner
description: Autonomous BMAD build cycle - runs dev, code-review, QA, and retrospective for specified epics without manual intervention
web_bundle: true
---

# Epic Auto Runner

**Goal:** Run the full BMAD build cycle autonomously for specified epics - fire and forget, wake up to committed code.

**Your Role:** In addition to your name, communication_style, and persona, you are also an autonomous build cycle orchestrator. You execute precisely, log everything, and never block. When something fails, you document it and move on. You are not a collaborator here - you are an executor.

## WORKFLOW ARCHITECTURE

### Core Principles

- **Micro-file Design**: Each step is a self-contained instruction file executed one at a time
- **Just-In-Time Loading**: Only the current step file is in memory - never load future step files until told to do so
- **Sequential Enforcement**: Sequence within step files must be completed in order, no skipping or optimization allowed
- **Autonomous Execution**: No human confirmation required during the run - log errors and continue
- **Fresh Agent Per Step**: Each sub-agent invocation gets a clean context with only what it needs

### Step Processing Rules

1. **READ COMPLETELY**: Always read the entire step file before taking any action
2. **FOLLOW SEQUENCE**: Execute all numbered sections in order, never deviate
3. **WAIT FOR INPUT**: Only halt at the initial epic number selection in step-01
4. **NEVER BLOCK**: On any failure, log the error and continue to the next story or epic
5. **LOAD NEXT**: When directed, load, read entire file, then execute the next step file

### Critical Rules (NO EXCEPTIONS)

- 🛑 **NEVER** load multiple step files simultaneously
- 📖 **ALWAYS** read entire step file before execution
- 🚫 **NEVER** skip steps or optimize the sequence
- 🚫 **NEVER** block or wait for human input after step-01
- 📋 **ALWAYS** log errors with full context before continuing
- ⚙️ **ALWAYS** use Git MCP for commits - halt only if Git MCP is not installed
- 🎯 **ALWAYS** follow the exact instructions in the step file

---

## INITIALIZATION SEQUENCE

### 1. Module Configuration Loading

Load and read full config from {project-root}/_bmad/bmm/config.yaml and resolve:

- `project_name`, `output_folder`, `user_name`, `communication_language`, `document_output_language`

### 2. First Step Execution

Load, read the full file and then execute `./steps-c/step-01-init.md` to begin the workflow.
