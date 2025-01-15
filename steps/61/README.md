# 将 PoE 集成到您的运行时中

Proof of Existence  Pallet 已经完成，但我们仍然需要将其集成到您的运行时中。

让我们来看看这个过程。

## 集成步骤

1. 首先，在您的 `struct Runtime` 中添加 `proof_of_existence` 字段。
2. 接下来，您需要更新 `fn new()` 以初始化 `proof_of_existence`。
3. 然后，创建一个新的具体 `type Content`，它是一个 `&'static str`。如前所述，通常这将是一个哈希，但为了简单起见，我们再次使用一个简单的静态字符串。

	> 如果您现在或将来想要使用哈希，只需更新这一行即可更改运行时和 Pallet 的所有类型。这就是我们一直努力实现的灵活性！

4. 之后，为 `Runtime` 实现 `proof_of_existence::Config`，使用您的 `types::Content`。
5. 此时，事情应该已经可以成功编译，因此将此作为一个检查点。
6. 为 `RuntimeCall` 引入一个新的变体 `ProofOfExistence`。
7. 最后，更新您的 `fn dispatch` 逻辑，以处理将 `ProofOfExistence` 调用重新分派到 `proof_of_existence::Pallet`。

希望从这个过程中，您可以看到我们引入的所有抽象是如何使将新 Pallet 集成到您的运行时中变得相当容易的。

我们将在不久的将来使用宏使这个过程更加容易！

到这一步结束时，一切都应该编译通过，没有警告。