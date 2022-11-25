# Section 7. Ownership - 所有权

所有权是rust最独特的特性，它让rust无需GC就可以保证内存安全

## 什么是所有权

rust的核心特性就是所有权

所有程序在运行时，都必须管理它们使用计算机内存的方式

- 有些语言有垃圾收集机制，在程序运行时，它们会不断寻找不再使用的内存（例如java）
- 在其它语言中，程序员必须显示地分配和释放内存（例如c）

rust采用了第三种方式：

- 内存是通过一个所有权系统来管理的，其中包含一组编译器在编译时检查的规则
- 当程序运行时，所有权特性不会减慢程序的运行速度

## Stack vs Heap

在像rust这样的系统级编程语言里，一个值是在stack上还是heap上对语言的行为和你为什么要做某些决定是有更大的影响的。

在你的代码运行时，stack和heap都是你的可用内存，但是它们的结构很不相同。

- **Stack**: 栈内存，按值的接收顺序进行存储，按相反的顺序将它们移除，即后进先出`LIFO`
    - 添加数据叫做压入栈，移除数据叫做弹出栈
    - stack的压入栈不叫分配
    - 所有存储在Stack上的数据必须拥有已知的固定大小
    - 因为指针是已知固定大小的，可以把指针存放在stack上，但是如果想要实际的数据，你必须使用指针来进行定位
    - 把数据压倒stack上要比在heap上快得多，因为操作系统不需要寻找用来存储新数据的空间，那个位置永远都在stack的顶端
- **Heap**: 堆内存，内存组织性差一些
    - 当你把数据放入heap时，会请求一定数量的空间
    - 操作系统在heap里找到一块足够大的空间，把它标记为在用，并返回一个指针，也就是这个空间的地址
    - 这个过程叫做在heap上进行分配，有时仅仅称为“分配”
    - 在heap上分配空间需要做更多的工作，操作系统首先需要寻找到一个足够大的空间来存放数据，然后要做好记录方便下次分配

### 数据访问

- 访问heap中的数据要比访问stack中的数据慢，因为需要通过指针才能找到heap中的数据，对于现代处理器来说，由于缓存的缘故，如果指令在内存中跳转的次数越少，那么速度就越快
- 如果数据存放的距离比较远，那么处理器的处理速度就会更快一些（stack上）
- 如果数据存放的距离比较近，那么处理器的处理速度就会慢一些（heap上）
    - 在heap上分配大量的空间也是需要时间的

### 函数调用
当你的代码在调用函数时，值被传入到函数（也包括指向heap的指针）。函数本地的变量被压倒stack上。当函数执行结束后，这些值会从stack上弹出

### 所有权存在的原因

所有权解决的问题：

- 跟踪代码的哪些部分正在使用heap的哪些数据
- 最小化heap上的重复数据
- 清理heap上未使用的数据以避免空间不足

一旦你懂了所有权，那么就不需要经常去想stack或heap了，但是知道管理heap数据是所有权存在的原因，有助于解释它为什么会这样工作。

## 所有权规则

1. 每个值都有一个变量，这个变量就是该值的所有者
2. 每个值同时只能有一个所有者
3. 当所有者超出作用域（scope）时，该值将被删除
    - 作用域是程序中一个项目的有效范围

```rust
fn main() {
    // s不可用
    let s = "hello";  // s可用
                      // 可以对s进行相关操作
}   // s作用域到此结束，s不再可用
```

### 根据String了解所有权

选择`String`类型的原因是，`String`比基础标量数据类型更复杂。

之前，我们使用的是字符串字面值的方式定义的字符串类型，它们是不可变的

rust中还有第二种字符串类型：`String`，它在heap上分配空间，能够存储在编译时未知数量的文本

#### 如何创建String类型的值

通过`from`函数从字符串字面值创建出`String`类型，语法如下：

- `let s = String::from("hello");`
    - `::`表示`from`是`String`类型下的函数

```rust
fn main() {
    let mut s = String::from("hello");
    s.push_str(", World");
    println!("{}", s);
}
```

通过上面的例子，我们可以发现`String`类型的值是可以修改的，但是之前使用过的字符串字面值却不能修改，这是因为它们处理内存的方式不同。

#### 内存和分配

- **字符串字面值**: 在编译时就知道它的内容了，其文本内容直接被硬编码到最终的可执行文件里
    - 速度快、高效是因为其不可变性
- **String类型**: 为了支持其可变性，需要在heap上分配内存来保存编译时未知的文本内容
    - 操作系统必须在运行时来请求内存，这一步通过调用`String::from`来实现
    - 当用完`String`后，需要使用某种方式将内存返还给操作系统
        - 有GC的语言中，GC会负责跟踪并清理不再使用的内存
        - 没有GC的语言中，需要我们去识别内存何时不再使用，并调用代码将其返回
            - 如果忘了，会浪费内存，即内存泄漏
            - 如果提前做了，会导致变量非法
            - 如果做了两次，也就是BUG，必须一次分配对应一次释放
        - **rust采用了不同的方式，对于某个值来说，当拥有它的变量走出作用范围时，内存会立即自动的返还给操作系统**
            - 当一个变量离开作用域时，rust会调用`drop`函数

### 变量和数据交互的方式：移动（Move）

多个变量可以与同一数据使用一种独特的方式来交互：

```rust
// 整数是已知且固定大小的简单值，这两个5被压到了stack中
let x = 5; 
let y = x;

// 接下来是string的版本
let s1 = String::from("hello");
let s2 = s1;
```

### 具体分析

#### String的基本内存结构

<img src="images/trpl04-01.svg" width=30% height=30%>

- 一个`String`由3部分组成：
    - 一个指向存放字符串内容的内存的指针
    - 一个长度
    - 一个容量
- 上面这些东西都存放在stack上
- 存放字符串内容的部分在heap上
- 长度`len`，就是存放字符串内容所需的字节数
- 容量`capacity`是指`String`从操作系统总共获得内存的总字节数

#### 假想的处理方式

<img src="images/trpl04-02.svg" width=30% height=30%>

当把`s1`赋给`s2`，`String`的数据被复制了一份，这会在`stack`上复制了一份指针、长度、容量完全一样的数据，但是它并没有复制指针所指向的heap上的数据。

当变量离开作用域时，rust会自动调用`drop`函数，并将变量使用的heap内存释放。

当`s1`, `s2`离开作用域时，它们都会尝试释放相同的内存，这就会引发**二次释放（double free）**的bug。

#### rust的处理方式

<img src="images/trpl04-04.svg" width=30% height=30%>

为了保证内存安全：

- rust不会尝试复制被分配的内存
- rust会让`s1`失效，当`s1`离开作用域时，`rust`不需要释放任何东西

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;
    println!("{}", s1);  // 这里会编译报错 -> borrow of moved value: `s1`
}
```

编程语言中都有*浅拷贝（shallow copy）*和*深拷贝（deep copy）*的概念，你也许会将复制指针、长度、容量视为浅拷贝，但由于rust让`s1`失效了，所以我们使用一个新的属于：**移动（Move）**。

隐含的一个设计原则是：rust不会自动创建数据的深拷贝。就运行时性能而言，任何自动复制的操作都是廉价的。

### 变量和数据交互的方式：克隆（Clone）

如果真的想对heap上的`String`数据进行深度拷贝，而不仅仅是stack上的数据，可以使用`clone`方法。

<img src="images/trpl04-03.svg" width=30% height=30%>

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone();
    println!("{}, {}", s1, s2);  // s1和s2都是有效的
}
```

### Stack上的数据：复制

下边的代码，可以发现执行了`let y = x;`后，`x`还是有效的，这是因为`x`是`i32`标量类型，这种类型在编译时已经确定了大小和值，直接存储在stack上，所以可以直接复制给变量`y`

```rust
fn main() {
    let x = 5;
    let y = x;
    println!("{}, {}", x, y);  // x, y都是5
}
```

`Copy`这个trait可以用于像整数这样完全存放在stack上的类型。

- 如果一个类型实现了`Copy`这个trait，那么旧的变量在赋值后仍然可以使用
- 如果一个类型或者该类型的一部分实现了`Drop`这个trait，那么rust不允许让它再去实现`Copy` trait了

一些拥有`Copy` trait的类型：

- 任何简单标量的组合类型都可以是`Copy`的
- 任何需要分配内存或某种资源的都不吃`Copy`的
- 一些拥有`Copy` trait的类型：
    - 所有整数类型，例如`u32`
    - `bool`类型
    - `char`类型
    - 所有浮点类型，例如`f64`
    - `Tuple` 元祖，如果其所有的字段都是`Copy`的，那么它就是`Copy`的
        - `(i32, i32)` -> 是
        - `(i32, String)` -> 不是

## 所有权与函数

在语义上，将值传递给函数和吧值赋给变量是类似的：将值传递给函数将发生**移动**或**复制**。

```rust
fn main() {
    let s = String::from("hello");
    take_ownership(s);  // s所有权被移动到makes_copy函数中
    let x = 5;
    makes_copy(x);  // x是i32类型，实现了Copy trait，实际传递的是复制，所以x还是有效的
    println!("x: {}", x);
}

fn take_owner_ship(some_string: String) {
    println!("{}", some_string);
}  // 函数结束后，some_number离开作用域，就失效了

fn makes_copy(some_number: i32) {
    println!("{}", some_number);
}

```

### 返回值与作用域

函数在返回值的过程中同样也会发生所有权的转移。

```rust
fn main() {
    let s1 = gives_ownership();
    let s2 = String::from("hello");
    let s3 = takes_and_gives_back(s2);
}

fn gives_ownership() {
    let some_string = String::from("hello");
    some_string
}

fn takes_and_gives_back(a_string: String) -> String {
    a_string
}
```

一个变量的所有权总是遵循相同的模式：
- 把一个值赋给其它变量时就会发生移动
- 当一个包含heap数据的变量离开作用域时，它的值就会被`drop`函数清理，除非数据的所有权移动到另一个变量上的

**如何让函数使用某个值，但是不获得其所有权？**

针对这个问题，下面是一个笨方法：

```rust
fn main() {
    let s1 = String::from("hello");
    let (s2, len) = calculate_length(s1);
    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len();
    (s, length)
}
```

实际上rust中有一个特性叫做**引用（reference）**，这会在下一节介绍。