---
title: 快速入门：在 Azure HDInsight 中执行 Apache Hive 查询 - Apache Zeppelin
description: 了解如何使用 Apache Zeppelin 运行 Apache Hive 查询。
keywords: hdinsight,hadoop,hive,交互式查询,LLAP
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 05/06/2019
ms.date: 06/10/2019
ms.author: v-yiso
ms.openlocfilehash: 144c36a0ac3cc183403e644b585fa4f4a2a6e9f8
ms.sourcegitcommit: e9c62212a0d1df1f41c7f40eb58665f4f1eaffb3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68878638"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>快速入门：使用 Apache Zeppelin 在 Azure HDInsight 中执行 Apache Hive 查询

本快速入门介绍如何使用 Apache Zeppelin 在 Azure HDInsight 中运行 [Apache Hive](https://hive.apache.org/) 查询。 HDInsight 交互式查询群集包括可用来运行交互式 Hive 查询的 [Apache Zeppelin](https://zeppelin.apache.org/) 笔记本。

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="prerequisites"></a>先决条件

一个 HDInsight 交互式查询群集。 若要创建 HDInsight 群集，请参阅[创建群集](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。  请确保选择“交互式查询”群集类型。 

## <a name="create-an-apache-zeppelin-note"></a>创建 Apache Zeppelin 笔记

1. 请将以下 URL 中的 `CLUSTERNAME` 替换为你的群集的名称：`https://CLUSTERNAME.azurehdinsight.cn/zeppelin`。 然后在 Web 浏览器中输入该 URL。

2. 输入群集登录用户名和密码。 在 Zeppelin 页中，可以创建新笔记，也可以打开现有笔记。 **HiveSample** 包含一些示例 Hive 查询。  

    ![HDInsight 交互式查询 zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. 选择“创建新笔记”。 

4. 在“创建新笔记”对话框中，键入或选择以下值： 

    - 笔记名称：输入笔记的名称。
    - 默认解释器：从下拉列表中选择“jdbc”。 

5. 选择“创建笔记”  。

6. 在代码部分输入以下 Hive 查询，然后按 **Shift + Enter**：

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight 交互式查询 zeppelin 运行查询](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    第一行中的 **%jdbc(hive)** 语句告诉笔记本使用 Hive JDBC 解释程序。

    该查询将返回一个名为 **hivesampletable** 的 Hive 表。

    以下是可以针对 **hivesampletable** 运行的两个附加 Hive 查询：

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    与传统 Hive 相比，返回查询结果的速度更快。

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要支付 HDInsight 群集费用，即使未使用。 由于群集费用高于存储空间费用数倍，因此在不使用群集时将其删除可以节省费用。

若要删除群集，请参阅[使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Apache Zeppelin 在 Azure HDInsight 中运行 Apache Hive 查询。 若要详细了解 Hive 查询，请参阅下一篇文章，其中介绍了如何使用 Visual Studio 执行查询。

> [!div class="nextstepaction"]
> [使用适用于 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="see-also"></a>另请参阅

* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Apache Hive 数据](../hadoop/apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Apache Hive 数据](./apache-hadoop-connect-hive-power-bi-directquery.md)。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 将 Excel 连接到 Apache Hadoop](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](../hdinsight-upload-data.md)。
