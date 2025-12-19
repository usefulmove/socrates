# Handling Large Codebases

- increase retrieval accuracy
- use models with large context windows
- explore agentic discovery (with tool use) over RAG (can suffer from retrieval miss issues)
- manage context with architecture breakdown summaries (markdown)




### 2. The Agentic Discovery Approach (Contextual Exploration)
Instead of a pre-indexed vector store, the agent is given tools (like `grep`, `mgrep`, `find`, or LSP-powered "go to definition").
*   **How it works:** The agent "explores" the codebase like a human developer—searching for keywords, reading relevant files, and following imports.
*   **Why it often beats RAG for coding:** It preserves the **topology** of the code. The agent understands *why* a file is relevant because it navigated there through a logical path.

### 3. Long-Context Window Utilization
With models supporting 200k to 2M+ tokens (e.g., Gemini 1.5 Pro, Claude 3.5), "In-Context Learning" is becoming a viable alternative to RAG for medium-to-large codebases.
*   **Approach:** Feed the entire relevant module or a summarized directory tree into the context.
*   **Benefit:** The model has "perfect memory" of everything in the window, avoiding the "retrieval miss" problem common in RAG.

### 4. Graph-Based Indexing (Code Graph)
Instead of treating code as chunks of text (standard RAG), you map the codebase as a graph (Abstract Syntax Trees, call graphs, and dependency maps).
*   **How it works:** Tools like `Sourcegraph` or `tree-sitter` create a map of how functions and classes relate.
*   **Value:** When an agent looks at a function, the system automatically pulls in the relevant class definitions and interface implementations, providing "semantic context" that simple vector math often misses.

### 5. Repository Mapping (The "Skeleton" Approach)
A hybrid approach where you provide a high-level "Map" of the repository (file structure, exported symbols, and READMEs) in the system prompt.
*   **Benefit:** It gives the agent a "mental model" of where things are, allowing it to use its search tools more effectively without needing to index every single line of code.




**Summary for Large Codebases:** 
RAG is best for **finding needles in haystacks**, but **Agentic Discovery + Repository Mapping** is generally superior for **complex engineering**, as it respects the structural nature of software.
