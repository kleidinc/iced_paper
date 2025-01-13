# Traits

This is part of the [Master Iced Paper](./README.md).

<!-- mtoc-start -->

* [What is a Trait?](#what-is-a-trait)
* [Pattern to a create a Trait?](#pattern-to-a-create-a-trait)
* [Default Implementations](#default-implementations)
* [Examples of Traits (used in Iced)](#examples-of-traits-used-in-iced)
  * [The Into Trait](#the-into-trait)
* [Usecases](#usecases)
  * [Traits as Parameters](#traits-as-parameters)
    * [Pattern to use Traits as Parameters](#pattern-to-use-traits-as-parameters)
      * [Element Type Alias](#element-type-alias)

<!-- mtoc-end -->

# What is a Trait?

Traits allow you to share functionality amongst types.

# Pattern to a create a Trait?

1. You define the trait with `trait TraitName`
2. Inside the trait you add function signatures.
3. You can also define `blank implementations` functions.
4. Then you implement all the trait functions for the type.

```rust
trait HealthyFood {
    fn is_healthy(&self) -> bool;
}
struct Apple {}
struct Meat {}

impl HealthyFood for Apple {
    fn is_healthy(&self) -> bool {
        false
    }
}

impl HealthyFood for Meat {
    fn is_healthy(&self) -> bool {
        true
    }
}
// ... you need to create impl for Meat ... with fn new

fn main() {
    let meat_food = Meat::new();
    println!("The meat is healthy? {:?}", meat_food.is_healthy());
}
```

# Default Implementations

<!-- TODO: Add explation  -->

# Examples of Traits (used in Iced)

## The Into Trait

When you come accross a new trait, the first thing you should do is to go to the [Rust documentation](https://doc.rust-lang.org/std/convert/trait.Into.html), and just read.

```rust
pub trait Into<T>: Sized {
    //
    fn into(self) -> T;
}
```

By simply looking at the code, you see that the Into trait, has a function `into`, that consumes the input value `self`, and returns the value as type `T`.
The Into trait is the opposite of the From trait.

In the case of the Into trait, if you need to implement Into for your type, you should instead implement the From trait, because that automatically implements the Into trait.

Now you should understand that `Into<T>` means that it should be convertable into type T. That means that any variable you provide has to comply to that contract, and has to be convertable to type T.

```rust
struct Wrapper<T>(Vec<T>);

impl<T> From<Wrapper<T>> for Vec<T> {
    fn from(w: Wrapper<T>) -> Vec<T> {
        w.0
    }
}
```

# Usecases

## Traits as Parameters

Usually we use Types to annotate the types variables can hold in functions (as explained in the [Generics Paper](./GENERICS.md).
But we can also use Traits to annotate variables.

The `handle_modal` function to handle pop-up forms used in the application we are building, uses traits as parameters.

### Pattern to use Traits as Parameters

We use the impl keyword and then the traitname, for example `impl TraitName`, which means that the variable can accept any type that implements the Trait.

```rust
fn handle_modal<'a, Message>(parent_screen: impl Into<Element<'a, Message>, child: impl Into<Element<'a, Message>>) -> Element<'a, Message>
where
Message: 'a + Clone
{
    //
}
```

In this case the trait is `Into<T> where T is Element`.

#### Element Type Alias

Element itself is an [Iced type alias](https://docs.rs/iced/latest/iced/type.Element.html#).

```rust
pub type Element<'a, Message, Theme = Theme, Renderer = Renderer> = Element<'a, Message, Theme, Renderer>;
```

A generic widget. This is an alias of iced_native element with a default renderer.

In this case the parent can accept any type that can be converted into an Element<Message>. The Into trait has a function `into()`, that converts it to a T.
