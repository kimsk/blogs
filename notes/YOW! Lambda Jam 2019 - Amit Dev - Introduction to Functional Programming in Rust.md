# Algebraic Data Type
## 0 values
```rs
enum Void {}
```

## 1 values
```rs
enum Unit {
    Unit
}
//or
()
```

## 1 + 1
```rs
enum Direction {
    Up,
    Down
}
//or
bool
```

## 1 + A
```rs
enum Option<A> {
    Ok(A),
    Err(B)
}
```

## A * B
```rs
(A, B)
//or
struct Type { a: A, b: B }
```

## L = 1 + AL (recursive type) like `list` needs something like `Box` or `Rc`
> Rust needs to know the size

# Pattern Matching
# Traits
- similar to interface but not quite
> clone, +, etc.
# Recursive function
# Closures
> capturing

```rs
fn factorial(n: u64) -> u64 {
    match n {
        0| 1 => 1,
        n    => n * factorial(n - 1),
    }
}

fn factorial(n: u64) -> u64 {
    (1..n+1).fold(1, |acc, i| acc * i)
}

use std::ops::Mul;
fn factorial(n: u64) -> u64 {
    (1..n+1).fold(1, Mul::mul)

}

fn factorial(n: u64) -> u64 {
    (1..n+1).product()
}
```

# Higher Order Functions
- Fn
```rs
fn apply<F, A, B>(f: F, arg: A) -> B
where F:Fn(A) -> B {
    f(arg)
}

fn square(a: i32) -> i32 { a * a }
let n = apply (square, 4)
let n = apply (|x| x * x , 4)

```
# Fn Trait Family
- FnOnce
- FnMut
- Fn

# Compose
```rs
fn compose<X, Y, Z, F, G>(f: F, g: G) -> Fn(X) -> Z
where F: Fn(X) -> Y, G: Fn(Y) -> Z {
    |x| g(f(x))
}
```