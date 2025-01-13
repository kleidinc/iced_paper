# What is a Trait?

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

# Usecases

## Traits as Parameters
