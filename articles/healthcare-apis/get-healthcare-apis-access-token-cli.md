---
title: 使用 Azure CLI 获取访问令牌 - Azure API for FHIR
description: 本文介绍如何使用 Azure CLI 获取 Azure API for FHIR 的访问令牌。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: mihansen
ms.openlocfilehash: d5c9a57e2c5622fc00a7d7038be6499b05149a3f
ms.sourcegitcommit: 6ddc26f9b27acec207b887531bea942b413046ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80343831"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>使用 Azure CLI 获取 Azure API for FHIR 的访问令牌

本文介绍如何使用 Azure CLI 获取 Azure API for FHIR 的访问令牌。 [预配 Azure API for FHIR](fhir-paas-portal-quickstart.md) 时，可配置一组有权访问该服务的用户或服务主体。 如果你的用户对象 ID 包含在允许的对象 ID 列表中，则你可以使用通过 Azure CLI 获取的令牌来访问该服务。


## <a name="sign-in-with-azure-cli"></a>使用 Azure CLI 登录

在获取令牌之前，需使用你要获取其令牌的用户登录：

```azurecli
az login
```

## <a name="obtain-a-token"></a>获取令牌

Azure API for FHIR 结合与 FHIR 服务器的 URI `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` 相同的 URI 使用 `resource` 或 `Audience`。 可以使用以下命令获取令牌并将其存储在变量（名为 `$token`）中：

```azurecli
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com | jq -r .accessToken)
```

## <a name="use-with-azure-api-for-fhir"></a>与 Azure API for FHIR 配合使用

```azurecli
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何使用 Azure CLI 获取 Azure API for FHIR 的访问令牌。 若要了解如何使用 Postman 访问 FHIR API，请继续阅读 Postman 教程。

>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR API](access-fhir-postman-tutorial.md)