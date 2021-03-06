.. _`Network Dashboard`: https://dashboard.testnet.concordium.com/
.. _Discord: https://discord.gg/xWmQ5tp

.. _run-a-node:

==========
运行节点
==========

.. contents::
   :local:
   :backlinks: none

在本指南中，您将学习如何在计算机上运行参与 Concordium 网络的节点。这意味着您将从其他节点接收块和事务，并将有关块和事务的信息传播到 Concordium 网络中的节点。遵循本指南后，您将能够

-  运行一个 Concordium 节点
-  在网络仪表板上观察它，并
-  直接从您的机器查询 Concordium 区块链的状态。

您不需要帐户即可运行节点。

在你开始之前
================

在运行 Concordium 节点之前，您需要


1. 安装并运行 Docker。

   -  在Linux上，允许 Docker 以非 root用户身份运行。

2. 下载并解压缩 :ref:`concordium-node-and-client-download` 软件。

从早期版本的Open Testnet升级
===============================================

要升级到用于Open Testnet 4的当前Concordium软件，请执行以下操作：

- 请按照上述步骤操作，以 :ref:`download<downloads>` 下载最新的Concordium软件。


-   ``concordium-node-reset-data`` 从解压缩的存档中运行可执行文件。

   -  对于Mac用户：第一次打开该工具时，右键单击该  ``concordium-node-reset-data`` 文件，然后选择 **“打开”** 。将会出现一条消息，说明该软件来自一个身份不明的开发商。再次选择 **“打开”** 。
   -  对于Windows用户：第一次打开该工具时，双击该 ``concordium-node-reset-data`` 文件。将会出现一条消息，说明该软件来自一个身份不明的开发商。选择 **更多信息→仍然运行**。

-  该工具将询问：

      *您还想删除已保存的密钥吗？*

   为先前版本创建的帐户在Open Testnet 3上不再有效。因此，如果您存储了先前版本的帐户，我们建议输入y，这将删除所有帐户密钥。

.. _running-a-node:

运行节点
==============

要开始运行将加入Open Testnet的客户端，请按照下列步骤操作：

1. ``concordium-node`` 从解压缩的存档中打开可执行文件。

-  对于Mac用户：第一次打开该工具时，右键单击 ``concordium-node`` 二进制文件，然后选择 **“打开”** 。将会出现一条消息，说明该软件来自一个身份不明的开发商。 再次选择 **“打开”** 。
-  对于Windows用户：第一次打开该工具时，双击 ``concordium-node`` 二进制文件。将会出现一条消息，说明该软件来自一个身份不明的开发商。选择 **更多信息→仍然运行**。
-  当重新启动一个节点考虑使用 ``--no-block-state-import`` 选项。这将仅下载在节点处于非活动状态时发生的对 Concordium 区块链的更新，并可能加快启动过程。

2. 输入节点的名称。此名称将显示在公共仪表板中。

3. 如果该工具已经启动，则在启动之前将询问您是否要删除本地节点数据库。按y将删除并随后重新创建保存在计算机上的 Concordium 区块链状态信息。**请注意，删除本地节点数据库意味着您的节点要花更多的时间才能赶上Concordium 网络** 。

该工具现在将下载 Concordium Client 映像并将其加载到 Docker 中。客户端将启动并开始输出有关节点操作的日志记录信息。

在仪表板上看到您的节点
=================================

运行后， ``concordium-node`` 您可以

-  在  网络仪表板上  查看您的节点
-  :ref:`query<testnet-query-node>` 有关区块，交易和账户的信息

网络仪表板
-----------------

客户需要一段时间才能赶上 Concordium 区块链的状态。例如，这涉及下载有关链中所有块的信息。

在其他信息中，您可以在 `Network Dashboard`_ 上了解节点追上链需要多长时间。对于您可以在节点的比较 **长度** 与价值（收到节点块数）**链莱恩** 这是在仪表板的顶部显示值（块的网络中产业链最长号）。


启用入站连接
============================

如果在防火墙后或家庭路由器后运行节点，则可能只能连接到其他节点，但其他节点将无法启动与该节点的连接。很好，您的节点将完全参与 Concordium 网络。它将能够发送交易， 如果配置成 :ref:`if so configured<become-a-baker>`，则可以进行烘烤和完成。

但是，通过启用入站连接，您还可以使节点成为更好的网络参与者。默认情况下， ``concordium-node`` 在端口上侦听 ``8888`` 入站连接。根据您的网络和平台配置，您可能需要将外部端口转发到 ``8888`` 路由器上，或者在防火墙中打开它，或者同时使用这两者。具体操作方式取决于您的配置。

配置端口
-----------------

节点侦听四个端口，可以通过在启动节点时提供适当的命令行参数来进行配置。节点使用的端口如下：

- 8888，用于点对点网络的端口，可以使用 ``--listen-node-port``
-  8082，中间件使用的端口，可以使用 ``--listen-middleware-port``
-  10000，gRPC端口，可以使用 ``--listen-grpc-port``

在docker容器上方更改映射时，必须停止（:ref:`stop-a-node`），重置并重新启动。要重置容器，请使用 终端 ``concordium-node-reset-data`` 或 ``docker rm concordium-client`` 在终端中运行。

我们强烈建议您的防火墙应该被配置为只允许在端口8888的公共连接（对等网络的网络端口）。有权访问其他端口的人可能可以控制您的节点或您在该节点上保存的帐户。

.. _stop-a-node:

停止节点
=================

要停止该节点，请按 **CTRL+c** ，然后等待该节点执行干净关闭。

如果您在不显式关闭客户端的情况下意外关闭了窗口，它将在Docker中继续在后台运行。在这种情况下， ``concordium-node-stop`` 以与打开 ``concordium-node`` 可执行文件相同的方式使用二进制文件。

支持与反馈
==================

可以使用该``concordium-node-retrieve-logs`` 工具检索节点的日志信息 。这会将日志从运行映像保存到文件。此外，如果获得许可，它将检索有关系统上当前正在运行的程序的信息。

您可以将日志，系统信息，问题和反馈发送到 testnet@concordium.com。您也可以与我们的  `Discord`_ 联系，或查看我们的问题排查页面 :ref:`troubleshooting page<troubleshooting-and-known-issues>`

