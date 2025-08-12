# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the **bmad-godot-game-dev** expansion pack for the BMad-Method framework - a specialized system for developing games using Godot Engine and GDScript. It extends the core BMad-Method with game development agents, workflows, and templates optimized for both 2D and 3D Godot game development.

## Key Commands

### Build and Validation Commands
From the repository root (`../../`):
```bash
# Build the expansion pack agents and teams
npm run build

# Validate configuration files
npm run validate

# Build only agents
npm run build:agents

# Build only teams  
npm run build:teams

# List available agents
npm run list:agents

# Format markdown files
npm run format
```

### Installation Commands
```bash
# Install this expansion pack into a Godot project
npx bmad-method install
# Select "Install expansion pack" → "bmad-godot-game-dev"
```

## Architecture Overview

### Agent System
This expansion pack provides specialized game development agents:

- **game-designer** (`agents/game-designer.md`) - Game mechanics, creative design, GDD creation with Godot focus
- **game-developer** (`agents/game-developer.md`) - Godot implementation, GDScript coding, performance optimization  
- **game-architect** (`agents/game-architect.md`) - Godot system design, technical architecture
- **game-sm** (`agents/game-sm.md`) - Game story creation, sprint planning

### Core Workflow Pattern
The expansion follows a structured development cycle:
1. **Design Phase** (Web UI recommended) - Game concept → Game Design Document → Level Design → Technical Architecture
2. **Implementation Phase** (IDE required) - Document sharding → Story creation → Godot development → QA review

### Key Configuration Files
- **config.yaml** - Expansion pack metadata (name, version, slash prefix `bmadg`)
- **workflows/game-dev-greenfield.yaml** - Complete greenfield game development workflow
- **data/development-guidelines.md** - Godot & GDScript coding standards and performance requirements

## Development Standards

### Godot Project Structure
```
project/
├── scenes/          # Game scenes (.tscn files)
│   ├── main/        # Main menu, settings, etc.
│   ├── levels/      # Game levels and gameplay scenes
│   ├── ui/          # UI components and HUD
│   └── components/  # Reusable scene components
├── scripts/         # GDScript files (.gd)
│   ├── managers/    # Singleton managers (GameManager, etc.)
│   ├── components/  # Reusable script components
│   ├── data/        # Custom Resource classes
│   └── utils/       # Utility functions and helpers
├── assets/          # Game assets
│   ├── 2d/          # For 2D: sprites, textures, animations
│   ├── 3d/          # For 3D: models, materials, shaders
│   ├── audio/       # Music and sound effects
│   └── fonts/       # Font files
├── data/            # Game data files
│   └── resources/   # .tres resource files
└── tests/           # Unit and integration tests
    ├── unit/        # Unit tests using GUT
    └── integration/ # Integration tests
```

### GDScript Coding Standards
- **Naming**: snake_case for variables/functions, PascalCase for classes
- **Architecture**: Node-based design using Godot's scene composition
- **Performance**: Object pooling for high-frequency objects, optimize _process() usage
- **Input**: Use Godot's Input Map system for cross-platform compatibility
- **Testing**: Unit tests using GUT framework, integration tests for systems

### Performance Targets
- **Desktop**: 60+ FPS stable at 1080p
- **Mobile**: 60 FPS on mid-range devices, 30 FPS minimum on low-end
- **Web**: 30-60 FPS depending on browser performance
- **Loading**: Under 3 seconds scene transitions, under 5 seconds initial load
- **Memory**: Platform-specific budgets with garbage collection optimization

## File Organization

### Template System
Templates in `templates/` folder:
- **game-brief-tmpl.yaml** - Initial game concept document
- **game-design-doc-tmpl.yaml** - Comprehensive game requirements
- **level-design-doc-tmpl.yaml** - Level creation framework  
- **game-architecture-tmpl.yaml** - Godot technical architecture
- **game-story-tmpl.yaml** - Implementation story format

### Task System
Tasks in `tasks/` folder provide executable workflows:
- **game-design-brainstorming.md** - Creative ideation process
- **create-game-story.md** - Story creation workflow
- **validate-game-story.md** - Quality validation process

### Quality Assurance
Checklists in `checklists/` folder:
- **game-design-checklist.md** - Design review criteria
- **game-story-dod-checklist.md** - Story completion validation
- **game-architect-checklist.md** - Architecture review process

## IDE Integration

### Agent Loading Syntax
- **Claude Code**: `/bmadg/game-designer`, `/bmadg/game-developer`, `/bmadg/game-sm`, `/bmadg/game-architect`
- **Cursor**: `@bmadg/game-designer`, `@bmadg/game-developer`, `@bmadg/game-sm`, `@bmadg/game-architect`
- **Other IDEs**: Similar patterns with `@bmadg/` prefix

### Common Commands
All agents support:
- `*help` - Show available commands
- `*status` - Show current context/progress
- `*exit` - Exit agent mode

Agent-specific commands:
- `*game-design-brainstorming` - Game concept ideation (Game Designer)
- `*draft` - Create implementation story (Game SM)
- `*develop-story` - Implement approved story (Game Developer)

## Development Workflow

### Phase 1: Game Design (Web UI Recommended)
1. Use `game-designer` agent to create game brief and design document
2. Use `game-architect` agent to design Godot technical architecture  
3. Save documents as `docs/game-design-doc.md` and `docs/game-architecture.md`
4. Copy to Godot project before switching to IDE

### Phase 2: Godot Implementation (IDE Required)
1. **Document Sharding**: Use core BMad agents to break design documents into manageable pieces
2. **Story Creation**: Use `game-sm` agent to create implementation stories from sharded documents
3. **Development**: Use `game-developer` agent to implement approved stories in Godot
4. **QA Review**: Use core `qa` agent to validate implementations

### Critical Rules
- Always use Game SM agent for story creation (never bmad-master or bmad-orchestrator)
- Always use Game Dev agent for Godot implementation
- One story in progress at a time, worked sequentially
- Story statuses: Draft → Approved → InProgress → Done

## Key Dependencies

### External Dependencies
The expansion pack relies on:
- Core BMad-Method framework (`../../bmad-core/`)
- Common utilities (`../../common/`)
- Node.js 20+ for build tools
- Godot 4.x LTS for target projects

### Internal File Dependencies
When agents reference dependencies in tasks, files map to:
- `tasks/` folder for workflow files
- `templates/` folder for document templates
- `checklists/` folder for validation criteria
- `data/` folder for guidelines and knowledge base

## Performance Considerations

### Agent Context Management
- Use clean, fresh context windows between Game SM → Game Dev → QA cycles
- Agents are optimized for specific roles - don't use bmad-master for game development
- Game development agents maintain focus on Godot-specific patterns and performance

### Document Management
- Large design documents should be created in Web UI (cost-effective)
- Sharding breaks documents into manageable pieces for IDE development
- Always keep File Lists updated in story implementations

## Success Patterns

### Effective Usage
- Use Gemini in Web UI for cost-effective game design planning
- Follow Game SM → Game Dev cycle religiously for systematic progress
- Keep conversations focused (one agent, one task per conversation)
- Review and approve everything before marking features complete

### Common Pitfalls to Avoid
- Don't skip document sharding before Godot development
- Don't use core BMad agents for Godot-specific tasks
- Don't work on multiple stories simultaneously
- Don't modify story sections beyond authorized Dev Agent Record areas

## Godot-Specific Features

### Node System Architecture
- Embrace scene composition and node hierarchy
- Use signals for decoupled communication
- Leverage AutoLoad singletons for global systems
- Implement component patterns through child nodes

### Resource Management
- Use custom Resource classes for game data
- Implement proper resource loading/unloading
- Utilize scene instancing for reusable components
- Optimize texture and audio asset usage

### Testing with GUT Framework
- Unit tests for game logic components
- Integration tests for scene interactions
- Performance benchmarking for optimization
- Automated testing pipeline integration

This expansion pack transforms the general BMad-Method into a specialized Godot game development framework for both 2D and 3D games while maintaining the core principles of agent-driven, story-based development with clean handoffs and quality validation.