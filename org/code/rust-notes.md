# Rust Notes

Rust is a systems programming language that emphasizes memory safety, concurrency, and performance without garbage collection. Its ownership system eliminates entire classes of bugs at compile time while achieving zero-cost abstractions.

## Overview

**Key Features:**
- Memory safety without garbage collection (ownership system)
- Zero-cost abstractions
- Fearless concurrency
- Rich type system with algebraic data types
- Excellent error messages (compiler as teacher)
- Modern tooling (cargo, clippy, rustfmt)

**Philosophy:** "Hack without fear" — Rust's compiler catches errors that would be runtime bugs in other languages.

## String Types

A string literal is a string slice (&str) that points to a static string that isn't stored on the heap, it's stored in pre-allocated read-only memory embedded in the compiled binary executable. It exists for the entire duration of the program. The string literal itself (&'static str – the reference) is stored on the stack.

A string slice (&str) can also point to a mutable String with its contents stored on the heap, but the slice itself is immutable.

```rust
fn main() {
    // the bytes "hola" are in the binary.
    // the reference (fat pointer) s_literal
    // is on the stack.
    let s_literal: &'static str = "hola"; 

    // the bytes "hola" are on the heap.
    // the reference (fat pointer) s is on
    // the stack and manages the data on the heap.
    let s: String = String::from("hola");

    // the reference (fat pointer) s_literal
    // is on the stack. the data is owned by s.
    let s_slice: &str = &s[..];

    println!("{s_literal} {s} {s_slice}");
}
```

**Key distinctions:**
- `String`: Owned, heap-allocated, growable
- `&str`: Borrowed reference to string data
- `&'static str`: Reference to compile-time known string

## Ownership & Borrowing

Rust's ownership system is its most distinctive feature, enabling memory safety without a garbage collector.

### The Three Rules

1. **Each value has one owner**
2. **Only one owner at a time**
3. **Value is dropped when owner goes out of scope**

### Move Semantics

```rust
// Move ownership
let s1 = String::from("hello");
let s2 = s1;  // s1 is now invalid (moved to s2)
// println!("{}", s1);  // ERROR: borrow of moved value

// Types that implement Copy (integers, floats, etc.) don't move
let x = 5;
let y = x;  // x is still valid (copied, not moved)
println!("{}", x);  // OK
```

### Borrowing

Borrowing allows multiple references without transferring ownership:

```rust
fn calculate_length(s: &String) -> usize {
    s.len()
}  // s goes out of scope, but doesn't own the data, so nothing happens

fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);  // Borrow s1
    println!("Length of '{}' is {}.", s1, len);  // s1 still valid
}
```

**Borrowing rules:**
- You can have **either**:
  - One mutable reference (`&mut T`)
  - Any number of immutable references (`&T`)
- References must always be valid (no dangling pointers)

```rust
let mut s = String::from("hello");

let r1 = &s;      // OK: immutable borrow
let r2 = &s;      // OK: multiple immutable borrows
// let r3 = &mut s;  // ERROR: cannot borrow as mutable while immutable borrows exist

println!("{} {}", r1, r2);
// r1 and r2 are no longer used after this point

let r3 = &mut s;  // OK: no other borrows active
r3.push_str(", world");
```

### Lifetime Annotations

Lifetimes ensure references remain valid:

```rust
// Explicit lifetime annotation
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
// Return value lives as long as the shortest of x or y

// Struct with lifetime
struct ImportantExcerpt<'a> {
    part: &'a str,  // part must live as long as the struct
}

// Lifetime elision (compiler infers lifetimes)
fn first_word(s: &str) -> &str {  // Inferred: fn first_word<'a>(s: &'a str) -> &'a str
    let bytes = s.as_bytes();
    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }
    &s[..]
}
```

## Iterator Patterns

Rust's iterators are lazy and efficient, forming the backbone of functional-style programming in Rust.

### Iterator Types

```rust
let vec = vec![1, 2, 3];

// iter(): Borrow references (&T)
for item in vec.iter() {
    println!("{}", item);  // item is &i32
}

// iter_mut(): Mutable references (&mut T)
let mut vec = vec![1, 2, 3];
for item in vec.iter_mut() {
    *item *= 2;  // item is &mut i32
}

// into_iter(): Take ownership (T)
for item in vec.into_iter() {
    println!("{}", item);  // item is i32, vec is consumed
}
// vec is no longer valid here
```

### Common Adaptors

Iterators provide a rich set of functional operations:

```rust
let numbers = vec![1, 2, 3, 4, 5];

// map: Transform each element
let doubled: Vec<_> = numbers.iter()
    .map(|x| x * 2)
    .collect();

// filter: Select by predicate
let evens: Vec<_> = numbers.iter()
    .filter(|&x| x % 2 == 0)
    .collect();

// fold: Reduce to single value
let sum = numbers.iter()
    .fold(0, |acc, x| acc + x);

// flat_map: Map and flatten
let words = vec!["hello world", "rust lang"];
let chars: Vec<_> = words.iter()
    .flat_map(|s| s.chars())
    .collect();

// flatten: Flatten nested structures
let nested = vec![vec![1, 2], vec![3, 4]];
let flat: Vec<_> = nested.into_iter()
    .flatten()
    .collect();

// take / skip: Limit elements
let first_three: Vec<_> = numbers.iter()
    .take(3)
    .collect();

// zip: Combine two iterators
let names = vec!["Alice", "Bob"];
let ages = vec![25, 30];
let pairs: Vec<_> = names.iter()
    .zip(ages.iter())
    .collect();

// enumerate: Index and element pairs
for (i, value) in numbers.iter().enumerate() {
    println!("{}: {}", i, value);
}

// chain: Concatenate iterators
let a = vec![1, 2];
let b = vec![3, 4];
let combined: Vec<_> = a.iter().chain(b.iter()).collect();
```

### Chaining Operations

The power of iterators comes from chaining:

```rust
let result: Vec<_> = vec![1, 2, 3, 4, 5, 6]
    .into_iter()
    .filter(|x| x % 2 == 0)  // Keep evens: [2, 4, 6]
    .map(|x| x * x)          // Square: [4, 16, 36]
    .filter(|x| x > 10)      // Keep > 10: [16, 36]
    .collect();              // Collect into Vec

println!("{:?}", result);  // [16, 36]
```

### collect() Patterns

`collect()` is polymorphic and can create many types:

```rust
// Vec
let vec: Vec<i32> = (1..=5).collect();

// HashMap
use std::collections::HashMap;
let map: HashMap<_, _> = vec![(1, "one"), (2, "two")].into_iter().collect();

// String
let s: String = ['h', 'e', 'l', 'l', 'o'].iter().collect();

// Result<Vec<T>, E>: Collect until first error
fn parse_numbers(strings: Vec<&str>) -> Result<Vec<i32>, std::num::ParseIntError> {
    strings.iter()
        .map(|s| s.parse::<i32>())
        .collect()  // Returns Err on first parse failure
}

// Partition: Split into two collections
let numbers = vec![1, 2, 3, 4, 5];
let (evens, odds): (Vec<_>, Vec<_>) = numbers.into_iter()
    .partition(|x| x % 2 == 0);
```

### inspect() for Debugging

```rust
let result: Vec<_> = vec![1, 2, 3, 4]
    .into_iter()
    .inspect(|x| println!("before map: {}", x))
    .map(|x| x * 2)
    .inspect(|x| println!("after map: {}", x))
    .filter(|x| x > &4)
    .inspect(|x| println!("after filter: {}", x))
    .collect();

// Output shows values at each stage
```

## Error Handling

Rust uses types to represent success and failure, making errors explicit and forcing handling.

### Result<T, E> Type

```rust
use std::fs::File;
use std::io::Read;

fn read_file(path: &str) -> Result<String, std::io::Error> {
    let mut file = File::open(path)?;  // ? operator: return Err early
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}

// Handling Result
match read_file("data.txt") {
    Ok(contents) => println!("{}", contents),
    Err(e) => eprintln!("Error: {}", e),
}
```

### Option<T> Type

```rust
fn divide(a: i32, b: i32) -> Option<i32> {
    if b == 0 {
        None
    } else {
        Some(a / b)
    }
}

// Common Option methods
let result = divide(10, 2);
result.unwrap();           // 5 (panics if None)
result.expect("div by 0"); // 5 (panics with message if None)
result.unwrap_or(0);       // 5 (returns 0 if None)
result.unwrap_or_else(|| complex_calculation());

// map: Transform Some value
let doubled = result.map(|x| x * 2);  // Some(10)

// and_then: Chain operations that return Option
fn safe_sqrt(x: f64) -> Option<f64> {
    if x >= 0.0 { Some(x.sqrt()) } else { None }
}

divide(16, 2)
    .map(|x| x as f64)
    .and_then(safe_sqrt);  // Some(2.0)

// or_else: Provide alternative
divide(10, 0)
    .or_else(|| divide(10, 2));  // Some(5)
```

### The ? Operator

The `?` operator propagates errors:

```rust
fn process() -> Result<String, std::io::Error> {
    let contents = read_file("data.txt")?;  // Returns Err early if read_file fails
    let parsed = parse_data(&contents)?;
    Ok(format_output(parsed))
}

// Equivalent to:
fn process_verbose() -> Result<String, std::io::Error> {
    let contents = match read_file("data.txt") {
        Ok(c) => c,
        Err(e) => return Err(e),
    };
    let parsed = match parse_data(&contents) {
        Ok(p) => p,
        Err(e) => return Err(e),
    };
    Ok(format_output(parsed))
}
```

### match vs if let vs unwrap

```rust
let result: Result<i32, &str> = Ok(42);

// match: Exhaustive pattern matching
match result {
    Ok(val) => println!("Success: {}", val),
    Err(e) => eprintln!("Error: {}", e),
}

// if let: Handle one case
if let Ok(val) = result {
    println!("Success: {}", val);
}

// unwrap: Panic on error (use sparingly, mainly in examples/tests)
let val = result.unwrap();

// unwrap_or: Provide default
let val = result.unwrap_or(0);

// expect: Panic with custom message (better than unwrap for debugging)
let val = result.expect("Failed to process result");
```

## Pattern Matching

Rust's pattern matching is powerful and exhaustive.

### match Expressions

```rust
let number = 5;

match number {
    0 => println!("zero"),
    1..=9 => println!("single digit"),  // Range pattern
    10 => println!("ten"),
    _ => println!("something else"),     // Catch-all
}

// match is an expression (returns value)
let description = match number {
    0 => "zero",
    n if n < 0 => "negative",           // Guard
    n if n % 2 == 0 => "even",
    _ => "odd",
};
```

### Destructuring

```rust
// Tuples
let point = (0, 5);
let (x, y) = point;

match point {
    (0, 0) => println!("origin"),
    (0, y) => println!("y-axis: {}", y),
    (x, 0) => println!("x-axis: {}", x),
    (x, y) => println!("({}, {})", x, y),
}

// Structs
struct Point { x: i32, y: i32 }
let p = Point { x: 0, y: 7 };

let Point { x, y } = p;  // Destructure

match p {
    Point { x: 0, y: 0 } => println!("origin"),
    Point { x, y: 0 } => println!("x-axis: {}", x),
    Point { x: 0, y } => println!("y-axis: {}", y),
    Point { x, y } => println!("({}, {})", x, y),
}

// Enums
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
}

let msg = Message::Move { x: 10, y: 20 };

match msg {
    Message::Quit => println!("quit"),
    Message::Move { x, y } => println!("move to ({}, {})", x, y),
    Message::Write(text) => println!("text: {}", text),
}
```

### Guards

```rust
let num = Some(4);

match num {
    Some(x) if x < 5 => println!("less than five: {}", x),
    Some(x) => println!("{}", x),
    None => (),
}

// Multiple patterns with or
match num {
    Some(2) | Some(4) | Some(6) => println!("even"),
    Some(1) | Some(3) | Some(5) => println!("odd"),
    _ => println!("something else"),
}
```

## Functional Programming in Rust

### Why Rust Favors FP

*"Idiomatic Rust favors functional programming. It's a better fit for its ownership model."* — [Sylvain Kerkour](https://kerkour.com/rust-functional-programming)

**Reasons:**
- Immutability by default (`let` vs `let mut`)
- Ownership prevents shared mutable state
- Iterator chains avoid intermediate allocations
- Zero-cost abstractions (FP without performance penalty)
- Algebraic data types (enums, pattern matching)

### Ownership + FP Synergy

```rust
// Iterator chains are efficient and safe
let result: Vec<_> = data
    .into_iter()               // Consumes data (ownership transferred)
    .filter(|x| x.is_valid())  // No shared state
    .map(|x| x.process())      // Transformations are pure
    .collect();                // One allocation at the end

// Contrast with mutable approach (more error-prone)
let mut result = Vec::new();
for item in data {
    if item.is_valid() {
        result.push(item.process());
    }
}
```

### Common FP Patterns

```rust
// Pipeline
let result = input
    .map(|x| x * 2)
    .and_then(|x| validate(x))
    .unwrap_or_default();

// Fold (reduce)
let sum = vec![1, 2, 3, 4]
    .iter()
    .fold(0, |acc, x| acc + x);

// Composition (manual, no built-in compose)
fn compose<A, B, C, F, G>(f: F, g: G) -> impl Fn(A) -> C
where
    F: Fn(B) -> C,
    G: Fn(A) -> B,
{
    move |x| f(g(x))
}

let double = |x: i32| x * 2;
let increment = |x: i32| x + 1;
let double_then_increment = compose(increment, double);

double_then_increment(5);  // 11
```

**See:** [Functional Programming](./fp.md)

## Comparison with Other Languages

### Rust vs Python

| Aspect | Rust | Python |
|--------|------|--------|
| **Speed** | Very fast (compiled, zero-cost abstractions) | Slower (interpreted) |
| **Safety** | Compile-time guarantees (ownership, types) | Runtime errors common |
| **Memory** | Manual (ownership system) | Garbage collected |
| **Learning Curve** | Steep (ownership, lifetimes) | Gentle |
| **Use Cases** | Systems programming, performance-critical, embedded | Scripting, data analysis, rapid prototyping |
| **Concurrency** | Fearless (compile-time race condition prevention) | GIL limits parallelism |
| **Type System** | Strong static (with inference) | Dynamic (optional hints) |

**When to choose:**
- **Rust**: Performance matters, memory safety critical, systems programming, long-running services
- **Python**: Rapid development, data analysis, scripts, extensive library support needed

**See:** [Python Notes](./python-notes.md)

### Rust vs C++

| Aspect | Rust | C++ |
|--------|------|-----|
| **Memory Safety** | Guaranteed by compiler | Undefined behavior possible |
| **Learning Curve** | Steep but clear | Very steep, many footguns |
| **Tooling** | Modern (cargo, rustfmt, clippy) | Fragmented (many build systems) |
| **Backwards Compatibility** | Breaking changes allowed | Very strong (decades) |
| **Standard Library** | Smaller but growing | Large and mature |

**Key insight:** Rust prevents at compile time what C++ catches at runtime (if at all).

### Rust vs Scheme

| Aspect | Rust | Scheme |
|--------|------|--------|
| **Memory Model** | Ownership (no GC) | Garbage collected |
| **Type System** | Strong static | Dynamic |
| **Paradigm** | Multi-paradigm (FP + imperative) | Functional-first |
| **Performance** | Very fast | Variable (interpreter dependent) |
| **Use Cases** | Systems, performance | Education, language experiments |

**Complementary:** Learn Scheme for FP concepts, Rust for applying them with performance and safety.

**See:** [Scheme](./scheme.md)

## cargo Workflow Essentials

cargo is Rust's build tool and package manager:

### Common Commands

```bash
# Create new project
cargo new my_project        # Binary (executable)
cargo new --lib my_lib      # Library

# Build
cargo build                 # Debug build (faster compilation, slower runtime)
cargo build --release       # Release build (slower compilation, optimized)

# Run
cargo run                   # Build and run
cargo run --release         # Build with optimizations and run

# Test
cargo test                  # Run all tests
cargo test test_name        # Run specific test
cargo test --release        # Test with optimizations

# Documentation
cargo doc                   # Generate docs for project and dependencies
cargo doc --open            # Generate and open in browser

# Code Quality
cargo fmt                   # Format code (rustfmt)
cargo clippy                # Lint (catches common mistakes)
cargo check                 # Fast compile check (no binary output)

# Dependencies
cargo add package_name      # Add dependency (requires cargo-edit)
cargo update                # Update dependencies
cargo tree                  # Show dependency tree
```

### Project Structure

```
my_project/
├── Cargo.toml              # Manifest (dependencies, metadata)
├── Cargo.lock              # Exact versions (commit this!)
├── src/
│   ├── main.rs            # Binary entry point
│   ├── lib.rs             # Library entry point
│   └── utils.rs           # Module
├── tests/                 # Integration tests
│   └── integration_test.rs
├── benches/               # Benchmarks
│   └── my_benchmark.rs
└── examples/              # Example code
    └── example.rs
```

### Testing Patterns

```rust
// Unit tests (in same file as code)
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_addition() {
        assert_eq!(add(2, 3), 5);
    }

    #[test]
    #[should_panic]
    fn test_division_by_zero() {
        divide(10, 0);
    }

    #[test]
    fn test_result() -> Result<(), String> {
        if add(2, 2) == 4 {
            Ok(())
        } else {
            Err(String::from("math is broken"))
        }
    }
}

// Integration tests (in tests/ directory)
// tests/integration_test.rs
use my_project::public_function;

#[test]
fn test_integration() {
    assert!(public_function());
}
```

### Documentation

```rust
/// Calculate the sum of two numbers.
///
/// # Examples
///
/// ```
/// let result = my_crate::add(2, 3);
/// assert_eq!(result, 5);
/// ```
///
/// # Panics
///
/// This function does not panic.
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

// Module-level docs
//! This module provides mathematical utilities.
//!
//! It includes functions for basic arithmetic.
```

## Common Patterns

### Builder Pattern

```rust
pub struct Server {
    port: u16,
    host: String,
    max_connections: usize,
}

impl Server {
    pub fn builder() -> ServerBuilder {
        ServerBuilder::default()
    }
}

#[derive(Default)]
pub struct ServerBuilder {
    port: Option<u16>,
    host: Option<String>,
    max_connections: Option<usize>,
}

impl ServerBuilder {
    pub fn port(mut self, port: u16) -> Self {
        self.port = Some(port);
        self
    }

    pub fn host(mut self, host: impl Into<String>) -> Self {
        self.host = Some(host.into());
        self
    }

    pub fn max_connections(mut self, max: usize) -> Self {
        self.max_connections = Some(max);
        self
    }

    pub fn build(self) -> Result<Server, &'static str> {
        Ok(Server {
            port: self.port.ok_or("port required")?,
            host: self.host.ok_or("host required")?,
            max_connections: self.max_connections.unwrap_or(100),
        })
    }
}

// Usage
let server = Server::builder()
    .port(8080)
    .host("localhost")
    .max_connections(200)
    .build()?;
```

### Newtype Pattern

Wrap types to prevent mixing incompatible values:

```rust
struct UserId(u64);
struct ProductId(u64);

fn get_user(id: UserId) -> User { ... }
fn get_product(id: ProductId) -> Product { ... }

// Type safety prevents mistakes
let user_id = UserId(123);
let product_id = ProductId(456);

get_user(user_id);       // OK
get_user(product_id);    // ERROR: type mismatch
```

### Type State Pattern

Encode state in the type system:

```rust
struct Locked;
struct Unlocked;

struct Door<State> {
    _state: PhantomData<State>,
}

impl Door<Locked> {
    fn unlock(self) -> Door<Unlocked> {
        println!("Door unlocked");
        Door { _state: PhantomData }
    }
}

impl Door<Unlocked> {
    fn lock(self) -> Door<Locked> {
        println!("Door locked");
        Door { _state: PhantomData }
    }

    fn open(&self) {
        println!("Door opened");
    }
}

// Compile-time state enforcement
let door = Door::<Locked> { _state: PhantomData };
// door.open();  // ERROR: cannot open locked door
let door = door.unlock();
door.open();     // OK
```

## Resources

### Books
- *The Rust Programming Language* (Official "Book")
- *Rust for Rustaceans* (Jon Gjengset) - Advanced techniques
- *Programming Rust* (Blandy, Orendorff, Tindall) - Comprehensive guide

### Online
- [The Rust Book](https://doc.rust-lang.org/book/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [Rustlings](https://github.com/rust-lang/rustlings) - Interactive exercises
- [std Library Docs](https://doc.rust-lang.org/std/)

### Articles
- [Functional Rust](https://kerkour.com/rust-functional-programming)
- [Rust Ownership Explained](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html)

## Connections

- [Functional Programming](./fp.md) - FP patterns in Rust
- [Python Notes](./python-notes.md) - Comparison with Python

## Summary

Rust offers a unique combination of safety, performance, and modern ergonomics:

**Strengths:**
- Memory safety without garbage collection
- Zero-cost abstractions (pay only for what you use)
- Fearless concurrency (compiler prevents data races)
- Excellent tooling (cargo, clippy, rustfmt)
- Growing ecosystem

**Learning curve:**
- Ownership system takes time to internalize
- Borrow checker can feel restrictive initially
- Lifetimes add complexity
- But: compiler teaches you as you learn

**Best for:**
- Systems programming
- Performance-critical applications
- Concurrent services
- Embedded systems
- CLI tools

**Philosophy:** Make illegal states unrepresentable. Use the type system to encode correctness, letting the compiler catch bugs before runtime.

**From FP perspective:** Rust's ownership model naturally encourages functional patterns—immutability by default, no shared mutable state, value transformations through iterator chains. FP in Rust is not just idiomatic; it's the path of least resistance.