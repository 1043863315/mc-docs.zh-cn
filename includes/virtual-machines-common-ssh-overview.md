---
title: include 文件
description: include 文件
services: virtual-machines-linux
author: rockboyfor
ms.service: virtual-machines-linux
ms.topic: include
origin.date: 12/21/2018
ms.date: 11/11/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 8f0b7c065c12d6788e0c66bdd0941b870f66c524
ms.sourcegitcommit: 5844ad7c1ccb98ff8239369609ea739fb86670a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73831286"
---
## <a name="overview-of-ssh-and-keys"></a>SSH 和密钥的概述

[SSH](https://www.ssh.com/ssh/) 是一种加密的连接协议，利用该协议可以通过未受保护的连接进行安全登录。 SSH 是在 Azure 中托管的 Linux VM 的默认连接协议。 虽然 SSH 本身提供加密连接，但是将密码用于 SSH 连接仍使 VM 易受到强力破解攻击或猜测密码。 使用 SSH 连接到 VM 的更安全且首选的方法是使用公钥-私钥对，也称为 SSH 密钥  。 

* *公钥*放置在 Linux VM 上或者要对其使用公钥加密的任何其他服务中。

* *私钥*仍保留在本地系统上。 请保护好私钥， 不要透露给其他人。

当你使用 SSH 客户端连接到 Linux VM（具有公钥）时，远程 VM 会测试客户端以确保它拥有私钥。 如果客户端具有私钥，则授予其访问 VM 的权限。 

根据组织的安全策略，可重复使用单个公钥-私钥对来访问多个 Azure VM 和服务。 无需对要访问的每个 VM 或服务使用单独的密钥对。 

公钥可与任何人共享；但只有你（或本地安全基础结构）才应拥有私钥。

<!-- Update_Description: wording update -->
