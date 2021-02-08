.. _unit-test-contract:

============================
在Rust中对合同进行单元测试
============================

本指南将向您展示如何为用Rust编写的智能合约编写单元测试。有关测试智能合约Wasm模块的信息，请参阅：本地模拟。

Rust中的智能合约是作为库编写的，我们可以通过为#[test]属性添加功能注释来像库一样对其进行单元测试。

.. code-block:: rust

    // contract code
    ...

    #[cfg(test)]
    mod test {

        #[test]
        fn some_test() { ... }

        #[test]
        fn another_test() { ... }
    }

可以使用cargo以下命令运行测试：

.. code-block:: console

   $cargo test

默认情况下，此命令将合同编译并测试为本地目标（最有可能是x86_64）的机器代码，然后运行它们。这种测试对于初始开发和功能正确性测试很有用。对于全面测试，重要的是要使用目标平台Wasm32。平台之间存在许多细微的差异，这些差异可以改变合同的行为。区别在于指针的大小，其中Wasm32使用四个字节，而不是八个字节，这在大多数平台上都很常见。

编写单元测试
==================

U单元测试通常遵循三部分结构，您可以在其中进行以下操作：设置某些状态，运行某些代码单元以及对代码的状态和输出进行断言。

如果合同功能是使用#[init(..)]或 编写的#[receive(..)]，我们可以直接在单元测试中测试这些功能。

.. code-block:: rust

   use concordium_std::*;

   #[init(contract = "my_contract", payable, enable_logger)]
   fn contract_init(
      ctx: &impl HasInitContext<()>,
      amount: Amount,
      logger: &mut impl HasLogger,
   ) -> InitResult<State> { ... }

   #[receive(contract = "my_contract", name = "my_receive", payable, enable_logger)]
   fn contract_receive<A: HasActions>(
      ctx: &impl HasReceiveContext<()>,
      amount: Amount,
      logger: &mut impl HasLogger,
      state: &mut State,
   ) -> ReceiveResult<A> { ... }

在concordium-std名为的子模块中可以找到功能参数的测试存根 test_infrastructure。

.. 另

   请参见：:有关更多信息和示例，请参见
   concordium-std的包装箱文档。

.. todo::

   显示更多关于如何编写单元测试的信息

R在Wasm中运行测试
=====================

在大多数情况下，将测试编译为本机代码就足够了，但是也可以将测试编译为Wasm并使用节点使用的确切解释器运行它们。这使测试环境更接近于链上的运行环境，并且在某些情况下可能会捕获更多错误。

开发工具cargo-concordium包括一个用于Wasm的测试运行程序，它使用与Concordium节点中附带的Wasm解释程序相同的Wasm解释程序。

.. 另::

   有关如何安装“ congo -concordium”的指南，请参阅：setup-tools。

单元测试必须使用#[concordium_test]代替 #[test]，并且我们使用#[concordium_cfg_test]代替#[cfg(test)]：

.. code-block:: rust

   // contract code
   ...

   #[concordium_cfg_test]
   mod test {

       #[concordium_test]
       fn some_test() { ... }

       #[concordium_test]
       fn another_test() { ... }
   }

将#[concordium_test]在WASM要运行的宏设置我们的测试中，当 concordium-std与编译wasm-test功能，否则将回退到表现就像#[test]，这意味着它仍然可以运行单元测试使用针对本地代码cargo test。

类似地，宏#[concordium_cfg_test]在构建时会包含我们的模块 concordium-std，wasm-test否则行为类似于#[test]，从而使我们能够控制何时在构建中包含测试。

现在可以使用以下命令构建和运行测试：

.. code-block:: console

   $cargo concordium test

此命令编译wasm-test启用了功能的Wasm测试，concordium-std并使用中的测试运行器cargo-concordium。

.. 警告::

   编译为Wasm时，不会显示来自 panic！的错误消息，以及 assert！的不同变体。

   Instead use fail! and the claim! variants to do assertions when testing, as these reports back the error messages to the test runner before failing the test. Both are part of concordium-std.

.. todo::

   发布板条箱时，使用链接concordium-std：docs.rs/concordium-std。
