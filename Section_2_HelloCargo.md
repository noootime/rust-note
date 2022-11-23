# Section 2. Hello Cargo

## cargo常用命令

- `cargo new project_name`: 创建一个名为`project_name`的项目。
- `cargo build`: 编译项目，编译完成会生成`target/`目录，编译后产生的可执行文件在`target/debug`目录中。
- `cargo run`: 编译并执行，除了会编译项目，还会在编译完成后执行生成的可执行文件，若项目已编译且代码变动，则直接执行
- `cargo check`: 检查代码是否能够通过编译，不会生成任何可执行文件，在开发过程中很常用，因为它比`build`命令快的多
- `cargo build --release`: 发布项目，当需要正式发布项目的时候，使用这个命令。它会在编译时执行一些优化操作，使得程序运行更快，但是相比`build`，编译的时间会更长。它生成的二进制文件是在`target/release`目录中。

## cargo项目结构

通过`cargo new`命令创建项目后，项目结构如下：

```bash
guessing_game/
├── .git
├── .gitignore
├── Cargo.lock
├── Cargo.toml
└── src

2 directories, 3 files
```

- `hello_cargo`: 是项目的根路径
- `src`: Rust源代码放在这里
- `Cargo.toml`: Rust项目的描述、配置信息。含有该项目的名称、版本等，以及项目的依赖项
- `Cargo.lock`: 当首次执行`cargo build`时，会生成这个文件，它会将当前项目的所有依赖项版本锁定，当该项目重新编译时，只要有这个文件，编译就不会再去解析`Cargo.toml`文件
- `.git/.gitignore`: cargo会自动将当前项目初始化为git仓库
