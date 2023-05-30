---
title: A summary of "Error Handling in Rust"
date: 2023-05-30
description: I attempt to summarize an article by @burntsushi5
collections:
  - tech
  - rust
tags:
  - featured
  - wip
  - tech
  - rust
---

[Here's ](https://blog.burntsushi.net/rust-error-handling/) the article -

## Summary: Error Handling in Rust

- Panics are bad!
- Rust enables us to use **errors as variables and types** as opposed to errors as exceptions. This is better for composability.
- using `.unwrap()` is almost always not a good idea, errors can be handled so much better -> this is because `.unwrap()` forces a `panic!()`.
- It's good to know what `Option<T>` and `Result<T, E>` look like internally as this knowledge allows us to handle errors so much better.
- It's also good to know that `Option<T> == Result<T, ()>`, and that you can convert between the two
- If you're using `.unwrap()` multiple times in a row, it's probably a better idea to use a special _combinator_ type to allow composition.
  - These combinators (`.and_then()`, `.unwrap_or()`, `.or_else()`, `.map()`) allow us to elide repetitive stuff from the code and make the code easier to read.
- Combinators are sometimes hard to read and reason about (especially if you don't know the underlying implementation) -> this is why it's usually more readable (in my opinion) to stick to implicit case analysis by using things like the `try!()` macro and the `?` operator in conjunction with _early returns_
- If you're combining error types, you can explore multiple options, including, but not limited to -
  - Converting every error to a `String` - you lose contextual information like `io::ErrorKind`
  - Using `Box<dyn Err [+ Send + Sync]>` - same problem - because we're treating the error here as a [[Trait Object]], we lose all other information about it.
- The best option for library authors is to define their custom error types (see stdlib definitions of errors as a template), which also define `std::from::From<T>` on the error types that they depend on
  - this is so that you can easily use the `?` operator to handle unwraps and result err matches
  - Any other error type will try to be converted to your custom return error type, so it's good to define the `std::from::From<E: Err>` trait on your custom error type.
- Lastly, levels of error handling ordered by difficulty of understandability
  - TODO: Code Examples
  - Combinators
  - Explicit Case Analysis
  - Inline implicit unwraps but top-level catch
