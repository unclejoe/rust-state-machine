# 创建一个 Balances Pallet

如前所述，区块链的核心是一个状态机。

我们可以使用简单的 Rust 抽象来创建一个非常简单的状态机，并通过这个帮助来学习 Rust 在区块链环境中的使用。

我们希望保持代码的整洁，因此我们不会在 `main.rs` 文件中实际开始构建，而是在单独的 Rust 模块中进行。我们可以将 `main.rs` 文件视为将所有内容粘合在一起的胶水，我们将在贯穿整个教程中体会到这一点。

“Pallet”是 Polkadot SDK 特有的术语，指的是包含区块链运行时特定逻辑的 Rust 模块。我们这里使用这个术语与您在 Polkadot SDK 中看到的非常相似。

## 余额

几乎每个区块链都有处理用户余额的逻辑。

这个Pallet将告诉您：每个用户有多少余额，提供允许用户转移这些余额的功能，甚至提供一些低级功能，允许您的区块链系统在需要时操作这些余额。例如，如果您想铸造新的代币种类，即使这些代币以前并不存在。

这是一个很好的起点，也是我们将构建的第一个Pallet。

## 创建一个结构体

1. 在您的 `src` 文件夹中创建一个名为 `balances.rs` 的新文件。

	```
	touch src/balances.rs
	```

2. 在这个文件中，创建一个 `struct`，它将作为这个模块的状态和入口点：

	```rust
	pub struct Pallet {}
	```

3. 现在回到 `src/main.rs`，并导入这个新模块，它将包含其中的所有逻辑：

	```rust
	mod balances;
	```

4. 如果我们现在运行您的程序，您会看到它仍然可以编译和运行，但可能会显示一些警告，例如：

	```
	warning: struct `Pallet` is never constructed
	--> src/balances.rs:1:12
	|
	1 | pub struct Pallet {    }
	|              ^^^^^^
	|
	= note: `#[warn(dead_code)]` on by default

	warning: `pr` (bin "pr") generated 1 warning
	```

	这没关系！我们还没有开始使用我们的 `Pallet`，但您可以看到 Rust 编译器正在检测我们的新代码，并将该逻辑引入我们的主程序。这是构建我们的第一个状态机模块的开始。