---
title: include 文件
description: include 文件
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 01a27d31e29ebeea636bab64f474204ac71db331
ms.sourcegitcommit: dcd11929ada5035d127be1ab85d93beb72909dc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833220"
---
可使用 [az network vpn-connection show](/cli/network/vpn-connection) 命令来验证连接是否成功。 在此示例中，“--name”是指要测试的连接的名称。 当连接处于建立过程中时，连接状态会显示“正在连接”。 建立连接后，状态将更改为“已连接”。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
