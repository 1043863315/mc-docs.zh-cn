---
title: 术语表 - LUIS
titleSuffix: Azure Cognitive Services
description: 术语表解释使用 LUIS API 服务时可能遇到的术语。
services: cognitive-services
author: lingliw
manager: digimobile
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
origin.date: 10/25/2019
ms.date: 12/04/2019
ms.author: v-lingwu
ms.openlocfilehash: 803c4f20be8458d7758db7c66787e99d2f792669
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79292141"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>对包含常见词汇和概念的术语表进行语言理解
语言理解 (LUIS) 术语表解释使用 LUIS API 服务时可能遇到的术语。

<a name="active-version"></a>
## <a name="active-version"></a>活动版本

活动 LUIS 版本是接收所有模型更改的版本。 在 [LUIS](luis-reference-regions.md) 网站中，如果想对非活动版本进行更改，需先将其设为活动版本。

<a name="authoring"></a>
## <a name="authoring"></a>创作

创作是使用 [LUIS](luis-reference-regions.md) 网站或[创作 API](https://aka.ms/luis-authoring-api) 创建、管理和部署 [LUIS 应用](#luis-app)的能力。

<a name="authoring-key"></a>
## <a name="authoring-key"></a>创作密钥

以前名为“编程”密钥。 用于创作应用。 不用于生产级别的终结点查询。 有关详细信息，请参阅[密钥限制](luis-boundaries.md#key-limits)。

## <a name="batch-test-json-file"></a>批处理测试 JSON 文件

批处理测试是使用一组一致且已知的用户话语测试集验证当前 LUIS 应用模型的功能。 批处理测试在 [JSON 格式化文件](luis-concept-batch-test.md#batch-file-format)中定义。

另请参阅：
* [概念](luis-concept-batch-test.md)
* [操作说明](luis-how-to-batch-test.md)
* [教程](luis-tutorial-batch-testing.md)


## <a name="collaborator"></a>协作者

协作者/参与者不是应用的[所有者](#owner)，但具有相同权限，可以添加、编辑和删除意向、实体和话语。

## <a name="contributor"></a>参与者

参与者与[协作者](#collaborator)相同。

## <a name="descriptor"></a>描述符

描述符是在训练时应用到模型的特征，包括短语列表和[实体](#entity)。 

## <a name="domain"></a>域

在 LUIS 上下文中，域是一个知识领域  。 域特定于应用的知识领域。 这可以是一个宽泛领域，例如旅行代理应用。 旅行代理应用还可特定于公司的信息领域，例如特定的地理位置、语言和服务。

## <a name="endpoint"></a>终结点

[LUIS 终结点](https://go.microsoft.com/fwlink/?linkid=2092356) URL 是在创作并发布 [LUIS 应用](#luis-app)后提交 LUIS 查询的地方。 终结点 URL 包含所发布的应用的区域和应用 ID。 可以在应用的 **[密钥和终结点](luis-how-to-azure-subscription.md)** 页上找到终结点，也可以从[获取应用信息](https://{region}.dev.cognitive.azure.cn/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API 获取终结点 URL。

## <a name="entity"></a>实体

[实体](luis-concept-entity-types.md)是[话语](luis-concept-utterance.md)中的重要字词，用于描述与[意向](luis-concept-intent.md)相关的信息，有时起着至关重要的作用。 实体本质上是 LUIS 中的数据类型。
<!-- the content is unavailable-->
<a name="intent"></a>
## <a name="intent"></a>Intent

[意向](luis-concept-intent.md)表示用户想执行的任务或操作。 系指用户输入的内容中所表达的目的或目标，比如预订航班、支付帐单或查找新闻文章。 在 LUIS 中，意向预测基于整个话语。 而实体是话语片段。

## <a name="labeling"></a>标记

标记是将某个意向的[话语](#utterance)中的词或短语与[实体](#entity)（数据类型）相关联的过程。

## <a name="luis-app"></a>LUIS 应用

LUIS 应用是用于自然语言处理的语言模型的集合，包括[意向](#intent)、[实体](#entity)和标记的[话语](#utterance)。

## <a name="owner"></a>所有者

每个应用都有一个所有者，即创建应用的人。 所有者可以添加[协作者](#collaborator)。

## <a name="pattern"></a>模式
之前的模式功能已替换为[模式](luis-concept-patterns.md)。 使用模式，通过提供较少的训练示例来提高预测准确性。

## <a name="phrase-list"></a>短语列表

[短语列表](luis-concept-feature.md)包括一组值（词或短语），它们属于同一个类，并且必须以同样的方式处理它们（例如城市或产品名称）。 可互换的列表被视为同义词。

预生成域是为特定域配置的 LUIS 应用，例如家庭自动化 (HomeAutomation) 或餐厅订位 (RestaurantReservation)。 已为此域配置意向、话语和实体。

<a name="prebuilt-entity"></a>
## <a name="prebuilt-entity"></a>预生成实体

预生成实体是 LUIS 为常用信息类型提供的实体（例如数字、URL 和电子邮件）。 选择将预生成的实体添加到自己的应用程序。

<a name="precision"></a>
## <a name="precision"></a>Precision
在批处理测试中，精准率（也称为正预测值）是相关话语在检索到的话语中所占的比例。

## <a name="programmatic-key"></a>编程密钥

已重命名为[创作密钥](#authoring-key)。

## <a name="publish"></a>发布

发布指在暂存或生产[终结点](#endpoint)上提供一个 LUIS 活动版本。  

## <a name="quota"></a>配额

LUIS 配额是 [Azure 订阅层](https://www.azure.cn/zh-cn/pricing/details/cognitive-services)的限制。 可同时通过每秒请求数（HTTP 状态 429）和每月请求总数（HTTP 状态 403）来限制 LUIS 配额。

<a name="recall"></a>
## <a name="recall"></a>召回率
在批处理测试中，召回率（也称为敏感度）是 LUIS 进行通用化的能力。

## <a name="semantic-dictionary"></a>语义字典
可通过列表实体页以及短语列表页获取语义字典。 语义字典基于当前作用域提供字词建议。

## <a name="sentiment-analysis"></a>情绪分析
情绪分析提供[文本分析](https://www.azure.cn/zh-cn/home/features/cognitive-services/text-analytics/)所提供的话语的正值或负值。

## <a name="speech-priming"></a>语音启动

语音启动允许使用 LUIS 模型预先准备语音服务。

## <a name="spelling-correction"></a>拼写更正

启用必应拼写检查器，在进行预测前更正话语中拼写错误的字词。

## <a name="starter-key"></a>初学者密钥

首次开始使用 LUIS 时要使用的免费密钥。

## <a name="structure"></a>结构

向机器学习实体添加结构，为子组件提供描述符（特征）和约束（正则表达式或列表实体）。

## <a name="subscription-key"></a>订阅密钥

订阅密钥是与[你在 Azure 中创建](luis-how-to-azure-subscription.md)的 LUIS 服务关联的**预测终结点**密钥。 此密钥不是[创作密钥](#programmatic-key)。 如果有终结点密钥，则应为所有终结点请求使用该密钥，而非创作密钥。 可在 [LUIS](luis-reference-regions.md) 网站中的[“密钥和终结点”页](luis-how-to-azure-subscription.md)底部的终结点 URL 内看到当前的终结点密钥  。 它是“subscription-key”名称/值对的值  。

## <a name="test"></a>测试

测试 LUIS 应用就是将一个话语传递至 LUIS 并查看 JSON 结果。

## <a name="timezoneoffset"></a>时区偏移

包含 timezoneOffset 的终结点。 这是要从预生成的实体 datetimeV2 删除或向其添加的分钟数。 例如，如果话语为“现在几点了？”，则返回的 datetimeV2 是发出客户端请求时的当前时间。 如果客户端请求来自聊天机器人或其他不同于聊天机器人的用户的应用程序，则应传入机器人与该用户之间的时间偏差量。

请参阅[更改预生成的 datetimeV2 实体的时区](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)。

## <a name="token"></a>标记
标记是实体中可标记的最小单位。 词汇切分基于应用程序的[区域性](luis-language-support.md#tokenization)。

## <a name="train"></a>训练

训练指让 LUIS 了解并习得自上次训练之后对活动版本所做的更改的过程。

训练指让 LUIS 了解并习得自上次训练之后对[活动版本](#active-version)所做的更改的过程。

<a name="utterance"></a>
## <a name="utterance"></a>话语

话语是一条自然语言短语，例如“订 2 张下周二到西雅图的票”。 将话语示例添加到意向。

## <a name="version"></a>版本

LUIS [版本](luis-how-to-manage-versions.md)是与 LUIS 应用 ID 和所发布的终结点关联的特定数据模型。 每个 LUIS 应用至少有一个版本。
