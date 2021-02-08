.. _contract-module:

======================
智能合约模块
======================

Smart contracts are deployed on the chain in *smart contract modules*.
智能合约部署在链上的*智能合约模块*里.

.. note::

   一个智能合同模块通常只是简单地称为*模块*.

一个模块可以包含一个或多个智能合约，从而允许代码在合约之间共享，并且可以选择包含:ref:`contract schemas
<contract-schema>`.

.. graphviz::
   :align: center
   :caption: A smart contract module containing two smart contracts.

   digraph G {
       subgraph cluster_0 {
           node [fillcolor=white, shape=note]
           label = "Module";
           "Crowdfunding";
           "Escrow";
       }
   }

模块必须是自包含的，并且只有一个允许与链交互的受限导入列表。这些由主机环境提供，通过导入名为``concordium``的模块可用于智能合约。

.. seealso::

   Check out :ref:`host-functions` for a complete reference.

链上语言
=================

在Concordium区块链上，智能合约语言是`Web Assembly`_简称Wasm）的一个子集，它被设计成一个可移植的编译目标，并在沙盒环境中运行。这很有用，因为智能合约将由网络中不一定信任代码的面包师运行。

Wasm是一种低级语言，用手写是不切实际的。相反，人们可以用更高级的语言编写智能合约，然后编译成Wasm。

.. _wasm-limitations:

Limitations
-----------

.. todo::

   Add other limitations, such as start sections...

区块链环境非常特殊，每个节点必须能够以完全相同的方式执行合同，使用完全相同的资源量。否则节点将无法就链的状态达成共识。因此，智能合约需要在Wasm的一个受限子集中编写。

Floating point numbers
^^^^^^^^^^^^^^^^^^^^^^

Although Wasm does have support for floating point numbers, a smart contract is
disallowed to use them. The reason for this is that Wasm floating-point numbers
can have a special ``NaN`` ("not a number") value whose treatment can result in nondeterminism.

The restriction applies statically, meaning that smart contracts cannot contain
floating point types, nor can they contain any instructions that involve floating
point values.


Deployment
==========

Deploying a module to the chain means submitting the module bytecode as a
transaction to the Concordium network. If *valid* this transaction will be
included in a block. This transaction, as every other transaction, has an
associated cost. The cost is based on the size of the bytecode and is charged
for both checking validity of the module and on-chain storage.

The deployment itself does not execute
smart contract. To execute, a user must first create an *instance* of a contract.

.. seealso::

   See :ref:`contract-instances` for more information.

.. _smart-contracts-on-chain:

.. _smart-contracts-on-the-chain:

.. _contract-on-chain:

.. _contract-on-the-chain:

Smart contract on the chain
===========================

A smart contract on the chain is a collection of functions exported from a deployed
module. The concrete mechanism used for this is the `Web Assembly`_ export
section. A smart contract must export one function for initializing new
instances and can export zero or more functions for updating the instance.

Since a smart contract module can export functions for multiple different smart
contracts, we associate the functions using a naming scheme:

- ``init_<contract-name>``: The function for initializing a smart contract must
  start with ``init_`` followed by a name of the smart contract. The contract
  must consist only of ASCII alphanumeric or punctuation characters, and is not
  allowed to contain the ``.`` symbol.

- ``<contract-name>.<receive-function-name>``: Functions for interacting with a
  smart contract are prefixed with the contract name, followed by a ``.`` and a
  name for the function. Same as for the init function, the contract name is not allowed
  to contain the ``.`` symbol.

.. note::

   If you develop smart contracts using Rust and ``concordium-std``, the
   procedural macros ``#[init(...)]`` and ``#[receive(...)]`` set up the
   correct naming scheme.

.. _Web Assembly: https://webassembly.org/
