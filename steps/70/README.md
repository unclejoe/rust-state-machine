# 在 PoE 中添加调用宏

我们已经看到了 `#[macros::call]` 宏如何帮助清理 Balances Pallet。

现在，让我们也将其添加到 Proof of Existence Pallet 中，这里有更多的代码可以被消除。

## 添加你的调用宏

我们基本上需要在这里重复我们为 Balances Pallet 所做的步骤：

1. 将你的 `create_claim` 和 `revoke_claim` 函数移动到它自己的 `impl<T: Config> Pallet<T>` 中。
2. 在这个新的 `impl<T: Config> Pallet<T>` 上添加 `#[macros::call]` 属性。
3. 删除你现有的 `enum Call`。
4. 删除你现有的 `Dispatch for Pallet` 实现。
5. 然后，在你的 `main.rs` 文件中，将实例从：
    - `proof_of_existence::Call::CreateClaim` 更改为 `proof_of_existence::Call::create_claim` 使用 `snake_case`。
    - `proof_of_existence::Call::RevokeClaim` 更改为 `proof_of_existence::Call::revoke_claim` 使用 `snake_case`。

检查一切是否编译和运行正常。

## 扩展你的 Rust 代码

让我们借此机会向你展示如何更深入地了解宏正在做什么。

Rust 提供了 `cargo expand` 命令，允许你在项目中应用所有宏后输出生成的 Rust 代码。

要安装 `cargo expand`：

```bash
cargo install cargo-expand
```

然后，运行以下命令：

```bash
cargo expand > out.rs
```

这将把你的项目生成的代码输出到 `out.rs` 文件中。

然后查看该文件。

你应该注意到以下几点：

- 你所有的不同 `mod` 文件都被合并到了一个文件中，与你的 `main.rs` 在一起。
- 你会看到我们最终的 Pallet 代码已经生成了所有的 `Call` 和 `Dispatch` 逻辑！
- 你可能会注意到，第一个 `#[derive(Debug)]` 宏已经生成了代码：

    ```rust
    #[automatically_derived]
    impl<T: ::core::fmt::Debug + Config> ::core::fmt::Debug for Pallet<T>
    where
        T::Content: ::core::fmt::Debug,
        T::AccountId: ::core::fmt::Debug,
    {
        fn fmt(&self, f: &mut ::core::fmt::Formatter) -> ::core::fmt::Result {
            ::core::fmt::Formatter::debug_struct_field1_finish(f, "Pallet", "claims", &&self.claims)
        }
    }
    ```

- 你可能还会注意到其他较小的宏，如 `vec![]` 也发生了变化：

    ```rust
    extrinsics: <[_]>::into_vec(
        #[rustc_box]
        ::alloc::boxed::Box::new([
            // stuff
        ])
    )
    ```

- 以及 `println!()`：

    ```rust
    {
        ::std::io::_print(format_args!("{0:#?}\n", runtime));
    };
    ```

- 等等。

这里有两个主要的收获：

1. 宏最终遵循与常规 Rust 代码相同的规则，因为它确实生成了常规 Rust 代码。它们感觉很神奇，但实际上并没有什么神奇之处。
2. 宏是 Rust 生态系统的重要组成部分，广泛用于提高开发体验和代码质量。

如果你使用外部开发的宏，并且想更仔细地了解正在发生的事情，`cargo expand` 可以是一个有用的工具，帮助你更好地理解项目的一些隐藏架构细节。当你跳入 Polkadot SDK 时，我建议你继续使用这个工具来增强你的学习和理解。