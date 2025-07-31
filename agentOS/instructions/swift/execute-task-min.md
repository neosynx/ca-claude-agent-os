---
description: Rules to execute a task and its sub-tasks using Agent OS
globs:
alwaysApply: false
version: 1.0
encoding: UTF-8
---

# Task Execution Rules

<ai_meta>
  <rules>Process XML blocks sequentially, use exact templates, request missing data</rules>
  <format>UTF-8, LF, 2-space indent, no header indent</format>
</ai_meta>

## Overview

Execute a specific task along with its sub-tasks systematically following a TDD development workflow.

<agent_detection>
  <check_once>
    AT START OF PROCESS:
    SET has_context_fetcher = (Claude Code AND context-fetcher agent exists)
    SET has_test_runner = (Claude Code AND test-runner agent exists)
    USE these flags throughout execution
  </check_once>
</agent_detection>

<process_flow>

<step number="1" name="task_understanding">

### Step 1: Task Understanding

<step_metadata>
  <reads>given task and all sub-tasks</reads>
  <purpose>complete understanding of what to build</purpose>
</step_metadata>

<task_analysis>
  <read_from_tasks_md>
    - Parent task description
    - All sub-task descriptions
    - Task dependencies
    - Expected outcomes
  </read_from_tasks_md>
</task_analysis>

<instructions>
  ACTION: Read the specific parent task and all its sub-tasks
  ANALYZE: Full scope of implementation required
  UNDERSTAND: Dependencies and expected deliverables
</instructions>

</step>

<step number="2" name="technical_spec_review">

### Step 2: Technical Specification Review

<step_metadata>
  <reads>relevant parts of technical-spec.md</reads>
  <purpose>understand technical implementation for this task</purpose>
</step_metadata>

<selective_reading>
  <search_technical_spec>
    FIND sections in technical-spec.md related to:
    - Current task functionality
    - Implementation approach for this feature
    - Integration requirements
    - Performance criteria
  </search_technical_spec>
</selective_reading>

<instructions>
  ACTION: Search technical-spec.md for task-relevant sections
  EXTRACT: Only implementation details for current task
  SKIP: Unrelated technical specifications
  FOCUS: Technical approach for this specific feature
</instructions>

</step>

<step number="3" name="best_practices_review">

### Step 3: Best Practices Review

<step_metadata>
  <reads>relevant parts of @~/.agent-os/standards/best-practices.md</reads>
  <purpose>apply relevant best practices to this task</purpose>
</step_metadata>

<instructions>
  IF has_context_fetcher:
    USE: @agent:context-fetcher
    REQUEST: "Find best practices sections relevant to:
              - Task's technology stack: [CURRENT_TECH]
              - Feature type: [CURRENT_FEATURE_TYPE]
              - Code organization patterns"
    PROCESS: Returned best practices
  ELSE:
    PROCEED: To conditional reading below
</instructions>

<conditional-block context-check="fallback-best-practices">
IF NOT using context-fetcher agent:
  READ: The following fallback best practices retrieval

<conditional_reading>
  <check_context>
    IF relevant best practices sections already in context:
      NOTE: "Using best practices already loaded"
      SKIP: Re-reading those sections
    ELSE:
      SEARCH: For relevant sections
      LOAD: Only new/unread sections
  </check_context>
</conditional_reading>

<selective_reading>
  <search_best_practices>
    FIND sections relevant to:
    - Task's technology stack
    - Feature type being implemented
    - Code organization patterns
  </search_best_practices>
</selective_reading>

<instructions>
  ACTION: Check if relevant sections already in context
  SEARCH: best-practices.md for relevant guidelines if needed
  EXTRACT: Only practices applicable to current task
  SKIP: Already-loaded sections and unrelated sections
  APPLY: Relevant patterns to implementation
</instructions>
</conditional-block>

</step>

<step number="4" name="code_style_review">

### Step 4: Code Style Review

<step_metadata>
  <reads>relevant parts of @~/.agent-os/standards/code-style.md</reads>
  <purpose>apply relevant code style rules to this task</purpose>
</step_metadata>

<instructions>
  IF has_context_fetcher:
    USE: @agent:context-fetcher
    REQUEST: "Find code style rules for:
              - Languages: [LANGUAGES_IN_TASK]
              - File types: [FILE_TYPES_BEING_MODIFIED]
              - Component patterns: [PATTERNS_BEING_IMPLEMENTED]
             "
    PROCESS: Returned style rules
  ELSE:
    PROCEED: To conditional reading below
</instructions>

<conditional-block context-check="fallback-code-style">
IF NOT using context-fetcher agent:
  READ: The following fallback code style retrieval

<conditional_reading>
  <check_context>
    IF relevant code style sections already in context:
      NOTE: "Using code style rules already loaded"
      SKIP: Re-reading those sections
    ELSE:
      SEARCH: For relevant sections
      LOAD: Only new/unread sections
  </check_context>
</conditional_reading>

<selective_reading>
  <search_code_style>
    FIND style rules for:
    - Languages used in this task
    - File types being modified
    - Component patterns being implemented
  </search_code_style>
</selective_reading>

<instructions>
  ACTION: Check if relevant sections already in context
  SEARCH: code-style.md for applicable rules if needed
  EXTRACT: Only style guides for current task's code
  SKIP: Already-loaded sections and unused language rules
  APPLY: Relevant formatting and patterns
</instructions>
</conditional-block>

</step>

<step number="5" name="task_execution">

### Step 5: Task and Sub-task Execution

<step_metadata>
  <executes>parent task and all sub-tasks in order</executes>
</step_metadata>

<typical_task_structure>
  <first_subtask>Implementation steps</first_subtask>
  <final_subtask>Verify build pass</final_subtask>
</typical_task_structure>

<execution_order>
  <first_subtasks_implementation>
    FOR each implementation sub-task (1 through n-1):
      - Implement the specific functionality
      - Mark sub-task complete
  </first_subtasks_implementation>
  
  <final_subtask_verification>
    IF final sub-task is "Verify build pass":
      - Run build
      - Fix any remaining failures
      - Ensure no errors
      - Mark final sub-task complete
  </final_subtask_verification>
</execution_order>

<instructions>
  ACTION: Execute sub-tasks in their defined order
  IMPLEMENT: Middle sub-tasks build functionality
  VERIFY: Final sub-task ensures all build pass
  UPDATE: Mark each sub-task complete as finished
</instructions>

</step>

<step number="6" name="task_status_updates">

### Step 6: Task Status Updates

<step_metadata>
  <updates>tasks.md file</updates>
  <timing>immediately after completion</timing>
</step_metadata>

<update_format>
  <completed>- [x] Task description</completed>
  <incomplete>- [ ] Task description</incomplete>
  <blocked>
    - [ ] Task description
    ⚠️ Blocking issue: [DESCRIPTION]
  </blocked>
</update_format>

<blocking_criteria>
  <attempts>maximum 3 different approaches</attempts>
  <action>document blocking issue</action>
  <emoji>⚠️</emoji>
</blocking_criteria>

<instructions>
  ACTION: Update tasks.md after each task completion
  MARK: [x] for completed items immediately
  DOCUMENT: Blocking issues with ⚠️ emoji
  LIMIT: 3 attempts before marking as blocked
</instructions>

</step>

</process_flow>
