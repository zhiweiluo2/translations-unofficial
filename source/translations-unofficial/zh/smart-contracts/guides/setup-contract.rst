.. highlight:: toml

.. _setup-contract:

===================================
设置智能合约项目
===================================

Rust中的智能合约被编写为普通的Rust库箱。然后使用Rust目标将该库编译为Wasm ``wasm32-unknown-unknown`` ，由于它只是一个Rust库，因此我们可以使用 Cargo_ 进行依赖项管理。

要设置新的智能合约项目，请首先创建一个项目目录。在项目目录中，在终端中运行以下命令：

.. code-block:: console

   $cargo init --lib

这将通过创建一些文件和目录来设置默认的Rust库项目。您的目录现在应该包含一个 ``Cargo.toml`` 文件和一个 ``src`` 目录以及一些隐藏文件。

为了建造Wasm，我们需要告诉货物正确的权利 ``crate-type`` 。这是通过在文件中添加以下内容来完成的 ``Cargo.toml``::

   [lib]
   crate-type = ["cdylib", "rlib"]

添加智能合约标准库
==========================================

下一步是添加 ``concordium-std`` 为依赖项。它是一个用于Rust的库，其中包含程序宏和用于编写小型高效的智能合约的函数。

通过在以下部分中打开 ``Cargo.toml`` 并添加以下行 来添加该库 ``concordium-std = "*"`` （最好将*替换为最新版本的 `concordium-std`_） ``[dependencies]`` ：

   [dependencies]
   concordium-std = "0.4"

箱子文档可在 docs.rs_ 上找到。

.. 注意::

如果您想使用此板条箱的修改版，则必须 ``concordium-std`` 通过将以下内容添加到来克隆存储库，并在目录中具有依赖点 ``Cargo.toml``::
   
      [dependencies]
      concordium-std = { path = "./path/to/concordium-std" }

.. _Rust: https://www.rust-lang.org/
.. _Cargo: https://doc.rust-lang.org/cargo/
.. _rustup: https://rustup.rs/
.. _repository: https://gitlab.com/Concordium/concordium-std
.. _docs.rs: https://docs.rs/crate/concordium-std/
.. _`concordium-std`: https://docs.rs/crate/concordium-std/

这就对了！现在您可以开发自己的智能合约了。
