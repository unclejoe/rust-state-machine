# 通过构造简单 Rust 状态机 学习使用 Polkadot SDK 开发区块链应用

这个仓库是 IURDao 为初学者普及 Polkadot 生态应用开发而翻译制作。

## 目标

本教程的目标是通过实践经验教授 Rust 和区块链开发的各种入门级概念。

本教程具有主观性，深受 [Polkadot SDK](https://github.com/paritytech/polkadot-sdk)、[Substrate](https://github.com/paritytech/polkadot-sdk/tree/master/substrate) 和 [FRAME](https://github.com/paritytech/polkadot-sdk/tree/master/substrate/frame) 的影响。然而，本教程中的所有内容都是从头开始编写的，没有使用这些库。

本教程旨在让完成它的读者更好地理解 FRAME 背后的原理，并更有信心成功使用 Polkadot-SDK。

本教程将教授和回顾许多 Rust 概念，但它不能替代初级 Rust 教育课程。建议在开始本教程之前，您已经熟悉了 [Rust 书籍](https://doc.rust-lang.org/book/) 前 11 章中的概念。

## 如何使用

这个仓库不是直接使用的，而是作为使用每个提交中包含的源代码和自述文件生成交互式教程的源代码。

这个仓库管理 3 个分支，每个分支都有自己的历史和目的：

- [`master`](https://github.com/shawntabrizi/rust-state-machine): 这是教程中每个步骤和文件的扩展版本。每个步骤都有自己完整的源代码和自述文件，用于该步骤的教程。
- [`gitorial`](https://github.com/shawntabrizi/rust-state-machine/tree/gitorial): 这个分支将教程的所有步骤重新打包成一个单一的 Git 历史。你可以查看 [gitorial 分支的历史](https://github.com/shawntabrizi/rust-state-machine/commits/gitorial/)，并使用 Git diff 查看每个步骤的演变。
- `mdbook`: 这是教程的一个特殊重新打包，用于生成一个 [mdBook](https://github.com/rust-lang/mdBook)，可以直接供学生使用。

如果您需要对单个步骤进行小的更改，请随时打开一个问题或对 `master` 分支进行 PR。但是，对于可能影响多个步骤的更复杂的更改，请考虑学习更多关于 `gitorial` 格式的知识。

### 更多关于 Gitorial

本教程的核心是 [Gitorial 格式](https://github.com/gitorial-sdk)。

如果你浏览 [gitorial 分支的提交历史](https://github.com/shawntabrizi/rust-state-machine/commits/gitorial/)，你会看到每个提交都设计为教程中的一个步骤。

所有提交都以以下前缀之一开头：

- `section`: 这表示新步骤集的开始，这些步骤将有一个特定的目标。这些提交将只对 `README.md` 文件进行更改，用于介绍教程的新部分。
- `template`: 这个提交有一个 `README.md`，它教读者完成该步骤所需的所有信息。它还将包括带有 `TODO` 注释的文件，告诉用户具体需要做什么。`template` 提交总是会跟着一个 `solution` 提交。
- `solution`: 这些提交总是在 `template` 提交之后。这些提交将包含该步骤结束时项目中所有文件的最终状态。以 `solution` 为前缀的提交应该总是能够编译、运行和测试成功（编译器警告是可以的）。`template` 和 `solution` 提交应该一起呈现，以便读者可以将他们的工作与一个工作解决方案进行比较。这些提交也可以用于生成该步骤的 `diff`。这个提交中的 `README.md` 文件不需要呈现给用户。
- `action`: 这表示教程中的一个步骤，用户需要完成一些操作，不一定是编写代码。例如，用户可能需要导入一个新的 crate。在这种情况下，没有必要有一个 `template` 和 `solution`，而只需要在采取行动后的最终结果。前一个提交可以用于生成 `diff`。`README.md` 文件应该包含用户成功完成操作所需的任何信息。
- `readme`: 这只适用于这个仓库的最后一个提交，表示这个提交是专门为在 GitHub 上浏览这个仓库的用户制作 `README.md`。这个步骤不应该用于教程生成。

你可以使用 Git 对仓库的历史进行更改，然后使用 `git merge` 将这些更改干净地传播到你的仓库的其他部分。

## 维护

维护仓库意味着保持所有三个主要分支的同步。

为此，你可以使用 [`gitorial-cli`](https://github.com/gitorial-sdk/cli)。

一旦你对适当的分支进行了更改，你可以使用以下命令使所有分支保持有序：

- 将 `master` 转换为最新的 `gitorial` 分支：

    ```sh
    gitorial-cli repack -p /path/to/rust-state-machine -i master -s steps -o gitorial2
	```
	然后检查你的工作，并使用 gitorial2 对 gitorial 分支进行 git reset --hard。

- 将 `gitorial` 转换为最新的 `mdbook` 分支：

    ```sh
    gitorial-cli mdbook -p /path/to/rust-state-machine -i gitorial -o mdbook
	```

- 将 `gitorial` 转换为最新的 `master` 分支：

    ```sh
    gitorial-cli unpack -p /path/to/rust-state-machine -i gitorial -s steps -o master
	```