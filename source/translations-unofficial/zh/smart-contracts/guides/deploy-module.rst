.. _deploy-module:

==============================
部署智能合约模块
==============================

本指南将向您展示如何在链上部署智能合约模块及其命名方式。

制备
===========

确保您正在使用最新的Concordium软件<下载>运行节点<运行节点>，并且具有智能合约模块<安装工具>。 >`准备部署。

由于部署智能合约模块是以交易的形式完成的，因此您还需要concordium-client设置一个具有足够GTU的帐户来支付交易费用。

.. 注意::

   交易成本取决于智能合约模块的大小。concordium-client显示费用并在执行任何交易之前要求确认。

部署方式
==========

要my_module.wasm使用名称为account-name的帐户部署智能合约模块，请运行以下命令：

.. code-block:: console

   $concordium-client module deploy my_module.wasm --sender account_name

.. 注意::

   如果要使用帐户“默认”，则可以省略--sender选项。为简便起见，我们将在下面进行说明。

如果成功，则输出应类似于以下内容：

.. code-block:: console

   Module successfully deployed with reference: 'd121f262f3d34b9737faa5ded2135cf0b994c9c32fe90d7f11fae7cd31441e86'.

记下创建智能合约实例时使用的模块参考。

.. 请参阅
   ：有关如何从已部署的模块初始化智能合约的指南，请参见：initialize-contract。

   有关模块引用的更多信息，请参见链上引用。

.. _naming-a-module:

命名模块
===============

可以给模块一个本地别名或name，这使得引用起来更容易。该名称仅由本地存储concordium-client，在链上不可见。

.. 另请参见：

   有关名称和其他本地设置的
   存储方式和位置的说明，请参见local-settings。=

要在部署期间添加名称，请使用--name参数。在这里，我们为模块命名my_deployed_module：

.. code-block:: console

   $concordium-client module deploy my_module.wasm --name my_deployed_module

如果成功，则输出应类似于以下内容：

.. code-block:: console

   Module successfully deployed with reference: '9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2' (my_deployed_module).

也可以使用name命令来命名模块。要将引用的已部署模块命名 9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2为 some_deployed_module，请运行以下命令：

.. code-block:: console

   $concordium-client module name \
             9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2 \
             --name some_deployed_module

输出应类似于以下内容：

.. code-block:: console

   Module reference 9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2 was successfully named 'some_deployed_module'.
