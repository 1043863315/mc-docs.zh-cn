---
title: 使用 Azure 移动应用在 iOS 中添加身份验证
description: 了解如何使用 Azure 移动应用通过各种标识提供者（包括 Microsoft 帐户和 Azure Active Directory）对 iOS 应用的用户进行身份验证。
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
origin.date: 06/25/2019
ms.date: 09/10/2019
ms.author: v-tawe
ms.openlocfilehash: 9437f89ec58fb38f39720b1e4c0f0d82431b0307
ms.sourcegitcommit: 298eab5107c5fb09bf13351efeafab5b18373901
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2019
ms.locfileid: "74657932"
---
# <a name="add-authentication-to-your-ios-app"></a>Add authentication to your iOS app（将身份验证添加到 iOS 应用）
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center 正在投资于对移动应用开发至关重要的新集成服务。 开发人员可以使用**生成**、**测试**和**分发**服务来设置持续集成和交付管道。 部署应用后，开发人员可以使用**分析**和**诊断**服务监视其应用的状态和使用情况，并使用**推送**服务与用户互动。 开发人员还可以利用 **Auth** 对用户进行身份验证，利用**数据**服务在云中持久保存和同步应用数据。 立即查看 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-users)。
>

本教程介绍如何使用支持的标识提供者向 [iOS 快速入门] 项目添加身份验证。 本教程基于 [iOS 快速入门] 教程，必须先完成该教程。

## <a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>将应用添加到允许的外部重定向 URL

安全身份验证要求为应用定义新的 URL 方案。  此方案允许在完成身份验证过程后，身份验证系统重定向到应用。  在本教程中，我们自始至终使用 URL 方案 _appname_ 。  但是，可以使用任何你所选的 URL 方案。  该方案是移动应用的唯一方案。  在服务器端启用重定向：

1. 在 [Azure 门户]中，选择应用服务。

2. 单击“身份验证/授权”  菜单选项。

3. 在“验证提供程序”  部分下，单击“Azure Active Directory”  。

4. 将“管理模式”  设置为“高级”  。

5. 在“允许的外部重定向 URL”  中，输入 `appname://easyauth.callback`。  此字符串中的 _appname_ 是移动应用程序的 URL 方案。  它应该遵循协议的正常 URL 规范（仅使用字母和数字，并以字母开头）。  应记下此字符串，因为在一些地方需要使用此 URL 方案调整移动应用代码。

6. 单击 **“确定”** 。

7. 单击“保存”  。

## <a name="permissions"></a>将权限限制给已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Xcode 中，按“运行”  启动应用。 将引发一个异常，因为应用尝试以未经身份验证的用户身份访问后端，但 TodoItem  表现在要求身份验证。

## <a name="add-authentication"></a>向应用程序添加身份验证
**Objective-C**：

1. 在 Mac 的 Xcode 中打开 *QSTodoListViewController.m* 并添加以下方法：

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"microsoftaccount" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    使用 microsoftaccount  或 windowsazureactivedirectory  作为标识提供者。

    将 **urlScheme** 替换为应用的唯一名称。  urlScheme 应与在 Azure 门户中的“允许的外部重定向 URL”字段中指定的 URL 方案协议相同  。 身份验证回调使用 urlScheme 在完成身份验证请求之后切换回到应用。

2. 将 QSTodoListViewController.m  中 `viewDidLoad` 的 `[self refresh]` 替换为以下代码：

    ```Objective-C
    [self loginAndGetData];
    ```

3. 打开 `QSAppDelegate.h` 文件，并添加以下代码：

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. 打开 `QSAppDelegate.m` 文件，并添加以下代码：

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   在行读取 `#pragma mark - Core Data stack`之前直接添加此代码。  将 appname  替换为步骤 1 中使用的 urlScheme 值。

5. 打开 `AppName-Info.plist` 文件（将 AppName 替换为应用的名称），并添加以下代码：

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    此代码应放置在 `<dict>` 元素内部。  将 appname  字符串（位于  CFBundleURLSchemes 的数组内）替换为步骤 1 中所选的应用名称。  还可在 plist 编辑器中做这些更改 - 单击 XCode 中的 `AppName-Info.plist` 文件，打开 plist 编辑器。

    将 CFBundleURLName  的 `com.microsoft.azure.zumo` 字符串替换为 Apple 捆绑包标识符。

6. 按“运行”  启动应用，然后登录。 登录后，应可以查看 Todo 列表并进行更新。

**Swift**：

1. 在 Mac 的 Xcode 中打开 *ToDoTableViewController.swift* 并添加以下方法：

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"microsoftaccount", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    使用 microsoftaccount  或 windowsazureactivedirectory  作为标识提供者。

    将 **urlScheme** 替换为应用的唯一名称。  urlScheme 应与在 Azure 门户中的“允许的外部重定向 URL”字段中指定的 URL 方案协议相同  。 身份验证回调使用 urlScheme 在完成身份验证请求之后切换回到应用。

2. 删除 ToDoTableViewController.swift  中 `viewDidLoad()` 末尾的 `self.refreshControl?.beginRefreshing()` 和 `self.onRefresh(self.refreshControl)` 行。 在其位置上添加对 `loginAndGetData()` 的调用：

    ```swift
    loginAndGetData()
    ```

3. 打开 `AppDelegate.swift` 文件，将以下行添加到 `AppDelegate` 类：

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    将 appname  替换为步骤 1 中使用的 urlScheme 值。

4. 打开 `AppName-Info.plist` 文件（将 AppName 替换为应用的名称），并添加以下代码：

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    此代码应放置在 `<dict>` 元素内部。  将 appname  字符串（位于  CFBundleURLSchemes 的数组内）替换为步骤 1 中所选的应用名称。  还可在 plist 编辑器中做这些更改 - 单击 XCode 中的 `AppName-Info.plist` 文件，打开 plist 编辑器。

    将 CFBundleURLName  的 `com.microsoft.azure.zumo` 字符串替换为 Apple 捆绑包标识符。

5. 按“运行”  启动应用，然后登录。 登录后，应可以查看 Todo 列表并进行更新。

应用服务身份验证使用 Apple 应用间通信。  有关此主题的更多详细信息，请参阅 [Apple 文档][2]
<!-- URLs. -->
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html [Azure 门户]: https://portal.azure.cn

[iOS 快速入门]: app-service-mobile-ios-get-started.md

