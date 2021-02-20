+++
title = "Rust กับ println! ตอนที่ ๔"
date = 2021-02-19

[taxonomies]
tags = ["rust", "println!"]
+++

`println!` เป็นมาโครในกลุุ่มที่เรียกว่า [Formatted print](https://doc.rust-lang.org/rust-by-example/hello/print.html) ที่ยังมีมาโครตัวอื่นๆที่ทำงานคล้ายๆกับเช่น `format!` หรือ `print!` ถ้าอยากเรียนรู้เกี่ยวกับมาโครตัวอื่นๆหรือ **format string** เพิ่มเติมสามารถเข้าไปดูได้ที่ [std::fmt](https://doc.rust-lang.org/std/fmt/)

แต่โพสต์นี้เราจะมาลองดูกันเล่นๆว่า **format string** ที่เราใช้มานั้นแล้วเช่น `{:=^50}` หรือ `{:-<25}` มันถูกขยายยังไงก่อนถูกคอมไพล์โดยใช้ `-Z unstable-options --pretty expanded`
<!-- more -->

`-Z` นั้นใช้ได้กับ **nighlty version** ของ `rustc` เท่านั้น เพราะฉะนั้นเราต้องเปลี่ยนเวอร์ชั่นคอมไพเลอร์ของเราก่อนโดยใช้ `rustup default nightly`

{{ image(
    src="/karlkim-println-4-1.png",
    alt="Stonks",
    position="left",
    style="border-radius: 8px;"
    ) }}

> ผมใช้ `prompt` [starship](https://starship.rs/) เลยสามารถเห็นเวอร์ชั่นของ `rustc` และข้อมูลอย่างอื่นเช่น `git status` 

ข้างล่างเป็นโค้ดที่เราจะดูมาโครว่าจะถูกขยายอย่างไร
```rs
fn main() {
    print!("สวัสดี");
    let hello = format!("สวัสดี {}", "เจ้า");
    println!("{}", hello);
    
    println!("{:=^50}", "AMZN");
    println!("{:-<25}+{:->24}", "|", "|");

    #[derive(Debug)]
    struct Person {
        name: &'static str,
        age: u8,
    }

    let p = Person { name: "Rustacean 🦀", age: 42 };

    println!("{:?}", p);
    println!("{:#?}", p);
}
```
หลังจากนั้นใช้คำสั่ง `rustc src/main.rs -Z unstable-options --pretty expanded` หรือ `cargo rustc -- -Z unstable-options --pretty=expanded`

ผลที่ได้มาอาจจะดูยากเพราะฉะนั้นเราสามารถส่งต่อผลลัพธ์จากคำสั่งข้างบนไปที่ `rustfmt` ดังนี้ 

`cargo rustc -- -Z unstable-options --pretty=expanded | rustfmt`

แล้วเราก็จะได้โค้ดข้างล่าง
```rs
#![feature(prelude_import)]
#[prelude_import]
use std::prelude::v1::*;
#[macro_use]
extern crate std;
fn main() {
    ::std::io::_print(::core::fmt::Arguments::new_v1(
        &["\u{e2a}\u{e27}\u{e31}\u{e2a}\u{e14}\u{e35}"],
        &match () {
            () => [],
        },
    ));
    let hello = {
        let res = ::alloc::fmt::format(::core::fmt::Arguments::new_v1(
            &["\u{e2a}\u{e27}\u{e31}\u{e2a}\u{e14}\u{e35} "],
            &match (&"เจ้า",) {
                (arg0,) => [::core::fmt::ArgumentV1::new(
                    arg0,
                    ::core::fmt::Display::fmt,
                )],
            },
        ));
        res
    };
    {
        ::std::io::_print(::core::fmt::Arguments::new_v1(
            &["", "\n"],
            &match (&hello,) {
                (arg0,) => [::core::fmt::ArgumentV1::new(
                    arg0,
                    ::core::fmt::Display::fmt,
                )],
            },
        ));
    };
    {
        ::std::io::_print(::core::fmt::Arguments::new_v1_formatted(
            &["", "\n"],
            &match (&"AMZN",) {
                (arg0,) => [::core::fmt::ArgumentV1::new(
                    arg0,
                    ::core::fmt::Display::fmt,
                )],
            },
            &[::core::fmt::rt::v1::Argument {
                position: 0usize,
                format: ::core::fmt::rt::v1::FormatSpec {
                    fill: '=',
                    align: ::core::fmt::rt::v1::Alignment::Center,
                    flags: 0u32,
                    precision: ::core::fmt::rt::v1::Count::Implied,
                    width: ::core::fmt::rt::v1::Count::Is(50usize),
                },
            }],
        ));
    };
    {
        ::std::io::_print(::core::fmt::Arguments::new_v1_formatted(
            &["", "+", "\n"],
            &match (&"|", &"|") {
                (arg0, arg1) => [
                    ::core::fmt::ArgumentV1::new(arg0, ::core::fmt::Display::fmt),
                    ::core::fmt::ArgumentV1::new(arg1, ::core::fmt::Display::fmt),
                ],
            },
            &[
                ::core::fmt::rt::v1::Argument {
                    position: 0usize,
                    format: ::core::fmt::rt::v1::FormatSpec {
                        fill: '-',
                        align: ::core::fmt::rt::v1::Alignment::Left,
                        flags: 0u32,
                        precision: ::core::fmt::rt::v1::Count::Implied,
                        width: ::core::fmt::rt::v1::Count::Is(25usize),
                    },
                },
                ::core::fmt::rt::v1::Argument {
                    position: 1usize,
                    format: ::core::fmt::rt::v1::FormatSpec {
                        fill: '-',
                        align: ::core::fmt::rt::v1::Alignment::Right,
                        flags: 0u32,
                        precision: ::core::fmt::rt::v1::Count::Implied,
                        width: ::core::fmt::rt::v1::Count::Is(24usize),
                    },
                },
            ],
        ));
    };
    struct Person {
        name: &'static str,
        age: u8,
    }
    #[automatically_derived]
    #[allow(unused_qualifications)]
    impl ::core::fmt::Debug for Person {
        fn fmt(&self, f: &mut ::core::fmt::Formatter) -> ::core::fmt::Result {
            match *self {
                Person {
                    name: ref __self_0_0,
                    age: ref __self_0_1,
                } => {
                    let debug_trait_builder =
                        &mut ::core::fmt::Formatter::debug_struct(f, "Person");
                    let _ = ::core::fmt::DebugStruct::field(
                        debug_trait_builder,
                        "name",
                        &&(*__self_0_0),
                    );
                    let _ = ::core::fmt::DebugStruct::field(
                        debug_trait_builder,
                        "age",
                        &&(*__self_0_1),
                    );
                    ::core::fmt::DebugStruct::finish(debug_trait_builder)
                }
            }
        }
    }
    let p = Person {
        name: "Rustacean 🦀",
        age: 42,
    };
    {
        ::std::io::_print(::core::fmt::Arguments::new_v1(
            &["", "\n"],
            &match (&p,) {
                (arg0,) => [::core::fmt::ArgumentV1::new(arg0, ::core::fmt::Debug::fmt)],
            },
        ));
    };
    {
        ::std::io::_print(::core::fmt::Arguments::new_v1_formatted(
            &["", "\n"],
            &match (&p,) {
                (arg0,) => [::core::fmt::ArgumentV1::new(arg0, ::core::fmt::Debug::fmt)],
            },
            &[::core::fmt::rt::v1::Argument {
                position: 0usize,
                format: ::core::fmt::rt::v1::FormatSpec {
                    fill: ' ',
                    align: ::core::fmt::rt::v1::Alignment::Unknown,
                    flags: 4u32,
                    precision: ::core::fmt::rt::v1::Count::Implied,
                    width: ::core::fmt::rt::v1::Count::Implied,
                },
            }],
        ));
    };
}
```

โค้ดที่ถูกขยายออกมาค่อนข้างยาว แต่มีจุดที่น่าสนใจหลายจุดเช่นการที่คำว่า `สวัสดี` ใน **format string** ถูกแสดงออกมาเป็น unicode `\u{e2a}\u{e27}\u{e31}\u{e2a}\u{e14}\u{e35}` ซึ่ง `\u{e2a}` ก็คือ  [ส](https://graphemica.com/%E0%B8%AA)  นั่นเอง

`println!` ก็คือ `print!` ที่มี `&["", "\n"]` ในขณะที่ `{:?}`, `{:#?}`, หรือ `{:=^50}` เมื่อถูกขยายออกมาก็จะเห็น argument ต่างๆที่พอจะเอาได้ว่าทำอะไรเช่น `::core::fmt::Debug::fmt`, `fill: '='` หรือ `align: ::core::fmt::rt::v1::Alignment::Center`

เสร็จแล้วก็อย่าลืมเช็ต `rustc` ของเรากลับไปเป็นเวอร์ชั่น stable 
`rustup default stable`

> จริงๆแล้วเรามีวิธีในการใช้ `-Z unstable-options` โดยที่ไม่ต้องเปลี่ยนเวอร์ชั่น rustc ของเราได้โดยการใช้คำสั่งที่ยาวหน่อยดังนี้ `rustup run nightly cargo rustc --profile=check -- -Z unstable-options --pretty expanded` แต่ที่ง่ายที่สุดก็คือการใช้ [cargo-expand](https://github.com/dtolnay/cargo-expand) ซึ่งจะทำให้เราแค่่ใช้ค่ำสั่ง `cargo expand` ก็จะเหมือนการใช้คำสั่งยาวๆข้างบน