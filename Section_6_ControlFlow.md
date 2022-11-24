# Section 6. Control Flow

## if else

- `if`表达式允许根据条件来执行不同的代码分支
    - 这个条件必须是`bool`类型
- `if`表达式中，与条件相关联的代码块叫做分支（`arm`）
- 可选的，可以在后面加上一个`else`表达式
- 如果使用了多于一个`else if`，最好使用`match`来重构代码
- 因为`if`是一个表达式，所以可以将它放在`let`语句中等号的右边

```rust
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

```rust
let condition = true;
let number = if condition { 5 } else { 6 };
println!("The value of number is : {}", number);
```

## 循环

rust提供了3种循环：`loop`, `while`, `for`

### loop

`loop`关键字告诉rust反复执行一块代码，直到主动停止；可以使用`break`关键字停止循环

```rust
loop {
    println!("hello...");
}
```

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;
        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {}", result);
}
```

### while

另外一种常见模式是，每次循环之前都判断一次条件

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}! ", number);
        number -= 1;
    }

    println!("LIFIOFF!!!");
}
```

### for

