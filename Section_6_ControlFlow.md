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

- 可以使用`while`或`loop`循环来完成循环，但是它们容易出错
- 使用`for`循环更加简洁紧凑，它可以针对集合中的每一个元素来执行一些代码
- 由于`for`循环的安全、简洁性，它在rust中的使用最频繁

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    // 使用while的方式遍历
    let mut index = 0;
    while index < 5 {  // 一旦把5写成大于5的数字，就会报错
        println!("the value is {}", a[index]);
        index = index + 1;
    }

    // for循环改造，这个比较类似Java中的增强for循环，或是python中的 for ... in语法
    for element in a.iter() {
        println!("the value is {}", element);
    }
}
```

#### Range

- 由标准库提供
- 指定一个开始数字和一个结束数字，`Range`可以生成它们之间的数字（不含结束）
- `rev`方法可以反转`Range`

```rust
fn main() {
    for i in (1..4).rev() {
        println!("{}!", i);
    }
    println!("LIFIOFF!!!");
}
```
