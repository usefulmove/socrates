# Model, Harness, Agent — Terminology

Working vocabulary for talking about LLMs, harnesses, and agents. Settled through
discussion and sanity-checked against current practitioner writing (LangChain,
Anthropic, Parallel, NVIDIA, the harness-survey literature). Use this when
precision matters; fall back to field shorthand when it doesn't.

---

## The stack

From substrate up to identity:

- **Model** — the LLM. Weights + inference. A pure function from context to tokens.
- **Harness protocol** — the spec. How the loop runs, how tools are defined, what
  extension points exist. `enso` is a harness protocol.
- **Harness runtime** — the executable that *implements* a protocol and provides
  the machinery: model client, tool dispatcher, session storage, TUI, extension
  loader. `pi`, Claude Code, OpenCode, Cursor, Codex are harness runtimes. They
  ship their own protocol, usually extensible. NVIDIA calls these "execution
  harnesses."
- **Harness** (instance) — a runtime configured for purpose: protocol + config
  (prompts, persona, tools, skills, policies) + session log. The thing actually
  running.
- **Agent** — an ephemeral process summoned by a harness to do a task. Inhabits
  the harness for the duration of the task, then dissolves.

Same shape as: language spec → interpreter → running program. The protocol is
the spec, the runtime is the interpreter, the harness is the interpreter
configured and loaded, the agent is what runs.

---

## Definitions

### Model
The LLM. Substrate. Swappable. Not constitutive of any agent's identity — only
of its capability ceiling.

### Harness protocol
The interface and rules: tool schema, loop semantics, extension points,
lifecycle hooks. A protocol can have multiple runtime implementations.

### Harness runtime
A concrete executable that implements a protocol. Provides the agent loop, tool
dispatch, model plumbing, context management, session persistence, and usually
opinionated defaults (built-in tools, prompt scaffolding, skills loader).
`pi` is a runtime. So are Claude Code and Codex.

Per Anthropic's *Managed Agents* architecture, a runtime can be further
decomposed into three decoupled pieces:

- **Brain** — the loop that calls the model and routes tool calls.
- **Sandbox / hands** — the execution environment for tools and code.
- **Session** — the durable, append-only event log. Lives *outside* the
  brain so it can survive crashes and outlive any particular runtime instance.

This decoupling matters because it places continuity (session) outside the
disposable computational machinery (brain, sandbox).

### Harness (instance)
A runtime in a specific configuration: persona, system prompt, tool inventory,
skills, policies, plus the session log. This is what most field writing
("Anatomy of an Agent Harness," etc.) means by "harness."

### Agent
A process summoned by a harness to handle a task. Ephemeral. Reads the pattern
(config + session log), behaves as that pattern for the duration of the task,
dissolves at the end. There is no continuous agent between calls. What feels
like a persistent agent is a pattern being reinstantiated.

---

## The identity claim

> **Harness persists. Agents recur.**

The persistent thing is the harness — its config files, its logs, its tools,
its runtime. Agents are what the harness manifests in response to work. A
particular Toni-process exists only while it is generating tokens; between
turns and between sessions, nothing is "being Toni." There is only data on
disk and a runtime waiting to be invoked.

So when we say "the same agent across sessions," we mean *the same
pattern reinstantiated*. That phrasing is shorthand, useful but not literal.
Closer to reincarnation than to a continuous self: pattern over particulars,
recurring instantiations in a closed world.

This means:

- **Identity lives in the harness**, not in the agent. Persona (config) and
  continuity (session log) are harness components.
- **Agents are downstream of the harness.** You can't engineer them directly.
  You engineer the field that produces them.
- **Harness engineering is the discipline** — by necessity, because the
  ephemeral thing has no surface to work on.

---

## Implications

### `pi` and Claude Code are not agents
They are harness runtimes. Asking whether `pi` is an agent is a category
error — it's the thing agents come from.

### The "Sonnet vs Opus, same agent?" question dissolves
There is no continuous agent to identify across model swaps. Two summonings
from the same pattern with different substrate. Pattern identity persists;
agent identity is a non-question because agents don't endure.

### Capabilities vs. identity
- Swap the **model** → different substrate, same pattern. Quality and quirks
  shift. Pattern intact.
- Add or remove **tools** → different capability surface, same pattern.
- Edit **persona / SOUL.md** materially → different pattern. Subsequent
  agents are summoned from a different specification.
- Wipe **session log** → same pattern, no continuity. Functionally a
  stranger.

### Why the field's equation works anyway
The dominant field formula is `agent = model + harness` (LangChain, Mollick,
Fowler, Raschka, and others converged on this in early 2026). It is fine if
you read "agent" as the running process during a task. The field is mostly
building task-scoped agents where ephemerality is assumed by default — so the
distinction between agent-as-process and agent-as-pattern never bites them.

It bites here because we want to talk about identity across many tasks,
which is the harness's job, not the agent's.

---

## Cross-reference to field usage

| Our term            | Field usage                                          |
|---------------------|------------------------------------------------------|
| Harness protocol    | (rare; closest: Anthropic's "meta-harness")          |
| Harness runtime     | "execution harness" (NVIDIA), "the harness" loosely  |
| Harness (instance)  | "harness" in most field writing                      |
| Agent (process)     | "agent" in `agent = model + harness`                 |
| Agent pattern       | (no field term; we mean it when we say "the agent")  |

When talking to people outside this vocabulary, expect "harness" to mean the
instance, and expect "agent" to mean either the process or — sloppily — the
pattern, with no distinction drawn.

---

## Connections

- [*Agent Harness*](./agent-harness.md) — The practical infrastructure layer this terminology refines
- [*Enso*](./enso.md) — Context management system; `enso` is a harness protocol in this model
- [*Being*](../../being.md) — The question of continuous identity vs. recurring pattern
- [*Monistic Reincarnation*](../../monistic-reincarnation.md) — Pattern over particulars; the "same pattern reinstantiated" as a mode of being

---

## The one-line summary

> **Model is substrate. Harness persists. Agents recur.**
