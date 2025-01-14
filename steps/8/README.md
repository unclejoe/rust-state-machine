# 与 Balances Pallet 交互

现在我们已经建立了余额模块的基础，让我们添加一些与它交互的方法。

为此，我们将继续在 `Pallet` 上创建更多的函数，这些函数允许读取、写入和更新我们创建的 `balances: BTreeMap`。

最后，我们将从 `main.rs` 文件中实际开始与我们的 Balances Pallet进行交互。

## Rust 先决知识

在继续之前，让我们花点时间复习一些 Rust 知识，这些知识将在接下来的部分中使用。

### Option 类型

Rust 的一个关键原则是从代码中消除未定义的行为。

未定义行为可能发生的一种方式是允许 `null` 状态存在。Rust 通过要求用户显式处理所有情况来防止这种情况，这就是 `Option` 类型的创建原因。如果需要，请花点时间重新查看 [Rust Book 中关于 `Option` 的章节](https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html?highlight=option#the-option-enum-and-its-advantages-over-null-values)。

`BTreeMap` API 在从映射中读取值时使用 `Option`，因为可能会请求读取未设置键的值。例如：

```rust
use std::collections::BTreeMap;

let mut map = BTreeMap::new();
map.insert("alice", 100);
assert_eq!(map.get(&"alice"), Some(&100));
assert_eq!(map.get(&"bob"), None);
```

一旦我们有了 `Option` 类型，就可以使用 Rust 提供的多种方法来处理它。

最详细的方法是使用 `match` 语句：

```rust
let maybe_value = map.get(&"alice");
match maybe_value {
    Some(value) => {
        // 对 `value` 进行一些操作
    },
    None => {
        // 可能返回一个错误，因为没有值
    }
}
```

> 重要提示！

你不要盲目地使用 `unwrap()` 选项。这将导致代码 `panic`，这是 Rust 设计初衷来防止严重错误的！相反，你应该始终显式处理所有不同的错误逻辑情况，这样会使你的代码非常健壮和安全。

在我们为 Balances Pallet 设计的上下文中有一个映射，其中包含任意数量的用户键及其余额值。

当我们读取一个不存在于我们映射中的用户的余额时，我们应该怎么做？

这里的技巧是，在区块链的上下文中，用户余额为 `None` 和用户余额为 `0` 是相同的。当然，在用户存在于我们的状态中但余额为 `0` 和用户根本不存在之间存在一些细微的差别，但在此处于教学目的，我们可以将它们视为相同的。

这看起来像什么？

我们可以使用 `unwrap_or(...)` 来安全地处理这种情况，并使我们未来的 API 更易于使用。例如：

```rust
use std::collections::BTreeMap;

let mut map = BTreeMap::new();
map.insert("alice", 100);
assert_eq!(*map.get(&"alice").unwrap_or(&0), 100);
assert_eq!(*map.get(&"bob").unwrap_or(&0), 0);
```

如你所见，通过在从我们的映射中读取后使用 `unwrap_or(&0)`，我们能够将 `Option` 转换为基本整数，其中有余额的用户暴露其余额，而 `None` 用户转换为 `0`。

让我们看看如何使用它。

## 设置和读取用户余额

如你所见，我们的初始状态机启动时，每个人的余额都为零。

为了使我们的模块有用，我们至少需要一些函数来允许我们为用户铸造新的余额，并读取这些余额。

1. 在 `impl Pallet` 内部创建一个名为 `fn set_balance` 的新函数：

    ```rust
    impl Pallet {
        pub fn set_balance(&mut self, who: &String, amount: u128) {
            self.balances.insert(who.clone(), amount);
        }

        // -- snip --
    }
    ```

    如你所见，这个函数简单地接受关于我们想要设置余额的用户和我们想要为该用户设置的余额数。然后将这些数据插入到我们的 `BTreeMap` 中，仅此而已。

2. 在 `impl Pallet` 内部创建一个名为 `fn balance` 的新函数：

    ```rust
    pub fn balance(&self, who: &String) -> u128 {
        *self.balances.get(who).unwrap_or(&0)
    }
    ```

    如你所见，这个函数允许我们读取映射中用户的余额。该函数允许你输入某个用户，我们将返回他们的余额。

    > 重要细节！

    请注意，我们在这里使用了小技巧！我们没有公开一个强制后续程序处理 `Option` 的 API，而是能够通过将任何 `None` 值用户转换为 `0` 来始终返回 `u128`。

一如既往，确认一切是否正常编译。警告可以忽略。

接下来，我们将编写我们的第一个测试，并实际与我们的 Balances Pallet 进行交互。