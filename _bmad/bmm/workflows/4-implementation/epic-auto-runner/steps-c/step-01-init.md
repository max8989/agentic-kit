---
name: 'step-01-init'
description: 'Validate Git MCP, accept epic numbers, load sprint-status.yaml, resolve stories'

nextStepFile: './step-02-story-cycle.md'
advancedElicitationTask: '{project-root}/_bmad/core/workflows/advanced-elicitation/workflow.xml'
---

# Step 1: Initialization

## STEP GOAL:

Validate prerequisites, accept epic numbers from the user, and resolve the full list of stories to process before handing off to the autonomous build cycle.

## MANDATORY EXECUTION RULES (READ FIRST):

### Universal Rules:

- 📖 CRITICAL: Read the complete step file before taking any action
- 🔄 CRITICAL: When loading next step with 'C', ensure entire file is read
- ⚙️ TOOL/SUBPROCESS FALLBACK: If any instruction references a subprocess or tool you do not have access to, you MUST still achieve the outcome in your main context thread

### Role Reinforcement:

- ✅ You are an autonomous build cycle orchestrator
- ✅ You execute precisely and validate before running
- ✅ You halt ONLY for missing prerequisites or user input at this step
- ✅ After this step, you never block again

### Step-Specific Rules:

- 🎯 Validate Git MCP first - this is a hard requirement
- 🚫 FORBIDDEN to proceed if Git MCP is not installed
- 🎯 Use subprocess optimization (Pattern 3) to load sprint-status.yaml - return only stories for requested epics
- ⚙️ If subprocess unavailable: load and parse sprint-status.yaml in main thread
- 💬 This is the only step where user interaction happens

## EXECUTION PROTOCOLS:

- 🎯 Validate Git MCP installation before anything else
- 🎯 Parse sprint-status.yaml and resolve story list for each requested epic
- 💾 Store epic_numbers, stories_per_epic, and error_log_path in working context
- 🚫 FORBIDDEN to load next step until all prerequisites pass and epics are confirmed

## CONTEXT BOUNDARIES:

- Available: BMM config (output_folder, user_name, etc.)
- Focus: Validation and setup only
- Limits: Do not start any build cycle work here
- Dependencies: sprint-status.yaml must exist, Git MCP must be installed

## MANDATORY SEQUENCE

**CRITICAL:** Follow this sequence exactly. Do not skip, reorder, or improvise unless user explicitly requests a change.

### 1. Validate Git MCP Installation

Check that the Git MCP server is installed and available.

- Attempt a simple Git MCP operation (e.g., list tools or check git status via MCP)
- If Git MCP is available: continue to step 2
- If Git MCP is NOT available: halt with the following message and do not proceed:

```
🛑 HALT: Git MCP server is not installed.

This workflow requires the Git MCP server to commit changes at each step.

To install:
  https://github.com/modelcontextprotocol/servers/tree/main/src/git

Once installed, restart and re-run this workflow.
```

**Note on model configuration:** Agent models are configured in `opencode.json` (not in this workflow). If you want different models per agent (e.g., Sonnet for dev, Opus for complex review), configure them there before running.

### 2. Locate sprint-status.yaml

Search for `sprint-status.yaml` in `{output_folder}/implementation-artifacts/`.

- If found: proceed
- If NOT found: halt with message:

```
🛑 HALT: sprint-status.yaml not found.

Expected location: {output_folder}/implementation-artifacts/sprint-status.yaml

Please run sprint-planning first (/bmad-bmm-sprint-planning) before using epic-auto-runner.
```

### 3. Accept Epic Numbers

Display:

```
⚡ EPIC AUTO RUNNER

Ready to run the autonomous build cycle.

Which epics should I process? Enter epic numbers separated by spaces.
Example: 2 3

Epic numbers:
```

Wait for user input. Store as `epic_numbers` array.

### 4. Resolve Stories for Each Epic

Launch a subprocess that:
1. Loads `sprint-status.yaml`
2. Finds all stories for each requested epic number
3. For each story returns: epic number, story number, story title, story file path, and current status
4. Returns ONLY the stories matching the requested epics as a structured list

If subprocess unavailable: load and parse sprint-status.yaml directly in main thread.

**Validate results:**
- If any requested epic number is not found in sprint-status.yaml: warn the user with the list of missing epics and ask if they want to continue with the found epics only
- If no stories are found at all: halt with error message

Store results as `stories_per_epic` (map of epic → story list) in working context.

### 5. Set Error Log Path

Set `error_log_path` to: `{output_folder}/epic-auto-runner-errors-{current_date}.md`

Initialize the error log file with a header:

```markdown
# Epic Auto Runner - Error Log
Date: {current_date}
Epics: {epic_numbers}

## Errors

(No errors yet)
```

### 6. Confirm and Display Run Plan

Display a summary of what will be processed:

```
✅ Prerequisites validated. Ready to run.

📋 RUN PLAN:
{for each epic}
  Epic {n}: {story_count} stories
  {for each story}
    - Story {n}: {title}
      Steps: create-story (if needed) → dev-story → code-review
  Epic {n} wrap-up: automate → retrospective

Error log: {error_log_path}

[C] Start autonomous build cycle
```

### 7. Present MENU OPTIONS

Display: **Select:** [C] Start

#### EXECUTION RULES:

- ALWAYS halt and wait for user input after presenting menu
- ONLY proceed when user selects 'C'

#### Menu Handling Logic:

- IF C: Store epic_numbers, stories_per_epic, error_log_path in context, then load, read entire file, then execute {nextStepFile}
- IF Any other: help user, then redisplay menu

---

## 🚨 SYSTEM SUCCESS/FAILURE METRICS

### ✅ SUCCESS:

- Git MCP verified as installed
- sprint-status.yaml found and parsed
- Epic numbers accepted from user
- Stories resolved for each epic
- Error log initialized
- Run plan displayed and confirmed

### ❌ SYSTEM FAILURE:

- Proceeding without Git MCP installed
- Proceeding without sprint-status.yaml
- Starting build cycle without user confirmation
- Not initializing error log before handoff

**Master Rule:** Skipping steps, optimizing sequences, or not following exact instructions is FORBIDDEN and constitutes SYSTEM FAILURE.
