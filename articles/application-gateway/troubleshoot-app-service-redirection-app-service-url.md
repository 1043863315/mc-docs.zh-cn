---
title: 排查 Azure 应用程序网关与应用服务的问题 - 重定向到应用服务的 URL
description: 本文介绍如何排查将 Azure 应用程序网关与 Azure 应用服务配合使用时出现的重定向问题
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
origin.date: 02/22/2019
ms.date: 04/17/2019
ms.author: v-junlch
ms.openlocfilehash: 6d2c14c5799d0d37765975b196137e139d9f5828
ms.sourcegitcommit: 731da97453f3bd6b333dc2ec1058b9b91031d240
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871630"
---
# <a name="troubleshoot-application-gateway-with-app-service"></a>排查包含应用服务的应用程序网关的问题

了解如何诊断和解决充当后端服务器的应用程序网关和应用服务遇到的问题。

## <a name="overview"></a>概述

本文介绍如何排查以下问题：

> [!div class="checklist"]
> * 有重定向时，应用服务的 URL 公开在浏览器中
> * 应用服务的 ARRAffinity Cookie 域设置为应用服务主机名 (example.chinacloudsites.cn) 而不是原始主机

在应用程序网关后端池中配置面向公众的应用服务时，如果在应用程序代码中配置了重定向，访问应用程序网关时你可能会看到，浏览器会直接将你重定向到应用服务 URL。

此问题的主要可能原因如下：

- 在应用服务中配置了重定向。 只需在请求中添加一个尾随的斜杠即可配置重定向。
- Azure AD 身份验证导致重定向。
- 在应用程序网关的 HTTP 设置中启用了“从后端地址中选取主机名”开关。
- 未将自定义域注册到应用服务。

另外，在应用程序网关后面使用应用服务并使用自定义域来访问应用程序网关时，可能会看到由应用服务设置的 ARRAffinity Cookie 的域值带有“example.chinacloudsites.cn”域名。 如果希望原始主机名也是 Cookie 域，则请按本文中解决方案的要求操作。

## <a name="sample-configuration"></a>示例配置

- HTTP 侦听器：“基本”或“多站点”
- 后端地址池：应用服务
- HTTP 设置：已启用“从后端地址中选取主机名”
- 探测：已启用“从 HTTP 设置中选取主机名”

## <a name="cause"></a>原因

只能使用自定义域设置中配置的主机名访问应用服务，默认情况下，该主机名为“example.chinacloudsites.cn”。若要使用未注册到应用服务中的主机名或者使用应用程序网关的 FQDN 通过应用程序网关访问应用服务，则必须将原始请求中的主机名替代为应用服务的主机名。

为了在应用程序网关中实现此目的，我们在 HTTP 设置中使用了“从后端地址中选取主机名”开关；为了正常运行探测，我们在探测配置中使用了“从后端 HTTP 设置中选取主机名”。

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

出于此原因，当应用服务执行重定向时，除非另有配置，否则，它会使用 Location 标头中的主机名“example.chinacloudsites.cn”，而不是原始主机名。 可以查看下面的示例请求和响应标头。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.chinacloudsites.cn/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.chinacloudsites.cn

X-Powered-By: ASP.NET
```
在上面的示例中可以发现，响应标头包含重定向状态代码 301，location 标头包含应用服务的主机名而不是原始主机名“www.contoso.com”。

## <a name="solution"></a>解决方案

不在应用程序端使用重定向可以解决此问题，但是，如果无法做到这一点，则我们也必须将应用程序网关收到的同一主机标头传递给应用服务，而不要执行主机替代。

这样做后，应用服务会在指向应用程序网关而不是指向自身的同一原始主机标头中执行重定向（如果有）。

若要实现此目的，必须拥有一个自定义域并遵循下面所述的过程。

- 将该域注册到应用服务的自定义域列表。 为此，必须在自定义域中创建一个指向应用服务 FQDN 的 CNAME。 有关详细信息，请参阅[将现有的自定义 DNS 名称映射到 Azure 应用服务](/app-service/app-service-web-tutorial-custom-domain)。

    ![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 这样做后，应用服务已准备好接受主机名“www.contoso.com”。 现在，请更改 DNS 中的 CNAME 条目，使其重新指向应用程序网关的 FQDN。 例如“appgw.chinanorth.chinacloudapp.cn”。

- 确保执行 DNS 查询时，域“www.contoso.com”解析为应用程序网关的 FQDN。

- 设置自定义探测以禁用“从后端 HTTP 设置中选取主机名”。 为此，可以在门户上取消选中探测设置中的相应复选框，或者在 PowerShell 中，不要在 Set-AzApplicationGatewayProbeConfig 命令中使用 -PickHostNameFromBackendHttpSettings 开关。 在探测的主机名字段中，输入应用服务的 FQDN“example.chinacloudsites.cn”，因为应用程序网关发出的探测请求会在主机标头中携带此 FQDN。

  > [!NOTE]
  > 执行下一步时，请确保自定义探测未关联到后端 HTTP 设置，因为此时 HTTP 设置中仍然启用了“从后端地址中选取主机名”开关。

- 设置应用程序网关的 HTTP 设置以禁用“从后端地址中选取主机名”。 为此，可以在门户上取消选中相应的复选框，或者在 PowerShell 中，不要在 Set-AzApplicationGatewayBackendHttpSettings 命令中使用 -PickHostNameFromBackendAddress 开关。

- 将自定义探测重新关联到后端 HTTP 设置，并验证后端的运行状况是否正常。

- 这样做后，应用程序网关应会将相同的主机名“www.contoso.com”转发到应用服务，并且同一个主机名上会发生重定向。 可以查看下面的示例请求和响应标头。

若要针对现有设置使用 PowerShell 来实施上述步骤，请按下面的示例 PowerShell 脚本操作。 请注意，我们没有在探测和 HTTP 设置配置中使用 -PickHostname 开关。

```azurepowershell
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.chinacloudsites.cn" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://www.azure.cn/support/contact/)。

<!-- Update_Description: wording update -->