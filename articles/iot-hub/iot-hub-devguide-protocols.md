---
title: Azure IoT 中心通信协议和端口 | Azure
description: 开发人员指南 - 介绍了设备到云和云到设备通信支持的通信协议和必须打开的端口号。
author: dominicbetts
manager: timlt
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 01/29/2018
ms.author: v-yiso
ms.date: 04/06/2020
ms.openlocfilehash: 7f7ab4303c6a5344b6000868dd375cf1ba9ebbb1
ms.sourcegitcommit: 6ddc26f9b27acec207b887531bea942b413046ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80343589"
---
# <a name="reference---choose-a-communication-protocol"></a>参考 - 选择通信协议

IoT 中心允许设备使用以下协议进行设备端通信：

* [MQTT][lnk-mqtt]
* 基于 WebSocket 的 MQTT
* [AMQP][lnk-amqp]
* 基于 WebSockets 的 AMQP
* HTTPS

若要了解这些协议如何支持特定的 IoT 中心功能，请参阅[设备到云通信指南][lnk-d2c-guidance]和[云到设备通信指南][lnk-c2d-guidance]。

下表提供了针对协议选取的高水平建议：

| 协议 | 何时应选择此协议 |
| --- | --- |
| MQTT <br> 基于 WebSocket 的 MQTT |用于无需使用相同的 TLS 连接来连接多台设备（各有自己的设备凭据）的所有设备。 |
| AMQP <br> 基于 WebSocket 的 AMQP |用于利用跨设备连接复用的字段和云网关。 |
| HTTPS |用于不可支持其他协议的设备。 |

在选择设备端通信协议时，请考虑以下几点：

* **云到设备模式**。 HTTPS 没有用于实现服务器推送的有效方法。 因此，使用 HTTPS 时，设备会在 IoT 中心轮询从云到设备的消息。 此方法对于设备和 IoT 中心而言是低效的。 根据当前 HTTPS 准则，每台设备应每 25 分钟或更长时间轮询一次消息。 MQTT 和 AMQP 支持在收到云到设备的消息时进行服务器推送。 它们会启用从 IoT 中心到设备的直接消息推送。 如果传送延迟是考虑因素，最好使用 MQTT 或 AMQP 协议。 对于很少连接的设备，HTTPS 也适用。
* **现场网关**。 使用 MQTT 和 HTTPS 时，无法使用相同的 TLS 连接来连接多台设备（各有自己的设备凭据）。 对于需要对每个已连接设备在现场网关和 IoT 中心之间建立一个 TLS 连接的[现场网关方案][lnk-azure-gateway-guidance]，这些协议并不理想。
* **低资源设备**。 相比 AMQP 库的占用空间，MQTT 和 HTTPS 库的占用空间更小。 因此，如果设备的资源很少（如低于 1 MB RAM），可能只可实现这些协议。
* **网络遍历**。 标准 AMQP 协议使用端口 5671，而 MQTT 侦听端口 8883。 使用这些端口可能会给未向非 HTTPS 协议开放的网络带来问题。 在此情况下，使用基于 WebSockets 的 MQTT、基于 WebSockets 的 AMQP 或者 HTTPS。
* **有效负载大小**。 MQTT 和 AMQP 是二进制协议，因此，其有效负载比 HTTPS 的有效负载更精简。

> [!WARNING]
> 使用 HTTPS 时，每台设备应每 25 分钟或更短时间轮询一次云到设备消息。 在开发中，每台设备都可以根据需要更频繁地轮询。

## <a name="port-numbers"></a>端口号

设备可在 Azure 中使用各种协议来与 IoT 中心通信。 通常，选择的协议根据解决方案的具体要求而定。 下表列出了必须打开的、使设备能够使用特定协议的出站端口：

| 协议 | 端口 |
| --- | --- |
| MQTT |8883 |
| 基于 WebSocket 的 MQTT |443 |
| AMQP |5671 |
| 基于 WebSockets 的 AMQP |443 |
| HTTPS |443 |

在 Azure 区域创建 IoT 中心后，该 IoT 中心在其生存期内会保留同一 IP 地址。 但如果 Microsoft 将 IoT 中心移到其他缩放单元以保持服务质量，则向其分配新的 IP 地址。


## <a name="next-steps"></a>后续步骤

若要详细了解 IoT 中心如何实现 MQTT 协议，请参阅[使用 MQTT 协议与 IoT 中心通信][lnk-mqtt-support]。

[lnk-d2c-guidance]: ./iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: ./iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: ./iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: ./iot-hub-devguide-endpoints.md#field-gateways
