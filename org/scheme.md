# Scheme

Scheme is a minimalist dialect of Lisp, designed for elegance, simplicity, and expressiveness. It emphasizes functional programming, recursion, and provides powerful metaprogramming capabilities through macros. Scheme's uniform syntax and small core make it an ideal language for learning computer science fundamentals and exploring language-oriented programming.

## Overview

**Key Characteristics:**
- Minimal, uniform syntax (S-expressions)
- Functional-first paradigm
- First-class continuations (call/cc)
- Lexical scoping
- Tail call optimization (required by specification)
- Powerful hygienic macro system

**Philosophy:** "Everything is an expression." Scheme has no statements, only expressions that return values.

**Specifications:**
- R5RS (1998): Classic, widely supported
- R6RS (2007): More complex, libraries, records
- R7RS (2013): Modern, split into small/large standards

## Dialects Comparison

### Guile

GNU's official extension language, designed to embed in applications.

**Features:**
- C integration (easy FFI)
- Used in GNU projects (GDB, GIMP, etc.)
- Large standard library
- Module system
- Multi-threading support

**Best for:**
- Extending GNU software
- Application scripting
- Systems programming with Scheme

**Getting started:**
```bash
sudo apt install guile-3.0  # Linux
brew install guile          # Mac
guile                       # Start REPL
```

### Racket (formerly PLT Scheme)

Batteries-included Scheme with extensive libraries and IDE.

**Features:**
- DrRacket IDE (excellent for learning)
- Huge package ecosystem
- Web framework, GUI, 2D/3D graphics
- Language-oriented programming tools
- Strong typing option (#lang typed/racket)
- Beyond R6RS (extended heavily)

**Best for:**
- Learning Scheme/Lisp
- Production applications
- Web development
- Building DSLs
- Teaching programming

**Getting started:**
```bash
# Download from racket-lang.org
racket              # Command-line REPL
drracket            # IDE
```

**Example:**
```scheme
#lang racket

(define (hello name)
  (format "Hello, ~a!" name))

(hello "World")
```

### Chicken Scheme

Practical, production-ready Scheme that compiles to C.

**Features:**
- Compiles to portable C
- Eggs (package system with 800+ packages)
- Small runtime
- Good C FFI
- Fast compilation

**Best for:**
- Production deployments
- Performance-critical code
- Systems programming
- Portability (compiles anywhere C does)

**Getting started:**
```bash
sudo apt install chicken-bin  # Linux
brew install chicken          # Mac
csi                          # REPL
```

### MIT Scheme

Academic implementation, traditional and feature-complete.

**Features:**
- Edwin editor (Emacs-like)
- Full debugging support
- Used at MIT for SICP course
- R7RS compliant
- Native code compiler

**Best for:**
- Working through SICP
- Academic use
- Traditional Scheme experience

### Chez Scheme

High-performance, commercial-grade Scheme (now open source).

**Features:**
- Very fast (native code generation)
- R6RS compliant
- Used as Racket's backend
- Incremental compiler
- Professional quality

**Best for:**
- Performance-critical applications
- Production systems
- Research

**Fun fact:** Chez Scheme powers Racket's implementation.

## REPL-Driven Development

Scheme's interactive REPL enables rapid experimentation and incremental development.

### Interactive Workflow

The REPL (Read-Eval-Print Loop) is central to Scheme development:

1. **Start REPL:** Launch your Scheme interpreter
2. **Define functions:** Write and test functions interactively
3. **Test immediately:** Call functions to see results
4. **Refine:** Modify definitions based on feedback
5. **Build incrementally:** Compose larger programs from tested pieces

### Benefits

- **Immediate feedback:** No compile-wait-run cycle
- **Exploratory programming:** Try ideas quickly
- **Interactive debugging:** Test hypotheses in context
- **Rapid prototyping:** Build working code faster
- **Learning aid:** See results immediately

### Example Session

```scheme
; Start with simple function
> (define (square x) (* x x))
> (square 5)
25

; Build on it
> (define (sum-of-squares x y)
    (+ (square x) (square y)))
> (sum-of-squares 3 4)
25

; Test edge cases
> (sum-of-squares 0 0)
0

; Refine and extend
> (define (distance x1 y1 x2 y2)
    (sqrt (+ (square (- x2 x1))
             (square (- y2 y1)))))
> (distance 0 0 3 4)
5.0
```

### Development Pattern

```scheme
; 1. Define helper
(define (helper data)
  ...)

; 2. Test it
(helper test-data)

; 3. Define main function using helper
(define (main input)
  (helper (process input)))

; 4. Test main
(main sample-input)

; 5. Iterate
```

## Macros

Macros are programs that write programs, transforming code at compile time. They enable language extension and DSL creation.

### Syntax-rules (Hygienic Macros)

Scheme's `syntax-rules` system provides hygienic macros (no variable capture):

```scheme
; Simple macro: when (one-armed if)
(define-syntax when
  (syntax-rules ()
    ((when condition body ...)
     (if condition
         (begin body ...)))))

; Usage looks like built-in syntax
(when (> x 0)
  (display "positive")
  (newline))

; let* macro (sequential binding)
(define-syntax my-let*
  (syntax-rules ()
    ((my-let* () body ...)
     (begin body ...))
    ((my-let* ((name val) rest ...) body ...)
     (let ((name val))
       (my-let* (rest ...) body ...)))))

; for-each style loop
(define-syntax for
  (syntax-rules (in)
    ((for item in lst body ...)
     (for-each (lambda (item) body ...) lst))))

(for x in '(1 2 3)
  (display x)
  (newline))
```

### Why Macros Matter

- **Language extension:** Add constructs the language lacks
- **Code transformation:** Compile-time optimization
- **DSL creation:** Build domain-specific syntax
- **Zero runtime cost:** Transformations happen at compile time
- **Foundation of LOP:** Enable language-oriented programming

**See:** [Language-Oriented Programming](./language-oriented-programming.md)

### Practical Examples

```scheme
; unless (opposite of when)
(define-syntax unless
  (syntax-rules ()
    ((unless condition body ...)
     (if (not condition)
         (begin body ...)))))

; Pipeline operator (thread-first)
(define-syntax ->
  (syntax-rules ()
    ((-> x) x)
    ((-> x (f args ...) rest ...)
     (-> (f x args ...) rest ...))
    ((-> x f rest ...)
     (-> (f x) rest ...))))

(-> 5
    (+ 3)
    (* 2)
    (- 1))  ; => 15

; assert macro with custom message
(define-syntax assert
  (syntax-rules ()
    ((assert condition)
     (assert condition "Assertion failed"))
    ((assert condition message)
     (if (not condition)
         (error message)))))
```

### Macro Hygiene

Hygienic macros prevent variable capture:

```scheme
; This macro is safe from variable capture
(define-syntax swap
  (syntax-rules ()
    ((swap a b)
     (let ((temp a))
       (set! a b)
       (set! b temp)))))

; temp is guaranteed not to conflict with user's temp
(let ((temp 5)
      (x 1)
      (y 2))
  (swap x y)
  (list x y temp))  ; => (2 1 5) - temp unchanged
```

## Core Concepts

### Recursion

Scheme's primary iteration mechanism is recursion:

```scheme
; Direct recursion
(define (factorial n)
  (if (= n 0)
      1
      (* n (factorial (- n 1)))))

; Tail recursion (more efficient)
(define (factorial-tail n)
  (define (iter n acc)
    (if (= n 0)
        acc
        (iter (- n 1) (* n acc))))
  (iter n 1))

; List recursion
(define (length lst)
  (if (null? lst)
      0
      (+ 1 (length (cdr lst)))))

; Tail-recursive list processing
(define (reverse lst)
  (define (iter lst acc)
    (if (null? lst)
        acc
        (iter (cdr lst) (cons (car lst) acc))))
  (iter lst '()))
```

**Tail call optimization:** Scheme *requires* tail call optimization, making tail-recursive functions as efficient as loops.

### Higher-Order Functions

Functions that operate on functions:

```scheme
; map: Apply function to each element
(define (map f lst)
  (if (null? lst)
      '()
      (cons (f (car lst))
            (map f (cdr lst)))))

(map square '(1 2 3 4))  ; => (1 4 9 16)

; filter: Select elements by predicate
(define (filter pred lst)
  (cond ((null? lst) '())
        ((pred (car lst))
         (cons (car lst) (filter pred (cdr lst))))
        (else (filter pred (cdr lst)))))

(filter even? '(1 2 3 4 5))  ; => (2 4)

; fold (reduce)
(define (fold f init lst)
  (if (null? lst)
      init
      (fold f (f init (car lst)) (cdr lst))))

(fold + 0 '(1 2 3 4))  ; => 10

; compose
(define (compose f g)
  (lambda (x) (f (g x))))

(define sqrt-of-square (compose sqrt square))
(sqrt-of-square 5)  ; => 5.0
```

### Closures

Functions that capture their environment:

```scheme
; Counter with private state
(define (make-counter)
  (let ((count 0))
    (lambda ()
      (set! count (+ count 1))
      count)))

(define c1 (make-counter))
(c1)  ; => 1
(c1)  ; => 2

(define c2 (make-counter))
(c2)  ; => 1 (independent state)

; Parameterized closures
(define (make-adder n)
  (lambda (x) (+ x n)))

(define add5 (make-adder 5))
(add5 3)  ; => 8

; Closures for information hiding
(define (make-account balance)
  (define (withdraw amount)
    (if (>= balance amount)
        (begin
          (set! balance (- balance amount))
          balance)
        "Insufficient funds"))
  
  (define (deposit amount)
    (set! balance (+ balance amount))
    balance)
  
  (define (dispatch m)
    (cond ((eq? m 'withdraw) withdraw)
          ((eq? m 'deposit) deposit)
          ((eq? m 'balance) balance)
          (else (error "Unknown operation"))))
  
  dispatch)

(define acc (make-account 100))
((acc 'withdraw) 20)  ; => 80
((acc 'deposit) 50)   ; => 130
(acc 'balance)        ; => 130
```

### Continuations

Continuations represent "the rest of the computation":

```scheme
; call-with-current-continuation (call/cc)
(define (f return)
  (return 2)
  3)

(f (lambda (x) x))  ; => 3 (normal return)
(call/cc f)         ; => 2 (early exit via continuation)

; Non-local exit
(define (search pred lst)
  (call/cc
    (lambda (return)
      (for-each
        (lambda (item)
          (when (pred item)
            (return item)))
        lst)
      #f)))

(search even? '(1 3 5 6 7 8))  ; => 6 (exits early)

; Backtracking (advanced)
(define (amb . choices)
  ...)  ; Implementation uses continuations
```

**Note:** Continuations are powerful but complex. Use for advanced control flow (exceptions, backtracking, coroutines).

## Key Libraries & Tools

### SRFI (Scheme Requests for Implementation)

SRFIs are portable libraries across Scheme implementations:

**Common SRFIs:**
- **SRFI-1:** List library (rich list operations)
- **SRFI-9:** Define-record-type (structs)
- **SRFI-13:** String libraries
- **SRFI-26:** Cut (partial application)
- **SRFI-43:** Vector library
- **SRFI-69:** Hash tables

```scheme
; SRFI-1 examples
(use-modules (srfi srfi-1))  ; Guile
; or (import (srfi 1))        ; R7RS

(take '(1 2 3 4 5) 3)         ; => (1 2 3)
(drop '(1 2 3 4 5) 2)         ; => (3 4 5)
(split-at '(1 2 3 4) 2)       ; => ((1 2) (3 4))
(partition even? '(1 2 3 4))  ; => ((2 4) (1 3))

; SRFI-9 records
(define-record-type <point>
  (make-point x y)
  point?
  (x point-x point-x-set!)
  (y point-y point-y-set!))

(define p (make-point 3 4))
(point-x p)  ; => 3
```

### Common Libraries

**Racket:**
- Web server: `(require web-server/servlet)`
- JSON: `(require json)`
- Database: `(require db)`
- HTTP client: `(require net/http-client)`

**Guile:**
- Web: `(use-modules (web ...))`
- POSIX: `(use-modules (ice-9 posix))`
- Networking: `(use-modules (ice-9 sockets))`

## Scheme vs Other Lisps

### Scheme vs Common Lisp

| Aspect | Scheme | Common Lisp |
|--------|--------|-------------|
| **Size** | Minimal (R7RS-small: ~90 pages) | Large (ANSI: 1000+ pages) |
| **Namespaces** | Single (functions and variables share) | Separate (Lisp-1 vs Lisp-2) |
| **Macros** | Hygienic (syntax-rules) | Unhygienic (defmacro) |
| **Scope** | Lexical only | Lexical + dynamic |
| **Philosophy** | Minimalism, elegance | Practicality, batteries-included |
| **Community** | Academic, small | Industry, larger |

**Example of namespace difference:**
```scheme
; Scheme (Lisp-1): One namespace
(define (map f lst) ...)
(map + '(1 2))  ; ERROR: + is not a list

; Common Lisp (Lisp-2): Separate namespaces
(defun map (f lst) ...)
(map #'+ '(1 2))  ; OK: #' accesses function namespace
```

### Scheme vs Clojure

| Aspect | Scheme | Clojure |
|--------|--------|---------|
| **Platform** | Native/C | JVM (Java interop) |
| **Data Structures** | Lists primarily | Persistent vectors, maps, sets |
| **Syntax** | Pure S-expressions | Extended (vectors `[]`, maps `{}`) |
| **Concurrency** | Basic (varies by impl) | Rich (atoms, refs, agents, STM) |
| **Ecosystem** | Academic | Industry (web, data) |
| **Immutability** | Optional (mutable with set!) | Default |

**Syntax comparison:**
```scheme
; Scheme
(define data '(1 2 3))
(define point (cons 'x 5))
```

```clojure
; Clojure
(def data [1 2 3])          ; Vector (not list)
(def point {:x 5 :y 10})    ; Map
```

### Scheme vs Emacs Lisp

| Aspect | Scheme | Emacs Lisp |
|--------|--------|------------|
| **Scoping** | Lexical | Dynamic (mostly, lexical available) |
| **Purpose** | General-purpose | Emacs extension |
| **Tail Calls** | Required optimization | Not guaranteed |
| **Standard** | R7RS | Emacs-specific |
| **Continuations** | Full (call/cc) | None |

**Scoping example:**
```scheme
; Scheme (lexical)
(let ((x 10))
  (define (foo) x)
  (let ((x 20))
    (foo)))  ; => 10 (captures x from definition)
```

```elisp
; Emacs Lisp (dynamic by default)
(let ((x 10))
  (defun foo () x)
  (let ((x 20))
    (foo)))  ; => 20 (uses x from call site)
```

**See:** [Emacs Lisp](./emacs-lisp.md)

## Learning Path

### 1. Start with SICP

*Structure and Interpretation of Computer Programs* is the classic text for learning Scheme and computer science fundamentals.

**Topics covered:**
- Building abstractions
- Data abstraction
- Modularity and state
- Metalinguistic abstraction (interpreters)
- Register machines

**Why SICP?**
- Teaches fundamental CS concepts
- Uses Scheme to illuminate principles
- Focuses on abstraction, not syntax
- Timeless (written 1984, still relevant)

**See:** [SICP](./sicp.md), [Why SICP Matters](./why-sicp-matters.md)

### 2. Choose Your Dialect

Based on your goals:
- **Learning:** Racket (DrRacket IDE, great docs, batteries-included)
- **SICP:** MIT Scheme (designed for the book)
- **Production:** Chicken (compiles to C, good performance)
- **GNU ecosystem:** Guile (embed in applications)
- **Performance:** Chez Scheme (very fast)

### 3. Practice Recursion

Thinking recursively is central to Scheme:

**Exercises:**
- Implement list operations (length, reverse, append)
- Solve tree problems recursively
- Convert iterative algorithms to tail-recursive
- Understand base case + recursive case pattern

```scheme
; Start simple
(define (sum lst)
  (if (null? lst)
      0
      (+ (car lst) (sum (cdr lst)))))

; Then optimize (tail recursion)
(define (sum-tail lst)
  (define (iter lst acc)
    (if (null? lst)
        acc
        (iter (cdr lst) (+ acc (car lst)))))
  (iter lst 0))
```

### 4. Learn Macros

Start with `syntax-rules`, then explore more:

**Progression:**
1. Use existing macros (when, unless, etc.)
2. Write simple syntax-rules macros
3. Understand macro expansion
4. Build DSLs with macros
5. (Advanced) Explore syntax-case or defmacro

**Resources:**
- *The Scheme Programming Language* (Dybvig) - Chapter on macros
- *Let Over Lambda* (Hoyte) - Advanced macro techniques (Common Lisp, but concepts apply)

### 5. Read Quality Code

Study Scheme code to learn idioms:

**Sources:**
- SICP exercises and examples
- SRFI implementations
- Scheme interpreter source (small, readable)
- Racket standard library

### 6. Build Projects

Apply knowledge through projects:

**Ideas:**
- Interpreter for simple language
- Symbolic algebra system
- DSL for specific domain
- Web server (Racket)
- Parser combinator library

## Resources

### Books

**Foundational:**
- *Structure and Interpretation of Computer Programs* (Abelson & Sussman) - **Essential**
- *The Little Schemer* (Friedman & Felleisen) - Learn recursion through puzzles
- *The Seasoned Schemer* (Friedman & Felleisen) - Advanced techniques
- *The Scheme Programming Language* (Dybvig) - Comprehensive reference

**Advanced:**
- *Let Over Lambda* (Hoyte) - Macros (Common Lisp, but concepts apply)
- *On Lisp* (Graham) - Macros and abstraction (Common Lisp)

### Online

**Documentation:**
- [R7RS Specification](https://small.r7rs.org/)
- [Racket Documentation](https://docs.racket-lang.org/)
- [Guile Manual](https://www.gnu.org/software/guile/manual/)
- [Chicken Wiki](https://wiki.call-cc.org/)

**Tutorials:**
- [Yet Another Scheme Tutorial](http://www.shido.info/lisp/idx_scm_e.html)
- [Teach Yourself Scheme](https://ds26gte.github.io/tyscheme/)
- [Beautiful Racket](https://beautifulracket.com/) - Language-oriented programming

**Community:**
- [r/scheme subreddit](https://www.reddit.com/r/scheme/)
- [Racket Discord](https://discord.gg/6Zq8sH5)
- [comp.lang.scheme](news:comp.lang.scheme) (Usenet/Google Groups)

### Standards

- **R5RS:** Classic, widely compatible
- **R6RS:** Libraries, records, more complex
- **R7RS-small:** Modern small standard
- **R7RS-large:** Comprehensive library standard (WIP)

## Practical Applications

### When to Use Scheme

Scheme excels at:

1. **Educational purposes**
   - Learning programming fundamentals
   - Teaching recursion and abstraction
   - Exploring language implementation

2. **Language-oriented programming**
   - Building DSLs
   - Implementing interpreters
   - Metaprogramming experiments

3. **Rapid prototyping**
   - Interactive development
   - Quick experimentation
   - Algorithm exploration

4. **Extending applications**
   - Guile for GNU software
   - Embedded scripting
   - User customization

### Real-World Uses

**Industry:**
- Naughty Dog (game development, past)
- ITA Software (flight search, acquired by Google)
- Various Lisp shops

**Academic:**
- CS education (SICP courses)
- Programming language research
- Theorem proving

**Tools:**
- Guile (GNU extension language)
- Racket (web development, DSLs)
- LilyPond (music engraving, uses Scheme for scripting)

### When NOT to Use Scheme

**Consider alternatives if you need:**
- Large standard library (use Python, Racket)
- Web ecosystem (use JavaScript, Python, Racket)
- Performance-critical systems (use Rust, C++)
- JVM integration (use Clojure)
- Many collaborators (niche language, harder to hire)

## Example: List Processing

```scheme
; Common list operations

; map
(define (my-map f lst)
  (if (null? lst)
      '()
      (cons (f (car lst))
            (my-map f (cdr lst)))))

; filter
(define (my-filter pred lst)
  (cond ((null? lst) '())
        ((pred (car lst))
         (cons (car lst) (my-filter pred (cdr lst))))
        (else (my-filter pred (cdr lst)))))

; fold-left (tail recursive)
(define (fold-left f init lst)
  (if (null? lst)
      init
      (fold-left f
                 (f init (car lst))
                 (cdr lst))))

; fold-right (builds backwards)
(define (fold-right f init lst)
  (if (null? lst)
      init
      (f (car lst)
         (fold-right f init (cdr lst)))))

; Usage examples
(my-map square '(1 2 3 4))
; => (1 4 9 16)

(my-filter even? '(1 2 3 4 5 6))
; => (2 4 6)

(fold-left + 0 '(1 2 3 4))
; => 10

(fold-right cons '() '(1 2 3))
; => (1 2 3)
```

## Connections

- [SICP](./sicp.md) - The classic Scheme textbook
- [Why SICP Matters](./why-sicp-matters.md) - On teaching with Scheme
- [Starting with Lisp](./starting-with-lisp.md) - Benefits of starting with Lisp
- [Language-Oriented Programming](./language-oriented-programming.md) - Scheme for DSLs
- [Functional Programming](./fp.md) - FP concepts
- [Emacs Lisp](./emacs-lisp.md) - Another Lisp dialect
- [The Roots of Lisp](./the-roots-of-lisp.md) - Lisp fundamentals
- [LOP Sudoku Example](./lop-sudoku-article.md) - Scheme in practice

## Summary

Scheme offers a unique learning and development experience:

**Strengths:**
- Minimal, elegant syntax
- Powerful macro system
- First-class functions and continuations
- Tail call optimization
- Interactive REPL development

**Learning benefits:**
- Teaches fundamental CS concepts
- Illuminates programming language principles
- Develops recursive thinking
- Explores metaprogramming

**Practical use:**
- Excellent for education (SICP)
- Great for language-oriented programming
- Good for rapid prototyping
- Useful for extending applications (Guile)

**Trade-offs:**
- Smaller ecosystem than mainstream languages
- Niche (harder to find jobs, collaborators)
- Less batteries-included (except Racket)
- Parentheses take adjustment

**Philosophy:** "Begin your journey with a Lisp, whether it's Scheme from SICP, Racket, Guile, Clojure, or Emacs Lisp. It offers a profound insight into the art of sculpting code. Only by understanding its purity can we truly appreciate the nuances of other languages and recognize that limitations are often mere choices, not necessities."

Scheme is not just a programming language—it's a tool for thinking about computation itself.
