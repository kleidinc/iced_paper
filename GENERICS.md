# Generics

<!-- mtoc-start -->

* [What are Generics?](#what-are-generics)
* [When are they used?](#when-are-they-used)
* [What is Monomorphization?](#what-is-monomorphization)
* [Usecases](#usecases)
  * [In functions](#in-functions)
  * [In structs](#in-structs)
  * [In enums](#in-enums)
    * [Option<T>](#optiont)
    * [Result<T,E>](#resultte)
  * [In method definitions](#in-method-definitions)
  * [Constraining Generic Types](#constraining-generic-types)

<!-- mtoc-end -->

# What are Generics?

# When are they used?

# What is Monomorphization?

Monomorphization is the process of turning generic types into concrete types.
When Rust code is compiled, the Rust compiler will look everywhere where a generic type is used,
and replace it with a concrete type.

This is also why there is **no runtime cost for using generics**, since after compilation there are no generics anymore.

# Usecases

You will find the same examples of generics in the official Rust Book,
[Chapter 10. Generic Types, Traits, and Lifetimes](https://doc.rust-lang.org/book/ch10-01-syntax.html).

## In functions

```rust
fn largest<T>(list: &[T]) -> &T {}
```

## In structs

```rust
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let integer = Point {x: 5, y:10};
    let float = Point { x: 1.0, y: 4.0};
}
```

```rust
struct Point<T,U> {
    x: T,
    y: U
}

fn main() {
    let both_integer = Point {x: 5, y: 10};
    let both_float = Point {x: 5.0, y: 10.0};
    let integer_and_float = Point {x: 5.0, y: 10};
}
```

## In enums

### Option<T>

```rust
enum Option<T> {
    Some(T),
    None,
}
```

### Result<T,E>

```rust
enum Result<T,E> {
    Ok(T),
    Err(E),
}
```

## In method definitions

```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10};
    println!("p.x = {}", p.x());
}
```

Another example to showcase that the generics in definitions can be different in the methods.

```rust
struct Point<X1, Y1> {
    x: X1,
    y: Y1,
}

impl<X1,Y1> Point<X1,Y1> {
    fn mixup<X2,Y2>(self, other: Point<X2, Y2>) -> Point<X1, Y2> {
        Point {
            x: self.x,
            y: other.y,
        }

    }
}

fn main() {
    let p1 = Point {x: 5, y: 10.4};
    let p2 = Point {x: "Hello", y: 'c'};

    let p3 = p1.mixup(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);

}
```

## Constraining Generic Types
