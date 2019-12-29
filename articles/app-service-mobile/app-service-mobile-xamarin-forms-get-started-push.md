---
title: 向 Xamarin.Forms 应用添加推送通知 | Azure
description: 了解如何使用 Azure 服务将多平台推送通知发送到 Xamarin.Forms 应用。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
origin.date: 06/25/2019
ms.date: 12/16/2019
ms.author: v-tawe
ms.openlocfilehash: 4c034d19b2e48f653128cd19e3cb96b26aa7a108
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75336025"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>向 Xamarin.Forms 应用添加推送通知

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”  、“测试”  和“分发”  服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用**分析**和**诊断**服务监视其应用的状态和使用情况，并使用**推送**服务与用户互动。 开发人员还可以利用“身份验证”  对其用户进行身份验证，并使用“数据”  服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

## <a name="overview"></a>概述

本教程会将推送通知添加到由 [Xamarin.Forms 快速入门](app-service-mobile-xamarin-forms-get-started.md)生成的所有项目。 这意味着每次插入一条记录，都会向所有跨平台客户端发送一条推送通知。

如果不使用下载的快速入门服务器项目，则需要推送通知扩展包。 有关详细信息，请参阅[使用用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="prerequisites"></a>先决条件

对于 iOS，用户需要 [Apple Developer Program 会员资格](https://developer.apple.com/programs/ios/) 和物理 iOS 设备。 [iOS 模拟器不支持推送通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。

## <a name="configure-hub"></a>配置通知中心

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>更新服务器项目以发送推送通知

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

<!-- FCM not availible-->


## <a name="configure-and-run-the-ios-project-optional"></a>配置和运行 iOS 项目（可选）

本部分用于运行适用于 iOS 设备的 Xamarin iOS 项目。 如果不使用 iOS 设备，可以跳过本部分。

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>为 APNS 配置通知中心

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

接下来，会在 Xamarin Studio 或 Visual Studio 中配置 iOS 项目设置。

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>向 iOS 应用添加推送通知

1. 在 **iOS** 项目中，打开 AppDelegate.cs，并在代码文件的顶部添加以下语句。

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. 在 **AppDelegate** 类中，为 **RegisteredForRemoteNotifications** 事件添加重写以注册通知：

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. 并在 **AppDelegate** 中，为 **DidReceiveRemoteNotification** 事件处理程序添加以下重写：

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    运行该应用时，此方法可处理传入通知。

4. 在 **AppDelegate** 类中，向 **FinishedLaunching** 方法添加以下代码：

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    这会启用对远程通知的支持并请求推送注册。

应用现已更新，可支持推送通知。

#### <a name="test-push-notifications-in-your-ios-app"></a>在 iOS 应用中测试推送通知

1. 右键单击 iOS 项目，并单击“设为启动项目”  。
2. 在 Visual Studio 中按“运行”  按钮或 **F5** 生成项目并在 iOS 设备中启动应用， 然后单击“确定”  接受推送通知。

   > [!NOTE]
   > 必须显式接受来自应用程序的推送通知。 此请求只会在首次运行应用程序时出现。

3. 在应用中，键入一项任务，然后单击加号 ( **+** ) 图标。
4. 检查是否已收到通知，并单击“确定”  取消通知。

## <a name="configure-and-run-windows-projects-optional"></a>配置和运行 Windows 项目（可选）

本部分用于运行适用于 Windows 设备的 Xamarin.Forms WinApp 和 WinPhone81 项目。 这些步骤也支持通用 Windows 平台 (UWP) 项目。 如果不使用 Windows 设备，可以跳过本部分。

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>将用于推送通知的 Windows 应用注册到 Windows 通知服务 (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>为 WNS 配置通知中心

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>向 Windows 应用添加推送通知

1. 在 Visual Studio 中，打开 Windows 项目中的 **App.xaml.cs**，并添加以下语句。

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    将 `<your_TodoItemManager_portable_class_namespace>` 替换为包含 `TodoItemManager` 类的可移植项目的命名空间。

2. 在 App.xaml.cs 中，添加以下 **InitNotificationsAsync** 方法：

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    此方法获取推送通知通道，并注册模板以从通知中心接收模板通知。 支持 *messageParam* 的模板通知将传送到此客户端。

3. 在 App.xaml.cs 中，通过添加 `async` 修饰符更新 **OnLaunched** 事件处理程序方法定义。 然后在此方法的末尾添加以下代码行：

    ```csharp
    await InitNotificationsAsync();
    ```

    这可确保每次启动应用时，创建或刷新推送通知注册。 请务必执行此操作，以保证 WNS 推送通道始终处于活动状态。  

4. 在 Visual Studio 的解决方案资源管理器中，打开 **Package.appxmanifest** 文件，并在“通知”  下将“支持 Toast 通知”  设置为“是”  。
5. 生成该应用并确认没有错误。 客户端应用现在应从移动应用后端注册模板通知。 对于解决方案中的每个 Windows 项目，重复此部分的操作。

#### <a name="test-push-notifications-in-your-windows-app"></a>在 Windows 应用中测试推送通知

1. 在 Visual Studio 中，右键单击 Windows 项目，并单击“设为启动项目”  。
2. 按“运行”按钮生成项目并启动应用程序  。
3. 在应用中，为新 todoitem 键入一个名称，并单击加号 ( **+** ) 图标以添加它。
4. 确认在添加项时收到了通知。

## <a name="next-steps"></a>后续步骤

深入了解推送通知：


* [诊断推送通知问题](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  有多种原因可能导致通知被丢弃或最终未到达设备。 本主题演示如何分析和确定推送通知失败的根本原因。

还可继续学习以下教程之一：

* [向应用程序添加身份验证](app-service-mobile-xamarin-forms-get-started-users.md)  
  了解如何使用标识提供者对应用的用户进行身份验证。
* [为应用启用脱机同步](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  了解如何使用移动应用后端向应用添加脱机支持。 使用脱机同步，用户可以与移动应用进行交互（查看、添加或修改数据），即使在没有网络连接时也是如此。

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]:https://developer.apple.com/xcode/
[Xcode]: https://developer.apple.com/xcode/
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
