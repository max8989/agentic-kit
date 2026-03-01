---
stepsCompleted: ['step-01-discovery', 'step-02-classification', 'step-03-requirements', 'step-04-tools']
created: 2026-03-01
status: CLASSIFICATION
---

# Workflow Creation Plan

## Discovery Notes

**User's Vision:**
An autonomous BMAD build cycle that runs end-to-end without manual intervention. The user specifies which epic numbers to process (e.g., epics 2 and 3), kicks off a single command, and the full build cycle runs for every story in those epics while they sleep.

**Who It's For:**
The user (Bro) - a solo developer using the BMAD agile workflow who wants to reclaim time lost to manual agent handoffs and console confirmations.

**What It Produces:**
- Committed code at every build cycle step for each story (e.g., "code review story 4-6")
- An error log file documenting any blockers encountered, so the user can fix them manually after waking up

**Key Insights:**
- Pain point: constant manual confirmations between agent handoffs in the current BMAD build cycle
- Chain order: dev → code review → retro → QA → fix (following existing BMAD agile workflow)
- Agents auto-accept each other's outputs - no human in the loop during the run
- Triggered by a single command with epic numbers as input
- Error handling: log and continue - never block, document failures and move to next story
- "Done" signal: all stories across specified epics have committed code at every build cycle step

## Classification Decisions

**Workflow Name:** epic-auto-runner
**Target Path:** `_bmad/bmm/workflows/4-implementation/epic-auto-runner/`

**4 Key Decisions:**
1. **Document Output:** false
2. **Module Affiliation:** BMM
3. **Session Type:** single-session
4. **Lifecycle Support:** create-only

**Structure Implications:**
- Create-only: only needs `steps-c/` folder (no steps-e/ or steps-v/)
- Single-session: standard init, no continuation logic, no step-01b-continue.md needed
- Non-document: no output template, no stepsCompleted tracking in output frontmatter
- BMM module: lives at `_bmad/bmm/workflows/4-implementation/epic-auto-runner/`
- Has access to BMM module variables via bmm config.yaml

## Requirements

**Flow Structure:**
- Pattern: looping (per-story loop nested inside per-epic loop)
- Phases:
  1. Init - accept epic numbers, load sprint-status.yaml, resolve stories
  2. Per-story loop: create-story (SM) → dev-story (DEV) → code-review (DEV), commit after each
  3. Per-epic: automate (QA) → retrospective (SM), commit after each
  4. Complete - summary of what ran, what failed
- Estimated steps: 4 step files (init, story-cycle, epic-cycle, complete)

**User Interaction:**
- Style: fully autonomous - no human checkpoints during run
- Decision points: only at start (epic numbers input)
- Checkpoint frequency: none - fire and forget

**Inputs Required:**
- Required: epic numbers (e.g., `2 3`)
- Required: `sprint-status.yaml` must exist (sprint planning already done)
- Required: story files must exist in the epics
- Optional: none

**Output Specifications:**
- Type: actions + side-effect files
- Format: committed code at each step + error log file for failures
- Commit messages: descriptive per step (e.g., "dev-story epic-2 story-4", "code-review epic-2 story-4", "automate epic-2", "retrospective epic-2")
- Error log: documents failures with story/epic context, workflow continues on error
- Frequency: continuous per story/epic

**Success Criteria:**
- All stories in specified epics have commits for create-story, dev-story, code-review
- Each epic has commits for automate and retrospective
- Any failures are logged and skipped - workflow never blocks
- User wakes up to committed code and a clear error log

**Instruction Style:**
- Overall: prescriptive - exact steps, exact agent commands, no creative interpretation
- Each step spawns a fresh agent with clean context
- Agent passes right context to each fresh agent (story/epic reference, workflow to run)

## Tools Configuration

**Core BMAD Tools:**
- **Party Mode:** excluded - no creativity needed, pure execution
- **Advanced Elicitation:** excluded - no human in the loop
- **Brainstorming:** excluded - not applicable

**LLM Features:**
- **Web-Browsing:** excluded
- **File I/O:** included - read sprint-status.yaml, read story files, write error log
- **Sub-Agents:** included - core mechanism, each step spawns a fresh agent with clean context
- **Sub-Processes:** excluded - sequential execution (finish epic 2 fully before starting epic 3)

**Memory:**
- Type: single-session
- Tracking: simple in-memory state only, no sidecar file

**External Integrations:**
- **Git MCP:** required - used for all commits at each step
  - Check at init that Git MCP is installed; halt with clear error message if not
  - URL: https://github.com/modelcontextprotocol/servers/tree/main/src/git
  - requires_install: yes

**Key Logic Refinements:**
- create-story: check if story file already exists first; if yes, skip creation and proceed to dev-story
- Execution order: fully sequential - story by story, epic by epic
- Error handling: log failure with context, continue to next story/epic, never block

**Installation Requirements:**
- Git MCP server (required, must be verified at workflow init)
