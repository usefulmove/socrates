To use the BMAD method on your next project, follow this structured "growth cycle." The key is that you (and the AI) strictly follow the artifacts you create in steps 1-3 before writing any code.

### Phase 1: Definition (The Seed)
**Role:** Product Manager
1.  **Initialize**: Create your documentation structure.
    ```bash
    mkdir -p docs/specs docs/stories
    ```
2.  **Create PRD**: Copy the template and define *what* you are building.
    ```bash
    cp docs/templates/BMAD_PRD_TEMPLATE.md docs/specs/prd.md
    # Edit docs/specs/prd.md to define goals, user stories, and requirements.
    ```

### Phase 2: Structure (The Trellis)
**Role:** Architect
1.  **Create Architecture**: Define *how* it will be built.
    ```bash
    cp docs/templates/BMAD_ARCH_TEMPLATE.md docs/specs/architecture.md
    # Edit docs/specs/architecture.md to define tech stack, data flow, and diagrams.
    ```

### Phase 3: Planning (Pruning)
**Role:** Scrum Master
1.  **Create Stories**: Break the work into small, deterministic units.
    ```bash
    cp docs/templates/BMAD_STORY_TEMPLATE.md docs/stories/001-setup-core.md
    # Edit the story to provide specific context and constraints for this unit of work.
    ```
    *Tip: Be specific in the "Control Manifest" section of the story. This is where you constrain the AI to ensure safety.*

### Phase 4: Implementation (The Bloom)
**Role:** Developer
1.  **Execute**: Feed the context to the AI (me) to implement a specific story.
    *   **Prompt**: "Read `docs/specs/prd.md`, `docs/specs/architecture.md`, and `docs/stories/001-setup-core.md`. Implement Story 001 strictly according to the control manifest."
2.  **Verify**: I will implement the code, referencing the "Context" you engineered in the documents.

### Why this works?
Instead of a vague prompt like "Build me a login system," you are providing a **compiled context chain**:
`PRD (Why) -> Arch (How) -> Story (What Now) -> Code`

This makes the AI's output deterministic and high-quality because it isn't guessing; it's following your engineered specs.
