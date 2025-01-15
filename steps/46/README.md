# 分发调用

我们已经根据尚未实现的`dispatch`逻辑构建了`execute_block`逻辑。

让我们来实现它。

## 添加我们的调用

分发逻辑的全部内容是将用户的外部调用路由到适当的 Pallet 函数。到目前为止，我们创建的唯一用户可调用函数是`Balances` Pallet 中的`transfer`函数。

因此，让我们将该调用添加到`RuntimeCall`枚举中。

我们的`transfer`函数需要三个输入：

- `caller`：调用转移函数的账户，其余额将被减少。
- `to`：资金将被发送到的账户。
- `amount`：要转移的资金数量。

然而，请记住，我们的`dispatch`逻辑已经从`Block`中的`Extrinsic`获得了`caller`信息，因此我们在`RuntimeCall`中不需要再次提供此数据。

实际上，我们运行时中的每个`Call`都应该省略`caller`，并知道它是由我们的`dispatch`逻辑提供的。

因此，在向`RuntimeCall`添加新变体时，它应该看起来像这样：

```rust
pub enum RuntimeCall {
	BalancesTransfer { to: types::AccountId, amount: types::Balance },
}
```

用户向我们的状态机提交外部调用时，可以使用此枚举变体来指定他们想要调用的函数（`transfer`），以及该调用所需的参数。

## 分发逻辑

`dispatch`函数中的核心逻辑是一个简单的`match`语句。

基本上，给定一些`RuntimeCall`，我们需要匹配提供给我们的变体，然后将适当的参数传递给正确的 Pallet 函数。如前所述，`dispatch`已经可以访问`caller`信息，因此最终的逻辑非常简单：

```rust
match runtime_call {
	RuntimeCall::BalancesTransfer { to, amount } => {
		self.balances.transfer(caller, to, amount)?;
	}
}
```

分发逻辑真的就是这么简单！

请注意，我们使用`?`操作符传播函数调用返回的任何错误。这很重要，如果你想看到我们在`execute_block`逻辑中设置的错误消息。

## 编写你的分发逻辑

按照模板中的`TODO`来构建你的`RuntimeCall`并完成你的`dispatch`逻辑。

你的代码应该仍然可以编译，但会有一些“从未构建/使用”的警告。再完成一步，我们将消除所有这些警告！