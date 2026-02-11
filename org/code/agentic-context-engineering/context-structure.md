# Context Management System Structure

```
Context
    Agent
        model  # e.g., claude-sonnet-4-5, kimi-k2
        specialization  # coding, reasoning, planning

    Tools
        bash
        web-search
        filesystem
        language-server-protocol  # lsp
        model-context-protocol  # mcp servers
        retrieval-augmented-generation  # rag
        linter
        formatter
        git
        cli-tools
        agent-skills  # https://github.com/agentskills/agentskills
        tool-generation

    Working-context  # context window
        system-prompts
        session-history  # subject to compaction

    Near-context  # discoverable, one tool call away
        codebase  # via grep, lsp, glob
        docs/  # persistent context on disk
        external-sources  # web, apis

    Persistent-context  # docs/ — long-term memory
        core/  # source of truth
            prd
            architecture
            standards
        stories/  # active units of work
        reference/  # conventions, lessons, completed work
            lessons.md
            completed/
        skills/  # on-demand capabilities
        logs/  # session history, compaction summaries

    Human  # context orchestrator
        session-management
            agent-selection
            mode  # plan, build, review
            tool-selection
            prompting
            branching
            snapshotting
            compaction
        session-tools
            opencode
            cursor
            claude-code
            gemini-cli
            codex
        filesystem
        git  # context version control
        bash
        tmux
        os
```
