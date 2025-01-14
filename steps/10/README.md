# 测试 Balances Pallet

现在我们已经设置好了 `Pallet` 的基础部分，让我们实际与它进行交互。

为此，我们将回到 `main.rs` 文件，并创建我们的第一个 `#[test]`，它将使用我们迄今为止编写的代码。

1. 在你的 `src/balances.rs` 文件中，添加一个名为 `fn init_balances()` 的新 `#[test]`：

```rust
#[test]
fn init_balances() { }
```

2. 为了开始我们的测试，我们需要初始化一个新的 `Pallet` 实例：

```rust
#[test]
fn init_balances() {
    let mut balances = super::Pallet::new();
}
```

请注意，我们将这个变量声明为 `mut`，因为我们计划使用我们新创建的 API 来改变状态。

3. 最后，让我们检查我们的读写 API 是否按预期工作：

```rust
#[test]
fn init_balances() {
    let mut balances = super::Pallet::new();

    assert_eq!(balances.balance(&"alice".to_string()), 0);
    balances.set_balance(&"alice".to_string(), 100);
    assert_eq!(balances.balance(&"alice".to_string()), 100);
    assert_eq!(balances.balance(&"bob".to_string()), 0);
}
```

4. 我们可以使用 `cargo test` 运行我们的测试，希望你能看到它正常通过。**现在应该没有编译器警告了！**

我希望截至目前，你可以开始看到你的简单区块链状态机的雏形。