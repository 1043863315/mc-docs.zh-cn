---
title: "Azure MFA 版本和使用计划 | Microsoft 文档"
description: "有关多重身份验证客户端以及可用的方法和版本的信息。 有关每个使用计划的详细信息"
keywords: 
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: a114d832e9c5320e9a109c9020fcaa2f2fdd43a9
ms.openlocfilehash: 4863509884ba0d707f77457a22979396ee71648d
ms.lasthandoff: 04/14/2017

---

# <a name="how-to-get-azure-multi-factor-authentication"></a>如何获取 Azure 多重身份验证

如果想要保护帐户，应该在组织中标配双重验证。 对资源拥有访问特权的管理帐户尤其需要此功能。 为此，Microsoft 为 Office 365 和 Azure 管理员提供了基本的双重验证功能。 如果想要升级管理员的功能，或者将双重验证扩展到其他用户，可以购买 Azure 多重身份验证。 

本文说明提供给管理员的版本与完整 Azure MFA 版本之间的差别，并介绍每个版本提供的功能。 如果已准备好部署完整的 Azure MFA 产品，可在后续部分中了解实施选项，以及 Microsoft 如何计算使用量。

>[!IMPORTANT]
>本文旨在指导用户如何以不同的方式购买 Azure 多重身份验证。 有关定价和计费的具体详细信息，请始终参阅[多重身份验证定价页](/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure 多重身份验证版本

下表介绍了多重身份验证的三个版本之间的差别：

| 版本 | 说明 |
| --- | --- |
| 适用于 Office 365 的多重身份验证 |此版本专门与 Office 365 应用程序配合使用，可以从 Office 365 门户进行管理。 管理员可以[使用双重验证来保护 Office 365 资源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 此版本是 Office 365 订阅的一部分。 |
| 面向 Azure 管理员的多重身份验证 | Azure 管理员可以免费为其管理员帐户启用双重验证。|
| Azure 多重身份验证 | Azure 多重身份验证（通常称为“完整”版本）提供了最丰富的功能集。 它通过 [Azure 经典管理门户](https://manage.windowsazure.cn)、高级报告以及对一系列本地和云应用程序的支持，提供额外的配置选项。 Azure 多重身份验证已随附在 Azure Active Directory Premium（P1 与 P2 计划）和企业移动性 + 安全性（E3 和 E5 计划）中，可[在云中或本地](multi-factor-authentication-get-started.md)部署。 |

## <a name="feature-comparison-of-versions"></a>版本功能比较
下表提供了 Azure 多重身份验证的各个版本中可用的功能列表。

> [!NOTE]
> 此比较表讨论了每个版本的多重身份验证的部分功能。 如果拥有完整的 Azure 多重身份验证服务，某些功能可能不可用，具体取决于是否在云中使用 [MFA 或本地 MFA](multi-factor-authentication-get-started.md)。


| 功能 | 适用于 Office 365 的多重身份验证 | 面向 Azure 管理员的多重身份验证 | Azure 多重身份验证 |
| --- |:---:|:---:|:---:|
| 使用 MFA 保护管理员帐户 |● |● （仅适用于 Azure 管理员帐户） |● |
| 将移动应用用作第二个因素 |● |● |● |
| 将电话呼叫用作第二个因素 |● |● |● |
| 将短信用作第二个因素 |● |● |● |
| 不支持 MFA 的客户端的应用密码 |● |● |● |
| 管理员控制验证方法 |● |● |● |
| PIN 模式 | | |● |
| 欺诈警报 | | |● |
| MFA 报告 | | |● |
| 一次性跳过 | | |● |
| 通话的自定义问候语 | | |● |
| 通话的自定义呼叫方 ID | | |● |
| 受信任的 IP | | |● |
| 记住受信任的设备的 MFA |● |● |● |
| MFA SDK | | |●（需要多重身份验证提供程序和完整的 Azure 订阅） |
| 适用于本地应用程序的 MFA | | |● |

## <a name="next-steps"></a>后续步骤

- 有关定价详细信息，请参阅 [Azure MFA 定价](/pricing/details/multi-factor-authentication/)。

- 选择是要将 Azure MFA 部署[在云中还是本地](multi-factor-authentication-get-started-cloud.md)

