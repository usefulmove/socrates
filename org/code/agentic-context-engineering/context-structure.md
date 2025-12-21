# Context Management System Structure

```
Context
    agent:
        :tuple[Context.Agent]:

    tools:
        # bash, web-search, filesystem, git, cli-tools, etc.

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


Context::Tools::human
    session_management:
        agent choice
        mode # plan, build, review, ...
        tool selection
        prompting
        branching
        snapshotting
        context compaction
    filesystem
    git # context version control
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
        web-search
)

planning_agent = Context::Agent (
    model:
        opencode/google-gemini-3-flash
    tools:
        bash
        web-search
        retrieval-augmented-generation
)

...
```
