---
author: rothja
ms.service: cost-management-billing
ms.topic: include
origin.date: 11/09/2018
ms.date: 03/23/2020
ms.author: v-tawe
ms.openlocfilehash: c417f31cf315966d14b64542e2ac1754e389d744
ms.sourcegitcommit: 4ba6d7c8bed5398f37eb37cf5e2acafcdcc28791
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79133788"
---
下表列出了适用于 Azure IoT 中心设备预配服务资源的限制。

| 资源 | 限制 |
| --- | --- |
| 每个 Azure 订阅的最大设备预配服务数 | 10 个 |
| 最大登记数 | 1,000,000 |
| 最大注册数 | 1,000,000 |
| 最大登记组数 | 100 |
| 最大 CA 数 | 25 |
| 链接的 IoT 中心的最大数量 | 50 |
| 消息的最大大小 | 96 KB|

> [!NOTE]
> 若要增加预配服务上的登记和注册数量，请联系 [Azure 支持](https://www.azure.cn/support/contact/)。

> [!NOTE]
> 增加 CA 的最大数目不受支持。

超过以下配额时，设备预配服务将限制请求。

| 限制 | 每单位值 |
| --- | --- |
| 操作 | 200/分钟/服务 |
| 设备注册数 | 200/分钟/服务 |
| 设备轮询操作 | 5/10 秒/设备 |
