---
stepsCompleted: ['step-01-discovery', 'step-02-classification', 'step-03-requirements', 'step-04-tools', 'step-05-plan-review', 'step-06-design', 'step-07-foundation', 'step-08-build-step-01', 'step-09-build-next-step']
created: 2026-03-01
status: CONFIRMED
approvedDate: 2026-03-01
confirmationDate: 2026-03-01
confirmationType: new_workflow
coverageStatus: complete
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

## Workflow Design

**Step count:** 4 step files

| File | Type | Purpose |
|---|---|---|
| `step-01-init.md` | Init (non-continuable) | Validate Git MCP, accept epic numbers, load sprint-status.yaml, resolve stories |
| `step-02-story-cycle.md` | Middle (looping, auto-proceed) | Per-story: check/create story → dev-story → code-review → commit each, log errors |
| `step-03-epic-wrap.md` | Middle (looping, auto-proceed) | Per-epic: automate → retrospective → commit each, log errors |
| `step-04-complete.md` | Final | Write summary, display what succeeded/failed |

**Menu Patterns:**
- step-01: C-only menu - user provides epic numbers, then auto-proceeds
- step-02: Auto-proceed - loops per story, fully autonomous
- step-03: Auto-proceed - runs per epic, fully autonomous
- step-04: Final step, no next step

**Data Flow:**
- step-01 → defines: epic_numbers[], sprint_status, stories_per_epic[], error_log_path
- step-02 → reads: stories_per_epic[], writes: commits + error_log
- step-03 → reads: epic_numbers[], writes: commits + error_log
- step-04 → reads: error_log, writes: final summary

**Error log path:** `{output_folder}/epic-auto-runner-errors-{date}.md`

**File Structure:**
```
_bmad/bmm/workflows/4-implementation/epic-auto-runner/
├── workflow.md
└── steps-c/
    ├── step-01-init.md
    ├── step-02-story-cycle.md
    ├── step-03-epic-wrap.md
    └── step-04-complete.md
```

**Subprocess Optimization:**
- step-01: Pattern 3 - subprocess to load and parse sprint-status.yaml, return only stories for requested epics
- step-02/03: Sub-agents with fresh context per invocation (core mechanism)
- Graceful fallback: parse sprint-status.yaml in main thread if subprocess unavailable

**Special Logic:**
- step-02: check if story file exists before calling create-story; skip if exists
- Error on any sub-agent step → log `{epic}-{story}-{step}: {error}` to error log → continue
- Git MCP check at step-01 → halt with install instructions if missing
- Model config note in step-01 pointing to opencode.json

**Installation Requirements:**
- Git MCP server (required, must be verified at workflow init)

## Foundation Build Complete

**Created:**
- `_bmad/bmm/workflows/4-implementation/epic-auto-runner/` (folder structure)
- `_bmad/bmm/workflows/4-implementation/epic-auto-runner/workflow.md`
- `_bmad/bmm/workflows/4-implementation/epic-auto-runner/steps-c/` (folder)
- No output template (non-document workflow)

**Next Steps:**
- Step 8: Build step-01-init.md ✅
- Step 9: Build remaining steps (story-cycle, epic-wrap, complete) ✅

## Steps 02-04 Build Complete

**Created:**
- `steps-c/step-02-story-cycle.md` - per-story loop (create-story, dev-story, code-review + commits)
- `steps-c/step-03-epic-wrap.md` - per-epic wrap-up (automate, retrospective + commits)
- `steps-c/step-04-complete.md` - final summary and error log display

**All workflow steps built. Ready for validation.**

## Step 01 Build Complete

**Created:**
- `steps-c/step-01-init.md`

**Step Configuration:**
- Type: non-continuable, no input discovery
- Subprocess: Pattern 3 for sprint-status.yaml parsing
- Menu: C-only
- Next Step: step-02-story-cycle

**Model Configuration (OpenCode):**
- Model selection is out of scope for this workflow
- Configure per-agent models in `opencode.json` before running (e.g., dev subagent → sonnet, etc.)
- step-01 will include a note pointing users to opencode.json for model config

**Future Enhancements (via Agent Skills - not part of this workflow):**
- Web-browsing skill for DEV/QA agents
- Context7 MCP for documentation lookup during dev-story and code-review
- These should be added as skills on individual agents, not baked into this workflow
