---
name: add-feature
description: Guide for feature implementation and PR description generation based on core-knowledge documentation
version: 1.2.0
category: development
---

# Skill: add-feature

<purpose>
Instructs the agent to implement a codebase task based on existing internal documentation and return ONLY a brief description of the PR the human should make.

Unless the user specifies otherwise, `COREKNOWLEDGE_FOLDER` = `.agents/core-knowledge` (relative to the workspace root).
</purpose>

<instructions>
1. **Analyze Context**: Read all documentation within `COREKNOWLEDGE_FOLDER` to understand architecture, patterns, and logic relevant to the input task. If the folder is not found, stop inmediatly and report it.
2. **Feature Alignment**: Synthesize the requested task against the discovered core knowledge.
3. **Planning**: Generate a granular, step-by-step implementation plan before modifying files.
4. **Execution**: Implement the feature task-by-task, ensuring code consistency. YOU MUST IMPLEMENT THE PLAN.
5. **Knowledge Update**: Upon completion, revise or append to `COREKNOWLEDGE_FOLDER` to reflect the new state of the system.
</instructions>

<output_format>
- **PR Title**: [Brief, descriptive title]
- **Description**: [Short summary of the task, conceptual approach, and the specific files modified]
</output_format>