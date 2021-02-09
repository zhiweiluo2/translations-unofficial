.. _Rust: https://www.rust-lang.org/
.. _Cargo: https://doc.rust-lang.org/cargo/
.. _rust-analyzer: https://github.com/rust-analyzer/rust-analyzer

.. _compile-module:

====================================
编译Rust智能合约模块
====================================

本指南将向您展示如何将用Rust编写的智能合约模块编译为Wasm模块。

制备
===========

确保已安装Rust和 ``wasm32-unknown-unknown`` ， ``cargo-concordium``  并且要编译目标，目标以及智能合约模块的Rust源代码。

.. 也可以看看：：

   有关如何安装开发人员工具的说明，请参见
   设置工具

编译为Wasm
=================

为了帮助构建智能合约模块并利用 :ref:`contract schemas <contract-schema>` 之类的功能，我们建议使用该 ``cargo-concordium``  工具来构建Rust智能合约。

为了建立智能合约，请运行：

.. code-block:: console

   $cargo concordium build

这使用Cargo进行构建，但会对结果进行进一步的优化。

.. 也可以看看：：

   为了构建智能合约模块的架构，请参考
   需要准备<build-schema>`。

.. 注意::

   也可以通过运行以下命令直接使用Cargo进行编译：

   .. code-block:: console

      $cargo build --target=wasm32-unknown-unknown [--release]

   请注意，即使已 ``--release`` 设置，产生的Wasm模块也包含调试信息。
   

从构建中删除主机信息
====================================

编译后的Wasm模块可以包含来自构建二进制文件的主机的信息。信息，例如 ``.cargo`` 目录的绝对路径。

对于大多数人来说，这不是敏感信息，但重要的是要意识到这一点。

在Linux上，可以通过运行以下命令检查路径：

.. code-block:: console

   strings contract.wasm | grep /home/

.. rubric:: 解决方案

理想的解决方案是完全删除该路径，但是不幸的是，这通常不是一件容易的事。

``--remap-path-prefix`` 编译合同时可以通过使用标志来解决此问题。在类Unix的系统上，可以 ``cargo concordium`` 使用  ``RUSTFLAGS`` 环境变量将标志直接传递给调用：

.. code-block:: console

   $RUSTFLAGS="--remap-path-prefix=$HOME=" cargo concordium build

它将用空字符串替换用户的主路径。其他路径可以以类似方式映射。通常，using  ``--remap-path-prefix=from=to`` 将映射 ``from`` 到  ``to`` 任何嵌入式路径的开头。

也可以 ``.cargo/config`` 在构建部分下的箱子中的文件中永久设置该标志：

.. code-block:: toml

   [build]
   rustflags = ["--remap-path-prefix=/home/<user>="]

其中 `<user>` 应该用构建wasm模块的用户替换。

注意事项
-------

如果 ``rust-src`` 为Rust工具链安装了组件，则以上内容可能无法解决该问题。一些Rust工具（例如 rust-analyzer_.）需要此组件。

.. 另::

   一个报告--remap-path-prefix和rust-src问题的问题
   https://github.com/rust-lang/rust/issues/73167
