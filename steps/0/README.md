# 介绍 Rust 状态机

欢迎来到 Rust 状态机教程。

这是一个指导性教程，旨在教读者 Rust、区块链的基础知识，以及最终了解 [Polkadot SDK](https://github.com/paritytech/polkadot-sdk) 的内部工作原理。

根据我的经验，使用 Polkadot SDK 构建你的第一个区块链最困难的部分是 [Substrate](https://github.com/paritytech/polkadot-sdk/tree/master/substrate) 使用的高级 Rust 特性，以及理解各种宏背后的底层魔法，这些宏会为你生成代码。

本教程试图通过让你构建一个完全原生的 Rust 项目来解决这个问题，这个项目与 Polkadot SDK 一样具有所有相同的技巧，因此你可以第一手了解幕后发生的事情。

本教程不假设读者对 Rust、区块链或 Polkadot SDK 有很多先验知识，但是，本教程并不能替代这些主题的基础介绍。

强烈建议在开始本教程之前，你至少已经阅读了 [Rust Book](https://doc.rust-lang.org/book/) 的前 11 章。

你不需要成为这些内容的专家，但了解掌握所有权、基本数据类型、结构、枚举、crates、错误处理、traits、泛型类型和测试等各种知识点会有所帮助。

本教程分为几个部分，涵盖了读者的特定学习目标，并在你需要时随时回看。

本教程的所有内容都是开源的，可以免费访问，并且可以在 [这里](https://github.com/shawntabrizi/rust-state-machine) 找到。

如果你有任何可以改进本教程的建议，欢迎评论、问题和拉取请求。

废话不多说，希望你能学到很多东西！