---
title: 语言支持 - 文本分析 API
titleSuffix: Azure Cognitive Services
description: 文本分析 API 支持的自然语言列表。 本文介绍了以下每项操作支持哪些语言：情绪分析、关键短语提取、语言检测和实体识别。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: fcf7051a631e74f2f6db918c7c861e32295ae9e8
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732631"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>文本分析 API 的语言和区域支持

本文介绍了每个操作支持哪些语言：情绪分析、关键短语提取、语言检测和命名实体识别。

## <a name="language-detection"></a>语言检测

文本分析 API 可以检测各种语言、变种、方言和某些区域/文化语言。  语言检测返回一种语言的“脚本”。 例如，对于短语“I have a dog”，它将返回 `en` 而非 `en-US`。 唯一的特例是中文，对于中文，如果语言检测功能可以确定所提供的文本的脚本，则它将返回 `zh_CHS` 或 `zh_CHT`。 当无法识别中文文档的具体脚本时，它将简单地返回 `zh`。

我们不会发布此功能的确切语言列表，但它可以检测各种语言、变体、方言和一些区域/文化语言。 

如果内容是用较少使用的语言表示的，则可以尝试“语言检测”来查看它是否返回代码。 无法检测到的语言的响应为 `unknown`。

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>情绪分析、关键短语提取和命名实体识别

对于情绪分析、关键短语提取和实体识别，所支持语言的列表更具选择性，因为分析器已优化为适应更多语言的语言规则。 在命名实体识别 v2 中，对完整的[实体类型](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)集的支持当前限制为以下语言： 
* 英语
* 简体中文
* 法语
* 德语
* 西班牙语

对于其他语言，只返回 `Person`、`Location` 和 `Organization` 命名实体。

## <a name="language-list-and-status"></a>语言列表和状态

语言支持最初在预览版中推广，渐变到正式版 (GA) 状态，且各种语言彼此独立并且总体上独立于文本分析服务。 即使文本分析 API 转变为正式版时，有些语言也可能保持在预览版。

> [!NOTE]
> 有关命名实体识别（NER） v3 公共预览版的详细语言支持，请参阅[命名实体类型](named-entity-types.md)。

| 语言              | 语言代码 | 情绪 | 关键短语 | 识别命名实体 | 实体链接 |       说明        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| 阿拉伯语                |     `ar`      |           |             |           ✔ \*           |                |                    |
| 捷克语                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| 简体中文    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                |                    |
| 繁体中文   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| 丹麦语                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 荷兰语                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 英语               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| 芬兰语               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 法语                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| 德语                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| 希腊语                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| 匈牙利语             |     `hu`      |           |             |           ✔ \*           |                |                    |
| 意大利语               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 日语              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 韩语                |     `ko`      |           |      ✔      |           ✔ \*           |                |                    |
| 挪威语(博克马尔语)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 波兰语                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 葡萄牙语(葡萄牙) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | `pt` 也接受 |
| 葡萄牙语（巴西）   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| 俄语               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 西班牙语               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| 瑞典语               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 土耳其语               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

\* 语言支持处于预览阶段

\** 还可在[情绪分析 v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features)和/或[命名实体识别 v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)公共预览版中使用。

## <a name="see-also"></a>另请参阅

[认知服务文档页面](https://docs.microsoft.com/azure/cognitive-services/)   
[认知服务产品页](https://azure.microsoft.com/services/cognitive-services/)
