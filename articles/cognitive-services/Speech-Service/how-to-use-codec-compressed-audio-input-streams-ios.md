---
title: 在 iOS 上使用语音 SDK 流式传输编解码器压缩的音频
titleSuffix: Azure Cognitive Services
description: 了解如何在 iOS 上使用语音 SDK 将压缩音频流式传输到 Azure 语音服务。
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 09/20/2019
ms.date: 11/25/2019
ms.author: v-tawe
ms.openlocfilehash: 184ea30de365a7d750af5c46a0af286537835e9f
ms.sourcegitcommit: 9e92bcf6aa02fc9e7b3a29abadf6b6d1a8ece8c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74390007"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>如何：在 iOS 上的语音 SDK 中使用编解码器压缩的音频输入

语音 SDK 的**压缩音频输入流** API 提供了一种使用拉取或推送流将压缩音频流式传输到语音服务的方法。

> [!IMPORTANT]
> 若要在 iOS 上流式传输压缩的音频，需要语音 SDK 1.7.0 或更高版本。 它还支持用于 [Linux 上的 C++、C# 和 Java（Ubuntu 16.04、Ubuntu 18.04、Debian 9）](how-to-use-codec-compressed-audio-input-streams.md)和 [Android 中的 Java。](how-to-use-codec-compressed-audio-input-streams-android.md)

有关 wav/PCM，请参阅主线语音文档。 在 wav/PCM 之外，支持以下编解码器压缩的输入格式：

- MP3
- OPUS/OGG
- FLAC
- wav 容器中的 ALAW
- wav 容器中的 MULAW

## <a name="prerequisites"></a>先决条件

处理压缩音频是使用 [GStreamer](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，这些函数不能随附在 SDK 中，但应用程序开发人员需构建包含这些函数的包装器库，并将其随使用 SDK 的应用一起发送。

若要构建该包装器库，请先下载并安装 [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)。 然后，下载[包装器库](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)的 Xcode 项目。

在 Xcode 中打开此项目，针对**通用 iOS 设备**目标构建它 -- 如果针对特定目标构建它，它将无法正常运行。

构建步骤会生成一个动态框架捆绑包（其中的动态库适用于所有必需的体系结构），其名称为 `GStreamerWrapper.framework`。

此框架必须包含在所有将压缩音频流与语音服务 SDK 配合使用的应用中。

为此，请在 Xcode 项目中应用以下设置：

1. 复制刚构建的 `GStreamerWrapper.framework` 和认知服务语音 SDK 的框架，后者可以从[此处](https://aka.ms/csspeech/iosbinary)下载到示例项目所在的目录。
1. 在“项目设置”中调整框架的路径。 
   1. 在“嵌入式二进制文件”标头下的“常规”选项卡中，添加 SDK 库作为框架   ：“添加嵌入式二进制文件” > “添加其他...”> 导航到所选目录，然后选择两个框架。   。
   1. 转到“生成设置”  选项卡，激活“所有”设置  。
1. 将目录 `$(SRCROOT)/..` 添加到“搜索路径”标头下的“框架搜索路径”。  

## <a name="example-code-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的示例代码

若要以压缩音频格式流式传输到语音服务，请创建 `SPXPullAudioInputStream` 或 `SPXPushAudioInputStream`。

以下代码片段演示如何从 `SPXPushAudioInputStream` 的实例创建 `SPXAudioConfiguration`，并指定 mp3 作为流的压缩格式。

```objectivec
// <setup-stream>
SPXAudioStreamContainerFormat compressedStreamFormat = SPXAudioStreamContainerFormat_MP3;
SPXAudioStreamFormat *audioFormat = [[SPXAudioStreamFormat alloc] initUsingCompressedFormat:compressedStreamFormat];
SPXPushAudioInputStream* stream = [[SPXPushAudioInputStream alloc] initWithAudioFormat:audioFormat];

SPXAudioConfiguration* audioConfig = [[SPXAudioConfiguration alloc] initWithStreamInput:stream];
if (!audioConfig) {
    NSLog(@"Error creating stream!");
    [self updateRecognitionErrorText:(@"Error creating stream!")];
    return;
}
// </setup-stream>
```

下一代码片段演示如何从文件读取压缩的音频数据并将其泵入 `SPXPushAudioInputStream` 中。

```objectivec
// <push-compressed-stream>
NSInputStream *compressedStream = [[NSInputStream alloc] initWithFileAtPath:weatherFile];
[compressedStream open];
NSLog(@"result of opening stream: %@, %lu", compressedStream.streamError, (unsigned long)compressedStream.streamStatus);
if (nil == compressedStream)
{
    NSLog(@"Error while opening file");
    audioConfig = nil;
    return;
}

// start recognizing
[self updateRecognitionStatusText:(@"Recognizing from push stream...")];
[speechRecognizer startContinuousRecognition];

const NSInteger nBytesToRead = 1000;
// push data to stream;
uint8_t *buffer = malloc(nBytesToRead);
NSInteger nBytesRead = 0;
while (1)
{
    // read data
    nBytesRead = [compressedStream read:buffer maxLength:nBytesToRead];
    if (0 == nBytesRead) {
        NSLog(@"end of stream reached");
        [stream close];
        break;
    }
    else if (0 > nBytesRead) {
        NSLog(@"error reading stream (%ld): %@ (%lu)", nBytesRead, compressedStream.streamError, compressedStream.streamStatus);
        [stream close];
        break;
    }
    else
    {
        NSLog(@"Read %lu bytes from file", nBytesRead);
        NSData *data = [NSData dataWithBytesNoCopy:buffer length:nBytesRead freeWhenDone:NO];

        [stream write:data];
        NSLog(@"Wrote %lu bytes to stream", [data length]);
    }

    [NSThread sleepForTimeInterval:0.1f];
}

[speechRecognizer stopContinuousRecognition];
// </push-compressed-stream>
```

## <a name="next-steps"></a>后续步骤

- [获取试用订阅](https://www.azure.cn/pricing/1rmb-trial/)
- [查看如何在 Java 中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
