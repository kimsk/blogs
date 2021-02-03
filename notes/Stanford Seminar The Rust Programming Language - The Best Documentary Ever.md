# [Stanford Seminar The Rust Programming Language - March 2015 - Aaron Turon](https://www.youtube.com/watch?v=SZvs15hC81U)


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
- Rust's solution
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
- Borrowing ([code](https://github.com/kimsk/try-rust/blob/master/karlkim/ownership/src/main.rs))
  - Borrowing (`T`): transfer ownership
  - Shared Borrowing (`&T`)
    - shared references are immutable
    - allow aliasing but disallow mutation
  - Mutable Borrowing (`&mut T`)
    - disallow aliasing but allow mutation
    - cannot borrow both shared and mutable references at the same time
  - &: no writes, no move
  - &mut: no access at all
