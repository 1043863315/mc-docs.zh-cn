---
title: 测试 UI 定义文件
description: 介绍如何测试通过门户创建 Azure 托管应用程序的用户体验。
author: rockboyfor
ms.topic: conceptual
origin.date: 08/06/2019
ms.date: 03/23/2020
ms.author: v-yeche
ms.openlocfilehash: ad1103d2beccb5699627d63c866123fd2905882e
ms.sourcegitcommit: 1436f1851342ca5631eb25342eed954adb707af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2020
ms.locfileid: "79543878"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>测试 Azure 托管应用程序的门户接口

为托管应用程序[创建 createUiDefinition.json 文件](create-uidefinition-overview.md)之后，需要测试用户体验。 为了简化测试，请使用一个可在门户中加载文件的沙盒环境。 不需要真正部署该托管应用程序。 沙盒在当前的全屏门户体验中提供用户界面。 也可使用脚本来测试接口。 本文对这两种方法都进行了介绍。 建议使用沙盒来预览接口。

## <a name="prerequisites"></a>先决条件

* 一个 **createUiDefinition.json** 文件。 如果没有此文件，请复制[示例文件](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)。

* Azure 订阅。 如果没有 Azure 订阅，请在开始前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

## <a name="use-sandbox"></a>使用沙盒

1. 打开[创建 UI 定义沙箱](https://portal.azure.cn/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)。

    ![显示沙盒](./media/test-createuidefinition/show-sandbox.png)

1. 将空定义替换为 createUiDefinition.json 文件的内容。 选择“预览”。 

    ![选择“预览”](./media/test-createuidefinition/select-preview.png)

1. 此时会显示已创建的窗体。 可以单步执行用户体验，并填写值。

    ![显示窗体](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>故障排除

如果在选择“预览”  后窗体未显示，则可能存在语法错误。 在右侧滚动条上查找红色指示器，然后导航到它。

![显示语法错误](./media/test-createuidefinition/show-syntax-error.png)

如果窗体未显示，但看到一个带泪滴的云图标，则表明窗体出现错误，例如缺少属性。 在浏览器中打开“Web 开发人员工具”。 “控制台”显示有关界面的重要消息。 

![显示错误](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>使用测试脚本

若要在门户中测试界面，请将以下脚本之一复制到本地计算机：

* [PowerShell 端加载脚本 - Az 模块](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell 端加载脚本 - Azure 模块](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load 脚本](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

若要在门户中查看界面文件，请运行已下载的脚本。 该脚本在 Azure 订阅中创建一个存储帐户，并将 createUiDefinition.json 文件上传到该存储帐户。 在你第一次运行脚本时会创建此存储帐户，如果此存储帐户已删除，运行该脚本也会创建此存储帐户。 如果 Azure 订阅中已存在存储帐户，则该脚本会重复使用它。 该脚本将打开门户并从存储帐户加载文件。

提供存储帐户的位置，并指定包含 createUiDefinition.json 文件的文件夹。

对于 PowerShell，请使用：

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation chinaeast `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

对于 Azure CLI，请使用：

```bash
./sideload-createuidef.sh \
  -l chinaeast \
  -a .\100-Marketplace-Sample
```

如果 createUiDefinition.json 文件与该脚本位于同一文件夹中，并且你已创建了存储帐户，则无需提供这些参数。

对于 PowerShell，请使用：

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

对于 Azure CLI，请使用：

```bash
./sideload-createuidef.sh
```

脚本会在浏览器中打开新选项卡。 浏览器中会显示门户，其中包含用于创建托管应用程序的界面。

提供字段值。 完成后，会看到这些值已传递到模板（可以在浏览器的开发人员工具控制台中找到）。

![显示值](./media/test-createuidefinition/show-json.png)

你可以使用这些值作为参数文件来测试部署模板。

如果门户在摘要屏幕上挂起，则输出部分可能存在 Bug。 例如，你可能引用了不存在的控件。 如果输出中的某个参数为空，则该参数可能引用了不存在的属性。 例如，对控件的引用有效，但属性引用无效。

## <a name="test-your-solution-files"></a>测试解决方案文件

确认门户界面按预期方式工作后，可以验证 createUiDefinition 文件是否能够正常地与 mainTemplate.json 文件相集成。 可以运行验证脚本测试来测试解决方案文件的内容，包括 createUiDefinition 文件。 该脚本会验证 JSON 语法，检查文本字段中的正则表达式，并确保门户界面的输出值与模板参数相匹配。 有关运行此脚本的信息，请参阅[对模板运行静态验证检查](https://github.com/Azure/azure-quickstart-templates/tree/master/test)。

## <a name="next-steps"></a>后续步骤

验证门户界面之后，请了解如何[在市场中提供 Azure 托管应用程序](publish-marketplace-app.md)。

<!-- Update_Description: new article about test createuidefinition -->
<!--NEW.date: 01/20/2020-->