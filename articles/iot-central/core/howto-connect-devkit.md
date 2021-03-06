---
title: 将 DevKit 设备连接到 Azure IoT Central 应用程序 | Microsoft Docs
description: 了解如何使用 IoT 即插即用（预览版）以设备开发人员的身份将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序。
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: e61e8638da30cfdd4efbf405f8dc2b410e5c2f67
ms.sourcegitcommit: 6ddc26f9b27acec207b887531bea942b413046ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80343249"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序

本文介绍如何将 MXChip IoT DevKit (DevKit) 设备连接到 Azure IoT Central 应用程序。 该设备使用 DevKit 设备的已认证 IoT 即插即用（预览版）模型来配置与 IoT Central 的连接。

在本操作指南文章中，你将：

- 从 IoT Central 应用程序获取连接详细信息。
- 准备设备并将其连接到 IoT Central 应用程序。
- 在 IoT Central 中查看来自设备的遥测数据和属性。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要准备好以下资源：

- 一个 [DevKit 设备](https://aka.ms/iot-devkit-purchase)。
- IoT Central 应用程序。 可以遵循[创建 IoT Central 应用程序](./quick-deploy-iot-central.md)中的步骤。

## <a name="get-device-connection-details"></a>获取设备连接详细信息

1. 在 Azure IoT Central 应用程序中，选择“设备模板”选项卡，然后选择“+ 新建”。   在“使用预先配置的设备模板”  部分中，选择“MXChip IoT DevKit”  。

    ![MXChip IoT DevKit 的设备模板](media/howto-connect-devkit/device-template.png)

1. 在完成时选择“下一步:  自定义”，然后选择“创建”  。

1. 选择“设备”  选项卡。在设备列表中，选择“MXChip IoT DevKit”  ，然后选择“+ 新建”  以基于模板创建新设备。

    ![新建设备](media/howto-connect-devkit/new-device.png)

1. 在弹出窗口中，输入 `SampleDevKit` 作为设备 ID，输入 `MXChip IoT DevKit - Sample` 作为设备名称。   请确保关闭“模拟”  选项。 然后选择“创建”  。

    ![设备 ID 和名称](media/howto-connect-devkit/device-id-name.png)

1. 选择你创建的设备，然后选择“连接”  。 记下“ID 范围”、”设备 ID”和“主密钥”。    在本操作方法文章下文的步骤中，需要用到这些值。

    ![设备连接信息](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>准备设备

1. 从 GitHub 下载 DevKit 设备的最新[预建 Azure IoT Central 即插即用（预览版）固件](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin)。

1. 使用 USB 线缆将 DevKit 设备连接到开发计算机。 在 Windows 中，已映射到 DevKit 设备上的存储的驱动器中会打开一个文件资源管理器窗口。 例如，该驱动器可能名为 **AZ3166 (D:)** 。

1. 将 **iotc_devkit.bin** 文件拖放到驱动器窗口。 复制完成后，设备会使用新固件重新启动。

    > [!NOTE]
    > 如果屏幕上出现类似于“无 Wi-Fi”的错误，原因是 DevKit 尚未连接到 WiFi。 

1. 在 DevKit 上，按住**按钮 B** 不放，按下再松开“重置”按钮，然后松开**按钮 B**。  现在，设备处于接入点模式。 如果屏幕上显示“IoT DevKit - AP”和配置门户 IP 地址，则可以确认处于此模式。

1. 在计算机或平板电脑上，连接到设备屏幕上显示的 WiFi 网络名称。 WiFi 网络以 **AZ-** 开头，后接 MAC 地址。 连接到此网络时，不需要通过 Internet 访问。 此状态符合预期，只是在配置设备时才要与此网络保持短时间的连接。

1. 打开 Web 浏览器并导航到 [http://192.168.0.1/](http://192.168.0.1/)。 将显示以下网页：

    ![配置 UI](media/howto-connect-devkit/config-ui.png)

    在网页上输入：

    - WiFi 网络的名称 (SSID)。
    - WiFi 网络密码。
    - 连接详细信息：输入你之前记下的“设备 ID”  、“ID 范围”  和“SAS 主密钥”  。

    > [!NOTE]
    > IoT DevKit 目前只能连接到 2.4 GHz Wi-Fi，由于硬件限制，它不支持 5 GHz。

1. 选择“配置设备”，然后 DevKit 设备将重新启动并运行应用程序： 

    ![重新启动 UI](media/howto-connect-devkit/reboot-ui.png)

    DevKit 屏幕会显示一条确认消息，指出应用程序正在运行：

    ![DevKit 正在运行](media/howto-connect-devkit/devkit-running.png)

DevKit 首先在 IoT Central 应用程序中注册新设备，然后开始发送数据。

## <a name="view-the-telemetry"></a>查看遥测数据

遵循此步骤在 Azure IoT Central 应用程序中查看遥测数据。

在 IoT Central 应用程序中选择“设备”选项卡，然后选择添加的设备。  在“概述”选项卡中，可以查看来自 DevKit 设备的遥测数据： 

![IoT Central 设备概述](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>查看代码

若要查看代码或修改并编译代码，请参阅[代码示例](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)。

## <a name="next-steps"></a>后续步骤

了解如何将 DevKit 设备连接到 Azure IoT Central 应用程序后，建议接下来了解如何为自己的 IoT 设备[设置自定义设备模板](./howto-set-up-template.md)。
