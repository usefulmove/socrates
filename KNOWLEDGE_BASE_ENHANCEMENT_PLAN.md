# Knowledge Base Enhancement Plan

**Date:** December 12, 2025  
**Status:** Active Implementation

---

## Overview

This document tracks the comprehensive enhancement of the Socrates knowledge base, focusing on:
1. Cross-language programming reference
2. Language-Oriented Programming content  
3. Data analysis workflow patterns
4. Psychology expansion
5. Philosophical synthesis

---

## Current State Analysis

### Structure
- **Main KB** (`/org/`): 80+ files, ~4,900 lines
- **Agora** (`/agora/`): LLM-assisted learning system (core, learning-coach, duckdb)
- **Words** (`/words/`): Creative writing (4 pieces)
- **Index** (`socrates.md`): Main table of contents

### Content Assessment

#### Strong Areas
- Philosophy (Moloch series, consciousness - 91K and 99K files)
- Functional Programming (fp.md - 72 lines with cross-language table)
- DuckDB/Polars (regex cheat sheets, agora docs)
- Team Building (OKRs, CALMS, Google PMP)
- Meditation/Mindfulness (77 lines, solid)

#### Gaps & Stubs
- **Missing:** Python, Haskell dedicated files
- **Empty:** scheme.md (2 lines)
- **Minimal:** rust-notes.md (28 lines - just string types)
- **Stubs:** 23 files under 20 lines (engagement, interiority, will-to-power, etc.)
- **No LOP content yet** (have article in temp/)

---

## Phase 1: Core Content Integration (Priority 1)

### Status: READY TO START

### 1.1 Preserve Original LOP Article
- [x] Keep `/temp/language-oriented-programming.md` intact (Medium article)
- [ ] Consider moving to `/org/lop-sudoku-article.md` for permanent reference

### 1.2 Create New `/org/language-oriented-programming.md`
**Source:** `/temp/language-oriented-programming.md` (Sudoku article)

**Structure:**
```markdown
# Language-Oriented Programming (LOP)

## Introduction
- Definition: LOP vs DSL
- Philosophy: molding language to problem domain
- Connection to expressiveness

## Why LOP Matters
- Abstraction power
- Problem-domain fit
- Code as communication
- Reduced impedance mismatch

## Core Concepts
- Homoiconicity (code as data)
- Macros and metaprogramming
- First-class functions
- Domain-specific abstractions

## Examples

### Sudoku Solver in Scheme
[Full Sudoku article content]
- Backtracking algorithm
- Helper functions
- Rule encoding
- Elegance of expression

### Other LOP Examples
- **SQL**: Declarative data manipulation
- **Regex**: Pattern matching DSL
- **Make/Build Tools**: Dependency DSLs
- **HTML/CSS**: Document structure/styling
- **GraphQL**: Query language for APIs

## Practical Applications
- When to use LOP approach
- Building internal DSLs
- Tool selection for LOP
- Balancing abstraction vs complexity

## The Role of Lisp
- Macros as ultimate LOP tool
- Metacircular evaluator
- "Programmable programming language"
- Why Scheme/Lisp excels at LOP

## Connections
- [SICP Metalinguistic Abstraction](./sicp.md)
- [Expressiveness](./expressiveness.md)
- [Scheme](./scheme.md)
- [Why SICP Matters](./why-sicp-matters.md)
- [Emacs Lisp](./emacs-lisp.md)

## Further Reading
- GitHub: [Sudoku solver implementation](https://github.com/usefulmove/usefulmove/blob/main/lop/sudoku.scm)
- SICP Chapter 4 (Metalinguistic Abstraction)
- "Let Over Lambda" by Doug Hoyte
```

**Estimated size:** 150-200 lines  
**Status:** Not started

### 1.3 Replace `/org/fp.md` with Enhanced Version
**Current:** 72 lines with table  
**Source:** `/temp/fp.md` as starting point

**New Approach:** Move beyond table limitations

**Structure:**
```markdown
# Functional Programming

## Overview
[Validation paragraph from current version]
[Rust quote from temp version]

## Core Concepts
- Immutability
- Pure functions
- First-class functions
- Higher-order functions
- Function composition
- Recursion (tail recursion)
- Closures
- Currying
- Partial application
- Monads
- Category theory foundations

## Language Comparison

### Operation Reference
[Revised table with Haskell column added]
- Better formatting for readability
- Clear notation for language-specific patterns
- Examples where helpful

### Language-Specific Sections

#### Rust
- Iterator patterns (iter vs into_iter)
- Ownership + FP synergy
- inspect() for debugging
- [Link to rust-notes.md]

#### Python
- itertools and functools
- toolz/funcy libraries
- List/dict comprehensions
- [Link to python-notes.md]

#### Scala
- Tail recursion (@tailrec)
- Collections hierarchy
- For-comprehensions

#### JavaScript
- Array methods
- Ramda library
- Promise chains as FP

#### Haskell
- Lazy evaluation
- Type classes
- Monadic composition

#### Scheme/Lisp
- Lambda calculus roots
- Recursion patterns
- [Link to scheme.md]

## Design Patterns

### Map-Reduce
- Pattern explanation
- Cross-language examples
- Use cases

### Pipeline/Composition
- Function chaining
- Data transformation flows
- Unix philosophy

### Immutable Data Structures
- Benefits
- Persistent data structures
- Performance considerations

### Monadic Patterns
- Maybe/Option (handling absence)
- Result/Either (error handling)
- List monad (non-determinism)
- IO monad (side effects)

## Error Handling Across Languages

| Language | Success/Value | Failure/Error | Notes |
|----------|---------------|---------------|-------|
| Rust | `Ok(T)` | `Err(E)` | Result<T,E>, ? operator |
| Haskell | `Just a` | `Nothing` | Maybe monad |
| Scala | `Some(x)` | `None` | Option type |
| Python | Value | Exception / None | No native Result type |
| JavaScript | Value | null/undefined | Optional chaining ?. |

## Practical Applications
- When to use FP techniques
- FP in imperative languages
- Performance considerations
- Team adoption strategies

## Learning Path
1. Start with map/filter/reduce
2. Practice immutability
3. Learn recursion patterns
4. Understand higher-order functions
5. Explore monads
6. Study category theory foundations

## References
- [Baeldung Scala FP Guide](https://www.baeldung.com/scala/functional-programming)
- [Rust Iterator Documentation](https://doc.rust-lang.org/std/iter/trait.Iterator.html)
- [Functional Programming in Rust](https://kerkour.com/rust-functional-programming)

## Connections
- [Mastering Functional Programming](./mastering-functional-programming.md)
- [Language-Oriented Programming](./language-oriented-programming.md)
- [SICP](./sicp.md)
- Individual language notes (Python, Rust, Scheme, Haskell)
```

**Estimated size:** 200-250 lines  
**Status:** Not started

---

## Phase 2: Language-Specific References (Priority 2)

### 2.1 Create `/org/python-notes.md`

**Structure:**
```markdown
# Python Notes

## Overview
Python as a multi-paradigm language: OOP, FP, procedural

## Functional Programming Patterns

### Built-in Functions
- map(), filter(), reduce()
- zip(), enumerate()
- any(), all()
- sorted() with key functions

### Comprehensions
- List: `[f(x) for x in items if condition]`
- Dict: `{k: v for k, v in items}`
- Set: `{x for x in items}`
- Generator expressions: `(f(x) for x in items)`

### Libraries
- **itertools**: infinite iterators, combinatorics
- **functools**: reduce, partial, lru_cache, singledispatch
- **toolz**: compose, pipe, curry (FP utilities)
- **funcy**: decorators, collections, seqs

### Examples
[Practical code examples]

## Type System

### Type Hints
- Basic annotations: `def func(x: int) -> str:`
- Optional types: `Optional[T]`, `Union[T, U]`
- Generics: `List[T]`, `Dict[K, V]`
- Protocol (structural typing)

### mypy
- Static type checking
- Configuration
- Common patterns

## Data Analysis

### pandas
- DataFrame basics
- Common operations
- Method chaining
- Performance tips

### polars
- Eager vs lazy evaluation
- Expression syntax
- When to use vs pandas
- [Link to polars-regex cheat sheet]

### DuckDB Integration
- duckdb.connect() patterns
- Reading DataFrames: `duckdb.query("SELECT * FROM df")`
- Zero-copy Arrow integration
- [Link to duckdb.md]

## Iterators & Generators

### Generator Functions
- yield keyword
- yield from
- Generator expressions

### itertools Patterns
- chain, cycle, repeat
- combinations, permutations
- groupby, accumulate
- islice, takewhile

### Generator Pipelines
[Examples of chaining generators]

## Python-Specific Idioms

### Context Managers
- with statement
- Custom context managers
- contextlib utilities

### Decorators
- Function decorators
- Class decorators
- functools.wraps
- Practical examples

### Argument Patterns
- `*args` (variable positional)
- `**kwargs` (variable keyword)
- Keyword-only arguments: `def f(*, key=value)`
- Positional-only arguments: `def f(x, /)`

### List Slicing
- Basic: `lst[start:stop:step]`
- Negative indices: `lst[-1]`, `lst[::-1]`
- Copy idiom: `lst[:]`

### Unpacking
- `a, b = (1, 2)`
- `*rest` patterns
- Dict unpacking: `{**d1, **d2}`

## REPL Workflow

### IPython Features
- Magic commands (%timeit, %run, etc.)
- Tab completion
- ? and ?? for help
- History navigation

### Debugging
- pdb basics
- ipdb for IPython
- breakpoint() built-in
- Common commands: n, s, c, p, l

### Jupyter Integration
- Cell magic
- Rich display
- Inline plots
- [Link to jupyter.md if expanded]

## Comparison with Other Languages

### Python vs Rust
- Speed/safety tradeoff
- GIL limitations
- Memory management
- When to use each

### Python vs Scheme
- Imperative vs functional default
- Type system differences
- Expressiveness comparison

### Python vs JavaScript
- Syntax similarities
- Async differences
- Ecosystem comparison

## Common Gotchas

### Mutable Default Arguments
```python
# WRONG
def append_to(element, lst=[]):
    lst.append(element)
    return lst

# RIGHT
def append_to(element, lst=None):
    if lst is None:
        lst = []
    lst.append(element)
    return lst
```

### Late Binding Closures
```python
# WRONG
funcs = [lambda: i for i in range(3)]
# All return 2

# RIGHT
funcs = [lambda i=i: i for i in range(3)]
```

### Global Interpreter Lock (GIL)
- Impact on threading
- Multiprocessing alternatives
- When GIL matters

### Mutable vs Immutable
- Tuple vs list
- String immutability
- Dict keys must be immutable

## Best Practices
- PEP 8 style guide
- Docstrings
- Testing patterns
- Virtual environments

## Connections
- [Functional Programming](./fp.md)
- [DuckDB](./duckdb.md)
- [Polars Regex](./polars-regex-cheatsheet.md)
- [Data Analysis Patterns](./data-analysis-patterns.md)
```

**Estimated size:** 200-250 lines  
**Status:** Not started

### 2.2 Expand `/org/rust-notes.md`

**Current:** 28 lines (string types only)

**Add:**
```markdown
# Rust Notes

## String Types
[KEEP EXISTING CONTENT - good explanation]

## Ownership & Borrowing

### The Rules
1. Each value has one owner
2. Only one owner at a time
3. Value dropped when owner goes out of scope

### Move Semantics
```rust
let s1 = String::from("hello");
let s2 = s1; // s1 is moved, no longer valid
```

### Borrowing
- Immutable borrow: `&T`
- Mutable borrow: `&mut T`
- Rules: many immutable OR one mutable

### Lifetime Annotations
- Basics: `'a` syntax
- Function signatures
- Struct lifetimes
- When needed vs inferred

## Iterator Patterns

### Iterator Types
- `iter()`: by reference (`&T`)
- `iter_mut()`: mutable reference (`&mut T`)
- `into_iter()`: owned (`T`)

### Common Adaptors
```rust
vec![1, 2, 3, 4]
    .iter()
    .map(|x| x * 2)
    .filter(|x| x > &4)
    .collect::<Vec<_>>()
```

### Chaining Operations
- map, filter, fold
- flat_map, flatten
- take, skip, zip
- enumerate, chain

### collect() Patterns
- Vec, HashMap, String
- Result<Vec<T>, E> (early return on error)
- Partition into two collections

### inspect() for Debugging
```rust
.inspect(|x| println!("value: {}", x))
```

## Error Handling

### Result<T, E> Type
```rust
fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        Err("division by zero".to_string())
    } else {
        Ok(a / b)
    }
}
```

### Option<T> Type
- Some(T) vs None
- Common methods: unwrap, expect, unwrap_or, unwrap_or_else
- map, and_then, or_else

### The ? Operator
```rust
fn process() -> Result<String, Error> {
    let content = read_file()?; // early return on Err
    let parsed = parse(content)?;
    Ok(parsed)
}
```

### match vs if let vs unwrap
```rust
// match - exhaustive
match result {
    Ok(val) => println!("{}", val),
    Err(e) => eprintln!("Error: {}", e),
}

// if let - one case
if let Ok(val) = result {
    println!("{}", val);
}

// unwrap - panic on error (avoid in production)
let val = result.unwrap();
```

## Pattern Matching

### match Expressions
```rust
match value {
    0 => "zero",
    1..=9 => "single digit",
    10 => "ten",
    _ => "something else",
}
```

### Destructuring
- Tuples: `let (x, y) = point;`
- Structs: `let Point { x, y } = point;`
- Enums: `match option { Some(x) => ..., None => ... }`

### Guards
```rust
match num {
    x if x < 0 => "negative",
    x if x > 0 => "positive",
    _ => "zero",
}
```

## Functional Programming in Rust

### Why Rust Favors FP
- Immutability by default
- Ownership model fits FP
- No shared mutable state issues
- [Link to fp.md]

### Ownership + FP Synergy
- Iterator chains avoid intermediate allocations
- Zero-cost abstractions
- Compile-time guarantees

### Common FP Patterns
```rust
// Pipeline
result
    .map(|x| x * 2)
    .and_then(|x| validate(x))
    .unwrap_or_default()

// Fold
vec.iter().fold(0, |acc, x| acc + x)
```

## Comparison with Other Languages

### Rust vs Python
| Aspect | Rust | Python |
|--------|------|--------|
| Speed | Very fast (compiled) | Slower (interpreted) |
| Safety | Compile-time guarantees | Runtime errors |
| Memory | Manual (ownership) | GC |
| Learning curve | Steep | Gentle |
| Use case | Systems, performance | Scripting, data analysis |

### Rust vs Scheme
- Ownership vs garbage collection
- Static vs dynamic typing
- Compiled vs interpreted
- Systems programming vs exploratory

### Rust vs C++
- Memory safety without GC
- No undefined behavior
- Modern tooling (cargo)
- Simpler than C++

## cargo Workflow Essentials

### Common Commands
```bash
cargo new project_name        # Create new project
cargo build                   # Build debug
cargo build --release         # Build optimized
cargo run                     # Build and run
cargo test                    # Run tests
cargo doc --open              # Generate and open docs
cargo fmt                     # Format code
cargo clippy                  # Linter
```

### Project Structure
```
project/
├── Cargo.toml                # Manifest
├── src/
│   ├── main.rs              # Binary entry point
│   └── lib.rs               # Library entry point
└── tests/                   # Integration tests
```

### Testing Patterns
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_something() {
        assert_eq!(2 + 2, 4);
    }
}
```

### Documentation
```rust
/// Documentation comment
/// 
/// # Examples
/// ```
/// let result = my_function(5);
/// assert_eq!(result, 10);
/// ```
pub fn my_function(x: i32) -> i32 {
    x * 2
}
```

## Common Patterns

### Builder Pattern
```rust
let server = Server::builder()
    .port(8080)
    .host("localhost")
    .build();
```

### Newtype Pattern
```rust
struct UserId(u64);
// Prevents mixing up different ID types
```

### Type State Pattern
```rust
// Encode state in types
struct Locked;
struct Unlocked;

struct Door<State> {
    _state: PhantomData<State>,
}
// Can only unlock Locked door, etc.
```

## Resources
- [The Rust Book](https://doc.rust-lang.org/book/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [Functional Rust](https://kerkour.com/rust-functional-programming)

## Connections
- [Functional Programming](./fp.md)
- [Python Notes](./python-notes.md) (comparison)
```

**Estimated size:** 250-300 lines  
**Status:** Not started

### 2.3 Expand `/org/scheme.md`

**Current:** 2 lines (empty)

**Structure:**
```markdown
# Scheme

## Overview

Scheme is a minimalist dialect of Lisp, designed for elegance and simplicity. It emphasizes functional programming, recursion, and provides powerful metaprogramming through macros.

Key characteristics:
- Minimal, uniform syntax
- First-class continuations
- Lexical scoping
- Tail call optimization (required by spec)

## Dialects Comparison

### Guile
- GNU's extension language
- Used in GNU projects
- C integration
- Large standard library

### Racket (formerly PLT Scheme)
- Batteries-included
- IDE (DrRacket)
- Package ecosystem
- Great for learning and production
- Extended beyond R6RS

### Chicken Scheme
- Practical, production-ready
- Compiles to C
- Eggs (package system)
- Small, efficient

### MIT Scheme
- Academic, traditional
- Edwin editor (Emacs-like)
- Good for SICP

### Chez Scheme
- Fast, commercial-grade
- R6RS compliant
- Now open source
- Used by Racket backend

## REPL-Driven Development

### Interactive Workflow
1. Start REPL
2. Define functions interactively
3. Test immediately
4. Refine based on feedback
5. Build program incrementally

### Benefits
- Immediate feedback
- Exploratory programming
- Interactive debugging
- Rapid prototyping

### Example Session
```scheme
> (define (square x) (* x x))
> (square 5)
25
> (define (sum-squares x y)
    (+ (square x) (square y)))
> (sum-squares 3 4)
25
```

## Macros

### Syntax-rules (Hygienic Macros)
```scheme
(define-syntax when
  (syntax-rules ()
    ((when condition body ...)
     (if condition
         (begin body ...)))))

(when (> x 0)
  (display "positive")
  (newline))
```

### Why Macros Matter
- Code transformation at compile time
- Create new language constructs
- Zero runtime overhead
- Foundation of LOP
- [Link to language-oriented-programming.md]

### Practical Examples
```scheme
; Create a for-each style loop
(define-syntax for
  (syntax-rules (in)
    ((for item in lst body ...)
     (for-each (lambda (item) body ...) lst))))

(for x in '(1 2 3)
  (display x)
  (newline))
```

## Core Concepts

### Recursion
```scheme
; Direct recursion
(define (factorial n)
  (if (= n 0)
      1
      (* n (factorial (- n 1)))))

; Tail recursion
(define (factorial-tail n)
  (define (iter n acc)
    (if (= n 0)
        acc
        (iter (- n 1) (* n acc))))
  (iter n 1))
```

### Higher-Order Functions
```scheme
(define (map f lst)
  (if (null? lst)
      '()
      (cons (f (car lst))
            (map f (cdr lst)))))

(define (filter pred lst)
  (cond ((null? lst) '())
        ((pred (car lst))
         (cons (car lst) (filter pred (cdr lst))))
        (else (filter pred (cdr lst)))))
```

### Closures
```scheme
(define (make-counter)
  (let ((count 0))
    (lambda ()
      (set! count (+ count 1))
      count)))

(define c1 (make-counter))
(c1) ; => 1
(c1) ; => 2
```

### Continuations
```scheme
; call-with-current-continuation (call/cc)
(define (f return)
  (return 2)
  3)

(f (lambda (x) x)) ; => 3
(call/cc f)        ; => 2 (early return)
```

## Key Libraries & Tools

### SRFI (Scheme Requests for Implementation)
- SRFI-1: List library
- SRFI-9: Define-record-type
- SRFI-13: String libraries
- SRFI-26: Cut (partial application)
- Many more...

### Common Libraries
- Pattern matching
- Unit testing
- Web frameworks (depending on dialect)

## Scheme vs Other Lisps

### Scheme vs Common Lisp
| Aspect | Scheme | Common Lisp |
|--------|--------|-------------|
| Size | Minimal | Large standard |
| Namespaces | Single | Separate for functions/values |
| Macros | Hygienic (syntax-rules) | Unhygienic (defmacro) |
| Philosophy | Minimalism | Practicality |

### Scheme vs Clojure
| Aspect | Scheme | Clojure |
|--------|--------|---------|
| Platform | Native/C | JVM (Java) |
| Data structures | Lists | Persistent collections |
| Concurrency | Basic | Rich (atoms, refs, agents) |
| Syntax | Pure S-expressions | Extended (vectors, maps) |

### Scheme vs Emacs Lisp
| Aspect | Scheme | Emacs Lisp |
|--------|--------|------------|
| Scoping | Lexical | Dynamic (mostly) |
| Purpose | General | Emacs extension |
| Tail calls | Required | Not guaranteed |
| Standard | R7RS | Emacs-specific |

## Learning Path

1. **Start with SICP**
   - [Structure and Interpretation of Computer Programs](./sicp.md)
   - [Why SICP Matters](./why-sicp-matters.md)

2. **Choose a Dialect**
   - Racket for learning (DrRacket IDE)
   - Guile for GNU ecosystem
   - Chicken for production

3. **Practice Recursion**
   - Think recursively
   - Master tail recursion
   - Understand the call stack

4. **Learn Macros**
   - Start with syntax-rules
   - Build DSLs
   - Read "Let Over Lambda"

5. **Read Code**
   - Study SICP examples
   - Read Scheme implementations
   - Explore SRFIs

## Resources

### Books
- *Structure and Interpretation of Computer Programs* (Abelson & Sussman)
- *The Little Schemer* (Friedman & Felleisen)
- *Scheme and the Art of Programming* (Springer & Friedman)
- *Let Over Lambda* (Hoyte) - advanced macros

### Online
- [Racket Documentation](https://docs.racket-lang.org/)
- [Community Scheme Wiki](http://community.schemewiki.org/)
- R7RS specification

## Practical Applications

### When to Use Scheme
- Language-oriented programming
- Educational purposes
- Rapid prototyping
- Domain-specific tools
- Metaprogramming experiments

### Real-World Uses
- GNU extensions (Guile)
- Academic research
- DSL implementation
- Theorem provers
- Embedded scripting

## Connections
- [SICP](./sicp.md)
- [Why SICP Matters](./why-sicp-matters.md)
- [Starting with Lisp](./starting-with-lisp.md)
- [Language-Oriented Programming](./language-oriented-programming.md)
- [Functional Programming](./fp.md)
- [Emacs Lisp](./emacs-lisp.md)
- [The Roots of Lisp](./the-roots-of-lisp.md)
```

**Estimated size:** 250-300 lines  
**Status:** Not started

### 2.4 Create `/org/haskell-notes.md` (OPTIONAL - awaiting priority decision)

**Status:** Awaiting user priority decision

---

## Phase 3: Data Analysis Workflows (Priority 3)

### Decision: Option A + B (expand duckdb.md + create sql-patterns.md)

### 3.1 Expand `/org/duckdb.md`

**Current:** 34 lines (basic operations)

**Add:**
```markdown
# DuckDB Notes

## Quick Start

### Persistent Storage
[KEEP EXISTING CONTENT]

### Copy to Parquet
[KEEP EXISTING CONTENT]

### Transactions
[KEEP EXISTING CONTENT]

## Common Patterns

### Data Cleaning

#### Handling Nulls
```sql
-- Replace nulls with default
SELECT COALESCE(column, 'default') FROM table;

-- Filter out nulls
SELECT * FROM table WHERE column IS NOT NULL;

-- NULLIF (reverse of COALESCE)
SELECT NULLIF(column, 'bad_value') FROM table;
```

#### String Cleaning
```sql
-- Trim whitespace
SELECT TRIM(column) FROM table;

-- Regex replacement
SELECT REGEXP_REPLACE(column, '\s+', ' ') FROM table;

-- Remove specific characters
SELECT REPLACE(column, ',', '') FROM table;

-- Case normalization
SELECT LOWER(column) FROM table;
```

#### Type Casting
```sql
-- Cast with error handling
SELECT TRY_CAST(column AS INTEGER) FROM table;

-- String to date
SELECT STRPTIME(date_str, '%Y-%m-%d') FROM table;

-- Date to string
SELECT STRFTIME(date_col, '%Y-%m-%d') FROM table;
```

#### Deduplication
```sql
-- Remove exact duplicates
SELECT DISTINCT * FROM table;

-- Keep first occurrence by ordering
SELECT * FROM (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY id ORDER BY timestamp) as rn
  FROM table
) WHERE rn = 1;

-- Group by and aggregate
SELECT id, MAX(timestamp) as latest
FROM table
GROUP BY id;
```

### Window Functions

#### Running Totals
```sql
SELECT
  date,
  amount,
  SUM(amount) OVER (ORDER BY date) as running_total
FROM transactions;
```

#### Moving Averages
```sql
SELECT
  date,
  value,
  AVG(value) OVER (
    ORDER BY date
    ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
  ) as moving_avg_7day
FROM metrics;
```

#### Ranking
```sql
-- ROW_NUMBER: unique sequential
-- RANK: same rank for ties, gaps
-- DENSE_RANK: same rank for ties, no gaps

SELECT
  name,
  score,
  ROW_NUMBER() OVER (ORDER BY score DESC) as row_num,
  RANK() OVER (ORDER BY score DESC) as rank,
  DENSE_RANK() OVER (ORDER BY score DESC) as dense_rank
FROM scores;
```

#### LAG/LEAD for Time-Series
```sql
SELECT
  date,
  value,
  LAG(value, 1) OVER (ORDER BY date) as prev_value,
  LEAD(value, 1) OVER (ORDER BY date) as next_value,
  value - LAG(value, 1) OVER (ORDER BY date) as diff
FROM time_series;
```

### CTEs (Common Table Expressions)

#### Basic CTE
```sql
WITH filtered_data AS (
  SELECT * FROM table WHERE condition
)
SELECT * FROM filtered_data;
```

#### Multiple CTEs
```sql
WITH
  step1 AS (SELECT ...),
  step2 AS (SELECT ... FROM step1),
  step3 AS (SELECT ... FROM step2)
SELECT * FROM step3;
```

#### Recursive CTEs
```sql
-- Generate series
WITH RECURSIVE series(n) AS (
  SELECT 1
  UNION ALL
  SELECT n + 1 FROM series WHERE n < 100
)
SELECT * FROM series;

-- Hierarchical data (org chart, etc.)
WITH RECURSIVE hierarchy AS (
  SELECT id, name, manager_id, 1 as level
  FROM employees
  WHERE manager_id IS NULL
  
  UNION ALL
  
  SELECT e.id, e.name, e.manager_id, h.level + 1
  FROM employees e
  JOIN hierarchy h ON e.manager_id = h.id
)
SELECT * FROM hierarchy;
```

#### When to Materialize
```sql
-- Materialize CTE (compute once, reuse)
WITH MATERIALIZED large_computation AS (
  SELECT ... -- expensive computation
)
SELECT * FROM large_computation
UNION ALL
SELECT * FROM large_computation; -- reuses materialized result
```

### JSON & Nested Data

#### Extracting JSON Fields
```sql
-- Get JSON field
SELECT json_column->'$.field' FROM table;
SELECT json_extract(json_column, '$.field') FROM table;

-- Get nested field
SELECT json_column->'$.level1.level2' FROM table;

-- Extract all keys
SELECT json_keys(json_column) FROM table;
```

#### Unnesting Arrays
```sql
-- Unnest array column
SELECT unnest(array_column) FROM table;

-- Unnest with original row
SELECT id, unnest(array_column) as item FROM table;

-- Unnest JSON array
SELECT unnest(json_column->'$.items') FROM table;
```

#### Working with Structs
```sql
-- Access struct field
SELECT struct_column.field FROM table;

-- Create struct
SELECT {'field1': value1, 'field2': value2} as my_struct;

-- Unnest struct
SELECT (unnest(struct_column)).* FROM table;
```

### Performance

#### When to Use Indexes
```sql
-- DuckDB auto-indexes in many cases
-- Explicit index for repeated queries
CREATE INDEX idx_name ON table(column);

-- Multi-column index
CREATE INDEX idx_name ON table(col1, col2);

-- When to use:
--   - Repeated WHERE clauses
--   - JOIN keys
--   - ORDER BY columns (sometimes)
```

#### Materialized CTEs
```sql
-- Force materialization for reuse
WITH MATERIALIZED expensive_step AS (
  ... -- computed once
)
SELECT * FROM expensive_step
UNION ALL
SELECT * FROM expensive_step;
```

#### Query Optimization Tips
- Use EXPLAIN to see query plan
- Filter early (WHERE before JOIN when possible)
- Avoid SELECT * in production
- Use appropriate types (smaller is faster)
- Partition large parquet files
- Use columnar formats (parquet over CSV)

#### Memory Management
```sql
-- Set memory limit
SET memory_limit='4GB';

-- Set thread count
SET threads TO 4;

-- Check memory usage
SELECT * FROM duckdb_memory();
```

### Parquet Integration

#### Reading Partitioned Parquet
```sql
-- Single file
SELECT * FROM 'data.parquet';

-- Glob pattern
SELECT * FROM 'data/*.parquet';

-- Hive partitioning
SELECT * FROM 'data/year=*/month=*/*.parquet';

-- Specific partitions
SELECT * FROM 'data/year=2024/month=12/*.parquet';
```

#### Writing Partitioned Parquet
```sql
-- Write with partitioning
COPY (SELECT * FROM table)
TO 'output'
(FORMAT PARQUET, PARTITION_BY (year, month));

-- Single file with compression
COPY (SELECT * FROM table)
TO 'output.parquet'
(FORMAT PARQUET, COMPRESSION zstd);

-- Multiple formats
COPY table TO 'output.parquet' (FORMAT PARQUET);
COPY table TO 'output.csv' (FORMAT CSV, HEADER);
```

#### Compression Strategies
| Compression | Speed | Ratio | Use Case |
|-------------|-------|-------|----------|
| uncompressed | Fastest | 1x | Temporary files |
| snappy | Fast | 2-3x | Hot data, frequent access |
| zstd | Medium | 3-5x | **Recommended default** |
| gzip | Slow | 4-6x | Cold storage |

### Python Integration

#### Basic Connection
```python
import duckdb

# In-memory
conn = duckdb.connect(':memory:')

# Persistent
conn = duckdb.connect('my_database.duckdb')

# Execute query
result = conn.execute("SELECT * FROM table").fetchall()
```

#### Reading DataFrames
```python
import pandas as pd
import polars as pl

# Pandas
df = pd.read_csv('data.csv')
result = duckdb.query("SELECT * FROM df WHERE value > 10")

# Polars (zero-copy via Arrow)
df = pl.read_csv('data.csv')
result = duckdb.query("SELECT * FROM df WHERE value > 10")
```

#### Writing to DataFrames
```python
# To pandas
result_df = conn.execute("SELECT * FROM table").df()

# To polars
result_pl = conn.execute("SELECT * FROM table").pl()

# To Arrow
result_arrow = conn.execute("SELECT * FROM table").arrow()
```

#### Arrow Integration (Zero-Copy)
```python
# DuckDB can query Arrow tables directly
import pyarrow as pa

arrow_table = pa.table({'col': [1, 2, 3]})
result = duckdb.query("SELECT * FROM arrow_table")
```

#### Common Patterns
```python
# Register DataFrame as view
conn.register('my_view', df)
conn.execute("SELECT * FROM my_view")

# One-liner query
result = duckdb.query("SELECT * FROM 'data.parquet'").df()

# Parameterized queries
conn.execute("SELECT * FROM table WHERE id = ?", [user_id])
```

### Analytical Queries

For common analytical patterns (cohorts, funnels, retention), see [SQL Patterns](./sql-patterns.md)

## Best Practices

1. **Use Parquet for storage** (columnar, compressed, fast)
2. **Filter early** (push predicates down)
3. **Materialize expensive CTEs** when reused
4. **Use Arrow for Python integration** (zero-copy)
5. **Partition large datasets** (by date, category, etc.)
6. **Set memory limits** in production
7. **Use EXPLAIN** to understand query plans

## Resources
- [DuckDB Documentation](https://duckdb.org/docs/)
- [SQL Reference](../agora/duckdb/sql/) (comprehensive local docs)
- [DuckDB Python API](https://duckdb.org/docs/api/python/overview)

## Connections
- [SQL Patterns](./sql-patterns.md)
- [DuckDB Regex Cheat Sheet](./duckdb-regex-cheatsheet.md)
- [Polars](./polars-regex-cheatsheet.md)
- [Python Notes](./python-notes.md)
- [Data Analysis Patterns](./data-analysis-patterns.md)
```

**Estimated size:** 350-400 lines  
**Status:** Not started

### 3.2 Create `/org/sql-patterns.md`

```markdown
# SQL Patterns for Analytics

Common analytical query patterns using SQL (DuckDB-focused, but widely applicable).

## Time-Series Analysis

### Date Binning
```sql
-- Daily aggregation
SELECT
  DATE_TRUNC('day', timestamp) as day,
  COUNT(*) as events
FROM events
GROUP BY 1
ORDER BY 1;

-- Weekly (starting Monday)
SELECT
  DATE_TRUNC('week', timestamp) as week,
  COUNT(*) as events
FROM events
GROUP BY 1;

-- Monthly
SELECT
  DATE_TRUNC('month', timestamp) as month,
  COUNT(*) as events
FROM events
GROUP BY 1;

-- Custom bins (hourly)
SELECT
  DATE_TRUNC('hour', timestamp) as hour,
  COUNT(*) as events
FROM events
GROUP BY 1;
```

### Gaps and Islands Problem
```sql
-- Find continuous date ranges
WITH ordered_dates AS (
  SELECT
    date,
    date - INTERVAL (ROW_NUMBER() OVER (ORDER BY date)) DAY as island_start
  FROM events
  GROUP BY date
)
SELECT
  island_start,
  MIN(date) as range_start,
  MAX(date) as range_end,
  COUNT(*) as days_in_range
FROM ordered_dates
GROUP BY island_start
ORDER BY range_start;
```

### Moving Windows
```sql
-- 7-day moving average
SELECT
  date,
  value,
  AVG(value) OVER (
    ORDER BY date
    ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
  ) as moving_avg_7d
FROM metrics;

-- 30-day rolling sum
SELECT
  date,
  value,
  SUM(value) OVER (
    ORDER BY date
    RANGE BETWEEN INTERVAL 29 DAY PRECEDING AND CURRENT ROW
  ) as rolling_sum_30d
FROM metrics;
```

### Seasonality Detection
```sql
-- Compare to same period last year
SELECT
  date,
  value,
  LAG(value, 365) OVER (ORDER BY date) as value_last_year,
  value - LAG(value, 365) OVER (ORDER BY date) as yoy_diff,
  (value / LAG(value, 365) OVER (ORDER BY date) - 1) * 100 as yoy_pct_change
FROM daily_metrics;

-- Day of week patterns
SELECT
  DAYNAME(date) as day_of_week,
  AVG(value) as avg_value,
  STDDEV(value) as stddev_value
FROM metrics
GROUP BY DAYNAME(date), DAYOFWEEK(date)
ORDER BY DAYOFWEEK(date);
```

## Cohort Analysis

### Defining Cohorts
```sql
-- User cohorts by signup month
WITH user_cohorts AS (
  SELECT
    user_id,
    DATE_TRUNC('month', signup_date) as cohort_month
  FROM users
)
SELECT * FROM user_cohorts;
```

### Retention Calculation
```sql
-- Monthly retention by cohort
WITH cohorts AS (
  SELECT
    user_id,
    DATE_TRUNC('month', signup_date) as cohort_month
  FROM users
),
activity AS (
  SELECT
    user_id,
    DATE_TRUNC('month', activity_date) as activity_month
  FROM events
),
cohort_activity AS (
  SELECT
    c.cohort_month,
    a.activity_month,
    COUNT(DISTINCT a.user_id) as active_users,
    DATEDIFF('month', c.cohort_month, a.activity_month) as months_since_signup
  FROM cohorts c
  JOIN activity a ON c.user_id = a.user_id
  GROUP BY 1, 2, 4
),
cohort_sizes AS (
  SELECT
    cohort_month,
    COUNT(*) as cohort_size
  FROM cohorts
  GROUP BY cohort_month
)
SELECT
  ca.cohort_month,
  ca.months_since_signup,
  ca.active_users,
  cs.cohort_size,
  ROUND(100.0 * ca.active_users / cs.cohort_size, 2) as retention_pct
FROM cohort_activity ca
JOIN cohort_sizes cs ON ca.cohort_month = cs.cohort_month
ORDER BY ca.cohort_month, ca.months_since_signup;
```

### Cohort Pivot Table
```sql
-- Pivot retention by cohort
WITH retention AS (
  ... -- retention query from above
)
PIVOT retention
ON months_since_signup
USING SUM(retention_pct)
GROUP BY cohort_month
ORDER BY cohort_month;
```

## Funnel Analysis

### Step-by-Step Conversion
```sql
WITH funnel_steps AS (
  SELECT 'view_page' as step, 1 as step_order UNION ALL
  SELECT 'add_to_cart', 2 UNION ALL
  SELECT 'checkout', 3 UNION ALL
  SELECT 'purchase', 4
),
step_counts AS (
  SELECT
    step,
    COUNT(DISTINCT user_id) as users
  FROM events
  WHERE step IN ('view_page', 'add_to_cart', 'checkout', 'purchase')
  GROUP BY step
)
SELECT
  fs.step,
  fs.step_order,
  sc.users,
  ROUND(100.0 * sc.users / FIRST_VALUE(sc.users) OVER (ORDER BY fs.step_order), 2) as pct_of_top,
  ROUND(100.0 * sc.users / LAG(sc.users) OVER (ORDER BY fs.step_order), 2) as pct_of_previous
FROM funnel_steps fs
JOIN step_counts sc ON fs.step = sc.step
ORDER BY fs.step_order;
```

### Drop-off Identification
```sql
-- Users who reached step N but not N+1
WITH step_users AS (
  SELECT
    user_id,
    MAX(CASE WHEN event_type = 'view' THEN 1 ELSE 0 END) as reached_view,
    MAX(CASE WHEN event_type = 'add_to_cart' THEN 1 ELSE 0 END) as reached_cart,
    MAX(CASE WHEN event_type = 'purchase' THEN 1 ELSE 0 END) as reached_purchase
  FROM events
  GROUP BY user_id
)
SELECT
  COUNT(*) as total_users,
  SUM(reached_view) as viewed,
  SUM(reached_view AND NOT reached_cart) as dropped_at_view,
  SUM(reached_cart AND NOT reached_purchase) as dropped_at_cart
FROM step_users;
```

### Time-Based Funnels
```sql
-- Conversion within 24 hours
WITH first_view AS (
  SELECT
    user_id,
    MIN(timestamp) as first_view_time
  FROM events
  WHERE event_type = 'view'
  GROUP BY user_id
),
first_purchase AS (
  SELECT
    user_id,
    MIN(timestamp) as first_purchase_time
  FROM events
  WHERE event_type = 'purchase'
  GROUP BY user_id
)
SELECT
  COUNT(DISTINCT fv.user_id) as viewers,
  COUNT(DISTINCT fp.user_id) as purchasers,
  COUNT(DISTINCT CASE
    WHEN fp.first_purchase_time <= fv.first_view_time + INTERVAL 24 HOUR
    THEN fp.user_id
  END) as purchasers_within_24h,
  ROUND(100.0 * COUNT(DISTINCT CASE
    WHEN fp.first_purchase_time <= fv.first_view_time + INTERVAL 24 HOUR
    THEN fp.user_id
  END) / COUNT(DISTINCT fv.user_id), 2) as conversion_rate_24h
FROM first_view fv
LEFT JOIN first_purchase fp ON fv.user_id = fp.user_id;
```

## Aggregation Strategies

### GROUP BY Patterns
```sql
-- Multiple aggregations
SELECT
  category,
  COUNT(*) as count,
  AVG(price) as avg_price,
  SUM(revenue) as total_revenue,
  MIN(date) as first_date,
  MAX(date) as last_date
FROM sales
GROUP BY category;

-- GROUP BY with multiple dimensions
SELECT
  DATE_TRUNC('month', date) as month,
  category,
  region,
  SUM(revenue) as revenue
FROM sales
GROUP BY 1, 2, 3;
```

### ROLLUP (Hierarchical Totals)
```sql
-- Subtotals and grand total
SELECT
  COALESCE(region, 'TOTAL') as region,
  COALESCE(category, 'All Categories') as category,
  SUM(revenue) as revenue
FROM sales
GROUP BY ROLLUP(region, category)
ORDER BY region, category;
```

### CUBE (All Combinations)
```sql
-- All possible subtotals
SELECT
  COALESCE(region, 'All Regions') as region,
  COALESCE(category, 'All Categories') as category,
  SUM(revenue) as revenue
FROM sales
GROUP BY CUBE(region, category);
```

### GROUPING SETS (Custom Combinations)
```sql
-- Specific grouping combinations
SELECT
  region,
  category,
  product,
  SUM(revenue) as revenue
FROM sales
GROUP BY GROUPING SETS (
  (region, category),
  (region, product),
  (category, product),
  ()  -- grand total
);
```

### Conditional Aggregation
```sql
-- CASE inside aggregate
SELECT
  user_id,
  COUNT(*) as total_orders,
  SUM(CASE WHEN status = 'completed' THEN 1 ELSE 0 END) as completed_orders,
  SUM(CASE WHEN status = 'cancelled' THEN 1 ELSE 0 END) as cancelled_orders,
  SUM(CASE WHEN status = 'completed' THEN amount ELSE 0 END) as completed_revenue
FROM orders
GROUP BY user_id;

-- FILTER clause (cleaner syntax)
SELECT
  user_id,
  COUNT(*) as total_orders,
  COUNT(*) FILTER (WHERE status = 'completed') as completed_orders,
  COUNT(*) FILTER (WHERE status = 'cancelled') as cancelled_orders,
  SUM(amount) FILTER (WHERE status = 'completed') as completed_revenue
FROM orders
GROUP BY user_id;
```

## JOIN Patterns

### Self-Joins
```sql
-- Find managers and their reports
SELECT
  e1.name as employee,
  e2.name as manager
FROM employees e1
LEFT JOIN employees e2 ON e1.manager_id = e2.id;

-- Compare consecutive rows
SELECT
  curr.date as current_date,
  curr.value as current_value,
  prev.date as previous_date,
  prev.value as previous_value,
  curr.value - prev.value as difference
FROM metrics curr
LEFT JOIN metrics prev ON curr.date = prev.date + INTERVAL 1 DAY;
```

### Lateral Joins
```sql
-- Top N per group
SELECT
  c.category,
  p.product_name,
  p.revenue
FROM categories c
LEFT JOIN LATERAL (
  SELECT product_name, revenue
  FROM products
  WHERE category = c.category
  ORDER BY revenue DESC
  LIMIT 3
) p ON true;
```

### Anti-Joins (Finding Absences)
```sql
-- Users who never purchased (NOT EXISTS)
SELECT *
FROM users u
WHERE NOT EXISTS (
  SELECT 1
  FROM orders o
  WHERE o.user_id = u.user_id
);

-- Users who never purchased (LEFT JOIN + NULL)
SELECT u.*
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE o.user_id IS NULL;

-- NOT IN (careful with NULLs!)
SELECT *
FROM users
WHERE user_id NOT IN (
  SELECT user_id FROM orders WHERE user_id IS NOT NULL
);
```

### Cross Joins for Combinatorics
```sql
-- All possible pairs
SELECT
  a.user_id as user1,
  b.user_id as user2
FROM users a
CROSS JOIN users b
WHERE a.user_id < b.user_id;  -- avoid duplicates

-- Generate date spine
WITH date_spine AS (
  SELECT UNNEST(GENERATE_SERIES(
    DATE '2024-01-01',
    DATE '2024-12-31',
    INTERVAL 1 DAY
  )) as date
)
SELECT * FROM date_spine;
```

## Advanced Patterns

### Running Calculations
```sql
-- Running total
SELECT
  date,
  amount,
  SUM(amount) OVER (ORDER BY date) as running_total
FROM transactions;

-- Running maximum
SELECT
  date,
  value,
  MAX(value) OVER (ORDER BY date) as max_so_far
FROM metrics;
```

### Percentiles and Quantiles
```sql
SELECT
  category,
  PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY price) as q1,
  PERCENTILE_CONT(0.50) WITHIN GROUP (ORDER BY price) as median,
  PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY price) as q3,
  PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY price) as p95
FROM products
GROUP BY category;
```

### Dense Data from Sparse Events
```sql
-- Forward-fill missing dates
WITH RECURSIVE date_range AS (
  SELECT MIN(date) as date FROM events
  UNION ALL
  SELECT date + INTERVAL 1 DAY
  FROM date_range
  WHERE date < (SELECT MAX(date) FROM events)
)
SELECT
  dr.date,
  LAST_VALUE(e.value IGNORE NULLS) OVER (ORDER BY dr.date) as filled_value
FROM date_range dr
LEFT JOIN events e ON dr.date = e.date
ORDER BY dr.date;
```

## Performance Tips

### Index Usage
```sql
-- Create indexes for common filters/joins
CREATE INDEX idx_user_id ON orders(user_id);
CREATE INDEX idx_date ON events(date);

-- Composite index for multi-column filters
CREATE INDEX idx_user_date ON events(user_id, date);
```

### Avoid SELECT *
```sql
-- BAD (reads all columns)
SELECT * FROM large_table WHERE id = 123;

-- GOOD (reads only needed columns)
SELECT id, name, email FROM large_table WHERE id = 123;
```

### Predicate Pushdown
```sql
-- BAD (filter after join)
SELECT *
FROM (SELECT * FROM large_table) a
JOIN small_table b ON a.id = b.id
WHERE a.date > '2024-01-01';

-- GOOD (filter before join)
SELECT *
FROM (SELECT * FROM large_table WHERE date > '2024-01-01') a
JOIN small_table b ON a.id = b.id;
```

### JOIN Order
```sql
-- Start with smallest table
-- Let query optimizer help, but be aware:
SELECT *
FROM small_table s
JOIN medium_table m ON s.id = m.small_id
JOIN large_table l ON m.id = l.medium_id;
```

## Connections
- [DuckDB Notes](./duckdb.md)
- [Data Analysis Patterns](./data-analysis-patterns.md)
- [DuckDB SQL Documentation](../agora/duckdb/sql/)
```

**Estimated size:** 400-450 lines  
**Status:** Not started

---

## Phase 4: Psychology Content (Priority 4)

### 4.1 Create `/org/psychology-fundamentals.md`
**Estimated size:** 150-180 lines  
**Status:** Not started

### 4.2 Create `/org/learning-psychology.md`
**Estimated size:** 180-220 lines  
**Status:** Not started

---

## Phase 5: Philosophy Personal Synthesis (Priority 5)

### 5.1 Expand `/org/will-to-power.md` (9 lines → ~40-50 lines)
**Status:** Not started

### 5.2 Expand `/org/being.md` (13 lines → ~40-50 lines)
**Status:** Not started

### 5.3 Expand `/org/selfishness.md` (13 lines → ~40-50 lines)
**Status:** Not started

### 5.4 Expand `/org/duality.md` (17 lines → ~50-60 lines)
**Status:** Not started

---

## Phase 6: Organization & Clean-Up (Priority 6)

### 6.1 Update `/socrates.md` Main Index
**Changes:**
- Add LOP to Coding section
- Add Python, Rust (explicit), Haskell to Languages
- Update Scheme (now has content)
- Create new Psychology section
- Add sql-patterns.md to Data Analysis
- Remove broken gum.md reference
- Add cross-references throughout

**Status:** Not started

### 6.2 Handle Stub Files
**Decisions needed:**
- `overdose.md` (1 line)
- `the-secret-to-writing.md` (2 lines)
- `not-the-critic.md` (3 lines)
- `jupyter.md` (16 lines)
- `sonya-story.md` (move to separate project)

**Status:** Awaiting decision

### 6.3 Add Cross-Links
- Add "Connections" sections to all new files
- Add "See Also" to existing files
- Create knowledge graph effect

**Status:** Not started

---

## Implementation Checklist

### Phase 1: Core Content
- [ ] Copy `/temp/language-oriented-programming.md` → `/org/lop-sudoku-article.md` (preserve original)
- [ ] Create `/org/language-oriented-programming.md` (comprehensive)
- [ ] Create enhanced `/org/fp.md` (replace existing)

### Phase 2: Language References
- [ ] Create `/org/python-notes.md`
- [ ] Expand `/org/rust-notes.md`
- [ ] Expand `/org/scheme.md`
- [ ] Create `/org/haskell-notes.md` (if priority HIGH)

### Phase 3: Data Analysis
- [ ] Expand `/org/duckdb.md`
- [ ] Create `/org/sql-patterns.md`

### Phase 4: Psychology
- [ ] Create `/org/psychology-fundamentals.md`
- [ ] Create `/org/learning-psychology.md`

### Phase 5: Philosophy Synthesis
- [ ] Expand `/org/will-to-power.md`
- [ ] Expand `/org/being.md`
- [ ] Expand `/org/selfishness.md`
- [ ] Expand `/org/duality.md`

### Phase 6: Organization
- [ ] Update `/socrates.md` index
- [ ] Handle stub files (decision needed)
- [ ] Add cross-links throughout
- [ ] Clean up broken references

---

## Open Questions

### Q1: Haskell Priority
- **High:** Create haskell-notes.md in Phase 2
- **Medium:** Create in Phase 3+
- **Low:** Skip for now
- **Status:** AWAITING DECISION

### Q2: Stub Files
What to do with minimal files?
- **Option A:** Leave as-is (reminders)
- **Option B:** Remove from index only
- **Option C:** Delete files
- **Status:** AWAITING DECISION

### Q3: Philosophy Synthesis Depth
- **Option A:** Focus on practical applications
- **Option B:** Include deeper personal reflections
- **Option C:** Both (longer files)
- **Status:** AWAITING DECISION

---

## Notes

- Original LOP article to be preserved intact (posted on Medium)
- FP documentation will take new approach beyond table format
- Data analysis getting significant expansion (high practical value)
- Cross-linking will create knowledge graph effect
- All new content should include "Connections" sections

---

## Success Metrics

- **Completeness:** All phases 1-5 implemented
- **Cross-linking:** Every new file links to 3+ related files
- **Practical value:** Content you reference regularly
- **Maintainability:** Structure supports future additions
- **Index accuracy:** socrates.md reflects all content

---

## Timeline Estimate

- **Phase 1:** 2-3 days (core content integration)
- **Phase 2:** 3-4 days (language references)
- **Phase 3:** 2-3 days (data analysis)
- **Phase 4:** 2-3 days (psychology)
- **Phase 5:** 1-2 days (philosophy synthesis)
- **Phase 6:** 1 day (organization)

**Total:** ~2 weeks at steady pace
