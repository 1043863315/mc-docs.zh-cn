---
title: Azure Stack 中的存储帐户 | Microsoft Docs
description: 了解如何创建 Azure Stack 存储帐户。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
origin.date: 05/16/2019
ms.date: 07/29/2019
ms.author: v-jay
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: cf6fdb65d3640ce069429270592183b4df770834
ms.sourcegitcommit: 4d34571d65d908124039b734ddc51091122fa2bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68513431"
---
# <a name="storage-accounts-in-azure-stack"></a>Azure Stack 中的存储帐户

Azure Stack 中的存储帐户包括 Blob 和表服务，以及你的存储数据对象的唯一命名空间。 默认情况下，只有你，即存储帐户所有者，才能使用帐户中的数据。

1. 在 Azure Stack POC 计算机上，以[管理员](../asdk/asdk-connect.md)身份登录到 `https://adminportal.local.azurestack.external`，然后单击“+ 创建资源” > “数据 + 存储” > “存储帐户”。   

   ![创建存储帐户](media/azure-stack-provision-storage-account/image01.png)
2. 在“创建存储帐户”  边栏选项卡中，键入存储帐户的名称。 创建新**资源组**或选择现有的资源组，然后单击“创建”以创建存储帐户。 

   ![查看存储帐户](media/azure-stack-provision-storage-account/image02.png)
3. 若要查看新存储帐户，请单击“所有资源”，然后搜索该存储帐户并单击其名称。 

    ![你的存储帐户名称](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>后续步骤
[使用 Azure Resource Manager 模板](../user/azure-stack-arm-templates.md)

[了解 Azure 存储帐户](/storage/common/storage-create-storage-account)

[下载与 Azure 一致的 Azure Stack 存储验证指南](https://aka.ms/azurestacktp1doc)
