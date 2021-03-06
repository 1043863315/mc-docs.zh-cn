---
title: 检查存储帐户的“上次同步时间”属性
titleSuffix: Azure Storage
description: 了解如何检查异地复制存储帐户的“上次同步时间”  属性。 “上次同步时间”  属性表示，最近一次将主要区域中的所有写入数据成功写入到次要区域的时间。
services: storage
author: WenJason
ms.service: storage
ms.topic: how-to
origin.date: 01/16/2019
ms.date: 03/09/2020
ms.author: v-jay
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 4e66b2ba71daf7c086a88b2981152e95c31849d2
ms.sourcegitcommit: fbc7584f403417d3af7bd6bbbaed7c13a78c57b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412538"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>检查存储帐户的“上次同步时间”属性

在配置存储帐户时，可以指定将数据复制到距离主要区域数百英里的次要区域。 异地复制在主要区域发生严重中断（例如自然灾害）时为你的数据提供持续性。 如果你额外启用对次要区域的读取访问，则当主要区域不可用时，你的数据仍可以用于读取操作。 你可以将应用程序设计为当主要区域无响应时无缝切换到从次要区域进行读取。

异地冗余存储 (GRS) 以异步方式将数据复制到次要区域。 若要对次要区域进行读取访问，请启用读取访问异地冗余存储 (RA-GRS)。 若要详细了解 Azure 存储提供的冗余的各种选项，请参阅 [Azure 存储冗余](storage-redundancy.md)。

本文介绍了如何检查存储帐户的“上次同步时间”  属性，以便评估主要区域与次要区域之间的任何差异。

## <a name="about-the-last-sync-time-property"></a>关于“上次同步时间”属性

因为异地复制是异步的，所以在发生中断时，已写入到主要区域的数据可能尚未写入到次要区域。 “上次同步时间”  属性表示，最近一次将主要区域中的数据成功写入到次要区域的时间。 在上次同步时间之前对主要区域所做的所有写入都可以从次要位置读取。 在上次同步时间之后对主要区域所做的写入尚不一定可供读取。

“上次同步时间”  属性是一个 GMT 日期/时间值。

## <a name="get-the-last-sync-time-property"></a>获取“上次同步时间”属性

可以使用 PowerShell 或 Azure CLI 检索“上次同步时间”  属性的值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 获取存储帐户的上次同步时间，请安装可支持获取异地复制统计信息的 Azure 存储预览版模块。例如：

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

然后检查存储帐户的 **GeoReplicationStats.LastSyncTime** 属性。 请务必将占位符值替换为你自己的值：

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 获取存储帐户的上次同步时间，请检查存储帐户的 **geoReplicationStats.lastSyncTime** 属性。 使用 `--expand` 参数返回在 **geoReplicationStats** 下嵌套的属性的值。 请务必将占位符值替换为你自己的值：

```azurecli
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>另请参阅

- [Azure 存储冗余](storage-redundancy.md)
- [更改存储帐户的冗余选项](redundancy-migration.md)
- [使用读取访问异地冗余存储设计高度可用的应用程序](storage-designing-ha-apps-with-ragrs.md)