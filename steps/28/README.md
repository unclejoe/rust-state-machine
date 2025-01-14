# 导入 Num Crate

Rust 设计得非常轻量级，开箱即用的功能非常少。

在生态系统中，许多您可能期望包含在 Rust `std` 或 `core` 中的功能实际上被委托给了更为轻量、知名且广泛使用的crates。

下一步，我们希望访问基本数值操作的特征，例如：

- `CheckedAdd` - 支持 `checked_add` 的类型
- `CheckedSub` - 支持 `checked_sub` 的类型
- `Zero` - 调用 `zero()` 时可以返回零值的类型
- `One` - 调用 `one()` 时可以返回一值的类型

要访问这些特征，我们需要将一个新的 crate 导入到我们的项目中。

## Cargo.toml

当我们第一次初始化项目时，会为我们生成一个 `Cargo.toml` 文件。

如前所述，它与您在 Node.js 项目中期望找到的 `package.json` 文件非常相似。

在您的 `Cargo.toml` 中已经包含了项目的元数据，如 `name`、正在构建的 crate 的 `version` 以及您正在使用的 Rust `edition`。

您可以看到，您还可以向 crate 中添加 `dependencies`，这将允许您在项目中使用其他外部 crates 和库。

您可以通过手动编辑 `Cargo.toml` 文件来添加依赖项，也可以运行 `cargo add num`。

## Crates.io

这个 crate 是从哪里来的？

Rust 社区在 [crates.io](https://crates.io/) 上有一个大型的可用crate注册表。当您导入crate时，默认情况下它将使用 `crates.io`。

您也可以直接从 github 导入crate，指定可以找到源代码的仓库。

就像这样：

```toml
[dependencies]
pallet-balances = { version = "4.0.0-dev", default-features = false, git = "https://github.com/paritytech/substrate.git", branch = "polkadot-v1.0.0" }
```

## 将 Num crate添加到您的项目中

这一步非常简单。

在您的项目目录中运行 `cargo add num`，并确保项目编译通过。

您应该会看到类似这样的内容：

```bash
➜  rust-state-machine git:(master) ✗ cargo add num
    Updating crates.io index
      Adding num v0.4.1 to dependencies.
             Features:
             + std
             - alloc
             - libm
             - num-bigint
             - rand
             - serde
    Updating crates.io index
➜  rust-state-machine git:(master) ✗ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target/debug/rust-state-machine`
```

请注意，您需要确保您的项目目录中已经有了 `Cargo.toml` 文件，并且您有网络连接以访问 `crates.io`。