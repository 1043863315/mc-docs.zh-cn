---
title: Azure Stack Hub 验证报表
titleSuffix: Azure Stack Hub
description: 使用 Azure Stack Hub 就绪性检查器来生成验证报表。
author: WenJason
ms.topic: conceptual
origin.date: 01/07/2020
ms.date: 02/24/2020
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 13dd6839065b7e4e54cdf23e40979a001d79e13b
ms.sourcegitcommit: afe972418a883551e36ede8deae32ba6528fb8dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77540250"
---
# <a name="azure-stack-hub-validation-report"></a>Azure Stack Hub 验证报表

使用 [Azure Stack Hub 就绪性检查器](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.2002.1111.69)工具运行验证来为 Azure Stack Hub 环境的部署和维护提供支持。 该工具将结果写入到 .json 报表文件。 该报表显示有关 Azure Stack Hub 部署的先决条件状态的详细数据和汇总数据。 该报表还显示有关现有 Azure Stack Hub 部署的机密轮换的信息。  

## <a name="where-to-find-the-report"></a>在何处可以找到该报表

该工具运行时，它会将结果记录到 **AzsReadinessCheckerReport.json** 中。 该工具还会创建一个名为 **AzsReadinessChecker.log** 的日志。 这些文件的位置会随验证结果一起显示在 PowerShell 中：

![Azure Stack Hub 就绪性检查器的运行-验证结果](./media/azure-stack-validation-report/validation.png)

当在同一计算机上运行后续验证检查时，这两个文件都会持久保留这些验证检查的结果。 例如，可以运行该工具来验证证书，再次运行它来验证 Azure 标识，第三次运行它来验证注册。 所有三次验证的结果都在生成的 .json 报表中。  

这两个文件默认写入到 `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`。  

- 可以在命令行的末尾使用 `-OutputPath <path>` 参数来指定不同的报告位置。
- 可以在命令行的末尾使用 `-CleanReport` 参数从 **AzsReadinessCheckerReport.json** 中清除有关以前运行此工具的相关信息。

## <a name="view-the-report"></a>查看报告

若要在 PowerShell 中查看报表，请将报表路径提供为 `-ReportPath` 的值。 此命令显示报表内容，并指明尚没有结果的验证。

例如，若要从打开到报表所在位置的 PowerShell 提示符查看报表，请运行以下命令：

```powershell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

输出类似于以下示例：

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.partner.onmschina.cn
Azure Environment             : AzureChinaCloud
Azure Active Directory Tenant : contoso.partner.onmschina.cn
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>查看报表摘要

若要查看报表摘要，可以在 PowerShell 命令的末尾添加 `-summary` 参数。 例如：

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

摘要会显示没有结果的验证并且会指明已完成的验证是通过还是失败。 输出类似于以下示例：

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>查看经筛选的报表

若要查看基于单一验证类型筛选的报表，请将 `-ReportSections` 参数与以下值之一结合使用：

- 证书
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- 作业
- 全部  

例如，若要仅查看证书的报表摘要，请使用以下 PowerShell 命令行：

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
