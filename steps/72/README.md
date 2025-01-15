# 使用 runtime 宏

最后，让我们在 `main.rs` 文件中添加 `#[macros::runtime]` 宏，以消除大量的样板代码。

## 运行时宏

`#[macros::runtime]` 宏的目的是消除我们为 `Runtime` 实现的所有样板函数，包括 `fn new()` 和 `fn execute_block()`。与 `Call` 宏类似，它还生成了 `enum RuntimeCall` 和所有重新分派到 Pallet 的 `dispatch` 逻辑。

我们将 `#[macros::runtime]` 属性应用于主 `struct Runtime` 对象的顶部。

### 解析

为了生成我们想要的代码，我们需要跟踪以下信息：

1. 表示我们运行时的 `struct` 的名称。通常这是 `Runtime`，但我们为开发者提供了灵活性。
2. 包含在我们 `Runtime` 中的 Pallet 列表：
   1. 它们的名称，由用户指定。
   2. 它们的具体类型，例如 `balances::Pallet` 与 `proof_of_existence::Pallet`。

所有这些信息都记录在 `RuntimeDef` 结构体中。

我们还检查了我们的 `Runtime` 定义是否始终包含 System Pallet ，并且作为我们 `Runtime` 定义中的第一个 Pallet 。我们将在下面解释宏的假设。

### 展开

一旦我们解析了所有需要的数据，我们只需要生成我们期望的代码。

从 `let runtime_impl = quote!` 开始，你会看到整个 `impl Runtime` 代码块都被宏吞噬了。由于我们知道你的 `Runtime` 中的所有 Pallet ，我们可以自动实现像 `new()` 这样的函数。`execute_block` 函数没有利用任何解析的数据，但代码完全是样板，所以我们将其隐藏起来。

然后我们有另一个代码块，使用 `let dispatch_impl = quote!` 生成，这是 `enum RuntimeCall` 和 `Dispatch for Runtime` 的实现。

同样，由于使用宏的怪癖，我们的 `RuntimeCall` 枚举将具有与 `Runtime` 结构体中的字段名称完全匹配的 `snake_case` 变体。

## 宏假设

这些宏编程的假设之一是 System Pallet 的存在。例如，在 `execute_block` 逻辑中，我们需要访问 `system.inc_block_number` 和 `system.inc_nonce`。

一些宏级别的假设是有意的，实际上定义了设计这些宏的框架的架构决策。System Pallet 就是这种情况，因为区块链框架的许多部分都依赖于一致的元层。

其他假设的存在仅仅是因为如果做出这些假设，宏的编写会更容易。

这里的主要收获是，宏几乎总是可以不断改进，为开发者提供越来越好的用户体验。它只需要有人识别出需要做出哪些改进，然后有人将这些改进编程到低级宏代码中。

## 添加运行时宏

让我们最后一步一步地将 `#[macros::runtime]` 属性添加到你的 `Runtime` 中。

1. 在 `main.rs` 中，在你的 `pub struct Runtime` 顶部添加 `#[macros::runtime]`。
2. 删除整个 `impl Runtime` 代码块。
3. 删除整个 `enum RuntimeCall`。
4. 删除整个 `Dispatch for Runtime` 的实现。
5. 更新 `RuntimeCall` 枚举的实例以使用 `snake_case`：
   - 将 `RuntimeCall::Balances` 更改为 `RuntimeCall::balances`。
   - 将 `RuntimeCall::ProofOfExistence` 更改为 `RuntimeCall::proof_of_existence`。

就是这样！你现在已经完成了构建简单 Rust 状态机的完整教程。🎉