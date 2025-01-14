# System 和 Runtime 模块

在这一部分，你将创建一个名为 System Pallet 的较为底层的Pallet，所谓“底层”的含义是指，不直接实现生产业务的逻辑，而是用于管理基本的区块链系统的状态。然后，你将把 Balances Pallet 和 System Pallet 集成到你的状态转换结构中，这个结构被称为 Runtime。