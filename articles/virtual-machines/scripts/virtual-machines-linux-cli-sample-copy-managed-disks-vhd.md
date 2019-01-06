---
title: Azure CLI 示例 - 将托管磁盘复制到存储帐户
description: Azure CLI 示例 - 将托管磁盘导出或复制到存储帐户。
services: virtual-machines-linux
documentationcenter: storage
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 09/17/2018
ms.date: 12/24/2018
ms.author: v-yeche
ms.custom: mvc,seodec18
ms.openlocfilehash: fbcf643778dce1f089109efb57b159026d5c5151
ms.sourcegitcommit: 96ceb27357f624536228af537b482df08c722a72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53736097"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>使用 Azure CLI 将托管磁盘导出/复制到存储帐户

此脚本将托管磁盘的基础 VHD 导出到相同或不同区域中的存储帐户。 它首先生成托管磁盘的 SAS URI，然后使用该 SAS URI 将 VHD 复制到存储帐户。 使用此脚本复制托管磁盘以进行区域扩展。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#Provide the subscription Id where managed disk is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where managed disk is created
resourceGroupName=myResourceGroupName

#Provide the managed disk name 
diskName=myDiskName

#Provide Shared Access Signature (SAS) expiry duration in seconds e.g. 3600.
#Know more about SAS here: https://docs.azure.cn/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file of the managed disk. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Provide the name of the destination VHD file to which the VHD of the managed disk will be copied.
destinationVHDFileName=myvhdfilename

az account set --subscription $subscriptionId

sas=$(az disk grant-access --resource-group $resourceGroupName --name $diskName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令生成托管磁盘的 SAS URI，然后使用该 SAS URI 将基础 VHD 复制到存储帐户。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az disk grant-access](https://docs.azure.cn/zh-cn/cli/disk?view=azure-cli-latest#az-disk-grant-access) | 生成只读 SAS，使用该 SAS 可以将基础 VHD 文件复制到存储帐户或将其下载到本地  |
| [az storage blob copy start](https://docs.azure.cn/zh-cn/cli/storage/blob/copy?view=azure-cli-latest#az-storage-blob-copy-start) | 以异步方式将 blob 从一个存储帐户复制到另一个存储帐户 |

## <a name="next-steps"></a>后续步骤

[从 VHD 创建托管磁盘](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[从托管磁盘创建虚拟机](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli/index?view=azure-cli-latest)。

可以在 [Azure Linux VM 文档](../../app-service/app-service-cli-samples.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机和托管磁盘 CLI 脚本示例。

<!-- Update_Description: new articles on virtual machines linux cli samplecopy managed disks vhd -->
<!--ms.date: 10/22/2018-->