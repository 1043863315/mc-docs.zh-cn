---
title: 将资源部署到订阅
description: 介绍了如何在 Azure 资源管理器模板中创建资源组。 它还展示了如何在 Azure 订阅范围内部署资源。
ms.topic: conceptual
origin.date: 03/09/2020
ms.date: 03/23/2020
ms.author: v-yeche
ms.openlocfilehash: b16e3849ca249e36cb0fd6580200a01ce4c7f7cd
ms.sourcegitcommit: 1436f1851342ca5631eb25342eed954adb707af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2020
ms.locfileid: "79543808"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>在订阅级别创建资源组和资源

为了简化 Azure 订阅中的资源管理，可以在整个订阅中定义和分配[策略](../../governance/policy/overview.md)或[基于角色的访问控制](../../role-based-access-control/overview.md)。 使用订阅级别模板，可以在订阅中以声明方式应用策略和分配角色。 还可以创建资源组和部署资源。

若要在订阅级别部署模板，请使用 Azure CLI、PowerShell 或 REST API。 Azure 门户不支持在订阅级别部署。

## <a name="supported-resources"></a>支持的资源

可以在订阅级别部署以下资源类型：

* 预算
* 部署 - 适用于部署到资源组的嵌套模板。
* peerAsns
* policyAssignments
* policyDefinitions
* policySetDefinitions
* resourceGroups
* roleAssignments
* roleDefinitions

<!--Noy Available on * [budgets](https://docs.microsoft.com/azure/templates/microsoft.consumption/budgets)-->
<!--Noy Available on * [deployments](https://docs.microsoft.com/azure/templates/microsoft.resources/deployments)-->
<!--Noy Available on * [peerAsns](https://docs.microsoft.com/azure/templates/microsoft.peering/peerasns)-->
<!--Noy Available on * [policyAssignments](https://docs.microsoft.com/azure/templates/microsoft.authorization/policyassignments)-->
<!--Noy Available on * [policyDefinitions](https://docs.microsoft.com/azure/templates/microsoft.authorization/policydefinitions)-->
<!--Noy Available on * [policySetDefinitions](https://docs.microsoft.com/azure/templates/microsoft.authorization/policysetdefinitions)-->
<!--Noy Available on * [resourceGroups](https://docs.microsoft.com/azure/templates/microsoft.resources/resourcegroups)-->
<!--Noy Available on * [roleAssignments](https://docs.microsoft.com/azure/templates/microsoft.authorization/roleassignments)-->
<!--Noy Available on * [roleDefinitions](https://docs.microsoft.com/azure/templates/microsoft.authorization/roledefinitions)-->

### <a name="schema"></a>架构

用于订阅级别部署的架构不同于资源组部署的架构。

对于模板，请使用：

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

对于所有部署范围，参数文件的架构都相同。 对于参数文件，请使用：

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>部署命令

用于订阅级别部署的命令不同于资源组部署的命令。

对于 Azure CLI，请使用 [az deployment create](https://docs.azure.cn/cli/deployment?view=azure-cli-latest#az-deployment-create)。 以下示例通过部署模板来创建资源组：

```azurecli
az deployment create \
  --name demoDeployment \
  --location chinaeast \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=chinaeast
```

对于 PowerShell 部署命令，请使用 [New-AzDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment) 或 **New-AzSubscriptionDeployment**。 以下示例通过部署模板来创建资源组：

```powershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location chinaeast `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation chinaeast
```

对于 REST API，请使用[部署 - 在订阅范围内创建](https://docs.microsoft.com/rest/api/resources/deployments/createorupdateatsubscriptionscope)。

## <a name="deployment-location-and-name"></a>部署位置和名称

对于订阅级别部署，必须为部署提供位置。 部署位置独立于部署的资源的位置。 部署位置指定何处存储部署数据。

可以为部署提供一个名称，也可以使用默认部署名称。 默认名称是模板文件的名称。 例如，部署一个名为 **azuredeploy.json** 的模板将创建默认部署名称 **azuredeploy**。

每个部署名称的位置不可变。 当某个位置中已有某个部署时，无法在另一位置创建同名的部署。 如果出现错误代码 `InvalidDeploymentLocation`，请使用其他名称或使用与该名称的以前部署相同的位置。

## <a name="use-template-functions"></a>使用模板函数

对于订阅级别部署，在使用模板函数时有一些重要注意事项：

* 不支持 [resourceGroup()](template-functions-resource.md#resourcegroup) 函数。 
* 支持 [reference()](template-functions-resource.md#reference) 和 [list()](template-functions-resource.md#list) 函数。
* 使用 [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) 函数获取在订阅级别部署的资源的资源 ID。

    例如，若要获取策略定义的资源 ID，请使用：

    ```json
    subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
    ```

    返回的资源 ID 具有以下格式：

    ```json
    /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
    ```

## <a name="create-resource-groups"></a>创建资源组

若要在 Azure 资源管理器模板中创建资源组，请为该资源组定义包含名称和位置的 Microsoft.Resources/resourceGroups 资源。 你可以创建一个资源组并在同一模板中将资源部署到该资源组。

<!--Not Available on [Microsoft.Resources/resourceGroups](https://docs.microsoft.com/azure/templates/microsoft.resources/allversions)-->

以下模板创建空资源组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

结合使用 [copy 元素](copy-resources.md)与资源组来创建多个资源组。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

有关资源迭代的信息，请参阅[在 Azure 资源管理器模板中部署资源的多个实例](./copy-resources.md)，以及[教程：使用资源管理器模板创建多个资源实例](./template-tutorial-create-multiple-instances.md)。

## <a name="resource-group-and-resources"></a>资源组和资源

若要创建资源组并向其部署资源，请使用嵌套模板。 嵌套模板定义要部署到资源组的资源。 将嵌套模板设置为依赖于资源组，确保资源组存在，然后再部署资源。

以下示例将创建一个资源组，并向该资源组部署存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              }
              "kind": "StorageV2",
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="create-policies"></a>创建策略

### <a name="assign-policy"></a>分配策略

以下示例将现有的策略定义分配到订阅。 如果策略使用参数，请将参数作为对象提供。 如果策略不使用参数，请使用默认的空对象。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

若要使用 Azure CLI 部署此模板，请使用：

```azurecli
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location chinaeast \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['chinanorth']} }"
```

若要使用 PowerShell 部署此模板，请使用：

```powershell
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("chinanorth", "chinanorth2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location chinaeast `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>定义和分配策略

可以在同一模板中[定义](../../governance/policy/concepts/definition-structure.md)和分配策略。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "chinaeast2"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

若要在订阅中创建策略定义，然后将其应用到订阅，请使用以下 CLI 命令：

```azurecli
az deployment create \
  --name demoDeployment \
  --location chinaeast \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

若要使用 PowerShell 部署此模板，请使用：

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location chinaeast `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="template-samples"></a>模板示例

* [创建资源组、将其锁定并授予其权限](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)。
* [创建资源组、策略和策略分配](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何分配角色，请参阅[使用 RBAC 和 Azure 资源管理器模板管理对 Azure 资源的访问权限](../../role-based-access-control/role-assignments-template.md)。
* 若要通过示例来了解如何为 Azure 安全中心部署工作区设置，请参阅 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
* 示例模板可在 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments) 中找到。
* 还可以在[管理组级别](deploy-to-management-group.md)部署模板。

<!--Pending on  and [tenant level](deploy-to-tenant.md)-->
<!-- Update_Description: update meta properties, wording update, update link -->