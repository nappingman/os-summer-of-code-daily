# rust 入门

主要参考资料：[Rust 程序设计语言](https://kaisery.github.io/trpl-zh-cn/)第一章、第二章

## 在linux上面安装Rust

环境：

uname -a

> Linux ubuntu 5.4.0-39-generic #43-Ubuntu SMP Fri Jun 19 10:28:31 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

运行：

curl https://sh.rustup.rs -sSf | sh

> Welcome to Rust!
> Rust is installed now. Great!

重启；

rustc --version

>rustc 1.44.1 (c7087fe00 2020-06-17)

很新鲜的样子

查看文档：rustup doc 

装了个插件：Rust support for Visual Studio Code

然后跳了个框，说有些组件还没装...

## Hello, World!

$ mkdir hello_world
$ cd hello_world
$ cd hello_world

输入：

```rs
fn main() {
    println!("Hello, world!");
}
```
```
yunwei@ubuntu:~/hello_world$ rustc main.rs
yunwei@ubuntu:~/hello_world$ ./main
Hello, world!
```

- main 函数是一个特殊的函数：在可执行的 Rust 程序中，它总是最先运行的代码。它没有参数也没有返回值。
- rustfmt 的自动格式化工具正在开发中？我等等去看看
- Rust 要求所有函数体都要用花括号包裹起来。
- println! 调用了一个 Rust 宏（macro）。
- 以分号结尾（;），这代表一个表达式的结束和下一个表达式的开始。大部分 Rust 代码行以分号结尾（看起来不是全部）。

## Hello, Cargo!

Cargo 是 Rust 的构建系统和包管理器。

yunwei@ubuntu:~/hello_world$ cargo --version

> cargo 1.44.1 (88ba85757 2020-06-11)

```
yunwei@ubuntu:~$ cargo new hello_cargo
     Created binary (application) `hello_cargo` package
yunwei@ubuntu:~$ cd hello_cargo
```

文件名: Cargo.toml

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
authors = ["yunwei <1067852565@qq.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]

```

- TOML (Tom's Obvious, Minimal Language) 格式
- Cargo 期望源文件存放在 src 目录中。项目根目录只存放 README、license 信息、配置文件和其他跟代码无关的文件。使用 Cargo 帮助你保持项目干净整洁，一切井井有条。

### 构建并运行 Cargo 项目

- cargo build 构建项目：

```
yunwei@ubuntu:~/hello_cargo$ cargo build
   Compiling hello_cargo v0.1.0 (/home/yunwei/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.72s
```

- cargo run 在一个命令中同时编译并运行生成的可执行文件：

```
yunwei@ubuntu:~/hello_cargo$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.00s
     Running `target/debug/hello_cargo`
Hello, world!
```

- cargo check 通常 cargo check 要比 cargo build 快得多，因为它省略了生成可执行文件的步骤。

```
yunwei@ubuntu:~/hello_cargo$ cargo check
    Checking hello_cargo v0.1.0 (/home/yunwei/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.11s
```

- 有别于将构建结果放在与源码相同的目录，Cargo 会将其放到 target/debug 目录。
- 发布（release）构建：cargo build --release

```
Some common cargo commands are (see all commands with --list):
    build       Compile the current package
    check       Analyze the current package and report errors, but don't build object files
    clean       Remove the target directory
    doc         Build this package's and its dependencies' documentation
    new         Create a new cargo package
    init        Create a new cargo package in an existing directory
    run         Run a binary or example of the local package
    test        Run the tests
    bench       Run the benchmarks
    update      Update dependencies listed in Cargo.lock
    search      Search registry for crates
    publish     Package and upload this package to the registry
    install     Install a Rust binary. Default location is $HOME/.cargo/bin
    uninstall   Uninstall a Rust binary
```

# 编写 猜猜看 游戏

- 为了获取用户输入并打印结果作为输出，我们需要将 io（输入/输出）库引入当前作用域。
- 默认情况下，Rust 将 prelude 模块中少量的类型引入到每个程序的作用域中。如果需要的类型不在 prelude 中，你必须使用 use 语句显式地将其引入作用域。
- // 语法开始一个注释，持续到行尾。
- `关联函数`
- `静态方法`
- & 表示这个参数是一个 `引用`（reference），它允许多处代码访问同一处数据，而无需在内存中多次拷贝。
- 使用 Result 类型来处理潜在的错误:Result 类型是 `枚举`（enumerations），通常也写作 enums。枚举类型持有固定集合的值，这些值被称为枚举的 成员（variants）。`Result` 的成员是 `Ok` 和 `Err`
- Cargo.lock 文件确保构建是可重现的
- cargo update 

碰到了这个问题：

```
yunwei@ubuntu:~/guessing_game$ cargo build
    Blocking waiting for file lock on package cache
```


解决方法:删除~/.cargo/.package_cache文件，然后 cargo clean ; cargo build 

源代码：

```rs
use rand::Rng;
use std::io;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = guess.trim().parse().expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

