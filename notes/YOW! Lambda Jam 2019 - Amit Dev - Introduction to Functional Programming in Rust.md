# Introduction to Functional Programming in Rust -- Amit Dev
- [video](https://www.youtube.com/watch?v=9x7W3_KKKeA)
- [slides](https://amitdev.github.io/rust-fp)

## Algebraic Data Type
### 0 values
```rs
enum Void {}
```

### 1 values
```rs
enum Unit {
    Unit
}
//or
()
```

### 1 + 1
```rs
enum Direction {
    Up,
    Down
}
//or
bool
```

### 1 + A
```rs
enum Option<A> {
    Ok(A),
    Err(B)
}
```

### A * B
```rs
(A, B)
//or
struct Type { a: A, b: B }
```

### L = 1 + AL (recursive type) like `list` needs something like `Box` or `Rc`
> Rust needs to know the size

## Pattern Matching
## Traits
- similar to interface but not quite
> clone, +, etc.
## Recursive function
## Closures
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

## Higher Order Functions
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
## Fn Trait Family
- FnOnce
- FnMut
- Fn

## Compose
```rs
fn compose<X, Y, Z, F, G>(f: F, g: G) -> Fn(X) -> Z
where F: Fn(X) -> Y, G: Fn(Y) -> Z {
    |x| g(f(x))
}
```
```
fn compose<X, Y, Z, F, G>(f: F, g: G) -> Fn(X) -> Z
                                        ^^^^^^^^^^^
                        doesn't have a size known at compile-time
```
```rs
fn compose<X, Y, Z, F, G>(f: F, g: G) -> impl Fn(X) -> Y
where F: Fn(X) -> Y, G: Fn(Y) -> Z {
    |x| g(f(x))
}
```
```
closure may outlive the current function, but it borrows `g` which is owned by the current function

|x| g(f(x))
^^^ - `g` is borrowed here
|
may outlive borrowed value `g`
```

move ownership of the function
```rs
fn compose<X, Y, Z, F, G>(f: F, g: G) -> impl Fn(X) -> Y
where F: Fn(X) -> Y, G: Fn(Y) -> Z {
  move |x| g(f(x))
}
```

## Iterator
```rs
data.lines()
    .filter(|line| line.contains(query))
    .map(str::to_lowercase)
    .take(2)
    .collect()
```
## Quick Sort
```rs
fn qsort<T>(lst: Vec<T>) -> Vec<T>
where T: PartialOrd {
    if lst.len() == 0 {
        return lst;
    }
    let pivot = lst[0];
    let (lo, hi) = partition(lst, pivot);
    let (left, right) = (qsort(lo), qsort(hi));
    appended(left, pivot, right)
}

fn parallel_qsort<T>(lst: Vec<T>) -> Vec<T>
where T: PartialOrd {
    if lst.len() == 0 {
        return lst;
    }
    let pivot = lst[0];
    let (lo, hi) = partition(lst, pivot);
    let (left, right) = rayon::join(|| qsort(lo), || qsort(hi));
    appended(left, pivot, right)
}
```

## Summary

- [x] Safe/Total functions
- [x] Algebraic data types
- [x] Immutability/Values
- [?] Higher order functions
- [?] Advanced function abstractions
- [ ] Pure functions

### Rust Target Domains
[Four target domains](https://blog.rust-lang.org/2018/03/12/roadmap.html#four-target-domains)
[![Build it in Rust](https://miro.medium.com/max/4048/1*1y6KGdKZETDfuE2Zh6yrEw.png)](https://medium.com/rustgamedev/a-simple-agenda-for-the-rust-game-development-wg-485cfc72b088)