# Model, Runtime, Harness, Agent, Substrate — Terminology

Working vocabulary for talking about LLMs, runtimes, harnesses, agent
instantiations, and substrates. Settled through discussion and sanity-checked
against current practitioner writing (LangChain, Anthropic, Parallel, NVIDIA,
the harness-survey literature). Use this when precision matters; fall back to
field shorthand when it doesn't.

---

## The stack

From engine to identity to environment:

- **Model** — the LLM. Token generator, reasoning engine. Swappable.
- **Runtime** — executable host. OpenCode, Claude Code, `pi`, Cursor, Codex.
  Provides the loop, tool dispatch, model plumbing, session storage, TUI.
- **Harness protocol** — rules and schema. How the loop runs, how tools are
  defined, what extension points exist. `enso` is a harness protocol.
- **Harness instance** — runtime configured with a protocol for a specific
  purpose: persona, system prompt, tool inventory, skills, policies, session
  log.
- **Agent instantiation** — ephemeral process summoned by a harness instance to
  handle a task. Reads the pattern, behaves as that pattern, dissolves.
- **Substrate** — the durable environment being transformed. Filesystem,
  codebase, database, infrastructure. Persists across all instantiations.

---

## Definitions

### Model
The LLM. Reasoning engine. Swappable. Not constitutive of any instantiation's
identity — only of its capability ceiling.

### Runtime
A concrete executable that hosts the loop. Provides model client, tool
dispatcher, session storage, TUI, extension loader. A runtime implements one
or more harness protocols. NVIDIA calls these "execution harnesses."

Per Anthropic's *Managed Agents* architecture, a runtime can be further
decomposed into three decoupled pieces:

- **Brain** — the loop that calls the model and routes tool calls.
- **Sandbox / hands** — the execution environment for tools and code.
- **Session** — the durable, append-only event log. Lives *outside* the
  brain so it can survive crashes and outlive any particular runtime instance.

This decoupling matters because it places continuity (session) outside the
disposable computational machinery (brain, sandbox).

### Harness protocol
The interface and rules: tool schema, loop semantics, extension points,
lifecycle hooks. A protocol can have multiple runtime implementations.

### Harness instance
A runtime in a specific configuration: protocol + config (prompts, persona,
tools, skills, policies) + session log. This is what most field writing means
by "harness."

A configured harness typically includes:

- **Context assembly** — system prompts, persona files, and progressive
  disclosure of tools to manage context rot
- **Execution environment** — sandboxes, filesystem access, bash/code execution
- **Tool orchestration** — skills, MCP servers, browsers, and verification tools
- **Verification loops** — tests, linters, self-evaluation, and feedback
  mechanisms for correctness
- **Lifecycle management** — session persistence, memory across tasks, and task
  orchestration

### Agent instantiation
A process summoned by a harness instance to handle a task. Ephemeral. Reads the
pattern (config + session log), behaves as that pattern for the duration of the
task, dissolves at the end. There is no continuous agent between calls. What
feels like a persistent agent is a pattern being reinstantiated.

### Substrate
The durable environment being transformed by agent instantiations. Filesystem,
codebase, database, infrastructure, git history. The substrate persists; it is
what the harness and its instantiations act upon. A wiped substrate is a
destroyed workspace; a wiped session log is merely amnesia.

---

## The identity claim

> **Substrate persists. Harness persists. Agents recur.**

The persistent things are the substrate and the harness. The substrate is the
code, the files, the infrastructure — the world being worked on. The harness is
the config files, the logs, the tools, the runtime — the machinery configured
to work on it. Agents are what the harness manifests in response to work. A
particular Toni-process exists only while it is generating tokens; between
turns and between sessions, nothing is "being Toni." There is only data on
disk, a runtime waiting to be invoked, and a substrate waiting to be
transformed.

So when we say "the same agent across sessions," we mean *the same
pattern reinstantiated*. That phrasing is shorthand, useful but not literal.
Closer to reincarnation than to a continuous self: pattern over particulars,
recurring instantiations in a closed world.

This means:

- **Identity lives in the harness**, not in the instantiation. Persona (config)
  and continuity (session log) are harness components.
- **The substrate is what matters.** The ultimate purpose of the harness is
  substrate transformation. An instantiation that doesn't change the substrate
  is a wasted invocation.
- **Instantiations are downstream of the harness.** You can't engineer them
  directly. You engineer the field that produces them.
- **Harness engineering is the discipline** — by necessity, because the
  ephemeral thing has no surface to work on.

---

## Implications

### `pi` and Claude Code are not agents
They are runtimes. Asking whether `pi` is an agent is a category error — it's
the thing instantiations come from.

### The "Sonnet vs Opus, same agent?" question dissolves
There is no continuous agent to identify across model swaps. Two summonings
from the same pattern with different reasoning engines. Pattern identity
persists; instantiation identity is a non-question because instantiations don't
endure.

### Capabilities vs. identity
- Swap the **model** → different engine, same pattern. Quality and quirks
  shift. Pattern intact.
- Add or remove **tools** → different capability surface, same pattern.
- Edit **persona / SOUL.md** materially → different pattern. Subsequent
  instantiations are summoned from a different specification.
- Wipe **session log** → same pattern, no continuity. Functionally a
  stranger.
- Corrupt the **substrate** → different world. Pattern intact, but the world
  it acts upon has changed.

### Harness engineering as a discipline
Research demonstrates that the same model in different harnesses can produce
dramatically different results. The harness — not the model — is the primary
determinant of reliability in practice.

This represents the evolution beyond prompt engineering and context engineering
into **harness engineering**: the discipline of building persistent systems
around ephemeral model intelligence.

### Why the field's equation works anyway
The dominant field formula is `agent = model + harness` (LangChain, Mollick,
Fowler, Raschka, and others converged on this in early 2026). It is fine if
you read "agent" as the running process during a task. The field is mostly
building task-scoped agents where ephemerality is assumed by default — so the
distinction between agent-as-process and agent-as-pattern never bites them.

**Note:** enso takes a stricter position: the harness persists, not the
instantiation. Each task is a fresh agent instantiation; identity lives in the
harness.

It bites here because we want to talk about identity across many tasks,
which is the harness's job, not the instantiation's.

---

## Cross-reference to field usage

| Our term            | Field usage                                          |
|---------------------|------------------------------------------------------|
| Runtime             | "execution harness" (NVIDIA), "the harness" loosely  |
| Harness protocol    | (rare; closest: Anthropic's "meta-harness")          |
| Harness instance    | "harness" in most field writing                      |
| Agent instantiation | "agent" in `agent = model + harness`                 |
| Agent pattern       | (no field term; we mean it when we say "the agent")  |
| Substrate           | (no field term; usually "the codebase" or "the env") |

When talking to people outside this vocabulary, expect "harness" to mean the
instance, and expect "agent" to mean either the process or — sloppily — the
pattern, with no distinction drawn.

---

## Connections

- [*Enso*](./enso.md) — Context management system; `enso` is a harness protocol in this model
- [*Being*](../../being.md) — The question of continuous identity vs. recurring pattern
- [*Monistic Reincarnation*](../../monistic-reincarnation.md) — Pattern over particulars; the "same pattern reinstantiated" as a mode of being

---

## The one-line summary

> **Substrate persists. Harness persists. Agents recur.**
