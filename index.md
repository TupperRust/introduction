---
# vim: set filetype=revealjs:
# vim: set syntax=markdown:

title: "Introduction to the Rust :crab: language"
# subtitle: "Subtitle"
# author: "Author"
# date: "2024-01-26"

revealjs-url: https://unpkg.com/reveal.js@latest
slideNumber: true
theme: solarized

header-includes: |
    <style>
    :root .reveal {
        background-image: url("https://upload.wikimedia.org/wikipedia/commons/c/c3/License_icon-mit.svg");
        background-repeat: no-repeat;
        background-size: 5vw 5vw;
        background-position: 2vw calc(100% - 2vw);
    }
    </style>
---

<style>
section {
   font-size: 32px;
}
</style>

This presentation roughly follows [The Rust Programming Language][TheBook] also called **The Book** by _Steve Klabnik_ and _Carol Nichols_ with contributions from the Rust Community

---

You can generate an offline version of [**The Book**][TheBook] with

```
$ rustup docs --book
```

[TheBook]: https://doc.rust-lang.org/stable/book/

# Installation

## `Linux` and `macOS`

```
$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

> You'll also need a _linker_ that usually comes with a `C` compiler.
> So, yeah, get your distro's build tools.

## `Windows`

Visit the [installation page](https://www.rust-lang.org/tools/install) and follow along.

> Just like with `*nix`, you'll need some build tools.
> They usually come with `Visual Studio`.

## Sanity check

::: columns
:::: column
Compiler

```
$ rustc --version
rustc x.y.z (acabacab yyyy-mm-dd)
```
::::
:::: column
Tooling

```
$ cargo --version
cargo x.y.z (acabacab yyyy-mm-dd)
```
::::
:::

## What's next?

::: columns
:::: column
Updating

```
$ rustup update
```
::::
:::: column
Uninstalling

```
$ rustup self uninstall
```
::::
:::

# Survival guide

## Project's anatomy

```
$ cargo new foo && cd foo && tree
.
├── Cargo.toml
└── src
    └── main.rs

2 directories, 2 files
```

## Project's configuration: <br/>`Cargo.toml`

```toml
[package]
name = "foo"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at
# https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

## Project's entry point: <br/>`src/main.rs`

```rust
fn main() {
    println!("Hello, world!");
}
```

## Rise and shine

```
$ cargo run
Compiling foo v0.1.0 (/path/to/the/project/foo)
    Finished dev [unoptimized + debuginfo] target(s) in 6.11s
     Running `target/debug/foo`
Hello, world!
```



# Printing


::: columns

:::: column

```rust
println!("This is plain text.");
println!("Now an integer: {}", 42);
let var = 4.5;
println!("This is var: {}", var);
println!("Var again: {var}");
let arr = [3, 4, 5];
println!("And an array: {arr:?}");
```

::::

:::: column

* Is a **macro**
* **First** argument: **format string**
* `{...}` in format string<br/>= display of a **value**
* Can print **almost anything** <br/>using `{:?}` (Debug formatting)

::::
:::

# Variables

## Declaration

```rust
fn main() {
    let x = 1312;
    println!("Here is some Rust wisdom: {x}")
}
```

```
$ cargo run
Here is some Rust wisdom: 1312
```

## Mutability

```rust
fn main() {
    let x = 1312;
    println!("Here is some Rust wisdom: {x}");

    x = 1337;
    println!("Here is more: {x}");
}
```

```
$ cargo run
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:5:5
  |
2 |     let x = 1312;
  |         -
  |         |
  |         first assignment to `x`
  |         help: consider making this binding mutable: `mut x`
...
5 |     x = 1337;
  |     ^^^^^^^^ cannot assign twice to immutable variable
```

---

```rust
fn main() {
    let mut x = 1312;
    println!("Here is some Rust wisdom: {x}");

    x = 1337;
    println!("Here is more: {x}");
}
```

```
$ cargo run
Here is some Rust wisdom: 1312
Here is more: 1337
```

## Shadowing

```rust
fn main() {
    let x = 5;
    let x = x + 1;
    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }
    println!("The value of x is: {x}");
}
```

```
$ cargo run
The value of x in the inner scope is: 12
The value of x is: 6
```

# Data types

* Every value is of a certain data type so that the compiler knows what operations can be performed,
* Rust is a statically typed language : types must be known at compile-time,
* The compiler is pretty good at inferring types for you, but not always.

---

```rust 
let guess = "42".parse().expect("Not a number!");
```

```
$ cargo run
 ✦ ❯ cargo run
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^        ----- type must be known at this point
  |
  = note: cannot satisfy `<_ as FromStr>::Err == _`
help: consider giving `guess` an explicit type
  |
2 |     let guess: /* Type */ = "42".parse().expect("Not a number!");
  |              ++++++++++++
```

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

## Scalar types

### Integers

| Length	| Signed	| Unsigned |
| :-: | :-: | :-: | 
| 8-bit	| i8	| u8 |
| 16-bit	| i16	| u16 |
| 32-bit	| i32	| u32 |
| 64-bit	| i64	| u64 |
| 128-bit	| i128	| u128 |
| arch	| isize	| usize |

### Integer literals

| Number literals	| Example |
| :-: | :-: |
| Decimal	| 98_222 |
| Hex	| 0xff |
| Octal	| 0o77 |
| Binary	| 0b1111_0000 |
| Byte (u8 only)	| b'A' |

### Others

::: columns
:::: column
**Floating points**

`f32` and `f64`

Always signed!
::::
:::: column
**Booleans**

`true` and `false` 

~~and `maybe`~~
::::
:::: column
**Characters**

`'a'`, `'Z'`, \':crab:\'

UTF-8 encoded
::::
:::

## Compound types

### Tuple

::: columns
:::: column
```rust
let tup: (i32, f64, u8) = (25, 6.4, 1);
println!("First: {}", tup.0)

let (x, _, z) = tup;
println!("First: {}", x);
println!("Last: {}", z);
```
::::
:::: column
* Fixed-size
* Zero-indexed
* Destructurable
* **Heterogeneous**.
::::
:::

### Array

::: columns
:::: column
```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
println!("First: {}", a[0]);

let [x, _, _, _, z] = a;
println!("First: {}", x);
println!("Last: {}", z);
```
::::
:::: column
* Fixed-size
* Zero-indexed
* Destructurable
* **Homogeneous**.
::::
:::

## Custom types

### Structures

```rust
#[derive(Debug)]
struct Player {
    position: (i32, u32),
    name: String,
}

fn main() {
    let player = Player {
        position: (0, 0),
        name: String::from("Jhon"),
    };

    println!("{player:?}")
}
```

```
$ cargo run
Player { position: (0, 0), name: "Jhon" }
```

---

```rust
let position = (0, 0);
let name = String::from("Jhon");
let player1 = Player {
    position,
    name,
};

let player2 = Player {
    name: String::from("Fab"),
    ..player1
};
```

### Enumerations

```rust
#[derive(Debug)]
enum Entity {
    Player,
    Slime,
}

fn main() {
    let player1 = Entity::Player;
    let enemy = Entity::Slime;
    println!("Player1 entity: {:?}", player1);
}
```

```
$ cargo run
Player1 entity: Player
```

---

```rust
#[derive(Debug)]
enum Entity {
    Player(i32, i32),
    Slime(i32, i32),
}

fn main() {
    let player1 = Entity::Player(3, 10);
    let enemy = Entity::Slime(2, 4);
    println!("Player1 at (x:3; y:10): {:?}", player1);
}
```

```
$ cargo run
Player1 at (x:3; y:10): Player(3, 10)
```

---


```rust
struct Player {
    position: (i32, u32),
    name: String,
}

enum Entity {
    Player(Player),
    Slime(i32, i32),
}

fn main() {
    let player1 = Entity::Player(Player {
        position: (3, 10),
        name: String::from("Steve"),
    });
    let enemy = Entity::Slime(2, 4);
    println!("Player1: {:?}", player1);
}
```

```
$ cargo run
Player1: Player(Player { position: (3, 10), name: "Steve" })
```

### The enums `Option` and `Result`

```rust
enum Option<T> {
    Some(T),
    None,
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

* Allow for **recoverable errors**
* Implies to have **an explicit error type**
* Have a **lot of tools** for easy handling


# References

We won't talk about the borrowing rules :slightly_smiling_face:

## Immutable references

* Allows to access the data
* Doesn't allow the data and its content to change
* Always points to **valid data**

```rust
let a = (100, 42);
let a_ref = &a;
println!("{}", a_ref.0); // yay, it's alright!
a_ref.1 = 5; // Grrr you can' t do that! (doesn't *compile*)
```

## Mutable references

* Allows to access the data
* Allows to **modify** the data
* Always points to **valid data**

```rust
let a = (100, 42);
let a_ref = &mut a;
println!("{}", a_ref.0); // yay, it's alright!
a_ref.0 = 5; // Yup, that's ok too!
```


# Functions, methods & traits

## Functions

---

 ```rust
 fn battle() {
     println!("What an epic battle!!!");
 }
 ```

---

  ```rust
fn battle(player: Player, mob: Slime) {
	// Do battle
    println!("What an epic battle!!!");
}
  ```

* Types are not inferred
* No overloading (instead, we can use macros or polymorphism)

---

```rust
fn battle(player: Player, mob: Slime) -> Entity {
	let winner = player;
    return Entity::Player(player);
}
```

* Return type not inferred

---

```rust
fn battle(player: Player, mob: Slime) -> Entity {
	let winner = player;
    Entity::Player(player)
}
```

* The last expression of the function (without semicolon) is the return value

## Methods

```rust
#[derive(Debug)]
struct Player {
    position: (i32, u32),
    name: String,
}

impl Player {
    fn exchange_position(&mut self, other: &mut Player) {
        std::mem::swap(&mut self.position, &mut other.position);
    }
}
```

---

```rust
enum Entity {
    Player(Player),
    Slime(i32, i32),
}

impl Entity {
    fn position(&self) -> (i32, i32) {
        match self {
            Self::Player(content) => content.position,
            Self::Slime(x, y) => (x, y),
        }
    }
}
```

## Traits

```rust
trait Body {
    fn position(&self) -> (i32, i32);
    fn size(&self) -> (u32, u32);
}
```

---

```rust
impl Body for Player {
    fn position(&self) -> (i32, i32) {
        self.position
    }
    
    fn size(&self) -> (u32, u32) {
        (1, 2)
    }
}

impl Body for Slime {
    fn position(&self) -> (i32, i32) {
        self.position
    }
    
    fn size(&self) -> (u32, u32) {
        (3, 3)
    }
}
```

---

```rust
trait Body {
    fn position(&self) -> (i32, i32);
}

fn higher<B0: Body, B1: Body>(body1: B0, body2: B1) -> Ordering {
     let (_, b0_y) = body0.position();
     let (_, b1_y) = body1.position();
     if b0_y < b1_y {
         Ordering::Less
     } else if b0_y == b1_y {
         Ordering::Equal
     } else {
         Ordering::Greater
     }
}

let is_player_winning = higher(player, slime) == Ordering::Greater;
```
