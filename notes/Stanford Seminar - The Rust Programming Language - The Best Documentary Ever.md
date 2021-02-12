# [Stanford Seminar - The Rust Programming Language - March 2015 - Aaron Turon](https://www.youtube.com/watch?v=SZvs15hC81U)


# Control, Safety, and Programming Languages
- control (e.g., C++) + safety (e.g., ML, Haskel)
- zero-cost abstraction (c++): abiltiy to define abstractions that optimize away to nothing
  - memory layout, static dispatch, template expansion
  - e.g., vector of string that when compiled it down, there is no unnecessary code like in the case of Java/C# (lot of indirections, lot of bookkeeping code)
- safety: 
  - e.g., alaising (more than one owner at the same time) + mutation => undefined behavior 
  - undefined behavior => compiler is free to do anything and programmer has no control.
  - GC:
    - lost control of determinism (GC can run anytime)
    - requires runtime (extra code to do GC)
    - Not solving iterator invalidation, data races, etc.
# Rust's solution
  - Type system enforces ownership and borrowing:
    1. all resources (vector, file, lock, etc.) have a clear owner.
    2. others can borrow from the owner (immutable or mutable).
    3. owners cannot free or mutate the resource while it's borrowed.
  - Ownership/Borrowing
    - no need for runtime (c++)
    - memory safety (GC)
    - data-race freedom
  - lot of goodies
    - closures
    - pattern matching
    - type inference
    - traits
- Ownership
  - disallows aliasing
    - prevents use after free
    - prevents double moves
  - some type like int (without heap usage) can be copied instead of borrowed
# Borrowing ([code](https://github.com/kimsk/try-rust/blob/master/karlkim/ownership/src/main.rs))
  - Borrowing (`T`): transfer ownership
  - Shared Borrowing (`&T`)
    - shared references are immutable
    - allow aliasing but disallow mutation
  - Mutable Borrowing (`&mut T`)
    - disallow aliasing but allow mutation
    - cannot borrow both shared and mutable references at the same time
  - `&`: no writes, no move
  - `&mut`: no access at all

# Data race
  - two unsynchronized threads accessing same data where at least one writes.
  - aliasing + mutation + no ordering = data race
  - actors approach (e.g., Erlang) - forbid aliasing
  - functional approach (e.g., Haskell) - forbid mutation
  - Rust's Concurrency Models: forbid both aliasing & mutation to happen at the same time
    - Messaging (ownership)
      - e.g., pointer in stack are passed between threads (data in heap are not passed around). no dangling pointer!
    - Shared read-only access (ownership, borrowing)
      - ARC (Atomic Reference Count)
      - ARC basically owns the data and anything else has to access the data thru ARC
      - Thread safety checking
        - `<T: Send>` - "sendable" types
        - `Arc<Vec<int>>: Send`
        - `Rc<Vec<int>>: ! Send`
    - Locked mutable access (ownership, borrowing)
      - `mutex: &Mutex<int>`
      - `let mut data = mutext.lock()`
        - `lock()` yields a mutable reference to data
        - lock is releasing when the destructor is run (no `unlock()`), no dead lock
      - not a direct ownership but destructor releases lock (like guard in C++)
  - Parallelism
  ```rs
  // sequential
  fn qsort(vec: &mut [int]) {
    if vec.len() <= 1 { return; }
    let pivot = vec[random(vec.len())];
    let mid = vec.partition(vec, pivot);
    let (less, greater) = vec.split_at_mut(mid);
    qsort(less);
    qsort(greater);
  }

  // parallel
  fn parallel_qsort(vec: &mut [int]) {
    if vec.len() <= 1 { return; }
    let pivot = vec[random(vec.len())];
    let mid = vec.partition(vec, pivot);
    let (less, greater) = vec.split_at_mut(mid);
    parallel::join(
      || parallel_qsort(less),
      || parallel_qsort(greater),
    );
  }
  ```
  - `split_at_mut` ([Splitting Borrows](https://doc.rust-lang.org/nomicon/borrow-splitting.html))
  > mutable slices expose a `split_at_mut` function that consumes the slice and returns two mutable slices. One for everything to the left of the index, and one for everything to the right. Intuitively we know this is safe because the slices don't overlap, and therefore alias. However the implementation requires some **unsafety**:

# Unsafe
```rs
fn something_safe(...) {
  // trust compiler
  ...
  unsafe {
    // trust developer
    ...
  }
}
```
  - Useful for:
    - Uninitializaed memory 
    - Interfacing with C code
    - Building parallel abstractions like ARC
  - Ownership/borrowing permit creating safe abstraction boundries


# Conclusions
- Rust combines **high-level features** with **low-level control**.
- Rust gives **strong safety guarantees** beyond what GC can offer:
  - Deterministic destruction
  - Data race freedom
  - Iterator invalidation