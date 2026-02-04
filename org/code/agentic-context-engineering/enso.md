# Enso Context Management System

[https://github.com/usefulmove/enso](https://github.com/usefulmove/enso)

A context management system for AI-assisted development that structures project context into discrete, version-controlled units.

## Core Concept

Enso treats context as a first-class resource that requires deliberate management throughout the AI-assisted development lifecycle. Rather than letting context accumulate haphazardly across sessions, enso provides a structured approach to capturing, organizing, and evolving the knowledge that agents need to work effectively.

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
2. **Capture**: Developers and agents write context into structured markdown files
3. **Version Control**: Context files are committed alongside code changes
4. **Activation**: Agents load relevant context at session start
5. **Evolution**: Context is refined as the project evolves

## Principles

- **Context is code**: Version controlled, reviewed, and maintained
- **Explicit over implicit**: No assumptions about what agents know
- **Progressive disclosure**: Load only the context needed for the current task
- **Human-in-the-loop**: Developers curate and validate context quality

## Connections

- [*Context Engineering Overview*](./context-structure.md) - Structural patterns for agent context
- [*Large Codebases*](./context-engineering-in-large-codebases.md) - Scaling context to complex systems
