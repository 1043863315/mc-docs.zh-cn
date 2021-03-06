---
title: 文本翻译认知技能
titleSuffix: Azure Cognitive Search
description: 对文本进行评估，并针对每个记录在 Azure 认知搜索中的 AI 扩充管道中返回已翻译为指定目标语言的文本。
manager: nitinme
author: careyjmac
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 11/04/2019
ms.date: 03/16/2020
ms.openlocfilehash: 9e38b68a472882bdaebe5087cb511ef7099cd488
ms.sourcegitcommit: d5eca3c6b03b206e441b599e5b138bd687a91361
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78934842"
---
#   <a name="text-translation-cognitive-skill"></a>文本翻译认知技能

**文本翻译**技能对文本进行评估，并针对每个记录返回已翻译为指定目标语言的文本。 此技能使用认知服务中提供的[文本翻译 API v3.0](https://docs.azure.cn/cognitive-services/translator/reference/v3-0-translate)。

此功能适用于你预期自己的文档可能不会全部采用一种语言的情况，在这种情况下，你可能会在编制索引以方便搜索之前通过翻译将文本标准化为单一语言。  这也适用于本地化用例，即，你可能需要将同一文本的副本以多种语言发布。

[文本翻译 API v3.0](https://docs.azure.cn/cognitive-services/translator/reference/v3-0-reference) 是一项非区域性认知服务，这意味着，我们不保证你的数据与 Azure 认知搜索或附加的认知服务资源位于同一区域。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 当你行使内置技能时，我们会按现有的[认知服务预付费价格](https://www.azure.cn/pricing/details/cognitive-services/)收费。 图像提取定价如 [Azure 认知搜索定价页](https://www.azure.cn/pricing/details/search)所述。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为 50,000 个字符，通过 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) 进行测量。 如果在将数据发送到文本翻译技能之前需要将其拆分，请考虑使用[文本拆分技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入                | 说明 |
|---------------------|-------------|
| defaultToLanguageCode | （必需）将文档翻译为未显式指定语言的文档时所需的语言代码。 <br/> 请参阅[支持的语言的完整列表](https://docs.azure.cn/cognitive-services/translator/language-support)。 |
| defaultFromLanguageCode | （可选）将文档从未显式指定语言的文档进行翻译时所需的语言代码。  如果未指定 defaultFromLanguageCode，则会使用文本翻译 API 提供的自动语言检测功能来确定源语言。 <br/> 请参阅[支持的语言的完整列表](https://docs.azure.cn/cognitive-services/translator/language-support)。 |
| suggestedFrom | （可选）在 fromLanguageCode 输入和 defaultFromLanguageCode 参数均未提供且自动语言检测操作不成功情况下，将文档从源语言进行翻译所需的语言代码。  如果未指定 suggestedFrom 语言，则会将英语 (en) 用作 suggestedFrom 语言。 <br/> 请参阅[支持的语言的完整列表](https://docs.azure.cn/cognitive-services/translator/language-support)。 |

## <a name="skill-inputs"></a>技能输入

| 输入名称     | 说明 |
|--------------------|-------------|
| text | 要翻译的文本。|
| toLanguageCode    | 一个表示文本翻译目标语言的字符串。 如果未指定此输入，则使用 defaultToLanguageCode 来翻译文本。 <br/>请参阅[支持的语言的完整列表](https://docs.azure.cn/cognitive-services/translator/language-support)|
| fromLanguageCode  | 一个表示文本的当前语言的字符串。 如果此参数未指定，则会使用 defaultFromLanguageCode（在未提供 defaultFromLanguageCode 的情况下，则会使用自动语言检测功能）来翻译文本。 <br/>请参阅[支持的语言的完整列表](https://docs.azure.cn/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 说明 |
|--------------------|-------------|
| translatedText | 进行从 translatedFromLanguageCode 到 translatedToLanguageCode 的文本翻译时获得的字符串结果。|
| translatedToLanguageCode  | 一个表示文本翻译目标语言代码的字符串。 适用于需要翻译为多种语言且需要能够跟踪哪些文本是哪种语言的情况。|
| translatedFromLanguageCode    | 一个表示文本翻译源语言代码的字符串。 适用于已选择启用自动语言检测选项的情况，因为这种情况下的输出会提供该检测的结果。|

##  <a name="sample-definition"></a>示例定义

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>示例输入

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>示例输出

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>错误和警告
如果为源语言或目标语言提供的语言代码不受支持，则会生成错误且文本不会进行翻译。
如果你的文本为空，则不会生成警告。
如果文本大于 50,000 个字符，则只会翻译前 50,000 个字符，并会发出警告。

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
