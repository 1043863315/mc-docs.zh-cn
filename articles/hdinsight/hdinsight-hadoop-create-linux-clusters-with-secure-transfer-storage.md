---
title: 在 Azure HDInsight 中使用安全传输存储帐户创建 Hadoop 群集 | Azure
description: 了解如何使用启用安全传输的 Azure 存储帐户创建 HDInsight 群集。
keywords: hadoop 入门,hadoop linux,hadoop 快速入门,安全传输,azure 存储帐户
services: hdinsight
documentationcenter: ''
author: jasonwhowell
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 02/18/2020
ms.date: 03/23/2020
ms.author: v-yiso
ms.openlocfilehash: 919e67e7f13477b1ac2b567ddba29ed68e3e9277
ms.sourcegitcommit: 32997a7d7585deaeb0ab7b8f928d397b18b343fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295854"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>在 Azure HDInsight 中使用安全传输存储帐户创建 Apache Hadoop 群集

[需要安全传输](../storage/common/storage-require-secure-transfer.md)功能强制提交到帐户的所有请求都通过安全连接来进行，从而增强 Azure 存储帐户的安全性。 仅 HDInsight 群集 3.6 或更高版本支持此功能和 wasbs 方案。

> [!IMPORTANT]
> 在创建群集后启用安全存储传输可能会导致使用存储帐户时出错，因此不建议这样做。 最好使用已启用安全传输的存储帐户创建新群集。

## <a name="storage-accounts"></a>存储帐户

### <a name="azure-portal"></a>Azure 门户

默认情况下，在 Azure 门户中创建存储帐户时，会启用“需要安全传输”属性。

若要使用 Azure 门户更新现有存储帐户，请参阅[需要使用 Azure 门户进行安全传输](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)。

### <a name="powershell"></a>PowerShell

对于 PowerShell cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)，请确保将参数 `-EnableHttpsTrafficOnly` 设为 `1`。

若要使用 PowerShell 更新现有存储帐户，请参阅[需要使用 PowerShell 进行安全传输](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)。

### <a name="azure-cli"></a>Azure CLI

对于 Azure CLI 命令 [az storage account create](/cli/storage/account?view=azure-cli-latest#az-storage-account-create)，请确保将参数 `--https-only` 设为 `true`。

若要使用 Azure CLI 更新现有存储帐户，请参阅[需要使用 Azure CLI 进行安全传输](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)。

## <a name="add-additional-storage-accounts"></a>添加其他存储帐户

可以通过多个选项添加其他启用安全传输的存储帐户：

* 修改上一部分的 Azure 资源管理器模板。
* 使用 [Azure 门户](https://portal.azure.cn)创建一个群集，并指定关联的存储帐户。
* 使用脚本操作，将其他启用安全传输的存储帐户添加到现有的 HDInsight 群集。 有关详细信息，请参阅[将其他存储帐户添加到 HDInsight](hdinsight-hadoop-add-storage.md)。

## <a name="next-steps"></a>后续步骤

* 使用 Azure 存储 (WASB) 而非 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) 作为默认数据存储
* 有关 HDInsight 如何使用 Azure 存储的信息，请参阅[将 Azure 存储与 HDInsight 配合使用](hdinsight-hadoop-use-blob-storage.md)。
* 若要了解如何将数据上传到 HDInsight，请参阅[将数据上传到 HDInsight](hdinsight-upload-data.md)。
