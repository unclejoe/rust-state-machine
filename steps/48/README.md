# 使用执行区块

我们现在已经成功实现了构建和执行真实`Blocks`所需的`execute_block`和`dispatch`逻辑。

让我们将这些逻辑引入到我们的`main`函数中。

## 创建一个区块

您可以通过填写结构体的所有字段并将其分配给一个变量来创建一个新的`Block`。

例如：

```rust
let block_1 = types::Block {
    header: support::Header { block_number: 1 },
    extrinsics: vec![
        support::Extrinsic {
            caller: &"alice",
            call: RuntimeCall::BalancesTransfer { to: &"bob", amount: 69 },
        },
    ],
};
```

重要的是，您要正确设置区块编号，因为我们在`execute_block`函数中验证了这一点。我们状态机中的第一个区块将编号为`1`。

还要记住，您可以通过扩展向量在单个区块中添加多个外部交易。

## 执行一个区块

一旦您构建了`Block`，就可以将其传递给在运行时实现的`execute_block`函数。

```rust
runtime.execute_block(block_1).expect("invalid block");
```

请注意，如果`execute_block`函数返回错误，我们会使用消息`"invalid block"`让程序报错退出。这只应该在您的区块出现严重问题时才会发生，例如区块编号与我们的预期不符。

如果外部交易中存在错误，此程序报错退出不会被触发，因为我们在`execute_block`函数中主动“忽略”了这些错误。

## 更新您的 main 函数

继续使用`Block`类型和`execute_block`函数来更新您的`main`函数的逻辑。

按照模板中提供的`TODO`完成此步骤。

在这一步结束时，您的代码应该能够成功编译、测试和运行，并且没有编译器警告！