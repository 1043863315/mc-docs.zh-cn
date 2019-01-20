---
title: 准备目标环境以便将 VMware 复制到 Azure | Azure
description: 本文介绍如何准备目标 Azure 环境，以便将 VMware VM 复制到 Azure。
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: article
origin.date: 11/27/2018
ms.date: 01/21/2019
ms.author: v-yeche
ms.openlocfilehash: 15d21adcd86e98e3bf3e9fcd99d20c52c5adf6db
ms.sourcegitcommit: 26957f1f0cd708f4c9e6f18890861c44eb3f8adf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54363492"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>准备目标环境，以便将 VMware VM 或物理服务器灾难恢复到 Azure

本文介绍如何准备目标 Azure 环境，以便开始将 VMware 虚拟机或物理服务器复制到 Azure。

## <a name="prerequisites"></a>先决条件

本文假设：
- 已在 [Azure 门户](http://portal.azure.cn "Azure 门户")上创建恢复服务保管库，用于保护源计算机
- 已设置本地环境，用于将源 [VMware 虚拟机](vmware-azure-set-up-source.md)或[物理服务器](physical-azure-set-up-source.md)复制到 Azure。

## <a name="prepare-target"></a>准备目标

完成“步骤 1: 选择保护目标”和“步骤 2: 准备源”后，你将转到“步骤 3: 目标”

![准备目标](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **订阅：** 从下拉菜单中，选择要将虚拟机或物理服务器复制到的“订阅”。
2. **部署模型：** 选择部署模型（“经典”或“资源管理器”）

根据选择的部署模型，运行验证以确保目标订阅中至少具有一个兼容的存储帐户和虚拟网络，以将虚拟机或物理服务器复制并故障转移到此目标订阅中。

成功完成验证后，单击“确定”转到下一步。

如果你没有兼容的资源管理器存储帐户或虚拟网络，则可单击页面顶部的“+ 存储帐户”或“+ 网络”按钮来创建一个。

## <a name="next-steps"></a>后续步骤
[配置复制设置](vmware-azure-set-up-replication.md)。

<!-- Update_Description: update meta properties, wording update -->
