---
title: 排查 Azure 中的 Windows 虚拟机部署问题 | Azure
description: 排查在 Azure 资源管理器部署模型中部署 Windows 虚拟机时遇到的问题。
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
origin.date: 11/01/2018
ms.date: 05/20/2019
ms.author: v-yeche
ms.openlocfilehash: 9540181d9cd74b03ae33f7b1fe83c7d3f9e4e3ed
ms.sourcegitcommit: bf4afcef846cc82005f06e6dfe8dd3b00f9d49f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66004131"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>排查 Azure 中的 Windows 虚拟机部署问题

若要排查 Azure 中虚拟机 (VM) 的部署问题，请查看[常见问题](#top-issues)了解常见故障和解决方法。

如果对本文中的任何观点存在疑问，可以联系 [MSDN Azure 和 CSDN Azure](https://www.azure.cn/support/forums/)上的 Azure 专家。 或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://www.azure.cn/support/contact/)并选择“获取支持”。 

## <a name="top-issues"></a>常见问题
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>群集不支持请求的 VM 大小
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- 使用更小的 VM 大小来重试请求。
- 如果无法更改请求的 VM 大小：
    - 停止可用性集中的所有 VM。 依次单击“资源组”  > 资源组 >“资源”  > 可用性集 >“虚拟机”  > 虚拟机 >“停止”  。
    - 在所有 VM 都停止后，创建相应大小的 VM。
    - 先启动新 VM，再选择所有已停止的 VM 并单击“启动”。

## <a name="the-cluster-does-not-have-free-resources"></a>群集没有可用资源
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- 请稍后重试请求。
- 如果新 VM 属于不同的可用性集
    - 在不同的可用性集（位于同一区域）中创建 VM。
    - 将新 VM 添加到同一虚拟网络。

<!--Not Available ## How can I use and deploy a windows client image into Azure?-->
## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>如何使用混合使用权益 (HUB) 部署虚拟机？

有多种不同方法可使用 Azure 混合使用权益部署 Windows 虚拟机。

对于企业协议订阅：

•   可从通过 Azure 混合使用权益预配的特定市场映像中部署 VM。

对于企业协议：

•   可以上传自定义 VM，并使用 Resource Manager 模板或 Azure PowerShell 进行部署。

有关详细信息，请参阅以下资源：

 - [Azure 混合使用权益概述](https://www.azure.cn/pricing/hybrid-use-benefit/)

 - [可下载的常见问题解答](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [适用于 Windows Server 和 Windows 客户端的 Azure 混合使用权益](../windows/hybrid-use-benefit-licensing.md)。

 - [如何在 Azure 中使用混合使用权益](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

<!--Not Available ## How do I activate my monthly credit for Visual studio Enterprise (BizSpark)-->
<!--Not Available ## How to add Enterprise Dev/Test to my Enterprise Agreement (EA) to get access to Window client images?-->
## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>我的 Windows N 系列 VM 缺少驱动程序

基于 Windows 的 VM 的驱动程序位于[此处](../windows/n-series-driver-setup.md)。

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>在我的 N 系列 VM 中找不到 GPU 实例

若要利用运行 Windows Server 2016 或 Windows Server 2012 R2 的 Azure N 系列 VM 的 GPU 功能，在部署后必须在每个 VM 上安装 NVIDIA 图形驱动程序。 可获取 [Windows VM](../windows/n-series-driver-setup.md) 和 [Linux VM](../linux/n-series-driver-setup.md) 的驱动程序安装信息。

## <a name="is-n-series-vms-available-in-my-region"></a>我所在的区域是否支持 N 系列 VM？

有关可用性，可以参阅[可用产品（按区域）表](https://www.azure.cn/zh-cn/home/features/products-by-region)；有关定价，可以单击[此处](https://www.azure.cn/pricing/details/virtual-machines/)。

<!--Not Available ## What client images can I use and deploy in Azure, and how to I get them?-->
## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>重设 VM 大小时，我看不到所需的 VM 大小系列。

当 VM 正在运行时，将其部署到物理服务器。 Azure 区域中的物理服务器被分在常见物理硬件群集组中。 需要将 VM 移到其他硬件群集才能重设 VM 大小，具体操作因部署 VM 所用部署模型而异。

- 对于在经典部署模型中部署的 VM，必须删除并重新部署云服务部署，才能将 VM 大小更改为其他大小系列。

- 对于在资源管理器部署模型中部署的 VM，必须先停止可用性集中的所有 VM，然后才能更改可用性集中任何 VM 的大小。

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>在可用性集中部署时，列出的 VM 大小不受支持。

请选择可用性集的群集支持的大小。 建议在创建可用性集时，选择所需要的最大 VM 大小，并将其作为到可用性集的第一次部署。

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>是否可以将现有的经典 VM 添加到可用性集？

是的。 可以将现有经典 VM 添加到新的或现有的可用性集。 有关详细信息，请参阅[将现有虚拟机添加到可用性集](../windows/classic/configure-availability-classic.md#addmachine)。

## <a name="next-steps"></a>后续步骤
如果对本文中的任何观点存在疑问，可以联系 [MSDN Azure 和 CSDN Azure](https://www.azure.cn/support/forums/)上的 Azure 专家。

或者，也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://www.azure.cn/support/contact/)并选择 **获取支持**。

<!--Update_Description: update meta properties, wording update -->
