---
title: 区域 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务（包括语音转文本）的可用区域和终结点的列表。
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 11/05/2019
ms.date: 11/25/2019
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: 3e0143beb2d744ad1295ec99d50dbe909e69364c
ms.sourcegitcommit: 9e92bcf6aa02fc9e7b3a29abadf6b6d1a8ece8c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74389591"
---
# <a name="speech-service-supported-regions"></a>语音服务支持的区域

通过语音服务，应用程序可将音频转换为文本。 多个区域中均提供该服务，这些区域为语音 SDK 和 REST API 使用唯一终结点。

请务必使用与订阅的区域匹配的终结点。

## <a name="speech-sdk"></a>语音 SDK

在[语音 SDK](speech-sdk.md) 中，区域指定为字符串（例如，在 C# 语音 SDK 中用作 `SpeechConfig.FromSubscription` 的参数）。

### <a name="speech-to-text"></a>语音转文本

以下区域提供语音 SDK，用于“语音识别”  ：

| 区域           | 语音 SDK 参数 | 语音自定义门户    |
| ---------------- | -------------------- | ------------------------------ |
| 中国东部 2          | `chinaeast2`             | https://chinaeast2.cris.azure.cn         |

<!-- ### Intent recognition -->


<!-- ### Voice assistants -->


## <a name="rest-apis"></a>REST API

语音服务还为语音转文本请求公开 REST 终结点。

### <a name="speech-to-text"></a>语音转文本

有关语音转文本的参考文档，请参阅[语音转文本 REST API](rest-speech-to-text.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

<!-- ### Text-to-speech -->
