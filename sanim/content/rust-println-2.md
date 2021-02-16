+++
title = "Rust กับ println! ตอนที่ ๒"
date = 2021-01-25

[taxonomies]
tags = ["rust", "println!"]
+++

โพสต์ที่แล้วเราใช้ `println!` ในการแสดงค่าตัวแปรตัวเดียวแต่หลายรูปแบบ แต่ถ้าเรามีตัวแปรหลายตัว `println!` ก็มีวิธีที่ช่วยเราจัดการตัวแปรเหล่านั้น

<!-- more -->
ก่อนที่จะไปดูวิธีเหล่านั้น เราเพิ่มความซับซ้อนให้ struct โดยการเพ่ิม `team` เพื่อบอกว่าคนนี้อยู่ทีมไหน 
```rs
#[derive(Debug)]
enum Team {
    Red,
    Green,
    Blue
}

impl fmt::Display for Team {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match *self {
            Team::Red => write!(f, "🔴"),
            Team::Green => write!(f, "🟢"),
            Team::Blue => write!(f, "🔵"),
        }
    }
}

#[derive(Debug)]
struct Person {
    name: &'static str,
    age: u8,
    team: Team
}

impl fmt::Display for Person {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "team: {}, name: {}, age: {}", self.team, self.name, self.age)
    }
}
```

เราสามารถจัดลำดับตัวแปรที่จะแสดงได้โดยการใส่ตัวเลขกำกับเช่น `{2}` ซึ่งจะทำให้สำดับตัวแปรต่างจากลำดับที่เราต้องการจะแสดงได้
```rs
println!("team: {2}, name: {0}, age: {1}", p.name, p.age, p.team);
// team: 🔴, name: Rustacean 🦀, age: 42 
```

ง่ายไปกว่านั้นเรายังสามารถใส่ชื่อกำกับเช่น `{team}` เพื่อให้ง่ายต่อการเช็คโค้ด
```rs
println!("team: {team}, name: {name}; {motto}",
        name = p.name,
        team = p.team,
        motto = "Come on You Reds!");
// team: 🔴, name: Rustacean 🦀; Come on you Reds!
```

และแน่นอนเราสามารถใช้วิธีข้างบนร่วมกับวิธีจากโพสต์ที่แล้วเช่นกัน
```rs
let p = Person { name: "Rustacean 🦀", age: 42, team: Team::Red };
let p2 = Person { name: "SeeSharp", age: 60, team: Team::Blue };

println!("{rust:#?} พบกับ {csharp:#?}", rust = p, csharp = p2);
```

ค่าที่แสดงออกมา
```
Person {
    name: "Rustacean 🦀",
    age: 42,
    team: Red,
} พบกับ Person {
    name: "SeeSharp",
    age: 60,
    team: Blue,
}
```

เรื่อง `println!` นี่ยังไม่จบ ติดตามต่ออีกทีโพสต์ต่อไปครับ