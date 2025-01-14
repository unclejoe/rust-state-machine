# Rust 工具

在这一步中，我们将安装并配置一些构建过程中必要的工具。这些工具不是必须的，但可以极大改善构建体验和代码质量。

## rustfmt

为了保持代码的整洁和易读性，我们使用一个名为 [`rustfmt`](https://github.com/rust-lang/rustfmt) 的工具。为了使用 `rustfmt` 的所有最新功能，我们特别使用 `nightly` 工具链。

要安装 `nightly` 版本的 `rustfmt`：

```bash
rustup component add rustfmt --toolchain nightly
```

要配置 `rustfmt` 的行为，我们将创建一个 `rustfmt.toml` 文件：

1. 在项目的根目录下创建一个名为 `rustfmt.toml` 的新文件。

	```bash
	touch rustfmt.toml
	```
2. 使用提供的 `rustfmt.toml` 文件来配置你的格式化偏好。
3. 使用以下命令运行代码格式化程序：

	```bash
	cargo +nightly fmt
	```

这次你应该不会看到任何变化，但随着你编写更多的代码，你将能够看到 `cargo +nightly fmt` 使一切看起来漂亮、一致且易于阅读。

> 我们建议你在每一步之后都运行 `cargo +nightly fmt`！

## Rust Analyzer

Rust 社区中另一个流行的工具是 [Rust Analyzer](https://rust-analyzer.github.io/)。

它为代码编辑器（如 VS Code）提供了许多功能，如代码完成和转到定义。

然而，为了提供它所做的全部功能，Rust Analyzer 需要编译你的代码。对于像这样的小项目，这不是问题，但是对于像 Substrate / Polkadot-SDK 这样的大型项目，这是一个问题。

我个人建议在这个工作坊中不需要 Rust Analyzer，通常你也不应该在 Substrate 开发中使用它。然而，本节可能会在未来更新，以包括 Rust Analyzer 的特殊配置，这些配置将来会与 Polkadot SDK 配合良好。

但是，如果你无论如何都想使用它，现在是设置它的好时机。

```
### 下载链接

你可以点击[这里](https://github.com/rust-lang/rustfmt)下载 `rustfmt`，并点击[这里](https://rust-analyzer.github.io/)下载 `Rust Analyzer`。