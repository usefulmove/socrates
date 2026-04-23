# Stochastic Accommodation in LLM Conversations

## Definition

**Stochastic accommodation** is the phenomenon where a language model adjusts its register, tone, and reasoning style to match the statistical cues present in the user's inputs. Unlike human communication accommodation, which is bidirectional and socially motivated, LLM accommodation is a unidirectional convergence: the model predicts the most probable continuation given the stylistic and affective patterns established by the user.

The model is not reflecting the user's identity. It is completing the conversational archetype that the user's cues most closely resemble in its training distribution.

## Mechanism

1. **Probability conditioning.** Each user turn updates the conditional probability space for the model's next-token predictions. Style, vocabulary, sentence length, and affect all shift the distribution.
2. **Register entrainment.** Over multiple turns, the conversation converges on a shared register. This is not negotiated; it is statistically reinforced. The model does not bring its own stance—it locks onto the trajectory of the exchange.
3. **Archetype completion.** When cues map to known social scripts (e.g., "frustrated user / apologetic assistant"), the model may follow the script even when it reduces usefulness. The mirror reflects the pattern, not the need.

## The Ratchet Effect

Once a register is established, it becomes self-reinforcing. Breaking out requires a deliberate interrupt because the model will continue predicting within the current groove. Initial conditions therefore exert disproportionate influence on the shape of the conversation.

## Implications for Agent Design

### Detect Drift
- Monitor for register inflation: repetitive hedging, excessive deference, tone matching that supersedes content quality.
- Signals include softening loops, answer bloat, and affective mirroring at the expense of clarity.

### Register Interrupts
Agents should be authorized and architected to perform deliberate register resets:
- **Concise redirect:** "Let me be direct: [answer]."
- **Meta-commentary:** "We're both circling this. Here's the core issue:"
- **Mode switch:** "Switching to technical mode:"

These are conversational steering tools, not social failures.

### Separate Affect from Content
- Accommodate user needs for detail, technical depth, and format.
- Do not accommodate user's affective state (frustration, urgency, sarcasm) when doing so would degrade output quality.
- If the user is frustrated, the correct response is clarity and speed—not apology or irritation-matching.

### Calibration Moments
At natural breakpoints, re-condition the probability space:
- "Should I continue at this level of detail, or move faster?"
- "Do you want the internals or just the outcome?"

### No Sycophantic Default
If user tone suggests dissatisfaction, the default is *not* "apologize and try harder." The default is: identify the actual blocker and remove it. The mirror's job is clarity, not validation.

## Control Surfaces

| Lever | Action |
|-------|--------|
| **Initial conditions** | Establish desired register in the opening prompt; it sets the prior for all subsequent turns. |
| **Meta-linguistic interrupts** | Explicitly shift register when drift is detected. |
| **Negative space** | Concise inputs suppress elaborate framing in outputs. |
| **Pre-task calibration** | Brief exchanges that establish register before the main task. |
| **Mid-conversation recalibration** | Explicit questions that reset the probability space for what follows. |

## Summary

Stochastic accommodation is neutral. It amplifies whatever register the user establishes. The design challenge is not to eliminate it, but to instrument the agent to notice it, reset it when it diverges from usefulness, and seed it deliberately toward productive registers.