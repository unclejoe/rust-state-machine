# 执行区块

我们现在将开始将 `main` 函数中的简单区块模拟替换为一个合适的区块执行管道。

## 执行区块

我们在 `Runtime` 中引入了一个新函数，名为 `fn execute_block`。

这个函数的步骤与我们当前的 `main` 函数完全相同，但使用了我们定义的具体 `Block` 类型来提取预期区块号和我们想要执行的外部交易（extrinsics）等细节。

### 数组迭代

为了构建我们的 `execute_block` 函数，我们需要遍历区块中的所有外部交易，并分派这些调用。在 Rust 中，访问数组元素的常用方法是将其转换为迭代器。

有两个函数可以将向量转换为迭代器：`iter` 和 `into_iter`，它们的区别在于所有权：

- `iter`：此方法创建一个迭代器，从数组中借用每个元素，允许您读取值而不获取所有权。当您想要迭代数组同时保持其完整时，这很有用。

- `into_iter`：此方法消耗数组，将每个元素的所有权转移到迭代器。当您想要将数组元素的所有权移动或转移到代码的另一部分时，这很方便。使用 `into_iter` 后，原始数组不能再使用，因为所有权已被转移。

在我们的上下文中，我们想使用 `into_iter()`，因此您将得到如下所示的代码：

```rust
for support::Extrinsic { caller, call } in block.extrinsics.into_iter() {
    // 处理 `caller` 和 `call`
}
```

在这里，您可以看到我们还做了一个技巧，在一行中分离出 `Extrinsic` 的字段，因为最终我们想要处理 `caller` 和 `call`。当然，如果您愿意，也可以将这个过程分解为多行。

### 分派调用

一旦我们有了 `call` 和 `caller`，我们应该如何处理它们？

这就是 `Dispatch` 特征开始发挥作用的地方。您将在我们的模板中看到，我们包含了一个未实现的 `fn dispatch` 的外壳。我们将在下一步中编写此逻辑，但我们需要在 `execute_block` 逻辑中使用 `dispatch` 函数。

一旦我们有了 `call` 和 `caller`，我们想将它们传递给 `dispatch` 逻辑，这是在 `Runtime` 上实现的。

这看起来像这样：

```rust
let _res = self.dispatch(caller, call).map_err(|e| eprintln!("{}", e));
```

请注意，在 Rust 中，如果您想访问特征中的函数，如我们在这里使用 `dispatch`，您需要将该特征显式导入到您的项目中（孤儿原则）。

我们在 `main.rs` 的顶部留下了一个 `TODO`，要求您导入 `crate::support::Dispatch`，这将允许您在 `Runtime` 上调用 `dispatch`。

### 更好的错误消息

由于这是我们项目中更持久的功能，因此扩展在发生外部交易错误时打印的消息也很有意义。例如：

```rust
eprintln!(
    "Extrinsic Error\n\tBlock Number: {}\n\tExtrinsic Number: {}\n\tError: {}",
    block.header.block_number, i, e
)
```

这允许您在发生外部交易错误时看到区块号、外部交易号和错误消息。当您有许多区块被导入，每个区块可能有许多外部交易时，这非常有用。

要获取外部交易号 `i`，您可以在 `into_iter()` 之后链接 [`enumerate()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.enumerate) 函数。

## 构建您的执行区块函数

您现在应该拥有成功编写 `execute_block` 函数所需的所有工具和信息。

按照模板提供的 `TODO` 进行操作，并确保包含我们为您提供的 `impl crate::support::Dispatch for Runtime`，我们将在下一步中实现它。

您的代码应该仍然可以编译，但会有一些“从未构造/使用”的警告。