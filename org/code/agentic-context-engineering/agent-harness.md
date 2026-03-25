# Agent Harness

The infrastructure layer that constrains, informs, verifies, and corrects an AI agent. The harness encompasses everything between user intent and model output that is not the language model itself: system prompts, tool definitions, execution environments, verification loops, and lifecycle management.

> Agent = Model + Harness

The model contains the intelligence. The harness makes that intelligence useful.

## Components

A harness provides capabilities the model cannot do out of the box:

- **Context assembly** — System prompts, AGENTS.md files, and progressive disclosure of tools to manage context rot
- **Execution environment** — Sandboxes, filesystems, and bash/code execution for autonomous problem-solving
- **Tool orchestration** — Skills, MCPs, browsers, and verification tools that extend model capabilities
- **Verification loops** — Tests, linters, self-evaluation, and feedback mechanisms for correctness
- **Lifecycle management** — State persistence, memory across sessions, and task orchestration

## Why It Matters

The harness is the primary determinant of agent reliability. Research from LangChain demonstrates that the same model with different harnesses can produce dramatically different results. On the Terminal Bench 2.0 benchmark, Claude Opus 4.6 in Claude Code scored significantly lower than Opus 4.6 in optimized harnesses—moving from Top 30 to Top 5 through harness improvements alone.

This represents the evolution beyond prompt engineering and context engineering into **harness engineering**: the discipline of building systems around model intelligence.

## Connections

- [*Context Engineering*](./context-structure.md) — Structural patterns for assembling agent context
- [*BMAD*](./bmad.md) — Methodology for AI-driven development with structured context
- [*Enso*](./enso.md) — Context management system for AI-assisted development
