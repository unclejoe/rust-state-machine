# 使 Balances Pallet通用化

在接下来的几个步骤中，我们的目标是不断使我们的运行时在我们的 Pallet 使用的类型上更加通用和可配置。

## 为什么要通用化？

通用运行时的灵活性意味着我们可以编写适用于多种不同配置和类型的代码。

例如，到目前为止，我们一直在使用 `&'static str` 来表示用户的账户。这显然不是正确的做法，只适用于像这样的基本区块链教程。

如果要使用更传统的加密公钥，你需要改变什么？

目前， Balances Pallet和系统 Pallet 中都有账户类型的定义。想象一下，如果你有更多的 Pallet 呢！这种重构可能非常困难，但如果我们从一开始就使用通用类型，就可以完全避免。

实际上，在本教程中，通用类型的优势不会非常明显，但在构建像 Substrate 这样的区块链 SDK 时，这种灵活性将允许生态系统开发者充分发挥他们的潜力。

例如，一些团队使用 Substrate 构建了完全兼容的以太坊区块链，而其他团队则尝试了更为前沿的加密原语。这种通用框架允许不同目标的团队都能取得成功。

## 泛型类型

你已经通过 `Result` 类型接触过泛型类型。请记住，这种类型非常灵活，允许你配置 `Ok` 或 `Err` 时返回的类型。

如果我们想使我们的 `Pallet` 通用化，它看起来会像这样：

```rust
pub struct Pallet<AccountId, Balance> {
 	balances: BTreeMap<AccountId, Balance>,
}
```

并且在 `Pallet` 上实现函数会像这样：

```rust
impl<AccountId, Balance> Pallet<AccountId, Balance> {
	// 使用这些类型的函数
}
```

在这种情况下，我们没有具体定义 `AccountId` 和 `Balance` 类型是什么，只是说我们将存储一个 `BTreeMap`，其中 `AccountId` 类型是键，`Balance` 类型是值。

### 特征约束

`Result` 泛型类型非常灵活，因为它对 `Ok` 或 `Err` 类型没有任何约束。每种类型都适用于这种情况。

然而，我们的 Pallet 并不是那么灵活。`Balance` 类型不能是任何类型。因为我们有像 `fn transfer` 这样的函数，我们必须要求 `Balance` 类型至少可以访问 `checked_sub`、`checked_add` 函数，并且有某种形式的 `zero` 表示。

这就是 `num`  crate 派上用场的地方。从 `num` crate 中，你可以导入定义这些函数的特征：

```rust
use num::traits::{CheckedAdd, CheckedSub, Zero};
```

然后，在适用的地方，你需要约束你的通用类型以具有这些特征。

这看起来像：

```rust
impl<AccountId, Balance> Pallet<AccountId, Balance>
where
	AccountId: Ord,
	Balance: Zero + CheckedSub + CheckedAdd + Copy,
{
	// 使用这些类型并具有指定特征的函数
}
```

你会注意到添加了其他约束，如 `Copy` 和 `Ord`。这些约束来自于使用像 `BTreeMap` 这样的结构，它要求键类型是“可排序的”。

实际上，你可以尝试编译你的代码而不添加这些类型约束，编译器会告诉你缺少哪些约束，以及你需要包含哪些内容。

### 实例化泛型类型

最后一块拼图是实例化我们的泛型类型。

以前，我们可以简单地写：

```rust
let mut balances = super::Pallet::new();
```

但是现在 `Pallet` 是泛型的，我们需要在实例化它时具体定义这些类型。

语法看起来像：

```rust
let mut balances = super::Pallet::<&'static str, u128>::new();
```

你会注意到，现在类型是在任意可实例化泛型 `struct Pallet` 的代码中定义的。这意味着你可以将类型从 Pallet 中提取出来，并将它们移动到运行时中。

## 变得更为通用！

是时候让你的 Balances Pallet 变得通用了。

1. 按照 `balances.rs` 文件中的 `TODO` 注释，使 `Pallet` 泛型化。
2. 将 `AccountId` 和 `Balance` 的类型定义移动到你的 `main.rs` 文件中。
3. 更新你的 `struct Runtime`，在定义 `balances::Pallet` 时使用这些类型。

老实说，这是开发者在学习 Rust 时最常遇到问题的地方之一，这就是为什么在教学中如此强调让你自己学习和实践这些步骤的原因。

如果你在这一步遇到困难，不要害怕查看解决方案，但请尝试学习使用泛型类型的模式，以及所有语法在保证类型安全方面的意义。