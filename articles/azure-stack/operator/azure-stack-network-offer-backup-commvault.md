---
title: 将 Commvault 添加到 Azure Stack 市场 | Microsoft Docs
description: 了解如何将 Commvault 添加到 Azure Stack 市场。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 10/28/2019
ms.date: 11/18/2019
ms.author: v-jay
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 71426435bbe4bc4dc87693872013a6c7fda8357f
ms.sourcegitcommit: 7dfb76297ac195e57bd8d444df89c0877888fdb8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74020425"
---
# <a name="add-commvault-to-the-azure-stack-marketplace"></a>将 Commvault 添加到 Azure Stack 市场

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文逐步讲解如何提供 Commvault Live Sync，以更新位于独立 Azure Stack 缩放单元上的恢复 VM。 可以下载 Commvault 并将其作为用户的备份和复制解决方案提供。 

## <a name="notes-for-commvault"></a>Commvault 说明

- 用户需在其源 Azure Stack 订阅中的 VM 上安装备份和复制软件。 Azure Site Recovery 和 Azure 备份可以提供一个出栈位置来存储备份和恢复映像。 它们都要求先在 Azure 中创建恢复服务保管库，然后才能从以下位置下载将要安装到 Azure Stack 上的软件映像：[Azure 备份服务器](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409)和 [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus)。  
    
- 可能需要第三方软件（如果已选择）的许可证。
- 用户可能需要帮助才能通过备份和复制主机上的虚拟网关 (VPN) 或公共 IP 连接其源和目标。
- 以 Azure 云订阅或恢复目标 Azure Stack 上的订阅为目标。
- 以恢复目标 Azure Stack 上的资源组和 Blob 存储帐户为目标。
- 某些解决方案要求在目标订阅中创建需要 365 天全天候运行的虚拟机才能从源服务器接收更改。 在[使用 Commvault 在 Azure Stack 上备份 VM](../user/azure-stack-network-howto-backup-commvault.md)中，Commvault Live Sync 在初始配置过程中创建目标恢复 VM，并让其一直处于空闲（不运行、不收费）状态，直到需要在复制周期应用更改。


## <a name="get-commvault-for-your-marketplace"></a>获取用于市场的 Commvault

1. 打开 Azure Stack 管理门户。
2. 选择“市场管理” > “从 Azure 添加”。  

    ![用于 Azure Stack 的 Commvault](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. 输入 `commvault`。
4. 选择“Commvault 跟踪”。  然后选择“下载”。 


## <a name="next-steps"></a>后续步骤

[使用 Commvault 在 Azure Stack 上备份 VM](../user/azure-stack-network-howto-backup-commvault.md)

[在 Azure Stack 中提供服务概述](service-plan-offer-subscription-overview.md)
