# Enso Context Management System

[https://github.com/usefulmove/enso](https://github.com/usefulmove/enso)

A context management system for AI-assisted development that structures project context into discrete, version-controlled units.

## Core Concept

Enso treats context as a first-class resource that requires deliberate management throughout the AI-assisted development lifecycle. Rather than letting context accumulate haphazardly across sessions, enso provides a structured approach to capturing, organizing, and evolving the knowledge that harness instances use to summon effective agent instantiations.

## Structure

```
.enso/
├── context/
│   ├── project.md      # Project overview and conventions
│   ├── architecture.md # System design and patterns
│   ├── state.md        # Current state and recent decisions
│   └── history/        # Archived context snapshots
└── templates/          # Context generation templates
```

## Workflow

1. **Initialization**: `enso init` creates the context directory structure
2. **Capture**: Developers and agent instantiations write context into structured markdown files
3. **Version Control**: Context files are committed alongside code changes
4. **Activation**: Harness instances load relevant context at session start and pass it into fresh agent instantiations
5. **Evolution**: Context is refined as the project evolves

## Principles

- **Context is code**: Version controlled, reviewed, and maintained
- **Explicit over implicit**: No assumptions about what an instantiation knows
- **Progressive disclosure**: Load only the context needed for the current task
- **Human-in-the-loop**: Developers curate and validate context quality

## Connections

- [*Context Engineering Overview*](./context-structure.md) - Structural patterns for harness and agent context
- [*Large Codebases*](./context-engineering-in-large-codebases.md) - Scaling context to complex systems
