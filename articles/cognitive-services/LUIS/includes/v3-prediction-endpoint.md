---
title: 如何获取 V3 预测终结点
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 518ee2e05424521875d428340b1f19aa3a7ec0d4
ms.sourcegitcommit: cf73284534772acbe7a0b985a86a0202bfcc109e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74884476"
---
1. 在 LUIS 门户中，在“管理”部分（右上方菜单）的“密钥和终结点”页（左侧菜单）上，选择页面底部的“终结点 URL”。

    此操作会打开浏览器选项卡，其地址栏中包含终结点 URL。

    该 URL 具有应用 ID、密钥和槽名称。 V3 预测终结点 URL 如下所示：

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

