# 嵌套调度

现在我们已经在 Pallet 中定义了 Pallet 级别的调度逻辑，我们应该更新我们的 Runtime 以利用该逻辑。

之后，每当 Pallet 逻辑更新时，Runtime 调度逻辑也会自动更新并直接路由调用。这使得我们的代码更易于管理，并防止未来潜在的错误或维护问题。

## 嵌套调用

Balances Pallet 现在在 `balances::Call` 中公开了自己的调用列表。我们不再需要在 Runtime 中再次列出所有这些调用，而是可以使用嵌套枚举来正确路由我们的调用。

想象一下以下结构：

```rust
pub enum RuntimeCall {
    Balances(balances::Call<Runtime>),
}
```

在这种情况下，我们有一个变体 `RuntimeCall::Balances`，它本身包含一个类型 `balances::Call`。这意味着我们可以在这个变体下访问 `balances::Call` 公开的所有调用。随着我们创建更多的 Pallet 而扩展我们的调用，这种嵌套结构将使整体架构得到很好地扩展。

我们称 `RuntimeCall` 为“外部枚举”，`balances::Call` 为“内部枚举”。这种使用外部和内部枚举的结构在 Polkadot SDK 中非常常见。

## 重新调度到 Pallet

我们当前的 `dispatch` 逻辑直接调用 Pallet 中的函数。如前所述，将此逻辑放在 Pallet 之外会增加维护或错误的负担。

但是现在我们已经在 Pallet 本身中定义了 Pallet 级别的调度逻辑，我们可以使用它来使 Runtime 调度更具可扩展性。

为此，我们不再直接调用 Pallet 函数，而是可以从 `RuntimeCall` 中提取内部调用，然后使用 `balances::Pallet` 将该调用分派到适当的逻辑。

这看起来像这样：

```rust
match runtime_call {
    RuntimeCall::Balances(call) => {
        self.balances.dispatch(caller, call)?;
    },
}
```

在这里，你可以看到我们首先检查调用是否是 `Balances` 变体，然后从它提取 `call`，这是一个 `balances::Call` 类型，然后我们使用 `self.balances`，这是一个 `balances::Pallet`，来分派 `balances::Call`。

## 更新你的块

由于我们更新了 `RuntimeCall` 枚举的结构，我们还需要更新 `fn main` 中的 `Block` 构造。这里没有什么神奇的，只是需要使用 `RuntimeCall::Balances` 和 `balances::Call::Transfer` 构造一个嵌套枚举。

## 启用嵌套调度

现在是时候完成这一步，将 Pallet 级别的调度与 Runtime 级别的调度逻辑粘合在一起。

按照模板中提供的 `TODO` 完成你的端到端调度逻辑。到这一步结束时，应该没有编译器警告。