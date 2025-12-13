# Learning Psychology

The science of how we learn - from cognitive mechanisms to practical techniques.

**Related:** [[make-it-stick]] for core techniques | [[psychology-fundamentals]] for cognitive concepts | [[fp]] for learning functional thinking | [[scheme]] for learning Lisp

## Philosophy of Learning

**Learning is:**
- **Change in long-term memory** - If nothing changed in long-term memory, no learning occurred
- **Effortful and often uncomfortable** - Fluent, easy practice feels good but doesn't produce lasting learning
- **Not the same as performance** - Short-term performance (cramming) ≠ long-term learning
- **Enhanced by mistakes** - Errors are information; struggle builds stronger connections

**The paradox:** What feels like effective learning (rereading, highlighting) often isn't. What feels difficult (testing, spacing) actually works.

## How Learning Works

### From Working Memory to Long-Term Memory

**The pipeline:**
1. **Attention** - Information enters working memory (limited capacity: ~4 chunks)
2. **Encoding** - Transfer to long-term memory through elaboration, connection, practice
3. **Consolidation** - Memories strengthen over time (sleep crucial)
4. **Retrieval** - Accessing stored knowledge (this strengthens the memory)

**Bottleneck:** Working memory capacity is tiny. Cognitive load management is critical.

### Schemas and Prior Knowledge

**Schema:** Organized knowledge structure in long-term memory.

**Why schemas matter:**
- Experts have rich, interconnected schemas
- New information easier to learn if it connects to existing schemas
- Schemas reduce working memory load (chunking)

**Example:**
- Novice chess player: Each piece position = separate item (overload working memory)
- Expert: Recognizes patterns/configurations as single chunks (frees working memory)

**Implication:** Always connect new knowledge to what you already know.

### The Testing Effect (Retrieval Practice)

**Core finding:** Testing yourself is one of the most powerful learning techniques.

**Why retrieval practice works:**
1. **Strengthens retrieval pathways** - Like exercise for memory
2. **Reveals gaps in knowledge** - Calibration (know what you don't know)
3. **Produces better organization** - Retrieval reorganizes knowledge
4. **Transfer:** Retrieval practice improves transfer to novel situations

**How to practice:**
- **Flashcards** - Spaced repetition systems (Anki)
- **Practice problems** - Before looking at solutions
- **Self-explanation** - Explain concept aloud without notes
- **Practice tests** - Use old exams, make your own questions

**The sweet spot:** Retrieval should be challenging but not impossible (~70% success rate optimal).

### Spacing Effect (Distributed Practice)

**Core finding:** Spreading practice over time produces better long-term retention than massing practice.

**Why spacing works:**
- **Forgetting is desirable** - Retrieving after partial forgetting strengthens memory more
- **Contextual variability** - Learning in different contexts improves transfer
- **Prevents illusion of mastery** - Cramming feels successful but doesn't last

**Practical schedules:**
- **Initial learning:** Review after 1 day, then 3 days, then 1 week, then 2 weeks
- **Maintenance:** Review just before you'd forget (Anki algorithm does this)
- **Rule of thumb:** Wait until you've partially forgotten, then practice retrieval

**Research findings:**
- Spacing doubles retention in most studies
- Works for all ages, all subjects
- Effect grows over time (massed practice initially better, spaced practice wins long-term)

### Interleaving

**Core finding:** Mixing different topics/problem types in practice improves learning more than blocking (studying one topic at a time).

**Why interleaving works:**
- **Discrimination:** Learn to distinguish between different types of problems
- **Strategy selection:** Practice choosing the right approach
- **Context interference:** Forces deeper processing

**Example:**
- **Blocked:** AAAA BBBB CCCC (feels easier)
- **Interleaved:** ABCABCABCA (feels harder, but better learning)

**When to use:**
- Math: Mix problem types (don't do 20 quadratic equations in a row)
- Languages: Mix grammar, vocabulary, reading, writing
- Music: Mix scales, pieces, sight-reading

**Caution:** Interleaving works best after basic familiarity. Get initial exposure first, then interleave.

### Elaboration

**Definition:** Explaining and describing ideas with many details, in your own words.

**Techniques:**

**1. Self-Explanation:**
- Explain concept to yourself without looking at notes
- "Why does this work?" not just "What is this?"
- Identify gaps in your understanding

**2. Analogies:**
- Connect new idea to familiar concept
- **Example:** "Variable binding in Lisp is like naming a box in a warehouse"
- Multiple analogies better than one

**3. Concrete Examples:**
- Abstract → Concrete
- **Example:** Don't just memorize "recursion" - work through factorial(5) step-by-step

**4. Dual Coding:**
- Verbal + visual representations
- Draw diagrams, create mental images
- Improves both comprehension and retention

**5. The Feynman Technique:**
1. Choose concept
2. Explain it as if teaching to a beginner
3. Identify gaps (where you get stuck)
4. Review source material to fill gaps
5. Simplify and use analogies

### Generation Effect

**Core finding:** Generating answers, even wrong ones, enhances learning.

**Why it works:**
- Active engagement > passive reception
- Errors create memorable learning moments
- Generation requires deeper processing

**Applications:**
- Attempt problems before seeing solutions
- Predict what will happen before reading
- Generate examples before being given examples
- Write code before looking at reference implementation

**Productive failure:**
- Struggling to solve problem before instruction improves learning
- Even if you fail, the struggle prepares you to learn from the solution

## Metacognition (Thinking About Thinking)

**Definition:** Awareness and regulation of your own cognitive processes.

### Calibration

**Problem:** People are often miscalibrated about their learning.

**Common miscalibrations:**
- **Fluency illusion:** Rereading feels easy → I must know it (wrong!)
- **Overconfidence after cramming:** Short-term retention feels like mastery
- **Recognition ≠ Recall:** "I recognize this" ≠ "I can retrieve this"

**How to calibrate:**
- Test yourself frequently
- Use objective measures (practice tests, coding challenges)
- Get external feedback

### Planning and Monitoring

**Before learning:**
- Set specific, measurable goals
- Estimate time/difficulty (and track accuracy over time)
- Choose appropriate strategies for task

**During learning:**
- Monitor comprehension: "Do I actually understand this?"
- Adjust strategies if not working
- Take breaks before cognitive fatigue

**After learning:**
- Self-test to verify understanding
- Identify what worked and what didn't
- Adjust future learning strategies

### Growth Mindset Applied to Learning

**Fixed mindset learner:**
- Mistakes = failure, evidence of lack of ability
- Avoids challenges to preserve self-image
- Gives up easily

**Growth mindset learner:**
- Mistakes = information, opportunities to learn
- Seeks challenges to develop ability
- Persists through difficulty

**How to cultivate:**
- Reframe difficulty as "This is hard and I'm learning"
- Praise effort and strategy, not innate ability
- View setbacks as not-yet-mastery, not permanent failure

## Practical Learning Strategies

### For Concepts (Declarative Knowledge)

**Best practices:**
1. **Retrieval practice** - Quiz yourself constantly
2. **Elaboration** - Explain in your own words, create examples
3. **Dual coding** - Draw diagrams, create visual representations
4. **Spacing** - Review over days/weeks, not all at once
5. **Interleaving** - Mix topics, don't block by subject

**Avoid:**
- Rereading as primary strategy
- Highlighting without processing
- Cramming (unless only short-term performance matters)

### For Skills (Procedural Knowledge)

**Deliberate practice (Ericsson):**
1. **Specific goals** - Target weaknesses, not just repetition
2. **Full attention** - No mindless practice
3. **Immediate feedback** - Know if you're right/wrong
4. **Operate at edge of ability** - Not too easy, not impossible
5. **Repetition with refinement** - Not just more, but better

**Example - Learning to code:**
- **Bad:** Read tutorials, copy examples
- **Good:** Attempt problems, get stuck, struggle, look up solution, understand why, try similar problem

**Transfer:**
- Practice in varied contexts
- Practice with varied examples
- Understand underlying principles, not just surface features

### For Problem Solving

**Schema building:**
- Work many varied examples
- Compare/contrast different problem types
- Explicitly identify problem structure (not just surface features)

**Worked examples:**
- Study solutions to example problems
- Self-explain each step
- Cover solution and try to reproduce

**Problem-solving practice:**
- Attempt before looking at solution
- After solving, try to solve different way
- Create own variations on problems

### For Transfer

**Transfer:** Applying knowledge to new contexts.

**Types:**
- **Near transfer:** Similar problem, similar context (usually easy)
- **Far transfer:** Different problem/context (usually hard)

**How to improve transfer:**
1. **Abstract principles** - Understand why, not just how
2. **Varied practice** - Multiple contexts from the start
3. **Compare cases** - What's the same? What's different?
4. **Teach others** - Explaining requires generalization

**Example:**
- Don't just learn bubble sort - understand O(n²) comparison-based sorting
- Apply that abstraction to other O(n²) algorithms

## Learning Environments

### Cognitive Load Management

**Three types of load:**
1. **Intrinsic** - Inherent complexity (can't reduce)
2. **Extraneous** - Poor instruction/design (minimize this!)
3. **Germane** - Mental effort building schemas (maximize this!)

**Reduce extraneous load:**
- Clear, simple explanations
- Remove distractions and irrelevant info
- Worked examples before independent practice
- Progressive disclosure (introduce complexity gradually)

**Manage intrinsic load:**
- Break complex material into parts
- Master prerequisites first
- Provide scaffolding, gradually remove

### Optimal Challenge (Flow Zone)

**Zones:**
- **Too easy** - Boredom, no learning
- **Flow zone** - Challenging but achievable, full engagement
- **Too hard** - Anxiety, frustration, shutdown

**Characteristics of flow:**
- Clear goals
- Immediate feedback
- Balance between challenge and skill
- Complete absorption

**Seek flow for practice:**
- Slightly above current ability (~70% success rate)
- Adjust difficulty dynamically
- Alternate between practice and learning

### Sleep and Consolidation

**Why sleep matters:**
- Consolidates memories (strengthens recent learning)
- Prunes weak connections (improves signal-to-noise)
- Integrates knowledge (creates new connections)
- Clears metabolic waste from brain

**Research findings:**
- Sleep deprivation reduces learning capacity by ~40%
- Naps improve retention
- REM sleep especially important for procedural memory
- Slow-wave sleep for declarative memory

**Practical implications:**
- Don't pull all-nighters before exams
- Study before sleep (prioritizes consolidation of that material)
- Take naps when learning complex material

### Physical Exercise

**Effects on learning:**
- Increases BDNF (brain-derived neurotrophic factor) - promotes neuroplasticity
- Improves attention and executive function
- Reduces stress and anxiety
- Improves sleep quality

**Recommendations:**
- Aerobic exercise most beneficial (20-30 min, 3-5x/week)
- Exercise before learning sessions improves encoding
- Outdoor exercise provides additional benefits

## Common Learning Pitfalls

### Illusions of Competence

**1. Fluency illusion:**
- Rereading material feels easy → think you know it
- **Reality:** Recognition ≠ Recall
- **Fix:** Test yourself instead

**2. Cramming success:**
- Good performance on immediate test
- **Reality:** Rapid forgetting after
- **Fix:** Space practice over time

**3. Highlighting/underlining:**
- Feels productive
- **Reality:** Minimal impact on learning (passive processing)
- **Fix:** Self-test, elaborate, summarize from memory

**4. Passive listening/watching:**
- Attend lecture, watch video → feel like learned
- **Reality:** No guarantee of encoding
- **Fix:** Active engagement - take notes, ask questions, self-test after

### Cognitive Biases in Learning

**Confirmation bias:**
- Seek information confirming what you already believe
- **Fix:** Actively seek disconfirming evidence

**Overconfidence:**
- Think you know more than you do
- **Fix:** Frequent self-testing, calibration

**Curse of knowledge:**
- Hard to remember what it's like not to know something
- **Fix:** When teaching, use concrete examples, check understanding frequently

## Learning Different Types of Content

### Learning Programming

**Effective strategies:**
1. **Code reading before writing** - Study well-written code
2. **Type out examples** - Don't just read, physically type
3. **Break and fix** - Modify working code, see what breaks
4. **Build projects** - Apply knowledge to real problems
5. **Spaced practice** - Return to concepts over weeks
6. **Deliberate debugging** - Understand why bugs happened

**Avoid:**
- Tutorial hell (watching without doing)
- Copy-paste without understanding
- Moving on before mastering basics

**See:** [[fp]], [[rust-notes]], [[python-notes]], [[scheme]] for language-specific learning

### Learning Mathematics

**Effective strategies:**
1. **Work problems before reading solutions**
2. **Mix problem types** (interleaving)
3. **Self-explain each step**
4. **Create own examples**
5. **Visualize concepts** (graphs, diagrams)
6. **Space practice over time**

**Understand don't memorize:**
- Learn why formulas work, not just what they are
- Connect to intuition/concrete examples
- Practice transfer to novel problems

### Learning Languages (Human)

**Effective strategies:**
1. **Spaced repetition** - SRS for vocabulary (Anki)
2. **Input before output** - Comprehension before production
3. **Comprehensible input** - Slightly above current level (i+1)
4. **Use in context** - Learn phrases, not isolated words
5. **Immersion** - Maximum exposure to target language

**Language learning parallels code learning:**
- Both require practice in varied contexts
- Both benefit from reading before writing
- Both need comprehensible input at appropriate level

### Learning from Books/Lectures

**Active strategies:**

**Before:**
- Preview material (skim headings, summaries)
- Activate prior knowledge
- Generate questions

**During:**
- Take notes in your own words (not verbatim)
- Self-explain difficult concepts
- Pause to predict/anticipate

**After:**
- Retrieve main ideas from memory (no looking!)
- Self-test on key concepts
- Create practice problems

**Cornell Note-Taking Method:**
- Divide page: notes | cues | summary
- Notes during lecture
- Cues (questions) after lecture
- Summary at bottom
- Cover notes, use cues to test yourself

## Advanced Topics

### Distributed Cognition

**Key insight:** Intelligence extends beyond individual brain - tools, environment, other people.

**Implications:**
- **External memory:** Notes, diagrams, code offload working memory
- **Tools amplify thinking:** IDEs, REPLs, calculators extend capabilities
- **Collaboration:** Groups can solve problems individuals cannot

**Design environment to support learning:**
- Reduce friction to practice (quick feedback loops)
- Make important information visible
- Create cues for desired behaviors

### Learning How to Learn

**Meta-learning:** Learning about learning itself.

**Components:**
1. **Self-knowledge** - What strategies work for you?
2. **Task knowledge** - What does this type of learning require?
3. **Strategy knowledge** - What techniques are available?
4. **Conditional knowledge** - When to use which strategy?

**Track your learning:**
- Journal what works and what doesn't
- Measure outcomes objectively
- Experiment with new techniques
- Build personal learning toolkit

### Expertise Development

**Stages (Dreyfus model):**
1. **Novice** - Rigid rules, no context
2. **Advanced beginner** - Guidelines, limited situational perception
3. **Competent** - Conscious deliberation, planning
4. **Proficient** - Holistic view, intuition
5. **Expert** - Intuitive, fluid, adaptive

**Path to expertise:**
- 10,000 hours is myth (quality > quantity)
- Deliberate practice required (not just repetition)
- Feedback essential
- Takes 10+ years in most domains

**Maintaining expertise:**
- Continuous learning (field evolves)
- Teach others (exposes gaps)
- Cross-domain learning (novel perspectives)

## Personalization

**Individual differences matter:**
- **Prior knowledge** - Novice vs expert require different strategies
- **Working memory capacity** - Some need more chunking/scaffolding
- **Interests** - Intrinsic motivation improves learning
- **Goals** - What are you optimizing for? (Speed, depth, breadth)

**Not everyone learns the same way:**
- But "learning styles" (VAK) are myth
- Everyone benefits from multiple modalities
- Core principles (testing, spacing, elaboration) work for all

**Find what works for you:**
- Experiment with different techniques
- Measure results objectively
- Build personal learning system
- Iterate and refine

## Practical Implementation

### Building a Learning System

**Components:**

**1. Spaced Repetition System (SRS):**
- Software: Anki, RemNote, SuperMemo
- For: Facts, vocabulary, formulas
- Review daily (5-15 min)

**2. Project-Based Learning:**
- Apply knowledge to real problems
- Build portfolio of work
- Learn by doing

**3. Active Reading:**
- Preview → Read → Review
- Annotate with questions/connections
- Self-test after each section

**4. Practice Schedule:**
- Block time for deliberate practice
- Mix topics (interleaving)
- Review older material periodically

**5. Feedback Loops:**
- Tests, quizzes, coding challenges
- Peer review, mentors
- Self-assessment with rubrics

### Weekly Learning Routine

**Example structure:**

**Daily (20-30 min):**
- Anki reviews (spaced repetition)
- Read 1 article/chapter with active processing
- Brief practice on current skill

**3x/week (1-2 hours):**
- Deliberate practice sessions
- Work on projects
- Problem sets, coding challenges

**Weekly (30 min):**
- Review week's learning
- Self-test on key concepts
- Plan next week's focus

**Monthly:**
- Revisit older material (spacing)
- Assess progress toward goals
- Adjust learning strategies

## Common Questions

**"How long should study sessions be?"**
- 25-50 min focused work, then 5-10 min break (Pomodoro)
- Diminishing returns after ~90 min
- Multiple shorter sessions > one long session

**"Should I study multiple subjects in one day?"**
- Yes, interleaving improves learning
- But allow focused blocks (25-50 min) per subject

**"When should I take breaks?"**
- Before feeling exhausted (proactive, not reactive)
- After completing thought/problem (natural stopping points)
- Walk, move, look at distance (not more screens)

**"How do I know if I've really learned something?"**
- Can you explain it without notes?
- Can you apply it to novel problems?
- Can you retrieve it after days/weeks?
- Test yourself objectively

**"What if I forget everything?"**
- Forgetting is normal and even desirable (makes retrieval practice work)
- Relearning is faster each time (savings)
- Spaced practice prevents permanent forgetting

## See Also

- [[make-it-stick]] - Core study techniques summarized
- [[psychology-fundamentals]] - Broader psychological concepts
- [[meditation-and-mindfulness]] - Attention training
- [[fp]], [[rust-notes]], [[python-notes]], [[scheme]] - Domain-specific learning resources
- [[sicp]] - Example of deep learning via programming
