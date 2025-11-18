# Rust (notes)

## string types

A string literal is a string slice (&str) that points to a static string that isn't stored on the heap, it's stored in pre-allocated read-only memory embedded in the compiled machine code. The string literal itself (&'static str – the reference) is stored on the stack.

A string slice (&str) can also point to a mutable String with its contents stored on the heap.

```rust
fn main() {
    let s_literal: &'static str = "hola"; 
    let s: String = String::from("hola");
    let s_slice: &str = &s[..];

    println!("{s_literal} {s} {s_slice}");
}
```
