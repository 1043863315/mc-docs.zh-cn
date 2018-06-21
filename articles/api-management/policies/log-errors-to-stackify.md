---
title: Azure API 管理策略示例 - 将错误发送到 Stackify 进行日志记录
description: Azure API 管理策略示例 - 演示如何添加错误日志记录策略，以便将错误发送到 Stackify 进行日志记录。
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 10/13/2017
ms.date: 02/26/2018
ms.author: v-yiso
ms.openlocfilehash: 39decedcd6caddc41f19c5fa5c9bb243cea8bd90
ms.sourcegitcommit: 3629fd4a81f66a7d87a4daa00471042d1f79c8bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2018
ms.locfileid: "29286057"
---
# <a name="send-errors-to-stackify-for-logging"></a>将错误发送到 Stackify 进行日志记录

本文介绍 Azure API 管理策略示例，该示例演示如何添加错误日志记录策略，以便将错误发送到 Stackify 进行日志记录。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到 **on-error** 块中。

```xml
<!-- The policy defined in this file shows how to add an error logging policy to send errors to Stackify for logging. -->
<!-- You must specify the following named values: -->
  <!-- your-stackify-api-key  -->
  <!-- stackify-api-key  - Get this from your Stackify account -->
  <!-- environment-name - This will be send to Stackify for the environment filter, (Production, Dev, Test, etc) -->
  <!-- app-name - The Application name that will be sent to Stackify -->

<!-- Copy the following snippet into the on-error section. -->

<policies>
  <inbound>
    <base />
  </inbound>
  <backend>
    <base />
  </backend>
  <outbound>
    <base />
  </outbound>
  <on-error>
    <trace source="OnError">
      On Error
    </trace>
    <send-one-way-request mode="new">
      <set-url>https://api.stackify.com/Log/Save</set-url>
      <set-method>POST</set-method>
      <set-header name="Content-Type" exists-action="override">
        <value>value</value>
      </set-header>
      <set-header name="X-Stackify-PV" exists-action="override">
        <value>V1</value>
      </set-header>
      <set-header name="X-Stackify-Key" exists-action="override">
        <value>{{stackify-api-key}}</value>
      </set-header>
      <set-body>
        @{
        return new JObject(
        new JProperty("Environment","{{environment-name}}"),
        new JProperty("ServerName", context.Deployment.ServiceName),
        new JProperty("AppName", "{{app-name}}"),
        new JProperty("AppLoc", "/usr/local/stackify/stackify-agent"),
        new JProperty("Logger", "stackify-log-log4j12-1.0.12"),
        new JProperty("Platform", "java"),
        new JProperty("Msgs",
        new JArray(
        new JObject(
        new JProperty("Msg", context.LastError.Message),
        new JProperty("Th", "main"),
        new JProperty("EpochMs", (new DateTimeOffset(DateTime.Now)).ToUnixTimeSeconds() * 1000 ),
        new JProperty("Level", "error"),
        new JProperty("SrcMethod", context.LastError.Source)
        )))
        ).ToString();
        }
      </set-body>
    </send-one-way-request>
  </on-error>
</policies>
```

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

