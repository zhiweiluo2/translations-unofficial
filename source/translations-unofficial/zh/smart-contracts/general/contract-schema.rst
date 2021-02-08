.. Should answer:
..
.. - Why should I use a schema?
.. - What is a schema?
.. - Where to use a schema?
.. - How is a schema embedded?
.. - Should I embed or write to file?
..

.. _`custom section`: https://webassembly.github.io/spec/core/appendix/custom.html
.. _`implementation in Rust`: https://github.com/Concordium/concordium-contracts-common/blob/main/src/schema.rs

.. _contract-schema:

======================
Smart contract schemas
======================

智能合约模式描述了如何在更结构化的表示中表示字节。当显示智能合约实例的状态时，外部工具可以使用它，并使用结构化表示（如JSON）指定参数。

.. seealso::
   有关如何为中的智能合约模块构建架构的说明，请参阅：ref:`buildschema`。

Why use a contract schema
=========================

区块链上的数据，例如实例的状态和传递给init和receive函数的参数，被序列化为字节序列。序列化是为了提高效率而优化的，而不是为了提高可读性。

.. todo::

   区块链上的数据，例如实例的状态和传递给init和receive函数的参数，被序列化为字节序列。序列化是为了提高效率而优化的，而不是为了提高可读性。

通常，这些字节具有结构，智能合约将此结构视为合约函数的一部分，但在这些函数之外，很难理解字节的含义。当检查契约实例的复杂状态或将复杂参数传递给智能契约函数时，情况尤其如此。在后一种情况下，字节应该从结构化数据序列化或手动写入。

避免手动解析字节的解决方案是在智能合约模式中捕获此信息，该模式描述如何从字节生成结构，并可供外部工具使用。

.. 注意::

  ``concordium client``工具可以使用模式：ref:`serializejson parameters<init passing parameter JSON>`并将契约实例的状态反序列化为JSON。

   然后，该模式要么嵌入到部署到链的智能合约模块中，要么写入到文件并在链外传递。

Should you embed or write to a file?
====================================

契约模式应该嵌入还是写入文件取决于您的情况。

将模式嵌入智能合约模块会将模式与合约一起分发，以确保使用正确的模式，并允许任何人直接使用它。缺点是智能合约模块变得更大，因此部署成本更高。但是，除非智能合约对状态和参数使用非常复杂的类型，否则与智能合约本身的大小相比，模式的大小可能可以忽略不计。

将模式放在单独的文件中允许您在部署时拥有模式，而无需支付额外的字节。缺点是，您必须通过其他渠道分发模式文件，并确保合同用户在智能合同中使用正确的文件。
Whether a contract schema should be embedded or written to a file depends on
your situation.

The schema format
=================

.. todo::

   澄清我们是否谈论用户可以实现的任何抽象模式，或由康考迪姆提供的特定模式。然后只谈其中一个，或者至少清楚地分开讨论这些。

架构可以包含

- 智能合约模块的结构信息
- 智能合约状态描述
- 智能合约的init和receive函数的参数。

这些描述中的每一个都称为模式类型。架构类型总是可选的，可以包含在架构中。

目前，受支持的模式类型受Rust编程语言中常用的内容启发：

.. code-block:: rust

   enum Type {
       Unit,
       Bool,
       U8,
       U16,
       U32,
       U64,
       I8,
       I16,
       I32,
       I64,
       Amount,
       AccountAddress,
       ContractAddress,
       Timestamp,
       Duration,
       Pair(Type, Type),
       List(SizeLength, Type),
       Set(SizeLength, Type),
       Map(SizeLength, Type, Type),
       Array(u32, Type),
       Struct(Fields),
       Enum(List (String, Fields)),
   }

   enum Fields {
       Named(List (String, Type)),
       Unnamed(List Type),
       Empty,
   }


Here, ``SizeLength`` describes the number of bytes used to describe the length
of a variable length type, such as ``List``.

.. code-block:: rust

   enum SizeLength {
       One,
       Two,
       Four,
       Eight,
   }

有关如何将模式类型序列化为字节的参考，请参阅 `Rust中的实现`_ 。

.. _contract-schema-which-to-choose:

Embedding schemas on-chain
==========================

使用Wasm模块的 `自定义节`_ 特性将模式嵌入到智能合约模块中。这允许Wasm模块包含一个命名的字节段，这不会影响运行Wasm模块的语义。
所有模式都被收集并添加到一个名为 ``concordium-schema-v1`` 的自定义部分中。这个集合是一个对的列表，包含用UTF-8编码的契约名称和契约模式字节。
