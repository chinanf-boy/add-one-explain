# 加一

[![crate](https://img.shields.io/badge/crates.io-1.0-orange.svg)](https://crates.io/crates/add-one)
[![documentation](https://img.shields.io/badge/docs-1.0-blue.svg)](https://docs.rs/add-one)
![minimum rustc 1.26](https://img.shields.io/badge/rustc-%2B1.26-red.svg)
[![Travis status](https://travis-ci.org/o2sh/add-one.svg?branch=master)](https://travis-ci.org/o2sh/add-one)

> 返回 n + 1.

## 用法

将此添加到您的`Cargo.toml`:

```toml
[dependencies]
add-one = "1.0.0"
```

在你的根 crate，加入下面内容:

```rust
extern crate add_one;
```

## 例子

```rust
extern crate add_one;
use add_one::add_one;

use std::str;

fn main() {
    let mut bytes = Vec::new();

    match add_one("123".as_bytes(), &mut bytes) {
        Ok(()) => println!("{}", str::from_utf8(&bytes).unwrap()),
        Err(e) => {
            eprintln!("Error: {}", e);
        }
    }
}
```

或运行

```sh
$ cargo run 12
$ 13
```

## 谢谢

- [贡献者](https://github.com/o2sh/add-one/contributors)

## 执照

许可，位于

- MIT 许可证([LICENSE.md](LICENSE.md)要么<http://opensource.org/licenses/MIT>)

## 兼容性

该`add-one`箱子的测试，来自rustc 1.26 或更高.
