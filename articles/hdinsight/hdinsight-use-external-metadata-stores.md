---
title: 使用外部元数据存储 - Azure HDInsight
description: 将外部元数据存储与 Azure HDInsight 群集一起使用以及最佳做法。
services: hdinsight
documentationcenter: ''
author: jasonwhowell
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 03/02/2020
ms.author: v-yiso
ms.date: 04/06/2020
ms.openlocfilehash: d4d7314b6ed02979d0224d55d5a8f8921c34e6b9
ms.sourcegitcommit: 6ddc26f9b27acec207b887531bea942b413046ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80343613"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>使用外部元数据存储 - Azure HDInsight

可以通过 HDInsight 来控制数据和元数据，方法是：将关键元数据解决方案和管理数据库部署到外部数据存储。 此功能目前适用于 [Apache Hive 元存储](#custom-metastore)、[Apache Oozie 元存储](#apache-oozie-metastore)和 [Apache Ambari 数据库](#custom-ambari-db)。

HDInsight 中的 Apache Hive 元存储是 Apache Hadoop 体系结构的必备部分。 元存储是可供其他大数据访问工具（例如 Apache Spark、交互式查询 (LLAP)、Presto 或 Apache Pig）使用的中央架构存储库。 HDInsight 使用 Azure SQL 数据库作为 Hive 元存储。

![HDInsight Hive 元数据存储体系结构](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

可使用以下两种方式为 HDInsight 存储设置元存储：

* [默认元存储](#default-metastore)
* [自定义元存储](#custom-metastore)

## <a name="default-metastore"></a>默认元存储

默认情况下，HDInsight 为每一种群集类型创建一个元存储。 转而可指定自定义元存储。 默认元存储包括以下注意事项：
- 没有任何额外费用。 HDInsight 会为每个群集类型创建一个元存储，而不额外产生任何费用。
- 每个默认元存储都是群集生命周期的一部分。 删除群集时，也会删除相应的该元存储和元数据。
- 不可与其他群集共享默认元存储。
- 默认元存储使用基本 Azure SQL DB，它具有 5 个 DTU（数据库事务单位）的限制。
此默认元存储通常用于相对简单的工作负荷，这种工作负荷无需使用多个群集，且无需在群集生命周期结束后继续保留元数据。


## <a name="custom-metastore"></a>自定义元存储

HDInsight 还支持自定义元存储，建议对生产群集使用此项：
- 将自己的 Azure SQL 数据库指定为元存储。
- 元存储的生命周期不由群集生命周期决定，因此可创建和删除群集，而不会丢失元数据。 即使删除和重新创建 HDInsight 群集之后，系统仍然保留 Hive 架构等元数据。
- 通过自定义元存储，可将多个群集和群集类型附加到元存储。 例如，可跨交互式查询、Hive 和 HDInsight 中的群集的 Spark 共享单个元存储。
- 根据所选的性能级别收取元存储 (Azure SQL DB) 的费用。
- 可按需增加元存储。
- 群集和外部元存储必须托管在同一区域中。

![HDInsight Hive 元数据存储使用案例](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>针对自定义元存储创建并配置 Azure SQL 数据库

在为 HDInsight 群集设置自定义 Hive 元存储之前，需创建 Azure SQL 数据库或有一个现有的 Azure SQL 数据库。  有关详细信息，请参阅[快速入门：在 Azure SQL DB 中创建单一数据库](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)。

若要确保 HDInsight 群集能够访问连接的 Azure SQL 数据库，请配置 Azure SQL 数据库防火墙规则，允许 Azure 服务和资源访问服务器。

可以在 Azure 门户中启用此选项，方法是：单击“设置服务器防火墙”，  然后在“允许 Azure 服务和资源访问此服务器”下单击“启用”（针对 Azure SQL 数据库服务器或数据库）。   有关详细信息，请参阅[创建和管理 IP 防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![“设置服务器防火墙”按钮](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![允许 Azure 服务访问](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>在群集创建期间选择自定义元存储

可在群集创建期间将群集指向之前所创建的 Azure SQL 数据库，还可在创建群集之后配置 SQL 数据库。 通过 Azure 门户创建新的 Hadoop、Spark 或交互式 Hive 群集时，依次访问“存储”和“元存储”  设置来指定此选项。

![HDInsight Hive 元数据存储 Azure 门户](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Hive 元存储最佳做法

下面是一些常见的 HDInsight Hive 元存储最佳做法：

- 尽可能使用自定义元存储来帮助分离计算资源（正在运行的群集）和元数据（存储在元存储中）。
- 首先使用 S2 层，它提供 50 DTU 和 250 GB 的存储空间。 如果空间不够，可扩大数据库。
- 如果你希望多个 HDInsight 群集访问单独的数据，请对每个群集上的元存储使用单独的数据库。 如果在多个 HDInsight 群集之间共享元存储，则意味着这些群集将使用相同的元数据和底层用户数据文件。
- 请定期备份自定义元存储。 Azure SQL 数据库会自动生成备份，但备份保留时间范围会有所不同。 有关详细信息，请参阅[了解 SQL 数据库自动备份](../sql-database/sql-database-automated-backups.md)。
- 使元存储和 HDInsight 群集位于同一区域中，以获得最高性能和最低网络出口费用。
- 使用 Azure SQL 数据库监视工具（例如 Azure 门户或 Azure Monitor 日志）监视元存储的性能和可用性。
- 当针对现有的自定义元存储数据库创建一个新的更高版本的 Azure HDInsight 时，系统将升级元存储的架构，该架构在不从备份中还原数据库的情况下是不可逆的。
- 如果在多个群集之间共享元存储，请确保所有群集都具有相同的 HDInsight 版本。 不同的 Hive 版本使用不同的元存储数据库架构。 例如，不能在具有 Hive 1.2 版本的群集和具有 Hive 2.1 版本的群集之间共享元存储。 
- 在 HDInsight 4.0 中，Spark 和 Hive 使用独立目录来访问 SparkSQL 或 Hive 表。 Spark 创建的表位于 Spark 目录中。 Hive 创建的表位于 Hive 目录中。 这与 HDInsight 3.6 不同，在 HDInsight 3.6 中，Hive 和 Spark 共享公共目录。 HDInsight 4.0 中的 Hive 和 Spark 集成依赖于 Hive 仓库连接器 (HWC)。 HWC 在 Spark 和 Hive 之间起到桥梁作用。 [了解 Hive 仓库连接器](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)。

##  <a name="apache-oozie-metastore"></a>Apache Oozie 元存储

Apache Oozie 是一个管理 Hadoop 作业的工作流协调系统。  Oozie 支持对 Apache MapReduce、Pig 和 Hive 等模型执行 Hadoop 作业。  Oozie 使用元存储来存储当前工作流及历史工作流的相关详细信息。 可使用 Azure SQL 数据库作为自定义元存储，提高使用 Oozie 时的性能。 删除群集后，还可通过云存储访问 Oozie 作业数据。

若要了解如何使用 Azure SQL 数据库创建 Oozie 元存储，请参阅[使用 Apache Oozie 处理工作流](hdinsight-use-oozie-linux-mac.md)。

## <a name="custom-ambari-db"></a>自定义 Ambari DB

若要在 Apache Ambari on HDInsight 上使用自己的外部数据库，请参阅[自定义 Apache Ambari 数据库](hdinsight-custom-ambari-db.md)。

## <a name="next-steps"></a>后续步骤

- [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他组件在 HDInsight 中设置群集](./hdinsight-hadoop-provision-linux-clusters.md)
