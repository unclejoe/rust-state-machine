# 在 Balances 中添加 Call 宏

让我们开始在 Balances Pallet 中添加 `#[macros::call]` 宏。

## Call 宏的作用

`#[macros::call]` 宏的目的是自动从 pallet 的函数中生成 `enum Call`，以及在每个 Pallet 中找到的 pallet 级 `Dispatch` 逻辑。

我们可以将 `#[macros::call]` 属性放在 `impl<T: Config> Pallet<T>` 上，在那里实现了可调用的函数。从那里，宏可以解析整个对象，并提取它需要的数据。并不是所有的函数都打算被调用，因此你可以将应该包含的函数隔离在自己的 `impl<T: Config> Pallet<T>` 中，就像模板中所做的那样。

### 解析

为了生成我们想要的代码，我们需要跟踪：

1. 开发人员希望通过 Runtime 公开的每个可调用函数。
    1. 该函数的名称。
    2. 该函数的参数名称和类型。
2. 实现这些函数的 `struct` 名称，通常是 `Pallet`，但我们可以允许开发人员在命名上有一定的灵活性。

这些信息通过 `CallDef` 和 `CallVariantDef` 进行跟踪。

此外，在解析过程中，我们可能需要检查代码中的某些一致性。在这种情况下，我们要求每个可调用函数必须将 `caller` 作为其第一个参数，类型为 `T::AccountId`。这是有道理的，因为你设计了许多不同的可调用函数，它们都遵循这个模式。

这个检查逻辑由 `fn check_caller_arg` 处理。

### 展开

一旦我们解析了所有需要的数据，生成代码就非常直接了。

如果你跳到 `let dispatch_impl = quote!`，你会看到一堆代码，看起来像我们在教程早期使用的模板。我们只是在宏生成逻辑应该放置所有信息的地方留下了标记，以编写我们需要的代码。

## 宏的怪癖

宏在使用时通常非常“古怪”。由于所有输入到宏中的都是其他代码，有时这些代码的格式可能与你的预期不符。

例如，我们构建的原始 `Call` 枚举如下：

```rust
pub enum Call<T: Config> {
	Transfer { to: T::AccountId, amount: T::Balance },
}
```

变体被称为 `Transfer`，因为它代表的函数名为 `fn transfer`。

然而，如果我们想要生成 `Call` 枚举，而我们只有 `fn transfer`，我们将从哪里获得特定的字符串 `Transfer` 并大写 `T`？

可以进行字符串操作并调整所有内容以符合 Rust 的预期，但在这种情况下，我们的宏最好对用户编写的代码进行最小的修改。

这意味着什么？

当 `#[macros::call]` 宏生成我们的 `enum Call` 时，它实际上看起来像这样：

```rust
#[allow(non_camel_case_types)]
pub enum Call<T: Config> {
	transfer { to: T::AccountId, amount: T::Balance },
}
```

这里你看到 `transfer` 与函数名完全一致。通常，所有枚举变体都应该是 `CamelCase`，但由于 Rust 函数是 `snake_case`，我们的枚举变体也将是 `snake_case`。我们不会看到任何关于此的警告，因为我们启用了 `#[allow(non_camel_case_types)]`。

最终，这对你的底层代码没有显著影响。这只是人体工程学和预期的问题。

确实，宏可能很古怪，但它们节省的时间使其值得使用。

## 是时候添加你的 Call 宏了

1. 如果你还没有，将你的 `transfer` 函数移动到它自己的 `impl<T: Config> Pallet<T>` 中。我们只想将宏应用于这一个函数，因此我们需要将它与其他不打算调用的函数隔离开来。
2. 在这个新的 `impl<T: Config> Pallet<T>` 上添加 `#[macros::call]` 属性。
3. 删除你现有的 `enum Call`。
4. 删除你现有的 `Dispatch for Pallet` 实现。
5. 然后，在你的 `main.rs` 文件中，将 `balances::Call::Transfer` 实例更改为 `balances::Call::transfer`，其中 `t` 为小写。

此时，一切都应该像以前一样编译！我们正在见证宏自动为我们生成代码的力量！