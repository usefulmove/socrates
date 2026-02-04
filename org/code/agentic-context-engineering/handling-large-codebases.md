# Agentic LLM Use with Large Codebases

## 1. How to Approach Large Codebases

OpenCode is a Go-based CLI that provides a Terminal User Interface (TUI) and integrates with 75+ providers (including local Ollama/vLLM instances). To handle a large codebase effectively:

* **Don't "Dump" the Repo:** Even with 2026's 1M+ token context windows, feeding the entire repo creates "context rot" where the model loses focus. Instead, use OpenCode's **tool-calling** to let the agent search for relevant files as needed.
* **Establish "Living" Documentation:**
* **`ARCHITECTURE.md`:** Keep a high-level map of the system.
* **`CODING-GUIDELINES.md`:** Treat this as the "rulebook." When the agent makes a mistake, codify the fix here so it doesn't repeat it.


* **The "Plan-First" Workflow:** Use OpenCode’s **Plan Mode**. Before letting the agent edit files, have it output a `plan.md`. Review the plan for architectural alignment before granting write permissions.
* **Verification Loops:** Your agent's effectiveness is capped by your **Eval Loop**. Ensure OpenCode has access to your test suite via the bash tool. A common 2026 pattern is: *Agent writes code → Agent writes a one-off reproduction script → Agent runs tests → Agent iterates until green.*

---

## 2. The Latest State-of-the-Art (2026)

The industry has moved beyond basic vector search. Here is the current hierarchy of "Retrieval-Augmented Generation" for code:

### **Graph RAG (The Current Gold Standard)**

Traditional RAG treats code as text chunks. **Graph RAG** treats it as a **Knowledge Graph**.

* **What it does:** It maps entities (Classes, Functions, Constants) and their relationships (Inherits, Calls, Imports).
* **Why it matters:** It excels at **multi-hop reasoning**. If you ask, *"What are the side effects of changing the `CherryDataset` constructor?"*, Graph RAG traverses the edges to find every dependent module, even if they don't "semantically" look like a match in a vector database.

### **Agentic RAG**

This is what OpenCode actually implements. Instead of a static retrieval step, the LLM acts as an agent that decides:

1. "I need to find where the ResNet model is initialized." (Calls `grep` or `ls`).
2. "I found the file, let me read the first 50 lines." (Calls `read_file`).
3. "This looks like it uses a custom loader; let me search for that definition." (Recursive search).

### **Recursive Language Models (RLM)**

The newest 2026 paradigm involves models that can "spawn" sub-LLMs to handle specific sub-tasks in parallel (e.g., one sub-agent audits security while another generates unit tests), reporting back to a "Meta-Agent."

---

## 3. Comparison of Approaches for Large Projects

| Feature | Basic RAG | Graph RAG | Agentic (OpenCode) |
| --- | --- | --- | --- |
| **Best For** | Simple lookups / FAQs | Dependency mapping | Implementation & Refactoring |
| **Context** | Independent chunks | Structured relationships | Dynamic, tool-driven |
| **Accuracy** | ~50-60% on complex tasks | ~80%+ on relational tasks | High (due to feedback loops) |
| **Cost** | Low | High (Initial indexing) | Moderate (Per-turn cost) |

---

## 4. Recommended 2026 Tech Stack

If you are setting this up today for a production-grade project:

* **Orchestrator:** **OpenCode CLI** (for its TUI, MCP support, and bash execution).
* **Model:** * **Cloud:** **Claude 3.7 Sonnet** or **GPT-4.5-Preview** (Highest reasoning).
* **Local:** **Qwen3-Coder-32B** or **DeepSeek-R1** (Running on your NVIDIA H100 instance via Ollama).


* **Retrieval:** Use an **MCP (Model Context Protocol)** server for **GraphRAG**. This allows OpenCode to query a structured graph of your codebase without you having to manually provide files.

> [!TIP]
> **Pro Tip:** Since you're on Linux, utilize OpenCode's ability to run `perf` or `python -m cProfile` through the bash tool. When optimizing your ResNet-50 cherry classification system, don't just ask for faster code—tell the agent to "profile the bottleneck and propose a fix based on the output."

Would you like me to help you draft a `config.json` for OpenCode that connects to a local Ollama instance for your specific project?
