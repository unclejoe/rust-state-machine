# 使系统可配置

为了使我们的运行时尽可能通用和可配置，我们还需要采取一个步骤。

为了实现这一点，我们将需要利用 trait。

## 自定义 Trait

我们已经使用了 Rust 提供的 trait 来使我们的类型通用化。

让我们快速了解一下如何定义自定义 trait：

```rust
pub trait Config {}
```

Traits 可以包含两种内容：

1. 必须由类型实现的函数。
2. 关联类型。

### 自定义函数

Traits 更明显的用途是定义自定义函数。

假设我们想要公开一个返回某个东西名称的函数。

你可以创建一个名为 `GetName` 的 trait：

```rust
pub trait GetName {
    fn name() -> String;
}
```

然后，你可以为任何对象实现这个 trait。

```rust
struct Dan;
impl GetName for Dan {
    fn name() -> String {
        return "Dan".to_string();
    }
}
```

然后，你可以在实现了该 trait 的对象上调用这个函数。

```rust
fn main() {
    println!("{}", Dan::name());
}
```

在我们简单的区块链中，我们实际上不会使用 trait 的这个特性，但在开发更复杂的区块链系统时，这有很多用例。

### 关联类型

你还可以使用 trait 定义关联类型。

这在 [Rust Book的第 19 章](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html) "高级 Traits" 中有介绍。

让我们通过首先查看我们试图解决的问题来学习这个概念。

到目前为止，我们的简单区块链代码看起来非常好，使用了泛型类型。然而，让我们想象一下，随着我们的区块链变得越来越复杂，需要越来越多的泛型类型。

例如：

```rust
pub struct Pallet<AccountId, BlockNumber, BlockLength, BlockWeight, Hash, Nonce, Runtime, Version,...> {
    // 一堆东西
}
```

想象一下，每次你想要实例化这个结构体时，你都需要依次具体指明所有这些泛型的实际类型。系统因此会变得非常复杂，而能够将这些类型抽象到更高的层次可以真正简化你的代码，使其更具可读性和可配置性。

为此，我们将使用一个带有多个关联类型的 trait：

```rust
pub trait Config {
    type AccountId: Ord;
    type BlockNumber: Zero + One + AddAssign + Copy;
    type Nonce: Zero + One + Copy;
    // 如果需要，还可以有更多
}
```

然后，我们可以使用单个泛型参数来定义我们的泛型类型！

```rust
pub struct Pallet<T: Config> {
    block_number: T::BlockNumber,
    nonce: BTreeMap<T::AccountId, T::Nonce>,
}
```

并使用以下语法实现 Pallet：

```rust
impl<T: Config> Pallet<T> {
    // 在这里使用来自 T 的类型的函数
}
```

让我们快速理解一下这个语法。

1. 有一个泛型类型 `T`。`T` 只是没有具体意义的名称，因为它代表一堆东西，这是 Rust 中最常用的约定。
2. `T` 被要求实现 `Config` trait，这是我们之前定义的。
3. 因为 `T` 实现了 `Config`，并且 `Config` 有关联类型 `AccountId`、`BlockNumber` 和 `Nonce`，我们可以像这样访问这些类型：
    - `T::AccountId`
    - `T::BlockNumber`
    - `T::Nonce`

在这个上下文中，我们称这个 trait 为 `Config`，因为它用于配置我们的 `Pallet` 的所有类型。

### 实现 Config Trait

让我们通过展示如何实现和使用 `Config` trait 来结束这一部分。

就像之前一样，我们需要一些对象来实现这个 trait。在我们的例子中，我们可以使用 `Runtime` 结构体本身。

```rust
impl system::Config for Runtime {
    type AccountId = String;
    type BlockNumber = u32;
    type Nonce = u32;
}
```

然后，当在 `Runtime` 中定义 `system::Pallet` 时，我们可以使用以下语法：

```rust
pub struct Runtime {
    system: system::Pallet<Self>,
}
```

这里我们基本上是说 `Pallet` 将使用 `Runtime` 作为其泛型的具体类型，但这是在 `Runtime` 内部定义的，所以我们将其称为 `Self`。

## 使你的系统可配置

这是一个关键的步骤，对于新的 Rust 开发者来说，这是一个常见的地方，人们可能会非常困惑。

你将有机会在 `Balances Pallet` 中再次执行这个整个过程，所以如果你这次无法让你的代码工作，不要偷懒多看解决方案。

真的要花时间理解这一步，发生了什么，以及所有这些语法对 Rust 的意义。

请记住，Rust 是一种完全类型安全的语言，所以最终，所有这些泛型类型和配置都需要指明具体类型以对 Rust 编译器有意义。