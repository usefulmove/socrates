# Context Management System Structure


```
Context
    agent
        # e.g., anthropic-claude-sonnet-4-5, kimi-k2-coding
        # agent specialization

    tools
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

    short-term-memory::ram
        system-prompts
        session-history  # compaction

    long-term-memory::markdown
        shaping_documents
        planning_documents
        design_documents
        tracking_documents
        testing_documents
            unit
            integration
        context_summaries
            architecture_design_summaries
        document_history



Context::Tools::Human
    session_management
        agent selection
        mode  # plan, build, review, ...
        tool selection
        tool generation
        prompting
        branching
        snapshotting
        context compaction  # context compression (summarization) based on relevance to reduce or eliminate context rot

    session_management_tools
        opencode  # open source
        cursor
        claude  # anthropic
        gemini  # google
        codex  # openai

    filesystem
    git  # context version control
    bash
    tmux
    os
```
