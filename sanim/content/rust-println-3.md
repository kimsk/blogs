+++
title = "Rust กับ println! ตอนที่ ๓"
date = 2021-01-26

[taxonomies]
tags = ["rust", "println!"]
+++

สองโพสต์ที่ผ่านมาเราใช้ `println!` ในการแสดงค่าในตัวแปร ซึ่งมีประโยชน์ในทดลองหรือดีบักโค้ดของเรา `#` (pretty-printed) และช่วยทำให้ค่าที่แสดงดูอ่านง่ายขึ้น แต่เรายังสามารถปรับปรุงการแสดงผลได้อีก โพสต์นี้เราจะมาพูดถึงการใช้ padding กับ `println!` กันครับ

<!-- more -->
ก่อนที่จะมาเรียนรู้กัน มาดูตัวอย่างง่ายๆกันก่อน (ผมใช้ crate `rusty_money` เพื่อให้ง่ายกับการแสดงหน่วยเงิน แต่ไม่เกียวอะไรกับ `println!`)

เราสร้างฟังก์ชัน `print_stock` ขึ้นมาเพื่อให้แสดงตาราง bid/ask และ ticker ของหุ้น Amazon, Google, Facebook, และ GameStop
```rs
use rusty_money::{Money, iso};

fn print_stock(ticker: &str, bid: i64, ask: i64) {
    println!("{:=^50}", ticker);
    println!("|{: ^24}|{: ^23}|", "bid", "ask");
    println!("{:-<25}+{:->24}", "|", "|");

    let bid = Money::from_minor(bid, iso::USD);
    let ask = Money::from_minor(ask, iso::USD);
    println!("|{: ^24}|{: ^23}|", bid.to_string(), ask.to_string());
    println!("{:->25}+{:-<24}", "", "");
}

let stocks = vec![
    ("AMZN", 3_290_15, 3_294_95),
    ("GOOGL", 1885_00, 1902_09),
    ("FB", 275_70, 276_20),
    ("GME", 312_00, 315_00)
];

for (ticker, bid, ask) in stocks {
    print_stock(ticker, bid, ask);
}
```

มาดูการแสดงค่าที่สวยงามจากฟังก์ชัน `print_stock`
```
=======================AMZN=======================
|          bid           |          ask          |
|------------------------+-----------------------|
|       $3,290.15        |       $3,294.95       |
-------------------------+------------------------
======================GOOGL=======================
|          bid           |          ask          |
|------------------------+-----------------------|
|       $1,885.00        |       $1,902.09       |
-------------------------+------------------------
========================FB========================
|          bid           |          ask          |
|------------------------+-----------------------|
|        $275.70         |        $276.20        |
-------------------------+------------------------
=======================GME========================
|          bid           |          ask          |
|------------------------+-----------------------|
|        $312.00         |        $315.00        |
-------------------------+------------------------

```

คงจะสังเกตว่าข้างใน `println!` จะมีตัวอักษรแปลกๆเช่น `^`, `>`, และ `<` ที่เราไม่เคยเห็นแถมยังมีตัวเลขอีก เราลองมาดูว่ามันทำงานอย่างไรกัน

```rs
println!("{:=^50}", ticker);
//=======================AMZN=======================
println!("|{: ^24}|{: ^23}|", "bid", "ask");
//|          bid           |          ask          |
println!("{:-<25}+{:->24}", "|", "|");
//|------------------------+-----------------------|
println!("|{: ^24}|{: ^23}|", bid.to_string(), ask.to_string());
//|       $3,290.15        |       $3,294.95       |
println!("{:->25}+{:-<24}", "", "");
//-------------------------+------------------------
```
สำหรับบรรทัดแรก เราพอเดาได้ว่าโค้ดข้างบนจะต้องแสดงค่า `ticker` แต่ที่พิเศษไปกว่านั้นคือเราต้องการให้บรรทัดนี้มีขนาดกว้าง 50 โดยที่ `ticker` ต้องอยู่ตรงกลาง (กำหนดโดย `^`) และให้เติมพื้นที่ว่างด้านซ้ายและขวาด้วย `=`

บรรทัดที่สองคล้ายๆกับบรรทัดแรกแต่เราแบ่งบรรทัดนี้เป็นสองส่วน `bid` และ `ask`

บรรทัดที่สามเราใช้ `<` ใน `{:-<25}` ใช้สำหรับวาง `|` ตรงตำแหน่งซ้ายสุด ตามด้วย `-` ให้ทั้งคู่มีขนาดรวมกัน 25 ตัวอักษร และด้านขวาตามลำดับ ส่วน `>` ใช้เพื่อวาง `|` ตรงตำแหน่งขวาสุดนำด้วย `-`

บรรทัดที่สี่และห้าจะคล้ายกับบรรทัดที่สองและสามตามลำดับ

ตารางหุ้นเราดูเป็นรูปเป็นร่างขึ้นมา แต่ผมแก้โค้ดเพิ่มอีกหน่อยเพื่อให้เราสามารถกำหนดขนาดของตารางได้ ใช้ [extended ASCII codes](https://www.webopedia.com/definitions/extended-ascii/) เช่น `┌`เพื่อให้ตารางดูสวยขึ้นมาอีกหน่อย แล้วยังใช้ crate `colored` เพื่อแสดงสีที่ต้องการ

```rs
use colored::*;

fn print_stock_pretty_with_table_width(
        table_width: usize,
        ticker: &str,
        bid: i64, ask: i64) {

    let ticker_width = ticker.len();
    let left_width = (table_width - ticker_width)/2;
    let right_width = if ticker_width %2 == 0 {
        (table_width - ticker_width)/2
    } else {
        (table_width - ticker_width)/2 + 1 
    };
    let bid = Money::from_minor(bid, iso::USD).to_string();
    let ask = Money::from_minor(ask, iso::USD).to_string();

    println!("{:>left_width$}{:─^ticker_width$}{:<right_width$}", "┌", "", "┐",
        left_width=left_width, right_width=right_width,
        ticker_width=ticker_width);
    println!("┌{:─>left_width$}┤{}├{:─>right_width$}┐",
        "", ticker.blue(), "",
        left_width=left_width-2, right_width=right_width-2);
    println!("│{: ^left_width$}└{:─^ticker_width$}┘{: ^right_width$}│",
        "bid".green(), "┬", "ask".red(),
        left_width=left_width-2, right_width=right_width-2,
        ticker_width=ticker_width);
    println!("├{:─^left_width$}{:─^ticker_width$}{:─^right_width$}┤",
        "", "┼", "",
        left_width=left_width-1, right_width=right_width-1,
        ticker_width=ticker_width);
    println!("│{: ^left_width$}{: ^ticker_width$}{: ^right_width$}│",
        bid, "│", ask,
        left_width=left_width-1, right_width=right_width-1,
        ticker_width=ticker_width);
    println!("└{:─^left_width$}{:─^ticker_width$}{:─^right_width$}┘",
        "", "┴", "",
        left_width=left_width-1, right_width=right_width-1,
        ticker_width=ticker_width);
}

...

fn main() {
    let print_stock_pretty_40 = |ticker, bid, ask| {
        print_stock_pretty_with_table_width(40, ticker, bid, ask);
    };
    let print_stock_pretty_80 = |ticker, bid, ask| {
        print_stock_pretty_with_table_width(80, ticker, bid, ask);
    };

    let stocks = vec![
        ("AMZN", 3_290_15, 3_294_95),
        ("GOOGL", 1885_00, 1902_09),
        ("FB", 275_70, 276_20),
        ("GME", 312_00, 315_00),
    ];

    for (ticker, bid, ask) in stocks {
        print_stock(ticker, bid, ask);
        print_stock_pretty_40(ticker, bid, ask);
        print_stock_pretty_80(ticker, bid, ask);
    }
}
```
จบโพสต์นี้โดยการเปรียบเทียบตารางว่าแตกต่างกันอย่างไร เรื่อง `println!` ยังไม่จบมาติดตามโพสต์หน้าครับ

{{ image(
    src="/karlkim-println-3-1.png",
    alt="Stonks",
    position="left",
    style="border-radius: 8px;"
    ) }}
