---
title: Rustlang 101
date: 2022-04-28
description: Let's Learn about Rust!
collections:
  - tech
tags:
  - rust
  - latest
  - featured
---

# PES Innovation Lab - Rustlang Workshop

![](https://pbs.twimg.com/media/DdYl6MIWsAA_uSD?format=jpg&name=small)
_Slightly Modified XKCD on Rustlang_

Hello! I'm Anirudh Rowjee, and I'm extremely happy to be conducting this workshop on Rustlang for the PES Innovation Lab, which is a student research and development community located at PES University, RR Campus.

The Lab regularly does research-oriented projects, and will be recruiting soon! Keep your eyes peeled. You can follow the lab on Instagram at `@innovationlab.pes` for more updates.

Today's workshop covers the Rust programming language. Per the [website](https://www.rust-lang.org/), Rust is -

> A language empowering everyone to build reliable and efficient software.

And you'll see that this is true to an large extent. Rust makes it extremely easy to write fast, concurrent and low-level code, with Performance akin to C/C++, with the ease of writing in a high-level language like Python or Javascript.

Now, you might ask - okay, that's all well and good, but why should I care? Do people even use Rust?

Rust can be used to build everything from Compilers to Embedded Software. Command Line Applications, Databases, Web Applications and Networked Applications are all things you can easily write in Rust.

In fact, if you're using Mozilla Firefox, there's an extremely good chance you've already used Rust - the Gecko CSS Rendering engine, which applies CSS Rules in the browser, is a Rustlang product. Discord, too, extensively uses Rust on the backend for their web applications.

Interested? I know I am. Let's get started.

## What is Rust?

![](https://rustacean.net/assets/rustacean-orig-noshadow.svg)
_Ferris, the official mascot of the Rust Programming Language_

Rust is a programming language invented at Mozilla Research in 2006 by Graydon Hoare as a side project. It focuses on Performance (code go fast), Type Safety (code know difference between number and string) and Concurrency (code do many things at the same time).

We'll dive into these one by one.

### Performance

Rust code is verified to be just as fast, if not faster, than C, while having none of the pitfalls that C has when it comes to Memory Safety. A simple google search will show you multiple results for the same.

### Type Safety

Rust uses an extremely strong Type system

Let's ~shamelessy steal from~ refer to the website to understand what Rust _really_ cares about.

## Why should I care?

- memory safety
- compile-time verification
- Rust used by Discord
- Rust in the Embedded setup
- Mark Russinovich (Azure CTO) on Rust

### A Note about the Compiler

## Memory Model and Ownership

From the [Rust Book](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html),

> Ownership is a set of rules that governs how a Rust program manages memory.

Memory is important. Every program uses memory in some way or the other, and this mostly happens through interactions with the Kernel.

Some languages depend on the user for memory management, like C and C++. Other languages do not - they depend on the `language runtime` for Memory Management, like Python, or Javascript. These languages implement what is known as garbage collection (a very hard problem to solve), by automatically returning "unused" memory to the Kernel every once in a while.

The OS manages memory in two ways. Think of these two as the lead characters of the magnum opus that is memory management.

![](/static/images/memory_model.png)
_The Memory Model for a simple program_

### The Stack

The stack is the first way memory is used. Allocations are predictably sized, and are easier for the kernel to keep track of. These units are called stack frames. In the context of a single program, one program occupies and allocates many stack frames.

### The Heap

The Heap, also lovingly known as "The rest of memory", is a set of dynamically-allocated memory blocks. A program makes a runtime request for some memory, and the kernel allocates it, giving the program access to the same memory.

### What's the difference?

The problem is that because heap allocations aren'tstatically sized, allocations on the heap are _much_ slower than allocations on the stack, given there's an overhead to search for the best possible slot in which the kernel must allocate the memory.

Furthermore, accessing memory on the heap is slower, too, given you'll need to follow a pointer to get there.

TL;DR - Heap allocations are slow compared to stack allocations.

Since we can't allocate everything on the stack - because we need to know at compile-time - it's necessary to perform Heap Allocations.

The question now is - how do we make it as fast as possible?

### Ownership

Rust doesn't use a garbage collector at Runtime, but you do not have to manage memory manually. How does this happen?

Rust has three simple rules, called the Rules of Ownership.

1. Each value in Rust has an _owner_.
2. There can only be one owner at a time.
3. When the owner goes out of scope, the value will be dropped.

We'll understand more about this once we write some code, but this (3) is how Rust manages to be fast without having any runtime garbage collection at all. Since it's known at compile time as to which values have to be dropped when, it's extremely easy for the compiler to generate code to do that.

Since there's only one owner for one value, there's no question of a value being referenced after it's dropped, which we lovingly know as the Null Pointer Exception.

This is the reason that Rust takes time to compile, and also the reason that Rust is safe - once you know the model, it's easy for you to reason about whether or not a value will be in scope.

You can still share values between functions. The responsibility for making this happen lies with the Borrow Checker, which uses rules to make sure you're using memory and references responsibly.

![](https://rufflewind.com/img/rust-move-copy-borrow.png)
_[Rufflewind's Scratchpad - Graphical Depiction of Ownership and Borrowing in Rust](https://rufflewind.com/2017-02-15/rust-move-copy-borrow)_

Another very cool thing that this system allows Rust to do is to [_prevent data races at runtime!_](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html#:~:text=The%20benefit%20of%20having%20this%20restriction%20is%20that%20Rust%20can%20prevent%20data%20races%20at%20compile%20time).

## Setup

Let's get started. Use [this REPL](https://replit.com/languages/rust) to write some Rust code today. If you're using the REPL, don't worry about this next it.

For those of you on Linux - if you've already got Rust and Cargo installed -

```bash
$ cargo new rust-workshop
```

That should give you a structure something like this -

```
.
└── rust-workshop
    ├── Cargo.toml
    └── src
        └── main.rs

2 directories, 2 files
```

Cargo is the tool that rust ships with to allow building, debugging and testing to be easier. In fact, you just used it - `cargo new` helped you bootstrap a new project, much like the infamous `create-react-app`.

For those of you familiar with Javascript, `cargo.toml` is like the `package.json` of your Rust project. It's the single source of truth for all dependencies, commands and other information critical to the project.

Okay, let's get started. Pick your favorite editor (or butterfly), and crack open the `main.rs` file.

![](https://imgs.xkcd.com/comics/real_programmers.png)
_XKCD: Real Programmers_

## How do I print something?

Hello, world!

```rust
fn main() {
  println!("Hello, world!")
}
```

That's all. This is how you do Hello, world in Rust! This is a basic Rust program.

- `fn main` marks the entrypoint of the linker and assembler. Every Rust program _must_ have a main function.
- That funky `println!` is what's known as a Macro - the compiler replaces this with other code during compile-time. Here's what's actually underneath that code -

  ```rust
  // Recursive expansion of println! macro
  // ======================================

  {
      $crate::io::_print($crate::fmt::Arguments::new_v1(&[], &[]));
  }
  ```

  All this goes to say that the Rust compiler is very smart, and it's possible for it to know a _lot_ about the code that you write. It also catches things like memory safety bugs, which is a _huge_ win for companies writing software safe, reliable software (which is just about everyone).

## Say my name

Let's make this Rust program greet us. Expected behavior is as follows -

- [ ] Say Hello, world!
- [ ] Ask the user for their name
- [ ] Print this name

Say no more.

```rust
use std::io;

fn main() {

    // Print hello, world!
    println!("hello, world!");

    // Declare a new string
    let mut name = String::new();

    // Declare an I/O Handler to interact with the OS, and read from it
    let stdin = io::stdin();
    stdin.read_line(&mut name).unwrap();

    // Print the formatted name
    println!("Hello, {}", name);

}
```

Man, that's quite a bit. Now that we know this runs, and works, let's ask ourselves some questions -

- `use std::io`? What's that?

  Rust uses the `use` keyword to allow us to import packages or libraries into the program.

- `let mut`?

  Variables in Rust are immutable by default. `let mut` allows us to declare a mutable variable, meaning that its value can be changed.

- What the hell is `.unwrap()`?

  As we know, there are many operations that can fail when we're writing a program. The `.unwrap()` call just tells the compiler to crash if this fails.

  What if we don't want that? Let's look at what Unwrap does under the hood. Don't Panic!

  ```rust
  match read_result {
      Ok(bytes_read) => println!("{} Bytes Read", bytes_read),
      // looks like we panicked :(
      Err(err) => panic!("{}", err)
  }
  ```

  This works because of what's known as the `Result` enum. It's a set of pre-defined states or values, of which only one can be valid at a time.

## Building a wordle validator

The goal is simple - given two five letter words `target` and `input`, we match `input` to `target` letter by letter, emitting the color that you'd expect in the actual game.

```rust
fn main() {

}
```

## What are enums, and what's the match block?

Enums are values that can be in only one of a set of predefined states at once.

## What does a simple guessing game look like?

## What is going on?
