# 紧耦合

你可能已经注意到，在使我们的 Pallet 通用和可配置时，存在一些冗余。两个 Pallet 都定义了一个 `AccountId` 类型，而从技术上讲，我们可以以不同的方式定义它们的具体类型！

在实际的生产区块链中，我们不希望这样。相反，我们希望在一个地方定义通用类型，并在任何地方使用它们。

## 特征继承

Rust 具有特征继承的能力。也就是说，为了实现某个特征，你还需要实现它继承的所有特征。

让我们看一些例子。

### 特征函数

我们可以扩展之前的例子，展示特征继承如何处理函数。

```rust
pub trait GetName {
    // 返回一个字符串，表示对象的名称
    fn name() -> String;
}

pub trait SayName: GetName {
    // 将 `name()` 函数返回的名称打印到控制台
    fn say_name() {
        println!("{}", Self::name());
    }
}
```

注意在 `trait SayName` 的定义中，我们在冒号后面引用了 `GetName`。这意味着要实现 `SayName` 特征，你的对象必须同时实现 `GetName` 特征。请注意，我们甚至可以通过使用 `Self::name()` 函数来为 `get_name` 编写一个“默认”实现。

因此，当我们实现这些特征时，它看起来像这样：

```rust
struct Dan;
impl GetName for Dan {
    fn name() -> String {
        return "shawn".to_string();
    }
}

impl SayName for Dan {}
```

我们可以选择实现我们自己版本的 `SayName` 函数，例如：

```rust
impl SayName for Dan {
    fn say_name() {
        println!("My name is {}!", Self::name());
    }
}
```

但我们不必这样做。我们必须做的是确保 `GetName` 为 `Dan` 实现，否则你将无法使用 `SayName` 特征。同样，我们不会在本教程中使用这个，但很高兴看到这个如何被使用的例子。

### 关联类型

与其在每个需要它的 Pallet 中重新定义 `type AccountId`，不如我们在 `system::Config` 中定义它，并在其他 Pallet 配置中继承该类型？

让我们看看这会是什么样子：

```rust
pub trait Config: crate::system::Config {
    type Balance: Zero + CheckedSub + CheckedAdd + Copy;
}
```

在这里，你可以看到我们的 `balances::Config` 特征继承自 `crate::system::Config` 特征。这意味着通过 `balances::Config` 特征，可以访问 `system::Config` 定义的所有类型，包括 `AccountId`。因此，我们不需要在 `balances::Config` 中重新定义 `AccountId` 类型。

在 Polkadot SDK 生态系统中，我们称这为“紧耦合”，因为包含 Balances  Pallet 的运行时必须同时包含 System  Pallet 。从某种意义上说，这两个 Pallet 是紧密耦合的。实际上，在 Substrate 中，所有 Pallet 都与 System  Pallet 紧密耦合，因为 System  Pallet 提供了区块链系统的所有元类型。

## 将 Balances 紧密耦合到 System

让我们从 Balances  Pallet 中删除冗余的 `AccountId` 定义。

1. 在 `balances::Config` 特征中继承 `crate::system::Config` 特征。
2. 从 `balances::Config` 定义中删除 `AccountId` 类型。
3. 为 `TestConfig` 实现 `crate::system::Config` 特征。
4. 在 `main.rs` 中，只需从 `balances::Config` 中删除 `type AccountId`。