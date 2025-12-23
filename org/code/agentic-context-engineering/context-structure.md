# Context Management System Structure

```
Context
    agent:
        # e.g., anthropic-claude-sonnet-4-5, google-gemini-3-flash

    tools:
        bash
        web-search
        filesystem
        language-server-protocol
        model-context-protocol
        retrieval-augmented-generation
        linter
        formatter
        git
        cli-tools
        agent-skills #  https://github.com/agentskills/agentskills

    short-term-memory::ram
        system-prompts
        session-hist # compaction

    long-term-memory::markdown
        shaping_docs
        planning_docs_hist
        design_docs_hist
        tracking_docs_hist
        testing_docs_hist
            unit
            integration
        context_summaries:
            architecture_design_summaries


Context::Tools::Human
    session_management:
        agent choice
        mode # plan, build, review, ...
        tool selection
        prompting
        branching
        snapshotting
        context compaction
    session_management_tools:
        opencode # open source
        cursor
        claude # anthropic
        gemini # google
        codex # openai
    filesystem
    git # context version control
    bash
    tmux
    os
```
