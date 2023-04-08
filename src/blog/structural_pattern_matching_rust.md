---
title: Structual Pattern Matching with Rust
date: 2022-04-28
description: Some cool features of Rust!
tags:
  - rust
  - latest
  - WIP
---

Rust is so cool!

Let's say I want to check a struct to see if it has a field or not, and based on that, generate a new value for said field.

```rust

fn match() {
    let mut shortcode = String::new();
    // check if the user wants the shortcode to be generated or if they want us to generate one
    match &new_url.shortcode {
        Some(custom_shortcode) => {
            shortcode = custom_shortcode.to_string();
        }
        None => {
            // need to generate
            shortcode = generate_random_shortcode();
        }
    }
}
```

I can instead do -

```rust
fn match() {

    let mut shortcode = match &new_url.shortcode {
        Some(custom_shortcode) => {
            custom_shortcode.to_string();
        }
        None => {
            generate_random_shortcode();
        }
    };

}
```

How Cool is that?
