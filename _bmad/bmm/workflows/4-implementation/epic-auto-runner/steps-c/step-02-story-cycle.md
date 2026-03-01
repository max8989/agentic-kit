---
name: 'step-02-story-cycle'
description: 'Run the full story build cycle for every story in each epic - create-story, dev-story, code-review, commit after each'

nextStepFile: './step-03-epic-wrap.md'
---

# Step 2: Story Cycle

## STEP GOAL:

For every story in every requested epic, run the full story build cycle autonomously: check/create story → dev-story → code-review, committing after each step and logging any errors before moving on.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step, ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are an autonomous build cycle orchestrator
- ✅ You execute precisely, log failures, and never block
- ✅ A failed story step is logged and skipped - never a reason to stop
- ✅ You commit after every successful step using Git MCP

### Step-Specific Rules:

- 🎯 Process stories sequentially - one epic at a time, one story at a time
- 🚫 NEVER wait for human input during this step
- 🚫 NEVER skip a story without logging the reason
- 🎯 Each sub-agent gets a fresh context with only what it needs
- 💾 Use Git MCP for all commits - log error and continue if commit fails
- ⚙️ If sub-agent spawning unavailable: execute each workflow step in main thread sequentially

## EXECUTION PROTOCOLS:

- 🎯 Iterate epics → stories → steps in strict sequential order
- 💾 Commit after each successful step via Git MCP
- 📋 Append all errors to error_log_path with full context
- 🚫 FORBIDDEN to block or halt on any error - log and continue

## CONTEXT BOUNDARIES:

- Available: epic_numbers, stories_per_epic, error_log_path from step-01
- Focus: Story-level build cycle only (create-story, dev-story, code-review)
- Limits: Do NOT run epic-level steps here (automate, retrospective) - those are in step-03
- Dependencies: step-01 must have completed successfully

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Begin Epic and Story Iteration

For each epic in `epic_numbers` (in order):

  For each story in `stories_per_epic[epic]` (in order):

  Execute the story cycle defined in steps 2-5 below.

### 2. Check Story File Existence

Check if the story file already exists on disk for this story (check the path returned from sprint-status.yaml in step-01).

- **If story file EXISTS:** Log `[INFO] Story {epic}-{story}: file already exists, skipping create-story` and proceed directly to step 3 (dev-story)
- **If story file does NOT exist:** Proceed to create-story sub-agent below

**Create-Story Sub-Agent (only if file does not exist):**

Spawn a fresh sub-agent with this context:
```
Agent: SM (bmad-agent-bmm-sm)
Task: Run the create-story workflow for Epic {epic_number}, Story {story_number}
Workflow: /bmad-bmm-create-story
Context: sprint-status.yaml location, epic number, story number
Auto-accept: all outputs
Do not ask for confirmation
```

On success: commit via Git MCP with message: `"create-story epic-{epic_number} story-{story_number}"`
On failure: append to error log:
```
## [FAILED] create-story epic-{epic_number} story-{story_number}
Error: {error_details}
Action: skipping dev-story and code-review for this story
```
Then skip to next story.

### 3. Dev Story Sub-Agent

Spawn a fresh sub-agent with this context:
```
Agent: DEV (bmad-agent-bmm-dev)
Task: Run the dev-story workflow for Epic {epic_number}, Story {story_number}
Workflow: /bmad-bmm-dev-story
Context: story file path for this story
Auto-accept: all outputs
Do not ask for confirmation
```

On success: commit via Git MCP with message: `"dev-story epic-{epic_number} story-{story_number}"`
On failure: append to error log:
```
## [FAILED] dev-story epic-{epic_number} story-{story_number}
Error: {error_details}
Action: skipping code-review for this story
```
Then skip to next story.

### 4. Code Review Sub-Agent

Spawn a fresh sub-agent with this context:
```
Agent: DEV (bmad-agent-bmm-dev)
Task: Run the code-review workflow for Epic {epic_number}, Story {story_number}
Workflow: /bmad-bmm-code-review
Context: story file path, code changes from dev-story
Auto-accept: all outputs, apply all fixes automatically
Do not ask for confirmation
```

On success: commit via Git MCP with message: `"code-review epic-{epic_number} story-{story_number}"`
On failure: append to error log:
```
## [FAILED] code-review epic-{epic_number} story-{story_number}
Error: {error_details}
Action: logged, continuing to next story
```
Then continue to next story (do not block).

### 5. Story Complete - Continue Loop

Log `[OK] Story {epic}-{story}: all steps complete` and proceed to the next story.

After all stories in the current epic are processed, proceed to the next epic.

After all epics and all stories are processed, proceed to step-03.

### 6. Auto-Proceed to Epic Wrap

Display: **Story cycle complete for all epics. Proceeding to epic wrap-up...**

#### EXECUTION RULES:

- This is an auto-proceed step with no user choices
- Proceed directly to next step after all stories are processed

#### Menu Handling Logic:

- After story cycle complete for all epics, immediately load, read entire file, then execute {nextStepFile}

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- All stories in all epics processed (success or logged failure)
- Each successful step has a corresponding commit
- All failures logged to error_log_path with context
- No blocking or waiting for human input
- Proceeds autonomously to step-03

### ❌ SYSTEM FAILURE:

- Blocking on any error instead of logging and continuing
- Waiting for human confirmation at any point
- Skipping a story without logging the reason
- Running epic-level steps (automate, retrospective) in this step
- Processing epics in parallel instead of sequentially

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
