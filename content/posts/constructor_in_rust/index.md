+++
date = "2026-01-21T12:00:00+01:00"
draft = false
title = "Constructor in Rust"
tags = ["constructor", "design patterns"]
categories = ["programming", "rust"]
description = "How to make constructor in Rust. Beginner guide"
image = "thumbnail.png"
+++

# Constructor in Rust

I write a lot of Rust code, and one things that annoy me is that you need a lot of keyboard press to get the default value of a structure/enum using the [Default trait](https://doc.rust-lang.org/std/default/trait.Default.html), and it take a lot of space on the screen.

```rust
let x = i32::default(); // use the type
/* ... */
```

You can also use type deduction :
```rust
fn bar(x : i32) { /* ... */ }

// in main :
bar(Default::default()); // it know that the type is i32
```

Long code can be found when initiliazing a struct :

```rust
struct Player
{
    hp  : i32,

    lvl    : i32,
    damage : i32,
    invisible : bool,
    gold_coin : i32,
}

// in main :
// it take so much space on screen...
let p = Player { hp: 100, lvl: Default::default(), damage: Default::default(), invisible: Default::default(), gold_coin: Default::default() };
```

Here I don't use the `#[derive(Default)]` macro because the default `hp` should be 100, only the rest use the default value.

# Less typing and screen space wasted

So I was tired of those really long line, so I made this [crate, *Default is triple underscore*](https://crates.io/crates/default_is_triple_underscore). This crate is the manifestation of the lazy developer that I am to write less characters, and keep the code more easily readable.

It define a `___()` global function (using type inference), and a `i32::___()` function for each type that implement the Default trait.

The same code then becomes:
```rust
let x = i32::___();
```

```rust
fn bar(x : i32) { /* ... */ }

bar(___());
```

And most notably :

```rust
let p = Player { hp: 100, lvl: Default::default(), damage: Default::default(), invisible: Default::default(), gold_coin: Default::default() };
// =>
let p = Player { hp: 100, lvl: ___(), damage: ___(), invisible: ___(), gold_coin: ___() };
```

Of course, I can put the direct value in this case...

```rust
let p = Player { hp: 100, lvl: 0, damage: 0, ininvisible: false, gold_coin: 0 };
```

...but sometime I work with more complexe type like `HashMap`, `Vec`... and typing `___()` is quicker and take less space than `HashMap::new()`.

This crate is also the first one that I shared on crate io, so yep I don't do a lot of stuff, but it is somewhat useful I guess ?!

# Code Source

The source code for the crate is just :


```rust
pub fn ___<T:Default>() -> T {
    std::default::Default::default()
}

pub trait DefaultIsTripleUnderscore : Default
{
    fn ___() -> Self { Self::default() }
}

impl<T:Default> DefaultIsTripleUnderscore for T {}
```

You can add the crate with the cargo command  `cargo add default_is_triple_underscore` or by adding this line to your toml `default_is_triple_underscore = "0.2.0"`

# Some Link

- [default_is_triple_underscore on crate.io](https://crates.io/crates/default_is_triple_underscore)
- [git repos](https://github.com/Thomas-Mewily/triple_underscore_for_default)

- [This post was inspired by "Could we have `std::default()`?"](https://internals.rust-lang.org/t/could-we-have-std-default/8756)

This is also my first post, that why it is "simple" and not too technical.