# Context Management System Structure

```
Context
    agents:
        :tuple[Context.Agent]:

    short-term-memory::ram
        system-prompts
        session-hist

    long-term-memory::markdown
        shaping_docs
        planning_docs_hist
        tracking_docs_hist
        testing_docs_hist
            unit
            integration
        context_summaries:
            architecture_design_summaries

    tools::human
        filesystem
        git # context version control
        context_compaction
        bash
        tmux
        os


coding_agent = Context::Agent (
    model:
        opencode/anthropic-claude-sonnet-4.5
    tools:
        bash
        linter
        formatter
        language-server-protocol
        model-context-protocol # external context
        retrieval-augmented-generation
        web-search
)

planning_agent = Context::Agent (
    model:
        opencode/google-gemini-3-flash
    tools:
        bash
        web-search
)
```
