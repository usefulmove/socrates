# Agentic Discovery

In 2026, the "State of the Art" (SOTA) for agentic discovery has shifted from static retrieval (basic RAG) to **Dynamic Investigation**. The core problem—navigating a 1-million-line codebase without blowing the token budget—is no longer solved by just finding "similar" code, but by agents that actively explore, map, and document the environment as they work.

---

## 1. Initial Discovery: The "Explorer" Pattern

Modern agents (like those powering **Claude Code**, **Cursor**, and **Windsurf**) no longer rely on a single vector search. They use a multi-step "Explorer" loop to build a mental map of a new domain.

* **Repo-Mapping (Meta-RAG):** SOTA systems generate a "repo map"—a distilled, hierarchical tree of the codebase using symbols (classes, functions, types) rather than full code. This reduces codebase representation by **~80%** while maintaining a high "localization rate" (finding the right file).
* **Agentic Search (Grep + LSP):** Instead of just semantic search, agents now use **LSP (Language Server Protocol)** to "jump to definition" or "find references" across the entire repo. If an agent is confused, it uses `grep` or `ripgrep` to trace patterns, much like a human dev.
* **The Discovery Loop:**
1. **Grok:** Read the repo map and root `README`.
2. **Hypothesize:** "I think the auth logic is in `/services/auth`."
3. **Probe:** Run a targeted `ls -R` or `grep` for "session".
4. **Refine:** Read the specific file header/types and update the plan.



---

## 2. Long-Term Context: Agent-Instruction Files

The industry has standardized "Agent Context Files"—Markdown documents designed specifically for AI consumption. These serve as the agent's "long-term memory" and operational manual.

* **Root Configuration (`CLAUDE.md`, `AGENTS.md`):** These files contain high-level architectural rules, tech stack details, and "never-do" lists. Agents are hard-coded to check these first.
* **Sub-Directory `.md` Files:** For massive monorepos, local `.md` files provide domain-specific context. An agent entering `/packages/billing` will automatically read `/packages/billing/CONTEXT.md` to understand the specific state machine or external APIs used there.
* **Spec-Driven Development:** SOTA workflows involve the agent writing a `spec.md` or `plan.md` *before* touching code. This file becomes the "Source of Truth" for the duration of the task, preventing "context drift" where the agent forgets the original goal.

---

## 3. Context Management: "Just-in-Time" Loading

Instead of shoving the whole codebase into a massive 2M token window (which leads to "Lost in the Middle" syndrome), agents use **Just-in-Time (JIT) Context**.

| Technique | How it Works | Benefit |
| --- | --- | --- |
| **Context Pinning** | User or agent "pins" 3-5 core files to the active window. | Maintains focus on critical logic. |
| **Token-Distilled Summaries** | Summarizing a file into its public API before "closing" it. | Saves space while keeping the interface known. |
| **Multi-Agent Orchestration** | An "Explorer" finds info and passes a **distilled note** to the "Coder." | The Coder stays "clean" and isn't distracted by irrelevant logs. |

---

## 4. The "In-Place" Discovery Workflow

To enable agents to work effectively in your domain, you should implement the following structure:

1. **The Entry Point:** Maintain a `README-AI.md` that explains the *mental model* of the app, not just how to install it.
2. **The "Rules" File:** Use a `.cursorrules` or `CLAUDE.md` to define formatting (e.g., "Always use functional components," "Use `Zod` for validation").
3. **Active Documentation:** Use an agent to periodically update these `.md` files. In 2026, SOTA agents are "self-documenting"—after a major refactor, the agent automatically updates the relevant `CONTEXT.md` to reflect the new architecture.
