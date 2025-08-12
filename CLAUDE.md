# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the **BMAD-Godot-Expansion** repository containing the `bmad-godot-game-dev` expansion pack for the BMad-Method framework. This specialized system extends the core BMad-Method with game development agents, workflows, and templates optimized for Godot Engine and GDScript development for both 2D and 3D games.

## Key Commands

### From Parent Directory (BMAD-METHOD/)
The build system runs from the parent BMAD-METHOD directory:

```bash
# Navigate to parent directory first
cd ../BMAD-METHOD

# Build expansion pack agents and teams
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

### Expansion Pack Structure
This repository is an extension to the core BMad-Method framework located in the parent directory. The structure follows expansion pack conventions:

```
bmad-godot-game-dev/
├── agents/          # Specialized game development agents
├── agent-teams/     # Pre-configured team compositions
├── workflows/       # Game development workflows
├── templates/       # Document templates for game design
├── tasks/           # Executable workflow steps
├── checklists/      # Quality assurance checklists
├── data/           # Guidelines and knowledge base
└── config.yaml     # Expansion pack metadata
```

### Specialized Game Development Agents
- **game-designer** - Game mechanics, creative design, GDD creation with Godot focus
- **game-developer** - Godot implementation, GDScript coding, performance optimization  
- **game-architect** - Godot system design, technical architecture
- **game-sm** - Game story creation, sprint planning

### Core Development Workflow
1. **Design Phase** (Web UI recommended) - Game concept → Game Design Document → Level Design → Technical Architecture
2. **Implementation Phase** (IDE required) - Document sharding → Story creation → Godot development → QA review

### Key Configuration
- **Slash Prefix**: `bmadg` for agent loading (`/bmadg/game-designer`)
- **Parent Framework**: Requires core BMad-Method framework in `../BMAD-METHOD/`
- **Target Engine**: Godot 4.x LTS with GDScript

## Development Standards

### Godot Project Structure
Expected structure for projects using this expansion:
```
project/
├── scenes/          # Game scenes (.tscn files)
│   ├── main/        # Main menu, settings
│   ├── levels/      # Game levels and gameplay scenes
│   ├── ui/          # UI components and HUD
│   └── components/  # Reusable scene components
├── scripts/         # GDScript files (.gd)
│   ├── managers/    # Singleton managers
│   ├── components/  # Reusable script components
│   ├── data/        # Custom Resource classes
│   └── utils/       # Utility functions
├── assets/          # Game assets (2d/, 3d/, audio/, fonts/)
├── data/resources/  # Game data (.tres files)
└── tests/           # Unit and integration tests using GUT
```

### GDScript Coding Standards
- **Naming**: snake_case for variables/functions, PascalCase for classes
- **Architecture**: Node-based design using Godot's scene composition
- **Performance**: Object pooling, optimized _process() usage, 60+ FPS targets
- **Testing**: Unit tests using GUT framework

### Performance Requirements
- **Desktop**: 60+ FPS stable at 1080p
- **Mobile**: 60 FPS mid-range devices, 30 FPS minimum low-end
- **Web**: 30-60 FPS browser dependent
- **Loading**: <3s scene transitions, <5s initial load

## File Dependencies

### Internal Dependencies
When working with this expansion pack:
- `config.yaml` - Contains expansion metadata and slash prefix
- `workflows/game-dev-greenfield.yaml` - Primary development workflow
- `data/development-guidelines.md` - Comprehensive Godot/GDScript standards
- `templates/` - Document templates for game design and architecture

### External Dependencies
- Core BMad-Method framework in `../BMAD-METHOD/`
- Node.js 20+ for build tools
- Godot 4.x LTS for target projects

## Agent Usage Patterns

### IDE Integration
- **Claude Code**: `/bmadg/game-designer`, `/bmadg/game-developer`
- **Cursor**: `@bmadg/game-designer`, `@bmadg/game-developer`

### Development Phases
1. **Game Design** (Web UI) - Use game-designer for concept, GDD, level design
2. **Technical Architecture** - Use game-architect for Godot system design
3. **Implementation** (IDE) - Use game-sm for stories, game-developer for coding

### Critical Rules
- Always use Game SM agent for story creation (never bmad-master)
- Always use Game Developer agent for Godot implementation
- Work one story at a time, sequential implementation
- Follow Design → Architecture → Stories → Implementation → QA cycle

## Common Development Tasks

### Starting New Game Project
1. Use game-designer to create game-brief.md
2. Expand to game-design-doc.md and level-design-doc.md
3. Use game-architect for technical architecture
4. Set up Godot project structure
5. Use game-sm to create implementation stories

### Quality Assurance
- Use checklists in `checklists/` folder for validation
- Follow GDScript standards in `data/development-guidelines.md`
- Test with GUT framework for unit and integration tests
- Validate performance targets throughout development

## Performance Considerations

### Agent Context Management
- Game development agents optimized for Godot-specific patterns
- Keep conversations focused (one agent, one task per session)
- Use clean context windows between Game SM → Game Dev → QA cycles

### Development Efficiency
- Use Web UI (Gemini) for cost-effective design phase work
- IDE development only for story implementation
- Maintain File Lists in story implementations
- Regular QA validation to catch issues early