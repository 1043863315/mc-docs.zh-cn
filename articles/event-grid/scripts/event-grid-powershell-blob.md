---
title: Azure PowerShell - 订阅 Blob 存储帐户
description: 本文提供了一个示例 Azure PowerShell 脚本，演示如何订阅 Blob 存储帐户的事件网格事件。
services: event-grid
documentationcenter: na
author: lingliw
manager: digimobile
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 01/23/2020
ms.date: 3/16/2020
ms.author: v-lingwu
ms.openlocfilehash: 354807c63a495f0ed2dfeb4563e92a0756231fc5
ms.sourcegitcommit: 7995ca87e9e10388948f714f94c61d66880f3bb3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2020
ms.locfileid: "79452476"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>使用 PowerShell 订阅 Blob 存储帐户的事件

此脚本创建 Blob 存储帐户事件的事件网格订阅。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>示例脚本 - 稳定版

<!--[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]-->

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/get-started-azureps)。
