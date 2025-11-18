# Rust (notes)

## string types

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
