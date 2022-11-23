# Section 3. Guessing Game

```rust
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Welcome to Guessing Game!");
    let secret_num = rand::thread_rng().gen_range(1..101);

    loop {
        println!("Please guess a number: ");
        let mut guess_num = String::new();
        io::stdin().read_line(&mut guess_num).expect("Read line failed!");

        // shadow
        // let guess_num: u32 = guess_num.trim().parse().expect("Please type a number!");
        let guess_num: u32 = match guess_num.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("Your guess num: {}", guess_num);

        match guess_num.cmp(&secret_num) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            },
        }
    }
}

```

## 1. 外部crate的使用

程序依赖了随机数生成库`rand`

```toml
[dependencies]
rand = "0.8.0"
```

## 2. 如何接收标准输入

```rust
use std::io;

let mut guess_num = String::new();
io::stdin().read_line(&mut guess_num).expect("Read line failed!");
println!("Your guess num: {}", guess_num);
```

## 3. match表达式简介

match表达式的语法为:

```rust
match {Enum} {
    Enum1 => ...,
    Enum2 => ...,
    ...
}
```

比较两个数字

```rust
use std::cmp::Order;

let guess_num: u32 = ...;
let secret_num: u32 = ...;

match guess_num.cmp(&secret_num) {
    Ordering::Less => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal => println!("You win!"),
}
```

修改标准输入

```rust
let guess_num: u32 = match guess_num.trim().parse() {
    Ok(num) => num,
    Err(_) => println!("please type a number"),
};
```

## 4. 字符串转数字

```rust
let guess_num: u32 = guess_num.trim().parse().expect("Please type a number!");
```