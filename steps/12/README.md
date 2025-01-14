# 实现账户余额转账功能

现在我们已经初始化并开始使用余额模块，让我们添加可能是最重要的 API：`transfer`。

## 学点区块链基本原则

在编写函数之前，我们有必要回顾一下区块链和 Rust 的一些原则。

### 不良行为者

在区块链系统中，安全性是最重要的。不良行为者可能会试图利用漏洞，例如在账户余额不足时发起转账，这或将导致程序在执行加减法运算时出现溢出/下溢问题。Rust 的安全数学和错误处理机制有助于减轻这些风险。

### 安全数学

Rust 的安全数学操作可以防止溢出和下溢。`checked_add` 和 `checked_sub` 方法返回一个 `Option`，允许安全地处理潜在的算术错误。

在 Rust 中，`Option` 类型是标准库的一个基本部分，用于处理值可能存在或不存在的情况。它通常用于操作结果可能未定义或不存在的情况。

`checked_add` 和 `checked_sub` 等方法返回 `Option` 以指示由于溢出或下溢导致的成功或失败。

```rust
let result = a.checked_add(b);
match result {
    Some(sum) => println!("Sum: {}", sum),
    None => println!("Overflow occurred."),
}
```

### 错误处理

在 Rust 中，错误处理是编写健壮和安全代码的一个组成部分。`Result` 类型通常用于可能在执行过程中遇到错误的函数。

`Result` 类型是标准库中定义的一个枚举。它有两个变体：`Ok(value)` 表示成功的结果，`Err(error)` 表示错误：

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`T` 和 `E` 是泛型参数，允许你根据需要自定义结果类型。对于本教程，我们将始终返回 `Ok(())` 表示一切正常，返回 `Err(&'static str)` 表示任何错误，并附带一个基本的字符串描述。

你可以这样定义 `Result` 类型：

```rust
Result<(), &'static str>
```

### Options 和 Results

你可以使用 `Option` 类型来触发 `Err`，这在你希望函数仅在一切按预期执行时非常有用。

在这种情况下，我们想要一个函数，当任何安全数学操作返回 `None` 时，它将返回一个错误。

为此，我们可以在安全数学操作后直接链接 `ok_or` 和 `?`，如下所示：

```rust
let new_from_balance = from_balance
   .checked_sub(amount)
   .ok_or("Not enough funds.")?;
```

如果 `checked_sub` 返回 `None`，我们将返回一个带有消息 "Not enough funds." 的 `Err`，该消息可以显示给用户。否则，如果 `checked_sub` 返回 `Some(value)`，我们将直接将 `new_from_balance` 赋值为该值。

在这种情况下，我们正在编写代码，以安全和符合人体工程学的方式完全处理 `Option` 类型。

## 创建转账

按照模板中的说明在你的余额模块中创建一个安全简单的转账函数。

创建一个测试，证明一切按预期工作，包括错误处理。不应该有编译器警告！