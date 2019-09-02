---
title: Application Insights：语言、平台和集成 | Azure Docs
description: 适用于 Application Insights 的语言、平台和集成
services: application-insights
documentationcenter: ''
author: lingliw
manager: digimobile
origin.date: 08/20/2019
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: v-lingwu
ms.openlocfilehash: 4d4c9b710fc321a51615194c60e72a51da1cacec
ms.sourcegitcommit: 6999c27ddcbb958752841dc33bee68d657be6436
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69989690"
---
# <a name="supported-languages"></a>支持的语言

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>支持的平台和框架

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>已部署应用程序的检测（无代码、基于代理）
* [Azure VM 和 Azure 虚拟机规模集](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure 应用服务](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 适用于已处于活动状态的应用](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure 云服务](../../azure-monitor/app/cloudservices.md)，包括 Web 角色和辅助角色
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>通过代码进行检测 (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [通用 Windows 应用](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows 桌面应用程序、服务和辅助角色](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>记录框架
* [ILogger](/azure-monitor/app/ilogger)
* [Log4Net、NLog 或 System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java、Log4J 或 Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash 插件](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)



## <a name="unsupported-sdks"></a>不受支持的 SDK
我们知道还有其他几个社区支持的 SDK。 但是，Azure Monitor 仅在使用此页上列出的受支持 SDK 时提供支持。 我们一直在评估扩展对其他语言支持的机会，所以请关注我们的 [GitHub 公告](https://github.com/microsoft/ApplicationInsights-Announcements/issues)页，以获得最新的 SDK 消息。 
