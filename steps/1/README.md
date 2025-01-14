# 初始化你的 Rust 项目

在这一步中，我们将初始化一个基本的 Rust 项目，在这里我们可以开始构建我们的简单 Rust 状态机。

## cargo init

1. **创建目录**：在你想要存放项目的地方创建一个目录，并导航到该文件夹。我们将使用一个名为 `rust-state-machine` 的文件夹。

    ```sh
    mkdir rust-state-machine
    cd rust-state-machine
    ```

2. **初始化项目**：在该文件夹中，使用 `cargo init` 初始化你的 Rust 项目：

    ```sh
    cargo init
    ```

    这将搭建一个基本的 Rust 可执行文件，我们将在此基础上进行构建。

3. **验证项目**：你可以通过运行以下命令来验证你的新项目是否按预期工作：

    ```sh
    cargo run
    ```

    你应该会在编译结束时看到 "Hello, World!" 输出：

    ```sh
    ➜  rust-state-machine git:(master) ✗ cargo run
    Compiling rust-state-machine v0.1.0 (/Users/shawntabrizi/Documents/GitHub/rust-state-machine)
        Finished dev [unoptimized + debuginfo] target(s) in 2.19s
        Running `target/debug/rust-state-machine`
    Hello, world!
    ```

### 项目结构

如果你查看生成的内容，在该文件夹中，你会看到以下文件和目录：

- `src/main.rs` - 这是你的程序的入口点。我们将在 `src` 文件夹中构建这个项目的所有内容。
- `Cargo.toml` - 这是你的 Rust 项目的配置文件。与 Node.js 项目中的 `package.json` 类似。我们将在未来导入 crate 时修改这个文件，但现在可以先不管它。
- `Cargo.lock` - 这是一个自动生成的锁文件，基于你的 `cargo.toml` 和编译过程。它定义了每个导入 crate 的具体版本，不需要手动编辑。
- `target/*` - 如果你运行了 `cargo run`，你可能还会看到一个 `target` 文件夹。这是编译过程中所有编译后的二进制文件存放位置。我们不会将这个文件夹提交到 Git 历史记录中。

如果你对 Rust 已经有了一些基本的经验，那么这些内容应该都很熟悉。如果这些内容对你来说是全新的，我建议你先阅读 [Rust Book](https://doc.rust-lang.org/book/) 和 [Rust by Example](https://doc.rust-lang.org/rust-by-example/)，因为这表明本指南可能不适合你的知识水平。