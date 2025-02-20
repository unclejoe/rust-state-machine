# 使用命名类型

到目前为止，我们一直在将原始类型硬编码到我们的结构体和函数定义中。这可能会导致当业务逻辑逐渐增多时容易发生类型混淆，因为这并不总是清楚表明某种特定类型具体代表什么业务类型。例如，如果你看到一个函数接受一个 `u32` 参数，它是一个 `blocknumber` 还是一个 `nonce`？

为了使我们的代码更加清晰，让我们提取所有的原始类型，并为我们的结构体和函数定义自定义命名类型。在 Balances 和 System Pallets 的上下文中，我们需要定义以下类型：

1. `type AccountId = String;`
2. `type Balance = u128;`
3. `type Nonce = u32;`
4. `type BlockNumber = u32;`

请注意，将这些类型提取到通用类型定义中，不仅可以使我们的代码更具可读性，还可以在我们选择更改基础类型时更容易更新。

随着我们在本教程中的深入，我们将向您展示如何在为开发者构建区块链 SDK 的上下文中，使这些类型定义更加灵活和可定制。

## 创建自定义类型

按照模板中的 `TODO` 提示，将这些类型定义添加到每个 Pallet 中，并更新所有结构体和函数以使用这些类型。