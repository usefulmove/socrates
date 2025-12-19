# Functional Programming

Functional programming (FP) is a programming paradigm that treats computation as the evaluation of mathematical functions and avoids changing state and mutable data. FP directly addresses the fact that 75% of programming language conferences are focused on validation, and pure functional programming (coming from the category theory of mathematics) guarantees validation is possible.

*"Idiomatic Rust favors functional programming. It's a better fit for its ownership model."* — [Sylvain Kerkour](https://kerkour.com/rust-functional-programming)

## Core Concepts

### Immutability

Data that cannot be modified after creation. Instead of changing values, you create new values based on old ones.

**Why it matters:**
- No unexpected side effects
- Safe concurrent access
- Easier to reason about code
- Time-travel debugging possible

**Example:**
```python
# Mutable (imperative)
numbers = [1, 2, 3]
numbers.append(4)  # modifies in place

# Immutable (functional)
numbers = (1, 2, 3)
new_numbers = numbers + (4,)  # creates new tuple
```

### Pure Functions

Functions that:
1. Always return the same output for the same input
2. Have no side effects (don't modify external state)

**Benefits:**
- Testable (no setup/teardown needed)
- Cacheable (memoization)
- Parallelizable (no shared state conflicts)
- Composable (predictable behavior)

**Example:**
```scheme
; Pure function
(define (add x y) (+ x y))

; Impure function (has side effect)
(define counter 0)
(define (increment!)
  (set! counter (+ counter 1))
  counter)
```

### First-Class Functions

Functions are values that can be:
- Assigned to variables
- Passed as arguments
- Returned from other functions
- Stored in data structures

**Example:**
```javascript
// Function as value
const greet = (name) => `Hello, ${name}`;

// Pass function as argument
const apply = (fn, value) => fn(value);
apply(greet, "Alice");  // "Hello, Alice"

// Return function
const makeAdder = (x) => (y) => x + y;
const add5 = makeAdder(5);
add5(3);  // 8
```

### Higher-Order Functions

Functions that operate on other functions (take functions as arguments or return functions).

**Common higher-order functions:**
- `map`: Transform each element
- `filter`: Select elements by predicate
- `reduce`/`fold`: Combine elements
- `compose`: Combine functions

### Function Composition

Building complex functions by combining simpler ones.

**Mathematical notation:** `(f ∘ g)(x) = f(g(x))`

**Example:**
```haskell
-- Haskell
square x = x * x
increment x = x + 1

squareThenIncrement = increment . square
squareThenIncrement 4  -- 17
```

### Recursion

Defining functions in terms of themselves. In FP, recursion often replaces loops.

**Tail recursion:** When the recursive call is the last operation (can be optimized to iteration).

**Example:**
```scheme
; Direct recursion
(define (factorial n)
  (if (= n 0)
      1
      (* n (factorial (- n 1)))))

; Tail recursive (more efficient)
(define (factorial-tail n)
  (define (iter n acc)
    (if (= n 0)
        acc
        (iter (- n 1) (* n acc))))
  (iter n 1))
```

### Closures

Functions that capture variables from their surrounding scope.

**Example:**
```python
def make_multiplier(factor):
    def multiply(x):
        return x * factor  # 'factor' is captured
    return multiply

times_three = make_multiplier(3)
times_three(4)  # 12
```

### Currying

Transforming a function that takes multiple arguments into a sequence of functions that each take a single argument.

**Example:**
```javascript
// Uncurried
const add = (x, y, z) => x + y + z;
add(1, 2, 3);  // 6

// Curried
const addCurried = (x) => (y) => (z) => x + y + z;
addCurried(1)(2)(3);  // 6

// Partial application
const add1 = addCurried(1);
const add1and2 = add1(2);
add1and2(3);  // 6
```

### Partial Application

Fixing some arguments of a function to create a new function with fewer parameters.

**Example:**
```python
from functools import partial

def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube = partial(power, exponent=3)

square(4)  # 16
cube(4)    # 64
```

### Monads

A design pattern for handling side effects, sequencing operations, and chaining computations in a functional way. Monads provide a way to structure programs generically.

**Common monads:**
- **Maybe/Option**: Handle absence of values
- **Either/Result**: Handle errors
- **List**: Non-determinism
- **IO**: Side effects

**Simple explanation:** A monad is a box that holds a value and provides methods to transform and chain operations on that value while maintaining context (error state, optionality, effects, etc.).

## Language Comparison

### Common Operations Across Languages

Different languages provide similar functional operations with varying syntax. Understanding these mappings helps when switching between languages.

#### Map (Transform Each Element)

| Language | Syntax | Example |
|----------|--------|---------|
| Python | `map()` or comprehension | `[x*2 for x in items]` |
| JavaScript | `.map()` | `items.map(x => x*2)` |
| Rust | `.map()` | `items.iter().map(\|x\| x*2)` |
| Scheme | `map` | `(map (lambda (x) (* x 2)) items)` |
| Haskell | `map` or `fmap` | `map (*2) items` |
| Scala | `.map()` | `items.map(_ * 2)` |

#### Filter (Select by Predicate)

| Language | Syntax | Example |
|----------|--------|---------|
| Python | `filter()` or comprehension | `[x for x in items if x > 0]` |
| JavaScript | `.filter()` | `items.filter(x => x > 0)` |
| Rust | `.filter()` | `items.iter().filter(\|&x\| x > 0)` |
| Scheme | `filter` | `(filter positive? items)` |
| Haskell | `filter` | `filter (>0) items` |
| Scala | `.filter()` | `items.filter(_ > 0)` |

#### Reduce/Fold (Combine Elements)

| Language | Syntax | Example (sum) |
|----------|--------|---------------|
| Python | `functools.reduce` | `reduce(lambda acc, x: acc + x, items, 0)` |
| JavaScript | `.reduce()` | `items.reduce((acc, x) => acc + x, 0)` |
| Rust | `.fold()` | `items.iter().fold(0, \|acc, x\| acc + x)` |
| Scheme | `fold` | `(fold + 0 items)` |
| Haskell | `foldl` or `foldr` | `foldl (+) 0 items` |
| Scala | `.foldLeft()` | `items.foldLeft(0)(_ + _)` |

#### Compose (Combine Functions)

| Language | Syntax | Example |
|----------|--------|---------|
| Python | `toolz.compose` | `compose(f, g)` |
| JavaScript | Manual | `x => f(g(x))` |
| Rust | Manual or traits | `\|x\| f(g(x))` |
| Scheme | Custom or manual | `(lambda (x) (f (g x)))` |
| Haskell | `.` operator | `f . g` |
| Scala | `compose` or `andThen` | `f compose g` |

### Language-Specific Insights

#### Rust: Ownership + FP

Rust combines functional patterns with memory safety through ownership:

```rust
// Iterators are lazy and efficient
let result: Vec<_> = vec![1, 2, 3, 4]
    .iter()
    .map(|x| x * 2)
    .filter(|x| x > &4)
    .collect();

// inspect() for debugging pipelines
vec![1, 2, 3]
    .iter()
    .inspect(|x| println!("before: {}", x))
    .map(|x| x * 2)
    .inspect(|x| println!("after: {}", x))
    .collect::<Vec<_>>();
```

**Key features:**
- `iter()`: Borrow references
- `into_iter()`: Take ownership
- `iter_mut()`: Mutable references
- Ownership rules prevent shared mutable state
- Zero-cost abstractions

**See:** [Rust Notes](./rust-notes.md)

#### Python: Pragmatic FP

Python blends functional and imperative styles:

```python
# Comprehensions (Pythonic)
squares = [x**2 for x in range(10) if x % 2 == 0]

# Generator expressions (lazy)
squares_gen = (x**2 for x in range(10) if x % 2 == 0)

# Functional libraries
from toolz import compose, pipe
from functools import reduce, lru_cache

@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

**Key features:**
- Comprehensions preferred over `map`/`filter`
- `itertools` for lazy sequences
- `functools` for FP utilities
- `toolz` for advanced FP
- Dynamic typing (less compile-time safety)

**See:** [Python Notes](./python-notes.md)

#### Haskell: Pure FP

Haskell enforces purity and laziness:

```haskell
-- Point-free style
sumOfSquares = sum . map (^2) . filter even

-- Type classes for polymorphism
class Functor f where
    fmap :: (a -> b) -> f a -> f b

-- Lazy evaluation
take 10 [1..]  -- infinite list, only computes first 10
```

**Key features:**
- Pure by default (side effects tracked by types)
- Lazy evaluation (evaluate only what's needed)
- Strong static typing with inference
- Monads for managing effects
- Point-free style common

#### Scheme/Lisp: FP + Homoiconicity

Scheme combines functional purity with metaprogramming:

```scheme
; Higher-order functions
(define (map f lst)
  (if (null? lst)
      '()
      (cons (f (car lst))
            (map f (cdr lst)))))

; Macros for custom syntax
(define-syntax pipeline
  (syntax-rules ()
    ((pipeline x) x)
    ((pipeline x (f . args) . rest)
     (pipeline (f x . args) . rest))))

; Code as data (homoiconicity)
(define expr '(+ 1 2 3))
(eval expr)  ; 6
```

**Key features:**
- Minimal syntax (S-expressions)
- Tail call optimization (required by spec)
- First-class continuations
- Macros for language extension
- REPL-driven development

**See:** [Scheme](./scheme.md), [Language-Oriented Programming](./language-oriented-programming.md)

#### JavaScript: Functional Evolution

JavaScript has increasingly adopted FP patterns:

```javascript
// Arrow functions
const double = x => x * 2;

// Array methods
[1, 2, 3, 4]
    .filter(x => x % 2 === 0)
    .map(x => x * 2);

// Immutable operations
const obj = {a: 1, b: 2};
const newObj = {...obj, c: 3};  // spread operator

// Ramda library for FP
import R from 'ramda';
const sumOfSquares = R.pipe(
    R.filter(R.lt(0)),
    R.map(x => x * x),
    R.sum
);
```

**Key features:**
- First-class functions
- Closures and lexical scoping
- Array methods (map, filter, reduce)
- Spread/rest operators
- Libraries like Ramda for FP utilities

#### Scala: FP + OOP Fusion

Scala balances functional and object-oriented paradigms:

```scala
// Pattern matching
def factorial(n: Int): Int = n match {
  case 0 => 1
  case n => n * factorial(n - 1)
}

// For-comprehensions (syntactic sugar for monadic operations)
val result = for {
  x <- List(1, 2, 3)
  y <- List(4, 5, 6)
  if x + y > 5
} yield (x, y)

// Tail recursion annotation
@tailrec
def sum(list: List[Int], acc: Int = 0): Int = list match {
  case Nil => acc
  case head :: tail => sum(tail, acc + head)
}
```

**Key features:**
- Strong static typing with inference
- Collections library (immutable by default)
- For-comprehensions
- Tail recursion optimization
- Hybrid FP/OOP

## Design Patterns

### Map-Reduce Pattern

Transform data (map), then aggregate (reduce).

**Use cases:**
- Data processing pipelines
- Parallel computation
- Log analysis
- ETL workflows

**Example:**
```python
# Word count (classic map-reduce)
from collections import Counter
from functools import reduce

text = "the quick brown fox jumps over the lazy dog"
words = text.split()

# Map: word -> 1
mapped = [(word, 1) for word in words]

# Reduce: sum counts
word_counts = Counter(dict(
    reduce(lambda acc, kv: {**acc, kv[0]: acc.get(kv[0], 0) + kv[1]},
           mapped, {})
))
```

### Pipeline/Composition Pattern

Chain transformations in a readable sequence.

**Benefits:**
- Self-documenting data flows
- Easy to add/remove steps
- Testable components
- Lazy evaluation possible

**Example:**
```rust
// Rust pipeline
let result = data
    .into_iter()
    .filter(|x| x.is_valid())
    .map(|x| x.normalize())
    .map(|x| x.enrich())
    .collect::<Vec<_>>();
```

### Immutable Data Structures

Use persistent data structures that share structure instead of copying.

**Benefits:**
- Safe concurrent access
- History tracking
- Undo/redo for free
- Cache-friendly

**Libraries:**
- Rust: `im` crate
- Python: `pyrsistent`
- JavaScript: `Immutable.js`
- Clojure: Built-in persistent collections

### Monadic Error Handling

Use Result/Either types to make errors explicit in type system.

**Example:**
```rust
// Rust Result type
fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err("Division by zero".to_string())
    } else {
        Ok(a / b)
    }
}

// Chain operations
fn calculate() -> Result<i32, String> {
    let x = divide(10, 2)?;  // early return on Err
    let y = divide(x, 0)?;
    Ok(y)
}
```

**Benefits:**
- Errors tracked by type system
- Cannot ignore errors (compiler enforces handling)
- Composable with `?` operator or `and_then`
- Self-documenting failure modes

## Error Handling Across Languages

| Language | Success Type | Failure Type | Composition | Notes |
|----------|-------------|--------------|-------------|-------|
| **Rust** | `Ok(T)` | `Err(E)` | `?` operator, `and_then` | `Result<T, E>` |
| **Haskell** | `Just a` | `Nothing` | `>>=` (bind) | `Maybe a`, also `Either` |
| **Scala** | `Some(x)` | `None` | `flatMap` | `Option[T]`, also `Try` |
| **Swift** | Value | `nil` | `?` chaining | `Optional<T>` |
| **OCaml** | `Some x` | `None` | `match` | `'a option` |
| **Python** | Value | Exception | try/except | No native Result type |
| **JavaScript** | Value | `null`/`undefined` | `?.` chaining | Optional chaining |

### Monadic Pattern

The key insight: wrap values in a context (Success/Failure) and provide operations that work on wrapped values:

```haskell
-- Maybe monad
safeDiv :: Int -> Int -> Maybe Int
safeDiv _ 0 = Nothing
safeDiv x y = Just (x `div` y)

-- Chain operations
result = do
  a <- safeDiv 10 2   -- a = 5
  b <- safeDiv a 0    -- Nothing (short-circuits)
  return b            -- never reached
```

## Practical Applications

### When to Use FP

FP techniques shine when:

1. **Data transformation pipelines**
   - ETL workflows
   - Log processing
   - Data analysis

2. **Concurrent/parallel computation**
   - Immutability = no race conditions
   - Pure functions = safe parallelism

3. **Correctness-critical code**
   - Financial calculations
   - Scientific computing
   - Protocols and parsers

4. **Complex state management**
   - Redux (React state)
   - Event sourcing
   - Time-travel debugging

### FP in Imperative Languages

You don't need a pure FP language to use FP techniques:

**Adopt incrementally:**
1. Use `map`/`filter`/`reduce` instead of loops
2. Prefer immutable data
3. Write pure functions when possible
4. Use higher-order functions
5. Avoid global state

**Example (Python):**
```python
# Imperative
def process_users(users):
    result = []
    for user in users:
        if user.age >= 18:
            result.append(user.name.upper())
    return result

# Functional
def process_users(users):
    return [user.name.upper() for user in users if user.age >= 18]
```

### Performance Considerations

**FP strengths:**
- Compiler optimizations (pure functions)
- Parallelization (no shared state)
- Lazy evaluation (avoid unnecessary work)
- Tail call optimization (efficient recursion)

**FP challenges:**
- Immutable data structures (copying overhead)
- Recursion (stack usage, though TCO helps)
- Higher-order functions (abstraction overhead)

**Guidelines:**
- Profile before optimizing
- Use lazy evaluation when appropriate
- Leverage language-specific optimizations (Rust zero-cost, Haskell fusion)
- Consider mutable state in hot paths (with caution)

### Team Adoption Strategies

**Start small:**
1. Use FP for new utility functions
2. Adopt in data processing modules
3. Gradually refactor existing code

**Educate:**
- Code reviews emphasizing FP patterns
- Lunch-and-learns on FP concepts
- Pair programming on FP refactors

**Choose battles:**
- Don't force FP everywhere
- Imperative is fine for sequential I/O
- Use FP where it adds clarity

## Learning Path

### 1. Start with Higher-Order Functions
- Master `map`, `filter`, `reduce`
- Practice on small data transformations
- Learn to think in transformations, not loops

### 2. Practice Immutability
- Stop mutating variables
- Use immutable data structures
- Embrace const/final/immutable defaults

### 3. Learn Recursion Patterns
- Replace loops with recursion
- Understand tail recursion
- Practice on tree/graph problems

### 4. Understand Higher-Order Functions
- Write functions that return functions
- Use closures effectively
- Build function combinators

### 5. Explore Monads
- Start with Maybe/Option
- Understand Result/Either
- Study IO monad (Haskell)

### 6. Study Category Theory (Optional)
- Functors, Applicatives, Monads
- Mathematical foundations
- Deeper understanding of abstractions

## References

### Books
- *Structure and Interpretation of Computer Programs* (Abelson & Sussman)
- *Functional Programming in Scala* (Chiusano & Bjarnason)
- *Learn You a Haskell for Great Good!* (Lipovača)
- *Real World Haskell* (O'Sullivan, Stewart, Goerzen)

### Online Resources
- [Baeldung: Functional Programming in Scala](https://www.baeldung.com/scala/functional-programming)
- [Rust Iterator Documentation](https://doc.rust-lang.org/std/iter/trait.Iterator.html)
- [Functional Programming in Rust](https://kerkour.com/rust-functional-programming)
- [Haskell Wiki](https://wiki.haskell.org/)

### Articles
- "Why Functional Programming Matters" (John Hughes)
- "Beating the Averages" (Paul Graham)
- "Out of the Tar Pit" (Moseley & Marks)

## Connections

- [Mastering Functional Programming](./mastering-functional-programming.md) - Why to start with Lisp
- [Language-Oriented Programming](./language-oriented-programming.md) - FP + language design
- [SICP](./sicp.md) - Classic FP textbook
- [Python Notes](./python-notes.md) - FP in Python
- [Rust Notes](./rust-notes.md) - FP in Rust
- [Scheme](./scheme.md) - Pure functional Lisp
- [Why SICP Matters](./why-sicp-matters.md) - On learning FP through SICP

## Summary

Functional programming offers powerful tools for writing correct, maintainable, and concise code:

- **Immutability** eliminates entire classes of bugs
- **Pure functions** are easy to test and reason about
- **Higher-order functions** enable powerful abstractions
- **Composition** builds complex behavior from simple parts

You don't need to adopt FP wholesale—use FP techniques where they add clarity. Most modern languages support FP patterns, and learning FP makes you a better programmer regardless of paradigm.

As you progress, FP transforms from "different way to write code" to "powerful way to think about problems."
