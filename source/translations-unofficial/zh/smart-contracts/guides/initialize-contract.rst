.. _initialize-contract:

====================================
初始化智能合约实例
====================================

本指南将向您展示如何使用JSON或二进制格式的参数从已部署的智能合约模块初始化智能合约。此外，它将显示如何命名实例。

制备
===========

确保您正在使用最新的Concordium软件<下载>运行节点<run-a-node>，并确保您拥有智能合约：部署了<deploy-module> `在上链一些模块。

由于初始化智能合约是一项交易，因此您还应确保已concordium-client建立一个具有足够GTU的账户来支付交易费用。

.. 注意::

   此事务的成本取决于发送给init函数的参数的大小以及函数本身的复杂性。
   
初始化
==============

要my_contract 通过参考从已部署的模块 初始化无参数智能合约的实例，9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2同时允许使用最多1000 NRG，请运行以下命令：

.. code-block:: console

   $concordium-client contract init \
            9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2 \
            --sender my_account \
            --contract my_contract \
            --energy 1000

如果成功，则输出应类似于以下内容：

.. todo::

   Update address format to ``<i, s>`` from ``{"index": i, "subindex": s}``
   (throughout the documentation).

.. code-block:: console

   Contract successfully initialized with address: {"index":0,"subindex":0}

看到此消息意味着已使用显示的地址创建了一个新的链上合同实例。

.. 另

   请参阅
   ：要更深入地了解合同初始化，请参见：contract-instances-init-on-chain。

   有关模块引用和实例地址的更多信息，
   请参见参考链上。

   直接使用模块引用可能很不方便；为它们
   命名，请参阅：命名模块。

.. _init-passing-parameter-json:

以JSON格式传递参数
---------------------------------

如果提供了智能合约模式<contract-schema>作为文件或嵌入在模块中，则可以传递JSON格式的参数。该模式用于将JSON序列化为二进制。

.. seealso :: 

   ：参考：`了解更多关于为什么和如何使用智能合同模式<合同模式>`。

   参数也可以以二进制格式<init-passing-parameter-bin>传递。

要my_parameter_contract使用9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2带有my_parameter.jsonJSON格式的参数文件的引用从模块 初始化合同的实例，请运行以下命令：

.. code-block:: console

   $concordium-client contract init \
            9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2 \
            --contract my_parameter_contract \
            --energy 1000 \
            --parameter-json my_parameter.json

如果成功，则输出应类似于以下内容：

.. code-block:: console

   Contract successfully initialized with address: {"index":0,"subindex":0}

否则，将显示描述问题的错误。下一节将介绍常见错误。

.. 注意::

   如果以JSON格式提供的参数不符合架构中指定的类型，则将显示错误消息。例如：

    .. code-block:: console

       Error: Could not decode parameters from file 'my_parameter.json' as JSON:
       Expected value of type "UInt64", but got: "hello".
       In field 'first_field'.
       In {
           "first_field": "hello",
           "second_field": 42
       }.

.. 注意::

   如果给定的模块不包含嵌入式模式，则可以使用--schema /path/to/schema.bin参数提供它。

.. 注意::

   GTU也可以在初始化期间使用--amount AMOUNT参数转移到合同实例。

.. _init-passing-parameter-bin:

以二进制格式传递参数
-----------------------------------

当以二进制格式传递参数时，不需要合约模式<contract-schema>。

要使用二进制格式的参数文件my_parameter_contract引用模块 中的合同实例，请运行以下命令：9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2my_parameter.bin

.. code-block:: console

   $concordium-client contract init \
            9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2 \
            --contract my_parameter_contract \
            --energy 1000 \
            --parameter-bin my_parameter.bin


如果成功，则输出应类似于以下内容：

.. code-block:: console

   Contract successfully initialized with address: {"index":0,"subindex":0}

.. 另

   请参见
   ：有关如何在智能合约中使用参数的信息，请参阅参考参数。

.. _naming-an-instance:

命名合同实例
==========================

可以为合同实例指定本地别名或name，这使得引用起来更容易。该名称仅由本地存储concordium-client，在链上不可见。

.. 另请参见：

   有关名称和其他本地设置的
   存储方式和位置的说明，请参见local-settings。

要在初始化期间添加名称，请使用--name参数。

在这里，我们my_contract从部署的模块 初始化合约9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2并命名my_named_contract：

.. code-block:: console

   $concordium-client contract init \
            9eb82a01d96453dbf793acebca0ce25c617f6176bf7a564846240c9a68b15fd2 \
            --contract my_contract \
            --energy 1000 \
            --name my_named_contract


如果成功，则输出应类似于以下内容：

.. code-block:: console

   合同实例也可以使用以下name命令命名。要命名与地址索引的实例0为my_named_contract，下面的命令运行：


.. code-block:: console

   $concordium-client contract name 0 --name my_named_contract

如果成功，则输出应类似于以下内容：

.. code-block:: console

   Contract address {"index":0,"subindex":0} was successfully named 'my_named_contract'.

.. 另

   请参见
   ：有关合同实例地址的更多信息，请参阅参考资料。

.. _parameter_cursor():
   https://docs.rs/concordium-std/latest/concordium_std/trait.HasInitContext.html#tymethod.parameter_cursor
.. _get(): https://docs.rs/concordium-std/latest/concordium_std/trait.Get.html#tymethod.get
.. _read(): https://docs.rs/concordium-std/latest/concordium_std/trait.Read.html#method.read_u8
