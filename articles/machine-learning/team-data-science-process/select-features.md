---
title: Team Data Science Process의 기능 선택
description: 기능 선택의 목적을 설명하고 기계 학습의 데이터 향상 프로세스에서 수행하는 역할의 예를 제공합니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716681"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>TDSP(팀 데이터 과학 프로세스)의 기능 선택
이 문서에서는 기능 선택의 목적을 설명하고 기계 학습의 데이터 향상 프로세스에서 수행하는 역할의 예를 제공합니다. 이들 예는 Azure Machine Learning Studio에서 가져온 것입니다.

기능의 엔지니어링 및 선택은 [팀 데이터 과학 프로세스가 무엇인가요?](overview.md) 문서에 설명된 팀 데이터 과학 프로세스의 한 부분입니다. 기능 엔지니어링 및 선택은 TDSP의 **개발 기능** 단계의 일부입니다.

* **기능 엔지니어링**이 프로세스에서는 데이터의 기존 원시 기능에서 추가 관련 기능을 만들고 학습 알고리즘의 예측 능력을 향상시키려 합니다.
* **선택 기능**: 이 프로세스에서는 학습 문제의 차원 수를 줄이기 위해 원래 데이터 기능의 주요 하위 집합을 선택합니다.

일반적으로 추가 기능을 생성하기 위해 **기능 엔지니어링**을 먼저 적용한 다음, 관련이 없는 중복 기능이나 고도로 상관된 기능을 제거하기 위해 **기능 선택** 단계가 수행됩니다.

## <a name="filter-features-from-your-data---feature-selection"></a>데이터에서 기능 필터링 - 기능 선택
功能选择可用于分类或回归任务。 최소한의 기능 집합을 사용하여 데이터의 최대 분산 크기를 표시함으로써 차원수를 줄이는 원래 데이터 세트의 하위 집합을 선택하기 위해 사용합니다. 이 기능 하위 집합은 모델을 교육하는 데 사용됩니다. 기능 선택은 두 가지 기본 용도로 사용됩니다.

* 첫째 기능 선택을 수행하면 관련이 없는 중복된 기능이나 고도로 상관된 기능을 제거하여 분류 정확도를 높입니다.
* 둘째, 기능 수가 줄어들어 모델 학습 프로세스의 효율성을 높입니다. 效率对于培训昂贵的学习器（如支持矢量机）很重要。

기능 선택에서 모델을 학습시키는 데 사용하는 데이터 세트의 기능 수를 줄이려고 하지만, “차원 수 감소”라는 용어로 부르지 않습니다. 기능 선택 메서드에서는 원래 기능을 변경하지 않고 데이터에서 원래 기능의 하위 집합을 추출합니다.  차원 수 감소 메서드에서는 원래 기능을 변환하므로 기능을 수정할 수 있는 엔지니어링된 기능을 사용합니다. 차원 수 감소 메서드의 예로는 주성분 분석, 표준 상관 분석 및 특이값 분해가 있습니다.

그중에서도 감독된 컨텍스트에서 가장 널리 적용되는 기능 선택 메서드 범주는 “필터 기반 기능 선택"이라고 합니다. 이러한 메서드에서는 각 기능과 대상 특성 사이의 상관 관계를 평가하여, 각 기능에 점수를 할당하기 위해 통계 측정값을 적용합니다. 그런 다음 특정 기능을 보유하거나 제거하는 임계값을 설정하는 데 사용할 수 있는 점수별로 기능에 순위가 지정됩니다. 이러한 메서드에서 사용하는 통계 측정값의 예로는 Person 상관, 상호 정보 및 카이 제곱 테스트가 있습니다.

Azure Machine Learning Studio에서는 기능 선택에 제공되는 모듈이 있습니다. 如下图所示，这些模块包括[基于筛选器的特征选择][filter-based-feature-selection]和[费舍尔线性判别分析][fisher-linear-discriminant-analysis]。

![기능 선택 모듈](./media/select-features/feature-Selection.png)

例如，请考虑使用[基于筛选器的特征选择][filter-based-feature-selection]模块。 편의를 위해 텍스트 마이닝 예제를 계속 사용합니다. 假设您想要在通过[特征哈希][feature-hashing]模块创建一组256功能后构建回归模型，并且响应变量为包含书籍考核分级的 "Col1" （范围从1到5）。 "기능 점수 매기기 메서드”를 "Pearson 상관"으로 설정하고 "대상 열”은 "Col1"로 설정하며 "원하는 기능 수"는 50으로 설정합니다. 然后，[基于模块筛选器的特征选择][filter-based-feature-selection]将生成一个包含50功能的数据集以及目标属性 "Col1"。 다음 그림에서는 입력 매개 변수와 이 실험의 흐름을 보여줍니다.

![基于筛选器的特征选择模块属性](./media/select-features/feature-Selection1.png)

다음 그림에서는 결과 데이터 세트를 보여줍니다.

![필터 기반 기능 선택 모듈에 대한 결과 데이터 세트](./media/select-features/feature-Selection2.png)

각 기능과 대상 특성 "Col1" 사이의 Pearson 상관 관계에 따라 기능의 점수를 매깁니다. 점수가 가장 높은 기능이 유지됩니다.

선택한 기능의 해당 점수는 다음 그림에 표시되어 있습니다.

![필터 기반 기능 선택 모듈에 대한 점수](./media/select-features/feature-Selection3.png)

通过应用此[基于筛选器的特征选择][filter-based-feature-selection]模块，将会选择50的256功能，因为它们具有与目标变量 "Col1" 最相关的特征，基于计分方法 "皮尔逊相关"。

## <a name="conclusion"></a>결론
特征工程和特征选择是两个常见的工程和选择的功能，可提高尝试提取数据中包含的关键信息的定型过程的效率。 이러한 모델이 입력 데이터를 정확하게 분류하고 원하는 결과를 더욱 안정적으로 예측하는 기능도 향상됩니다. 컴퓨터를 통해 학습을 다루기가 더욱 쉽도록 기능 엔지니어링 및 선택을 결합할 수도 있습니다. 이 작업은 모델을 보정하거나 학습하는 데 필요한 기능을 향상시키고 해당 수를 줄여 수행합니다. 수학적인 관점에서 보자면 모델을 학습하기 위해 선택한 기능은 데이터의 패턴을 설명한 다음 결과를 성공적으로 예측하는 최소한의 독립 변수 집합입니다.

기능 엔지니어링이나 기능 선택을 반드시 항상 수행할 필요는 없습니다. 이러한 기능의 필요 여부는 수집한 데이터, 선택한 알고리즘 및 실험 목적에 따라 달라집니다.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

