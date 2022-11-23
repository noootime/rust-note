# Section 1. Hello World

Rust源文件后缀为`.rs`，通过执行`rustc hello.rs`进行编译

编译完成会生成可执行的二进制文件
- Windows下还会生成`.pdb`文件，它包含一些调试信息

示例代码: 

```rust
// hello.rs

fn main() {
    println!("Hello World!");
}
```

```bash
>> rustc hello.rs
>> ls
main  main.rs
>> ./main
Hello World!
```