---
title: 什么是语音服务？
titleSuffix: Azure Cognitive Services
description: 语音服务将“语音转文本”统合到单个 Azure 订阅。 使用语音 SDK、语音设备 SDK 或 REST API 在应用程序、工具和设备中添加语音。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
origin.date: 11/05/2019
ms.date: 11/25/2019
ms.author: v-tawe
ms.openlocfilehash: a7f77f0ae9354ad1613557d881f3c8d5bc235bcc
ms.sourcegitcommit: 9e92bcf6aa02fc9e7b3a29abadf6b6d1a8ece8c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74389460"
---
# <a name="what-are-the-speech-services"></a>什么是语音服务？

语音服务将“语音转文本”统合到单个 Azure 订阅。 使用[语音 SDK](speech-sdk-reference.md)、[语音设备 SDK](speech-devices-sdk-android-quickstart.md) 或 [REST API](rest-apis.md) 可以轻松在应用程序、工具和设备中启用语音。

<!-- > [!IMPORTANT] -->
<!-- > Speech Services have replaced Bing Speech API, Translator Speech, and Custom Speech. See _How-to guides > Migration_ for migration instructions. -->

这些功能构成了 Azure 语音服务。 请使用下表中的链接详细了解每项功能的常见用例或浏览 API 参考信息。

| 服务 | 功能 | 说明 | SDK | REST |
| ------- | ------- | ----------- | --- | ---- |
| [语音转文本](speech-to-text.md) | 语音转文本 | 语音转文本可将音频流实时听录为应用程序、工具或设备可以使用或显示的文本。 结合[语言理解 (LUIS)](https://docs.azure.cn/cognitive-services/luis/) 使用语音转文本可以从听录的语音中派生用户意向，以及处理语音命令。 | [是](https://docs.azure.cn/cognitive-services/speech-service/speech-sdk-reference) | [是](https://docs.azure.cn/cognitive-services/speech-service/rest-apis) |
|         | [批量听录](batch-transcription.md) | 使用批量听录能够以异步方式对大量的数据进行语音转文本听录。 这是一个基于 REST 的服务，它使用的终结点与自定义和模型管理相同。 | 否 | [是](https://chinaeast2.cris.azure.cn/swagger/ui/index) |
|         | [创建自定义语音模型](#customize-your-speech-experience) | 如果使用语音转文本在独特的环境中进行识别和听录，则可以创建并训练自定义的声学、语言和发音模型，以解决环境干扰或行业特定的词汇。 | 否 | [是](https://chinaeast2.cris.azure.cn/swagger/ui/index) |

## <a name="news-and-updates"></a>新增功能和更新

了解 Azure 语音服务的新增功能。

<!-- voice first/bot service not available -->

- 2019 年 9 月
  - 发布了语音 SDK 1.7.0。 有关更新、增强功能和已知问题的完整列表，请参阅[发行说明](releasenotes.md)。
- 2019 年 7 月
  - 发布了语音 SDK 1.6.0。 有关更新、增强功能和已知问题的完整列表，请参阅[发行说明](releasenotes.md)。
- 2019 年 5 月
  - 发布了语音 SDK 1.5.1。 有关更新、增强功能和已知问题的完整列表，请参阅[发行说明](releasenotes.md)。
  - 发布了语音 SDK 1.5.0。 有关更新、增强功能和已知问题的完整列表，请参阅[发行说明](releasenotes.md)。

## <a name="try-speech-services"></a>试用语音服务

我们提供了适用于大多数流行编程语言的快速入门，旨在帮助你在 10 分钟以内运行代码。 下表包含有关每项功能在最流行编程语言中的用法的快速入门。 使用左侧的导航栏可以浏览其他语言和平台。

| 语音转文本 (SDK) |
| -------------------- |
| [识别来自音频文件的语音](quickstarts/speech-to-text-from-file.md) |
| [使用麦克风识别语音](quickstarts/speech-to-text-from-microphone.md) |
| [识别存储在 Blob 存储中的语音](quickstarts/from-blob.md) |

> [!NOTE]
> “语音转文本”功能也有 REST 终结点和关联的快速入门。

<!-- After you've had a chance to use the Speech Services, try our tutorial that teaches you how to recognize intents from speech using the Speech SDK and LUIS. -->

<!-- - [Tutorial: Build a Flask app to translate text, analyze sentiment, and synthesize translated text to speech, REST](/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis) -->

## <a name="get-sample-code"></a>获取示例代码

GitHub 中提供了每个 Azure 语音服务的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。 使用以下链接查看 SDK 和 REST 示例：

- [语音转文本示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customize-your-speech-experience"></a>自定义语音体验

Azure 语音服务能够很好地与内置模型配合工作，但是，你可能想要根据自己的产品或环境，进一步自定义和优化体验。 自定义选项的范围从声学模型优化，到专属于自有品牌的语音字体。 生成自定义模型后，可将其与任何 Azure 语音服务配合使用。

| 语音服务 | 平台 | 说明 |
| -------------- | -------- | ----------- |
| 语音转文本 | [自定义语音](https://chinaeast2.cris.azure.cn/Home/CustomSpeech) | 根据需要和可用数据自定义语音识别模型。 克服语音识别障碍，如说话风格、词汇和背景噪音。 |

## <a name="reference-docs"></a>参考文档

- [语音 SDK](speech-sdk-reference.md)
- [语音设备 SDK](speech-devices-sdk.md)
- [REST API：语音转文本](rest-speech-to-text.md)
- [REST API：批量听录和自定义](https://chinaeast2.cris.azure.cn/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取语音服务订阅密钥以进行试用](get-started.md)
