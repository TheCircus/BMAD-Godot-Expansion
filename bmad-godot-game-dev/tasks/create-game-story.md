# Create Game Story Task

## Purpose

To identify the next logical game story based on project progress and epic definitions, and then to prepare a comprehensive, self-contained, and actionable story file using the `Game Story Template`. This task ensures the story is enriched with all necessary technical context, Godot-specific requirements, and acceptance criteria, making it ready for efficient implementation by a Game Developer Agent with minimal need for additional research or finding its own context.

## SEQUENTIAL Task Execution (Do not proceed until current Task is complete)

### 0. Load Core Configuration and Check Workflow

- Load `{root}/core-config.yaml` from the project root
- If the file does not exist, HALT and inform the user: "core-config.yaml not found. This file is required for story creation. You can either: 1) Copy core-config.yaml from GITHUB bmad-core/ and configure it for your game project OR 2) Run the BMad installer against your project to upgrade and add the file automatically. Please add and configure before proceeding."
- Extract key configurations: `devStoryLocation`, `gdd.*`, `gamearchitecture.*`, `workflow.*`

### 1. Identify Next Story for Preparation

#### 1.1 Locate Epic Files and Review Existing Stories

- Based on `gddSharded` from config, locate epic files (sharded location/pattern or monolithic GDD sections)
- If `devStoryLocation` has story files, load the highest `{epicNum}.{storyNum}.story.md` file
- **If highest story exists:**
  - Verify status is 'Done'. If not, alert user: "ALERT: Found incomplete story! File: {lastEpicNum}.{lastStoryNum}.story.md Status: [current status] You should fix this story first, but would you like to accept risk & override to create the next story in draft?"
  - If proceeding, select next sequential story in the current epic
  - If epic is complete, prompt user: "Epic {epicNum} Complete: All stories in Epic {epicNum} have been completed. Would you like to: 1) Begin Epic {epicNum + 1} with story 1 2) Select a specific story to work on 3) Cancel story creation"
  - **CRITICAL**: NEVER automatically skip to another epic. User MUST explicitly instruct which story to create.
- **If no story files exist:** The next story is ALWAYS 1.1 (first story of first epic)
- Announce the identified story to the user: "Identified next story for preparation: {epicNum}.{storyNum} - {Story Title}"

### 2. Gather Story Requirements and Previous Story Context

- Extract story requirements from the identified epic file or GDD section
- If previous story exists, review Dev Agent Record sections for:
  - Completion Notes and Debug Log References
  - Implementation deviations and technical decisions
  - Godot-specific challenges (scene issues, node management, performance)
  - Asset pipeline decisions and optimizations
- Extract relevant insights that inform the current story's preparation

### 3. Gather Architecture Context

#### 3.1 Determine Architecture Reading Strategy

- **If `gamearchitectureVersion: >= v3` and `gamearchitectureSharded: true`**: Read `{gamearchitectureShardedLocation}/index.md` then follow structured reading order below
- **Else**: Use monolithic `gamearchitectureFile` for similar sections

#### 3.2 Read Architecture Documents Based on Story Type

**For ALL Game Stories:** tech-stack.md, godot-project-structure.md, coding-standards.md, testing-resilience-architecture.md

**For Gameplay/Mechanics Stories, additionally:** gameplay-systems-architecture.md, component-architecture-details.md, physics-config.md, input-system.md, state-machines.md, game-data-models.md

**For UI/UX Stories, additionally:** ui-architecture.md, ui-components.md, ui-state-management.md, scene-management.md

**For Backend/Services Stories, additionally:** game-data-models.md, data-persistence.md, save-system.md, analytics-integration.md, multiplayer-architecture.md

**For Graphics/Rendering Stories, additionally:** rendering-pipeline.md, shader-guidelines.md, sprite-management.md, particle-systems.md

**For Audio Stories, additionally:** audio-architecture.md, audio-mixing.md, sound-banks.md

#### 3.3 Extract Story-Specific Technical Details

Extract ONLY information directly relevant to implementing the current story. Do NOT invent new patterns, systems, or standards not in the source documents.

Extract:

- Specific Godot nodes and scripts the story will use
- Godot plugin dependencies and their APIs (e.g., custom addons, third-party plugins)
- Plugin-specific configurations and setup requirements
- Scene structures and node organization requirements
- Input system bindings and configurations
- Physics settings and collision layers
- UI canvas and layout specifications
- Asset naming conventions and folder structures
- Performance budgets (target FPS, memory limits, draw calls)
- Platform-specific considerations (mobile vs desktop)
- Testing requirements specific to Godot features

ALWAYS cite source documents: `[Source: gamearchitecture/{filename}.md#{section}]`

### 4. Godot-Specific Technical Analysis

#### 4.1 Package Dependencies Analysis

- Identify Godot plugins required for the story
- Document plugin versions from project configuration
- Note any plugin-specific APIs or nodes being used
- List package configuration requirements (e.g., Input System settings, URP asset config)
- Identify any third-party Asset Store packages and their integration points

#### 4.2 Scene and Node Structure Planning

- Identify which scenes will be modified or created
- List scene compositions and node hierarchies needed
- Document packed scene instantiation requirements
- Specify scene loading/unloading and transition requirements

#### 4.3 Node and Script Architecture

- Define GDScript or C# scripts needed
- Specify custom Resource classes required
- Document node dependencies and processing order
- Identify required Godot signals and connections
- Note any plugin-specific nodes (e.g., custom addons, third-party plugins)

#### 4.4 Asset Requirements

- List sprite/texture requirements with resolution specs
- Define animation sequences and AnimationPlayer setups needed
- Specify audio clips and their import settings
- Document any shader or material requirements
- Note any Godot-specific assets (e.g., .tscn scenes, .tres resources, Input Map actions)

### 5. Populate Story Template with Full Context

- Create new story file: `{devStoryLocation}/{epicNum}.{storyNum}.story.md` using Game Story Template
- Fill in basic story information: Title, Status (Draft), Story statement, Acceptance Criteria from Epic/GDD
- **`Dev Notes` section (CRITICAL):**
  - CRITICAL: This section MUST contain ONLY information extracted from gamearchitecture documents and GDD. NEVER invent or assume technical details.
  - Include ALL relevant technical details from Steps 2-4, organized by category:
    - **Previous Story Insights**: Key learnings from previous story implementation
    - **Plugin Dependencies**: Godot addons required, versions, configurations [with source references]
    - **Godot Components**: Specific node types, custom resources, systems [with source references]
    - **Scene & Node Specs**: Scene modifications, node hierarchies, packed scenes [with source references]
    - **Input Configuration**: Input actions, bindings, control schemes [with source references]
    - **UI Implementation**: Canvas setup, layout groups, UI events [with source references]
    - **Asset Pipeline**: Asset requirements, import settings, optimization notes
    - **Performance Targets**: FPS targets, memory budgets, profiler metrics
    - **Platform Considerations**: Mobile vs desktop differences, input variations
    - **Testing Requirements**: Integration tests, GUT framework specifics
  - Every technical detail MUST include its source reference: `[Source: gamearchitecture/{filename}.md#{section}]`
  - If information for a category is not found in the gamearchitecture docs, explicitly state: "No specific guidance found in gamearchitecture docs"
- **`Tasks / Subtasks` section:**
  - Generate detailed, sequential list of technical tasks based ONLY on: Epic/GDD Requirements, Story AC, Reviewed GameArchitecture Information
  - Include Godot-specific tasks:
    - Scene setup and configuration
    - Node hierarchy creation and testing
    - Script implementation with proper lifecycle methods (_ready, _process, _physics_process)
    - Input system integration
    - Physics configuration
    - UI implementation with proper Control node anchoring
    - Performance profiling checkpoints
  - Each task must reference relevant gamearchitecture documentation
  - Include Play Mode testing as explicit subtasks
  - Link tasks to ACs where applicable (e.g., `Task 1 (AC: 1, 3)`)
- Add notes on Godot project structure alignment or discrepancies found in Step 4

### 6. Story Draft Completion and Review

- Review all sections for completeness and accuracy
- Verify all source references are included for technical details
- Ensure Godot-specific requirements are comprehensive:
  - All scenes and node hierarchies documented
  - Script and node dependencies clear
  - Asset requirements specified
  - Performance targets defined
- Update status to "Draft" and save the story file
- Execute `{root}/tasks/execute-checklist` `{root}/checklists/game-story-dod-checklist`
- Provide summary to user including:
  - Story created: `{devStoryLocation}/{epicNum}.{storyNum}.story.md`
  - Status: Draft
  - Key Godot nodes and systems included
  - Scene/node hierarchy modifications required
  - Asset requirements identified
  - Any deviations or conflicts noted between GDD and gamearchitecture
  - Checklist Results
  - Next steps: For complex Godot features, suggest the user review the story draft and optionally test critical assumptions in Godot Editor

### 7. Godot-Specific Validation

Before finalizing, ensure:

- [ ] All required Godot plugins are documented with versions
- [ ] Package-specific APIs and configurations are included
- [ ] All Godot node lifecycle methods are considered (_ready, _process, _physics_process)
- [ ] Scene composition workflows are clearly defined
- [ ] Scene management approach is specified
- [ ] Input system integration is complete (Input Map or direct input handling)
- [ ] UI Control setup follows Godot best practices
- [ ] Performance profiling points are identified
- [ ] Asset import settings are documented
- [ ] Platform-specific code paths are noted
- [ ] Godot version compatibility is verified (e.g., Godot 4.x features)

This task ensures game development stories are immediately actionable and enable efficient AI-driven development of Godot game features.
