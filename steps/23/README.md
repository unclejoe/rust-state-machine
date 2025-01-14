# Derive Debug

在 Rust 中，`derive` 宏提供了一种方便的方法来自动为自定义数据结构实现 trait 功能。

## 宏

宏是 Rust 中的一种特殊功能，允许你编写代码来生成其他代码。这有点像函数，但它们更强大，因为它们可以操作 Rust 的抽象语法树（AST）。宏可以让你的代码更简洁，避免重复，并允许你创建自己的编码规则。

在本教程的后面，我们将大量使用（但不编写）宏，你将看到它们的强大之处。现在，你可以把它们看作是“魔法”。

## Traits

在 Rust 中，Traits 是一种定义共享行为的方式。它们允许你为不同的类型定义一组必须实现的方法。这样，你可以确保代码的不同部分遵循相同的规则。

如果你需要复习 Traits，可以看看[这个例子](https://doc.rust-lang.org/rust-by-example/trait.html) 或重新阅读 [Rust Book](https://doc.rust-lang.org/book/ch10-02-traits.html) 中的相关章节。

在本教程的后面，我们将创建和使用自定义 Traits，但现在你只需要知道 `#[derive(Debug)]` 是一个宏，它为你的自定义类型实现了 `Debug` Trait。

### Debug Trait

`Debug` Trait 是 Rust 标准库中的一部分，用于打印和格式化值，以便于调试。它通过 `#[derive(Debug)]` 注解提供了一个默认实现。

例如：

```rust
#[derive(Debug)]
pub struct MyStruct {
    field1: i32,
    field2: String,
}
```

有了 `Debug` Trait 的实现，你现在可以打印 `struct` 到控制台：

```rust
let my_instance = MyStruct { field1: 42, field2: "Hello".to_string() };
println!("{:#?}", my_instance);
```

字符 `:#?` 有助于[格式化](https://doc.rust-lang.org/std/fmt/)输出，使其更具可读性。

## 为你的运行时派生 Debug Trait

这是一个非常简单但很有帮助的步骤！

我们希望能够在 `main` 函数的结尾处打印出 `Runtime` 的当前状态，以便我们可以轻松检查它的外观，并确保一切按预期运行。

要做到这一点，我们需要在 `struct Runtime` 上添加 `#[derive(Debug)]`。

然而，`struct Runtime` 是由 `system::Pallet` 和 `balances::Pallet` 组成的，因此这些结构体也需要实现 `Debug` Trait。

完成项目中不同文件中的 `TODO`，并在 `main` 函数的结尾处打印出你的最终运行时。

你可以使用 `cargo run` 来查看 `println` 的输出。一切应该编译并运行，没有警告。