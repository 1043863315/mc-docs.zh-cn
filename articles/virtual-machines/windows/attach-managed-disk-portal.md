---
title: 将托管数据磁盘附加到 Windows VM - Azure | Azure
description: 如何使用 Azure 门户将托管数据磁盘附加到 Windows VM
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
origin.date: 10/08/2018
ms.date: 12/24/2018
ms.author: v-yeche
ms.openlocfilehash: 71783c58e7fde31aae476b3b052841726bde8a9d
ms.sourcegitcommit: 96ceb27357f624536228af537b482df08c722a72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53736093"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>使用 Azure 门户将托管数据磁盘附加到 Windows VM

本文介绍如何使用 Azure 门户将新的托管数据磁盘附加到 Windows 虚拟机 (VM)。 VM 的大小决定了可以附加的数据磁盘数量。 有关详细信息，请参阅[虚拟机的大小](sizes.md)。

## <a name="add-a-data-disk"></a>添加数据磁盘

1. 在 [Azure 门户](https://portal.azure.cn)的左侧菜单中选择“虚拟机”。
2. 从列表中选择一个虚拟机。
3. 在“虚拟机”页上，选择“磁盘”。
4. 在“磁盘”页上，选择“添加数据磁盘”。
5. 在新磁盘的下拉列表中，选择“创建磁盘”。
6. 在“创建托管磁盘”页面中，键入磁盘名称，并根据需要调整其他设置。 完成操作后，选择“创建”。
7. 在“磁盘”页中，选择“保存”以保存 VM 的新磁盘配置。
8. 在 Azure 创建磁盘并将磁盘附加到虚拟机之后，新磁盘出现在“数据磁盘”下的虚拟机磁盘设置中。

## <a name="initialize-a-new-data-disk"></a>初始化新的数据磁盘

1. 连接到 VM。
1. 在正在运行的 VM 中选择 Windows“开始”菜单，然后在搜索框中输入 **diskmgmt.msc**。 “磁盘管理”控制台随即打开。
2. “磁盘管理”将识别出空的未初始化磁盘；同时会显示“初始化磁盘”窗口。
3. 确认已选择新磁盘，然后选择“确定”将其初始化。
4. 新磁盘显示为“未分配”。 右键单击磁盘上任意位置，选择“新建简单卷”。 “新建简单卷向导”窗口随即打开。
5. 完成向导中的每一步，保留所有默认值，完成后，选择“完成”。
6. 关闭“磁盘管理”。
7. 此时会显示一个弹出窗口，告知需要先格式化新磁盘，然后才能使用该磁盘。 选择“格式化磁盘”。
8. 在“格式化新磁盘”窗口中检查设置，然后选择“开始”。
9. 此时会显示一条警告，告知格式化磁盘会清除所有数据。 选择“确定” 。
10. 格式化完成后，选择“确定”。

## <a name="next-steps"></a>后续步骤

- 还可以[使用 PowerShell 附加数据磁盘](attach-disk-ps.md)。
- 如果应用程序需要使用 *D:* 盘存储数据，可以[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md?toc=%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

<!-- Update_Description: update meta properties, wording update -->