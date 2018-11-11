# o2sh/add-one [![explain]][source] [![translate-svg]][translate-list]

<!-- [![size-img]][size] -->

[explain]: http://llever.com/explain.svg
[source]: https://github.com/chinanf-boy/Source-Explain
[translate-svg]: http://llever.com/translate.svg
[translate-list]: https://github.com/chinanf-boy/chinese-translate-list
[size-img]: https://packagephobia.now.sh/badge?p=Name
[size]: https://packagephobia.now.sh/result?p=Name

「 rust , 加一~~库~~箱  」

---

## explain ✅

<!-- doc-templite START generated -->
<!-- time = '2018-10-06' -->
<!-- name = 'o2sh' -->
<!-- repo = 'add-one' -->
<!-- commit = '31b1f696840c5e32e2a7c9d85c56ffead9c418bc' -->
版本 | 与日期 | 最新更新 | 更多
---|---|---|---
[commit] | ⏰ 2018-10-06 | ![version] | [源码解释][source]

[commit]: https://github.com/o2sh/add-one/tree/31b1f696840c5e32e2a7c9d85c56ffead9c418bc
[version]: https://img.shields.io/github/last-commit/o2sh/add-one.svg

<!-- doc-templite END generated -->

### 中文 Readme

[中文](zh.md)

## 生活

[help me live , live need money 💰](https://github.com/chinanf-boy/live-need-money)

---

本库，是一个很好的入门crate制造例子

## 目录

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
<!-- END doctoc  -->

- [Cargo.toml](#cargotoml)
- [src/main.rs](#srcmainrs)
- [src/lib.rs](#srclibrs)
  - [docs.rs](#docsrs)
  - [正文](#%E6%AD%A3%E6%96%87)
  - [test](#test)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

### Cargo.toml

``` toml
[package]
name = "add-one"
description = "Adds one to a number"
version = "1.0.0"
authors = ["Ossama Hjaji"]
readme = "README.md"
license = "MIT"
repository = "https://github.com/o2sh/add-one"
```

### src/main.rs

> 默认的主文件，同时也是命令行文件的主入口, 请看[使用示例](zh.md)

- [extern crate](https://rustwiki.org/zh-CN/rust-by-example/crates/link.html) 链接一个 crate 到这个新库，必须使用 extern crate 声明。

- [use **](https://rustwiki.org/zh-CN/rust-by-example/mod/use.html) use 声明可以将一个完整的路径绑定到一个新的名字，从而更容易访问。

```rs
extern crate add_one; // 声明 crate, 类似导入

use add_one::add_one;
use std::env; // 改 std::env => env
use std::io::stdout; // I/O 输出
use std::process::exit; // 退出代码

fn main() {
    // 使用 改变名称后的 env
    let mut args = env::args(); // 获取命令行参数，返回的是一个迭代器

    // 推进迭代器，并返回 next-下一个 值, 不过记得返回的是一个 Option<String> 类型
    // Option<String>，具有两种值，Some-代表有东西；None-代表没有
    if args.next().is_none() { // 而这里，正是检测参数None的情况，因为我们需要有一个命令行参数，不然
        //文本打印到标准错误(io::stderr)
        eprintln!("Too few arguments. Expected program name and a single argument.");
        return;
    };

    // `if let` 结构解读：若 `let` 将 `args.next` 解构成 `Some(arg)`，则执行
    let input_str = if let Some(arg) = args.next() { // 只有
        // 且，一个函数中没有分号那行代码，是省略了return的语法糖，
        // 也就是 let input_str = arg
        arg
    } else {
        eprintln!("Too few arguments. Expected a single argument.");
        return;
    };

    // 这里，则是检测参数Some的情况
    if args.next().is_some() { // 因为仅仅 只需要 一个 命令行参数，不能有两个参数，不然
        //文本打印到标准错误(io::stderr)
        eprintln!("Too many arguments. Expected a single argument.");
        return;
    };

    // match 一般与 Result<T, E> 和 Option<T> 搭配使用
    // 这里就是调用 add_one(crate函数)，且匹配-match它的返回
    match add_one(input_str.as_bytes(), &mut stdout()) { // 注意⚠️这个 stdout 为 &mut可变指针 {&:指针;;mut:可变}
        Ok(()) => println!(), // 若是Ok
        Err(e) => { // 若是Err
            eprintln!("Error: {}", e);
            exit(1); // 错误退出代码
        }
    }
}
```

### src/lib.rs

#### docs.rs

一开始，这一长串的注释，一般会出现在[docs.rs 箱子文档室的对应项目索引页](https://docs.rs/add-one/1.0.0/add_one/)

> 基本使用md语法

``` rs
//! This add-one crate is meant to give you the opportunity to add one to a number.
//! 
//! # Examples
//! 
//! ```
//!extern crate add_one;
//!use add_one::add_one;
//!
//!use std::str;
//!
//!fn main() {
//!    let mut bytes = Vec::new();
//!
//!    match add_one("123".as_bytes(), &mut bytes) {
//!        Ok(()) => println!("{}", str::from_utf8(&bytes).unwrap()),
//!        Err(e) => {
//!            eprintln!("Error: {}", e);
//!        }
//!    }
//!}
//! ```
//!
//! ## Compatibility
//!
//! The `add-one` crate is tested for rustc 1.26 and greater.

```

#### 正文

``` rs
use std::io; // 使用内置库

// 1. pub, 说明它是公开的，(不像Go那样的，大写就行)
// 2. <T: io::Write> 泛型类型，指出第二个参数 &mut T，只要有 io::Write (Trait-特性)，
// 规范方法集合的另一种(英文)说法，
// 3. 注意 两个参数都是 &类型，这样就借用了，不会释放原数据 
// (若是移动所有权，一出这个函数范围，那就啥都没有了，全被释放掉了)
pub fn add_one<T: io::Write>(digits: &[u8], output: &mut T) -> Result<(), io::Error> {
    // 解析 负数的前缀 '-' 
    let (minus, digits) = match digits.split_first() {
// 单字节字符b'*'，仅限制于ASCII字符。 此外，
// 还可以使用r#"..."#标记来表示原始字符串，不需要对特殊字符进行转义。
        Some((&b'-', digits)) => (true, digits), // Negative number
        _ => (false, digits),
    };

    // 验证 (ASCII) 数字.
    // 为空，每一位数都大于等于0，小于等于9，或是'.'小数点号，否则错误
    if digits.is_empty() || !digits.iter().all(|&c| c >= b'0' && c <= b'9' || c == b'.') {
        return Err(io::Error::new(
            io::ErrorKind::InvalidInput,
            "Invalid characters in input".to_string(),
        ));
    }

    // 移除，领头的零
    // 获取不为0的位置，若不正确，unwrap_or控制拿到 整长度digits.len()
    let digits = &digits[digits.iter().position(|&c| c != b'0').unwrap_or(digits.len())..];

    //如上，问：为什么是|&c| 。答：这是&,作为借用，不会释放原内容
    let z = digits.iter().position(|&c| c == b'.').unwrap_or(digits.len()); // 小数点的位置

    // 查找连续的 9's (当为正数) 或 0's (当为 负数)， which will carry the carry bit.
    let (prefix, trailing) = digits[..z].split_at(
        digits[..z].iter()
            .rposition(|&c| c != if minus { b'0' } else { b'9' }) // 从个位开始，往百位数过去
            .map_or(0, |x| x + 1) // 最后一个 非-9/0 数 *之后* 的位置.
            // None - 0, Some - 运行 |x| x + 1
    );

    // 返回的 &digit 才是最后一位
    if let Some((&digit, prefix)) = prefix.split_last() {
        // prefix的最后一位必须递增/递减，
        // 以及其他都保持不变。
        let new_digit = if minus {
            if prefix.is_empty() && trailing.is_empty() && digit == b'1' {
                // -1为特例：结果不再是负数，为0。
                return output.write_all(b"0");    //写入这个output 可变泛型指针
            }
            output.write_all(b"-")?;// 负数写会负符号
            digit - 1
        } else {
            digit + 1
        };
        //  
        output.write_all(prefix)?; // ? 运算符用在返回值为 Result 的表式式后面
        // 再写入更改的数字，除非它成为领先的零。
        if !prefix.is_empty() || new_digit != b'0' {
            output.write_all(&[new_digit])?;
        }
    } else if minus && digits.len() > 0 && digits[0] == b'.' && !digits[1..].iter().all(|&d| d == b'0') {
        output.write_all(b"0")?;
    } else {
        output.write_all(b"1")?;
    }
    for _ in 0..trailing.len() {
        output.write_all(if minus { b"9" } else { b"0" })?;
    }
    if minus && z == 0 && digits.len() > 1 { //即是负数,
        // 应用于，例子 "-0.76987965465" => "0.23012034535"
        // 写小数
        output.write_all(&digits[0..1])?;
        let mut iter = digits[1..].iter().rev().peekable(); // 反转迭代器，并 开启瞧一瞧 peek
        let mut decimal_digits = Vec::new();
        while let Some(&b'0') = iter.peek() { // peek 可以查看下一个是否为0值，但不推进迭代器
            decimal_digits.push(*iter.next().unwrap()); // 是，就加上
        }
        if let Some(_) = iter.peek() { // 一次
            // 按例子来看，上面被反转，先是5
            decimal_digits.push(b'9' - *iter.next().unwrap() + b'0' + 1); 
            // 9-`5`+0+1 = 5
        }
        while let Some(_) = iter.peek() { // 多次
            //再然后是 6，4 ...
            decimal_digits.push(b'9' - *iter.next().unwrap() + b'0');
            // 9-`6`+0 = 3
            // 9-`4`+0 + 5
            // ...
        }
        // 在把结果数组 反转，复制，合成u8数组，再变slice，写到 output
        output.write_all(decimal_digits.iter().rev().cloned().collect::<Vec<u8>>().as_slice())?;
    } else {
        output.write_all(&digits[z..])?;// 打印十进制后的字符
    }
    Ok(())
}
```

- `?运算符`-[rust-by-example 中文](https://rustwiki.org/zh-CN/rust-by-example/std/result/question_mark.html)

- [**.unwrap() 使用 `unwrap`，当接收到 `None` 时返回一个 `panic`。](https://rustwiki.org/zh-CN/rust-by-example/error/option_unwrap.html)

#### test

- 测试代码，可通过

```
cargo test
``` 

测试 具有`#[test]`标志的函数

```rs
#[test]
fn add_one_test_integer() { // 测试整数
    fn test(num: &str, result: &str) {
        use std::str::from_utf8; //
        let mut s = Vec::new(); // 可变数组
        add_one(num.as_bytes(), &mut s).unwrap();
        assert_eq!(from_utf8(&s).unwrap(), result);
    }
    test("1234", "1235");
    test("0", "1");
    test("9", "10");
    test("19", "20");
    test("99", "100");
    test("99988999", "99989000");
    test("99999999", "100000000");
    test("0001234", "1235");
    test("-9", "-8");
    test("-10", "-9");
    test("-100", "-99");
    test("-0100", "-99");
    test("-1100", "-1099");
    test("-01100", "-1099");
    test("-1", "0");
    test("-001", "0");
    test("-0", "1");
    test("-000", "1");
    test(
        "1256146513513224524524524524522452165841613615616516516",
        "1256146513513224524524524524522452165841613615616516517",
    );
    test(
        "1237801293471034709342345050491203491230949139249123949999999",
        "1237801293471034709342345050491203491230949139249123950000000",
    );
    test(
        "-1237801293471034709342345050491203491230949139249123940000000",
        "-1237801293471034709342345050491203491230949139249123939999999",
    );
    test("-2", "-1");
}

#[test]
fn add_one_test_float() { // 测试浮点数
    fn test(num: &str, result: &str) {
        use std::str::from_utf8;
        let mut s = Vec::new();
        add_one(num.as_bytes(), &mut s).unwrap();
        assert_eq!(from_utf8(&s).unwrap(), result);
    }
    test("0.0", "1.0");
    test("5000.0", "5001.0");
    test("1139.67", "1140.67");
    test("123.321", "124.321");
    test("99.99", "100.99");
    test("-1.0", "0");
    test("2.0", "3.0");
    test("000.000", "1.000");
    test("-000.00", "1.00");
    test("-0.9", "0.1");
    test("-0.76987965465", "0.23012034535");
    test("0123456789.0987654321", "123456790.0987654321");
}
```