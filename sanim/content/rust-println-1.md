+++
title = "Rust กับ println! ตอนที่ ๑"
date = 2021-01-24

[taxonomies]
tags = ["rust", "println!"]
+++

`println!` เป็นมาโคร ที่ทุกคนที่เขียน Rust รู้จักและต้องใช้ในการแสดงค่าออกหน้าจอ โพสต์นี้ผมจะมาพูดถึงการใช้ `println!` ในรูปแบบต่างๆครับ

<!-- more -->

แบบง่ายๆที่สุดก็คือการแสดง literals ต่างๆ `&str` หรือ `i32`

```rs
println!("สวัสดี"); // สวัสดี
println!(1); // 1
```

แต่เราสามารถใช้ format string และ `{}` เพื่อแสดงค่าในตัวแปร
```rs
let i = 1;
println!("{} is {}", 'i', i);// i is 1
```

แต่ไม่ใช่ตัวแปรทุกตัวจะสามารถใช้กับ `println!` และ `{}` ได้ ต้องเป็นตัวแปรประเภทที่สนับสนุน `Display` trait เท่านั้น ตัวอย่างเช่นถ้าเราสร้าง struct ขึ้นมาใหม่ เราต้องเขียนโค้ด `std::fmt::Display` ด้วย

```rs
use std::fmt;
struct Person {
    name: &'static str,
    age: u8,
}
impl fmt::Display for Person {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "name: {}, age: {}", self.name, self.age)
    }
}

let p = Person { name: "Rustacean 🦀", age: 42 };
println!("{}", p);
// name: Rustacean 🦀, age: 42
```

แต่ถ้าเราไม่อยากเขียนโค้ด `std::fmt::Display` วิธีง่ายๆก็คือการใช้ `{:?}` แต่ struct แต่เราต้องใส่ `#[derive(Debug)]` กำกับ เพื่อสนับสนุน `std::fmt::Debug` เท่านี้เราก็จะสามารถแสดงค่าใน struct เราได้

```rs
#[derive(Debug)]
struct Person {
    name: &'static str,
    age: u8,
}

let p = Person { name: "Rustacean 🦀", age: 42 };

println!("{:?}", p);
// Person { name: "Rustacean 🦀", age: 42 }
```

ถ้าอยากให้ค่า struct เราถูกจัดเรียงให้อ่านง่าย เราสามารถใช้ `{:#?}`

```rs
println!("{:#?}", p);
```
ค่าที่แสดงออกมาจะถูกจัดให้อ่านง่ายขึ้น
```
Person {
    name: "Rustacean 🦀",
    age: 42,
}
```

ถ้าเริ่มอยากรู้เกี่ยวกับตัวแปรเรามากขึ้นเช่นถ้าอยากรู้ว่า memory address ของ reference เราเป็นเท่าไหร่ เราก็สามารถใช้ `{:p}` หรืออยากแสดงค่าเป็นเลขที่ไม่ใช่ฐานสิบ เราก็ทำได้ตามตัวอย่างข้างล่าง
```rs
println!("{:p}", &p); // 0x7ffeef75fc98
println!("Binary: {:b}, hexadecimal: {:x}, octal: {:o}", p.age, p.age, p.age);
// Binary: 101010, hexadecimal: 2a, octal: 52
println!("{:X}", 'ก' as u32); // E01
```
หรือพร้อมๆกับแสดงค่าใน struct
```rs
println!("{:#x?}", p);
```
เท่านี้เราก็สามารถแสดงอายุเป็นเลขฐานสิบหกได้
```
Person {
    name: "Rustacean 🦀",
    age: 0x2a,
}
```

โพสต์นี้เอาแค่นี้ก่อน เรื่อง `println!` นี่ยังเขียนได้อีกครับ ติดตามต่อโพสต์หน้า