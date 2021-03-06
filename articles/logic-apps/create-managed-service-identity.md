---
title: 使用托管标识进行身份验证
description: 使用托管标识在不使用凭据或机密登录的情况下访问其他 Azure Active Directory 租户中的资源
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
origin.date: 02/10/2020
ms.date: 03/30/2020
ms.author: v-yeche
ms.openlocfilehash: 1e8637194f28a59f73f4d9acaa5b3a0c1974c4fe
ms.sourcegitcommit: 2b084933215d0e7702456730f168d97e00c882a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2020
ms.locfileid: "80243597"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用托管标识验证对 Azure 资源的访问

若要在不登录的情况下访问其他 Azure Active Directory (Azure AD) 租户中的资源并对标识进行身份验证，逻辑应用可以使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)（前称为“托管服务标识”，或缩写为 MSI），而不使用凭据或机密。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。

Azure 逻辑应用支持[系统分配](../active-directory/managed-identities-azure-resources/overview.md)和用户分配的托管标识。  逻辑应用可以使用系统分配的标识（可以在整个逻辑应用组中共享该标识），但不能同时使用这两种标识。 目前，只有[特定的内置触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)支持托管标识，托管的连接器或连接则不支持托管标识，例如：

<!--Not Avaialble on [*user-assigned*](../active-directory/managed-identities-azure-resources/overview.md)-->
<!--Not Avaialble on or a *single* user-assigned identity-->

* HTTP
* Azure Functions
* Azure API 管理
* Azure 应用服务

本文介绍如何为逻辑应用设置这两种类型的托管标识。 有关详细信息，请参阅以下主题：

* [支持托管标识的触发器和操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [出站调用支持的身份验证类型](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [逻辑应用的托管标识限制](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [支持使用托管标识进行 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册 Azure 试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。 托管标识和需要访问的目标 Azure 资源必须使用相同的 Azure 订阅。

* 若要为托管标识授予对 Azure 资源的访问权限，需要为该标识添加对目标资源的角色。 若要添加角色，需要拥有 [Azure AD 管理员权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)，这些权限可将角色分配到相应 Azure AD 租户中的标识。

* 要访问的目标 Azure 资源。 在此资源中，为托管标识添加一个角色，以帮助逻辑应用验证对目标资源的访问。

* 要在其中使用[支持托管标识的触发器或操作](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)的逻辑应用

## <a name="enable-managed-identity"></a>启用托管标识

若要设置想要使用的托管标识，请单击该标识对应的链接：

* [系统分配的标识](#system-assigned)

    <!--Not Available on * [User-assigned identity](#user-assigned)-->

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>启用系统分配的标识

<!--Not Available on Unlike user-assigned identities, you don't have to manually create the system-assigned identity.-->

若要为逻辑应用设置系统分配的标识，可使用以下选项：

* [Azure 门户](#azure-portal-system-logic-app)
* [Azure Resource Manager 模板](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>在 Azure 门户中启用系统分配的标识

1. 在 [Azure 门户](https://portal.azure.cn)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单的“设置”下，选择“标识”   。 选择“系统分配” > “打开” > “保存”。    当 Azure 提示确认时，请选择“是”。 

    ![启用系统分配的标识](./media/create-managed-service-identity/enable-system-assigned-identity.png)

    <!--Not Available on NOTE-->
    <!--Not Available on > associated with the user-assigned identity. Before you can add the system-assigned identity-->

    现在，逻辑应用可以使用已注册到 Azure Active Directory 并由对象 ID 表示的系统分配的标识。

    ![系统分配的标识的对象 ID](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

    | 属性 | Value | 说明 |
    |----------|-------|-------------|
    | **对象 ID** | <*identity-resource-ID*> | 全局唯一标识符 (GUID)，表示 Azure AD 租户中逻辑应用的系统分配的标识 |
    ||||

1. 现在，请执行本主题稍后所述的[为该标识授予对资源的访问权限的步骤](#access-other-resources)。

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中启用系统分配的标识

若要自动创建和部署 Azure 资源（如逻辑应用），可以使用 [Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。 若要在模板中为逻辑应用启用系统分配的托管标识，请在模板中将 `identity` 对象和 `type` 子属性添加到逻辑应用的资源定义，例如：

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

当 Azure 创建逻辑应用资源定义时，`identity` 对象将获取以下附加属性：

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| 属性 (JSON) | Value | 说明 |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | 托管标识的服务主体对象的全局唯一标识符 (GUID)，表示 Azure AD 租户中的逻辑应用。 此 GUID 有时显示为“对象 ID”或 `objectID`。 |
| `tenantId` | <*Azure-AD-tenant-ID*> | 全局唯一标识符 (GUID)，表示逻辑应用为其中成员的 Azure AD 租户。 在 Azure AD 租户内，服务主体与逻辑应用实例具有相同名称。 |
||||

<a name="user-assigned"></a>

<!--Not Avaiable on ### Enable user-assigned identity-->


<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>为标识授予对资源的访问权限

使用逻辑应用的托管标识进行身份验证之前，请在你打算在其中使用该标识的 Azure 资源中为该标识设置访问权限。 若要完成此任务，请在目标 Azure 资源中向该标识分配适当的角色。 下面是可以使用的选项：

* [Azure 门户](#azure-portal-assign-access)
* [Azure Resource Manager 模板](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) - 有关详细信息，请参阅[使用 Azure RBAC 和 Azure PowerShell 添加角色分配](../role-based-access-control/role-assignments-powershell.md)。
* Azure CLI ([az role assignment create](https://docs.azure.cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-create)) - 有关详细信息，请参阅[使用 Azure RBAC 和 Azure CLI 添加角色分配](../role-based-access-control/role-assignments-cli.md)。
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>在 Azure 门户中分配访问权限

1. 在 [Azure 门户](https://portal.azure.cn)中，转到你希望托管标识在其中拥有访问权限的 Azure 资源。

1. 在资源的菜单中，选择“访问控制(IAM)” > “角色分配”，在其中可以查看该资源的当前角色分配。   在工具栏上，选择“添加” > “添加角色分配”。  

    ![选择“添加”>“添加角色分配”](./media/create-managed-service-identity/add-role-to-resource.png)

    > [!TIP]
    > 如果“添加角色分配”选项已禁用，则很可能是你没有权限。  有关可用于管理资源角色的权限的详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

1. 在“添加角色分配”下，选择一个可为标识授予对目标资源的必要访问权限的角色。  

    在本主题的示例中，标识需要一个[可以访问 Azure 存储容器中的 Blob 的角色](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)。

    ![选择“存储 Blob 数据参与者”角色](./media/create-managed-service-identity/select-role-for-identity.png)

1. 针对托管标识执行以下步骤：

    * **系统分配的标识**

        1. 在“将访问权限分配到”框中，选择“逻辑应用”。   显示“订阅”属性后，请选择与标识关联的 Azure 订阅。 

            ![为系统分配的标识选择访问权限](./media/create-managed-service-identity/assign-access-system.png)

        1. 在“选择”框下，从列表中选择你的逻辑应用。  如果列表太长，请使用“选择”框筛选列表。 

            ![为系统分配的标识选择逻辑应用](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

    <!--Not Available on * **User-assigned identity**-->

1. 完成后，选择“保存”  。

    目标资源的角色分配列表现在会显示所选的托管标识和角色。 此示例演示如何对一个逻辑应用使用系统分配的标识。
    
    <!--Not Available on and a user-assigned identity for a group of other logic apps-->

    ![已将托管标识和角色添加到目标资源](./media/create-managed-service-identity/added-roles-for-identities.png)

    有关详细信息，请参阅[使用 Azure 门户为托管标识分配对资源的访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。

1. 现在，请执行在支持托管标识的触发器或操作中[使用标识对访问进行身份验证的步骤](#authenticate-access-with-identity)。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>使用托管标识对访问进行身份验证

[为逻辑应用启用托管标识](#azure-portal-system-logic-app)并[为该标识授予对目标资源或实体的访问权限](#access-other-resources)后，可以在[支持托管标识的触发器和操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)中使用该标识。

> [!IMPORTANT]
> 如果要在某个 Azure 函数中使用系统分配的标识，请先[为 Azure 函数启用身份验证](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)。

这些步骤说明如何通过 Azure 门户对触发器或操作使用托管标识。 若要在触发器或操作的基础 JSON 定义中指定托管标识，请参阅[托管标识身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)。

1. 在 [Azure 门户](https://portal.azure.cn)的逻辑应用设计器中打开逻辑应用。

1. 添加[支持托管标识的触发器或操作](logic-apps-securing-a-logic-app.md#managed-identity-authentication)（如果尚未这样做）。

    例如，HTTP 触发器或操作可以使用你为逻辑应用启用的系统分配的标识。 一般情况下，HTTP 触发器或操作将使用以下属性来指定要访问的资源或实体：

    | 属性 | 必须 | 说明 |
    |----------|----------|-------------|
    | **方法** | 是 | 要运行的操作使用的 HTTP 方法 |
    | **URI** | 是 | 用于访问目标 Azure 资源或实体的终结点 URL。 URI 语法通常包含 Azure 资源或服务的[资源 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。 |
    | **标头** | 否 | 需要或者想要包含在传出请求中的任何标头值，例如内容类型 |
    | **查询** | 否 | 需要或者想要包含在请求中的任何查询参数，例如特定操作的参数，或要运行的操作的 API 版本 |
    | **身份验证** | 是 | 用于验证对目标资源或实体的访问的身份验证类型 |
    ||||

    举个具体的例子，假设你要针对 Azure 存储帐户中的某个 Blob 运行[快照 Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)，并且你已事先为自己的标识设置了访问权限。 但是，[Azure Blob 存储连接器](https://docs.microsoft.com/connectors/azureblob/)目前不提供此操作。 可以改用 [HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)或另一个 [Blob 服务 REST API 操作](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)来运行此操作。

    > [!IMPORTANT]
    > 若要使用 HTTP 请求和托管标识访问防火墙后面的 Azure 存储帐户，请确保同时在存储帐户中设置[允许受信任 Azure 服务进行访问的例外项](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)。

    若要运行[快照 Blob 操作](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)，HTTP 操作将指定以下属性：

    | 属性 | 必须 | 示例值 | 说明 |
    |----------|----------|---------------|-------------|
    | **方法** | 是 | `PUT`| 快照 Blob 操作使用的 HTTP 方法 |
    | **URI** | 是 | `https://{storage-account-name}.blob.core.chinacloudapi.cn/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Azure 全球云（公有云）环境中的 Azure Blob 存储文件的资源 ID，使用此语法 |
    | **标头** | 对于 Azure 存储为“是” | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | `x-ms-blob-type` 和 `x-ms-version` 标头值对于Azure 存储操作是必需的。 <p><p>**重要说明**：在 Azure 存储的传出 HTTP 触发器和操作请求中，标头需要 `x-ms-version` 属性，以及所要运行的操作的 API 版本。 <p>有关详细信息，请参阅以下主题： <p><p>- [请求标头 - 快照 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br />- [Azure 存储服务的版本控制](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
    | **查询** | 对于此操作为“是” | `comp` = `snapshot` | 快照 Blob 操作的查询参数名称和值。 |
    |||||

    下面是显示了所有这些属性值的示例 HTTP 操作：

    ![添加用于访问 Azure 资源的 HTTP 操作](./media/create-managed-service-identity/http-action-example.png)

1. 现在，请将“身份验证”属性添加到 HTTP 操作。  在“添加新参数”列表中，选择“身份验证”。  

    ![将“身份验证”属性添加到 HTTP 操作](./media/create-managed-service-identity/add-authentication-property.png)

    > [!NOTE]
    > 并非所有触发器和操作都支持添加身份验证类型。 有关详细信息，请参阅[针对出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 在“身份验证类型”列表中，选择“托管标识”。  

    ![对于“身份验证”，请选择“托管标识”](./media/create-managed-service-identity/select-managed-identity.png)

1. 在托管标识列表中，根据方案从可用选项中进行选择。

    * 如果设置了系统分配的标识，请选择“系统分配的托管标识”（如果尚未选择）。 

        ![选择“系统分配的托管标识”](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

    <!--Not Available on * If you set up a user-assigned identity, select that identity if not already selected.-->
    <!--Not Available on ![Select the user-assigned identity](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)-->

    此示例将继续使用“系统分配的托管标识”。 

1. 在某些触发器和操作中，还会显示“受众”属性用于设置目标资源 ID。  将“受众”属性设置为[目标资源或服务的资源 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。  否则，“受众”属性默认使用 `https://management.chinacloudapi.cn/` 资源 ID，即 Azure 资源管理器的资源 ID。 

   > [!IMPORTANT]
   > 请确保该目标资源 ID 与 Azure Active Directory (AD) 所需的值完全匹配，包括所有必要的尾部斜杠。  例如，所有 Azure Blob 存储帐户的资源 ID 都需要尾部斜杠。 但是，特定存储帐户的资源 ID 不需要尾部斜杠。 请查看[支持 Azure AD 的 Azure 服务的资源 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

   此示例将“受众”属性设置为 `https://storage.azure.com/`，使得用于身份验证的访问令牌对所有存储帐户均有效。  但是，也可以指定特定存储帐户的根服务 URL `https://fabrikamstorageaccount.blob.core.chinacloudapi.cn`。

   ![将“受众”属性设置为目标资源 ID](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   有关使用 Azure AD 授权访问 Azure 存储的详细信息，请参阅以下主题：

   * [使用 Azure Active Directory 授权访问 Azure Blob 和队列](../storage/common/storage-auth-aad.md)
   * [使用 Azure Active Directory 授权访问 Azure 存储](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 继续按照所需方式生成逻辑应用。

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>禁用托管标识

若要停止对逻辑应用使用托管标识，可使用以下选项：

* [Azure 门户](#azure-portal-disable)
* [Azure Resource Manager 模板](#template-disable)
* Azure PowerShell
    * [删除角色分配](../role-based-access-control/role-assignments-powershell.md)
    * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
    * [删除角色分配](../role-based-access-control/role-assignments-cli.md)
    * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
    * [删除角色分配](../role-based-access-control/role-assignments-rest.md)
    * [删除用户分配的标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

如果删除逻辑应用，Azure 将从 Azure AD 中自动删除托管标识。

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>在 Azure 门户中禁用托管标识

在 Azure 门户中，首先删除该标识对[目标资源](#disable-identity-target-resource)的访问权限。 接下来，禁用系统分配的标识。

<!--Not Available on  or remove the user-assigned identity from [your logic app](#disable-identity-logic-app)-->

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>从资源中删除标识访问权限

1. 在 [Azure 门户](https://portal.azure.cn)中，转到你要在其中删除托管标识访问权限的目标 Azure 资源。

1. 在目标资源的菜单中，选择“访问控制(IAM)”。  在工具栏下，选择“角色分配”。 

1. 在角色列表中，选择要删除的托管标识。 在工具栏上，选择“删除”。 

    > [!TIP]
    > 如果“删除”选项已禁用，则很可能是你没有权限。  有关可用于管理资源角色的权限的详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

现已删除该托管标识，它不再有权访问目标资源。

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>在逻辑应用中禁用托管标识

1. 在 [Azure 门户](https://portal.azure.cn)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单中的“设置”下选择“标识”，然后针对标识执行以下步骤：  

    * 选择“系统分配” > “打开” > “保存”。    当 Azure 提示确认时，请选择“是”。 

        ![禁用系统分配的标识](./media/create-managed-service-identity/disable-system-assigned-identity.png)

    <!--Not Available on * Select **User assigned** -->

现已在逻辑应用中禁用托管标识。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>在 Azure 资源管理器模板中禁用托管标识

如果使用 Azure 资源管理器模板创建了逻辑应用的托管标识，请将 `identity` 对象的 `type` 子属性设置为 `None`。 对于系统托管的标识，此操作还会从 Azure AD 中删除主体 ID。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>后续步骤

* [在 Azure 逻辑应用中保护访问和数据](../logic-apps/logic-apps-securing-a-logic-app.md)

<!-- Update_Description: new article about create managed service identity -->
<!--NEW.date: 03/30/2020-->