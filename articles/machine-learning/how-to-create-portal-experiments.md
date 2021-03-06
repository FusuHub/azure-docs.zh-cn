---
title: 生成 & 部署自动 ML 模型
titleSuffix: Azure Machine Learning
description: 在 Azure 机器学习 studio 中创建、管理和部署自动化机器学习试验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 808d7ac7ded9b250e0835da51b6b547c05c622a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720395"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>通过 Azure 机器学习 studio 创建、探索和部署自动化机器学习试验
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

 本文介绍如何在 Azure 机器学习 studio 中创建、浏览和部署自动化机器学习试验，无需编写任何代码。 自动机器学习自动执行选择要用于特定数据的最佳算法的过程，以便您可以快速生成机器学习模型。 [详细了解自动化机器学习](concept-automated-ml.md)。

 如果你更喜欢更多基于代码的体验，还可以使用[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)[在 Python 中配置自动化机器学习试验](how-to-configure-auto-train.md)。

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* 一个具有**企业版**类型的 Azure 机器学习工作区。 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.  若要将现有工作区升级到 Enterprise edition，请参阅[升级到 enterprise edition](how-to-manage-workspace.md#upgrade)。

## <a name="get-started"></a>시작하기

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다. 

1. 选择订阅和工作区。 

1. 导航到左窗格。 在**作者**部分下选择 "**自动 ML** "。

[![Azure Machine Learning Studio 탐색 창](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 如果这是你首次执行任何试验，你将看到一个空列表和文档链接。 

否则，你将看到最近自动机器学习试验的列表，包括使用 SDK 创建的实验。 

## <a name="create-and-run-experiment"></a>创建并运行试验

1. 选择 " **+ 创建试验**" 并填充窗体。

1. 从存储容器中选择数据集，或创建新的数据集。 可以从本地文件、web url、数据存储或 Azure 开放数据集创建数据集。 

    >[!Important]
    > 定型数据的要求：
    >* 数据必须为表格格式。
    >* 要预测的值（目标列）必须在数据中存在。

    1. 若要从本地计算机上的文件创建新的数据集，请选择 "**浏览**"，然后选择该文件。 

    1. 데이터 세트에 고유 이름을 지정하고 선택적 설명을 입력합니다. 

    1. 选择 "**下一步**"，将其上传到自动使用工作区创建的默认存储容器，或选择要用于试验的存储容器。 

    1. 查看**设置和预览**表单的准确性。 将根据文件类型智能地填充窗体。 

        필드| Description
        ----|----
        파일 형식| 파일에 저장된 데이터의 레이아웃 및 유형을 정의합니다.
        구분 기호| 一个或多个字符，用于在纯文本或其他数据流中的独立区域之间指定边界。
        Encoding| 데이터 세트를 읽는 데 사용할 문자 스키마 테이블을 식별합니다.
        열 머리글| 데이터 세트의 헤더(있는 경우)가 처리되는 방법을 나타냅니다.
        행 건너뛰기 | 데이터 세트에서 건너뛴 행(있는 경우)의 수를 나타냅니다.
    
        **다음**을 선택합니다.

    1. **架构**窗体根据 "**设置" 和 "预览**" 窗体中的选择进行智能填充。 此处配置每个列的数据类型，查看列名，并为试验选择**不包括**的列。 
            
        选择 "**下一步"。**

    1. "**确认详细**信息" 窗体是以前在**基本信息**和**设置和预览**表单中填充的信息的摘要。 你还可以选择使用启用了分析的计算来分析你的数据集。 [데이터 프로파일링](#profile)에 대한 자세한 정보

        **다음**을 선택합니다.
1. 出现后，选择新创建的数据集。 您还可以查看数据集和示例统计信息的预览。 

1. 在 "**配置运行**" 窗体上，输入唯一的实验名称。

1. 选择目标列;这是要对其进行预测的列。

1. 为数据事件探查和定型作业选择计算。 下拉列表中提供了现有计算的列表。 若要创建新计算，请按照步骤7中的说明进行操作。

1. 选择 "**创建新计算**"，为此试验配置计算上下文。

    필드|Description
    ---|---
    컴퓨팅 이름| 컴퓨팅 컨텍스트를 식별하는 고유한 이름을 입력합니다.
    가상 머신 크기| 컴퓨팅에 사용할 가상 머신 크기를 선택합니다.
    최소/최대 노드(고급 설정)| 데이터를 프로파일링하려면 하나 이상의 노드를 지정해야 합니다. 输入计算的最大节点数。 默认值为用于 AML 计算的6个节点。
    
    **만들기**를 선택합니다. 创建新计算可能需要几分钟时间。

    >[!NOTE]
    > 你的计算名称将指示你选择/创建的计算是否已*启用分析*。 （有关详细信息，请参阅[数据分析](#profile)部分）。

    **다음**을 선택합니다.

1. 在 "**任务类型" 和 "设置**" 窗体上，选择任务类型：分类、回归或预测。 有关详细信息，请参阅[如何定义任务类型](how-to-define-task-type.md)。

    1. 对于分类，还可以启用用于文本 featurizations 的深度学习。

    1. 对于预测：
        1. 选择时间列：此列包含要使用的时间数据。

        1. 选择预测范围：指示该模型能够预测到未来的时间单位（分钟/小时/天/周/月/年）。 需要进一步预测模型以预测未来，它的准确性越低。 [了解有关预测和预测范围的详细信息](how-to-auto-train-forecast.md)。

1. 可有可无添加配置：可用于更好地控制训练作业的其他设置。 그렇지 않으면 실험 선택 및 데이터를 기반으로 기본값이 적용됩니다. 

    추가 구성|Description
    ------|------
    기본 메트릭| 用于对模型进行评分的主要指标。 [详细了解模型指标](how-to-configure-auto-train.md#explore-model-metrics)。
    자동 기능화| 选择此以启用或禁用自动机器学习完成的预处理。 预处理包括用于生成综合功能的自动数据清理、准备和转换。 [详细了解预处理](#preprocess)。
    阻塞算法| 选择要从定型作业中排除的算法。
    종료 기준| 如果满足上述任一条件，则会停止训练作业。 <br> *训练作业时间（小时）* ：允许定型作业运行多长时间。 <br> *指标分数阈值*：所有管道的最小指标分数。 这可以确保如果你有想要达到的已定义目标指标，则不需要花费更多时间来完成培训作业。
    유효성 검사| 选择要在训练作业中使用的交叉验证选项之一。 [了解有关交叉验证的详细信息](how-to-configure-auto-train.md)。
    동시성| *最大并发迭代*数：定型作业中要测试的管道的最大数目（迭代）。 作业运行的次数不能超过指定的迭代次数。 <br> *每个迭代的最大核心*数：选择使用多核计算时要使用的多核限制。

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>数据事件探查 & 摘要统计信息

你可以在数据集中获取各种摘要统计信息，以验证你的数据集是否是 ML 就绪。 对于非数字列，它们仅包含最小值、最大值和错误计数等基本统计信息。 对于数字列，还可以查看其统计时间和估计分位数。 具体而言，我们的数据配置文件包括：

>[!NOTE]
> 对于具有不相关类型的功能，将显示空白条目。

통계|Description
------|------
기능| 正在汇总的列的名称。
프로필| 基于推断类型的行内可视化。 例如，字符串、布尔值和日期具有值计数，而小数（数字）则具有近似的直方图。 这使你可以快速了解数据的分布情况。
类型分发| 列中类型的行内值计数。 Null 是其自己的类型，因此此可视化效果可用于检测奇值或缺失值。
유형|推断列的类型。 可能的值包括：字符串、布尔值、日期和小数。
최소값| 列的最小值。 对于类型不具有固有顺序（例如布尔值）的功能，将显示空白项。
최대| 列的最大值。 
카운트| 列中缺失和缺失条目的总数。
누락되지 않은 수| 列中不存在的条目数。 空字符串和错误被视为值，因此它们不会影响 "不缺少计数"。
分位数| 每个分位上的近似值用于提供数据的分布。
평균| 列的算术平均值或平均值。
표준 편차| 度量此列数据的散射量或变体量。
Variance| 此列的数据超出其平均值的度量值。 
왜곡도| 衡量此列的数据与正态分布的不同之处。
첨도| 对此列的数据与正态分布进行比较的尾量的度量值。


<a name="preprocess"></a>

## <a name="advanced-featurization-options"></a>高级特征化选项

配置试验时，可以启用 "高级" 设置 `feauturization`。 

|特征化配置 | Description |
| ------------- | ------------- |
|"feauturization" = "FeaturizationConfig"| 指示应使用自定义的特征化步骤。 [了解如何自定义特征化](how-to-configure-auto-train.md#customize-feature-engineering)。|
|"feauturization" = "off"| 指示不应自动执行特征化步骤。|
|"feauturization" = "auto"| 指示在预处理过程中，将自动执行以下数据 guardrails 和特征化步骤。|

|预处理&nbsp;步骤| Description |
| ------------- | ------------- |
|높은 카디널리티 또는 분산 없는 기능 삭제|从定型集和验证集删除这些值，包括所有缺少值的特征、所有行中的相同值或基数极高的基数（例如，哈希、Id 或 Guid）。|
|누락 값 입력|对于数值特征，归结列中的值的平均值。<br/><br/>对于分类特征，归结是最常用的值。|
|추가 기능 생성|对于日期时间功能：年、月、日、星期几、每年的哪一天、每个季度、每周的哪一天、小时、分钟、秒。<br/><br/>对于文本功能：基于获得单元语法、bi 语法和三字符语法的术语频率。|
|转换和编码 |具有几个唯一值的数字特征会转换为分类特征。<br/><br/>对低基数分类执行一次热编码;用于高基数、一次热哈希编码。|
|Word 嵌入|文本特征化器，它使用预先训练的模型将文本标记的矢量转换为句型向量。 文档中的每个单词的嵌入向量聚合在一起，以生成文档功能矢量。|
|目标编码|对于分类功能，将每个类别与回归问题的平均目标值一起映射，并将每个类别的类概率映射到分类问题。 应用基于频率的权重和 k 折交叉验证，以减少稀疏数据类别导致的映射和干扰。|
|文本目标编码|对于文本输入，将使用带有词袋的堆积线性模型来生成每个类的概率。|
|证据的权重（WoE）|计算 WoE 作为分类列与目标列的相关性。 它计算为类与类外概率的比率的对数。 此步骤将为每个类输出一个数值特征列，无需显式归结缺失值和离群值处理。|
|群集距离|在所有数字列上训练 k 平均值聚类分析模型。  输出 k 个新功能，每个群集一个新的数字功能，其中包含每个样本与每个分类的质心的距离。|

### <a name="data-guardrails"></a>数据 guardrails

自动机器学习提供了数据 guardrails，可帮助您识别数据的潜在问题（例如，缺少值、类不平衡），并帮助采取纠正措施来提高结果。 有很多可用的最佳实践，可以应用这些方案来实现可靠的结果。 

下表描述了当前支持的数据 guardrails，以及用户在提交试验时可能会遇到的关联状态。

Guardrail|상태|&nbsp;触发器的条件&nbsp;
---|---|---
&nbsp;插补法缺少&nbsp;值 |**过来** <br> <br> **小数点**|    任何输入&nbsp;列中都没有缺失值 <br> <br> 某些列缺少值
교차 유효성 검사|**效率**|如果未提供显式验证集
高&nbsp;基数&nbsp;功能&nbsp;检测|  **过来** <br> <br>**效率**|   未检测到高基数功能 <br><br> 检测到高基数输入列
类余额检测 |**过来** <br><br><br>**触发** |类在定型数据中平衡;如果每个类在数据集中具有良好的表示形式，则将数据集视为均衡，如样本的数量和比率 <br> <br> 训练数据中的类不均衡
时序数据一致性|**过来** <br><br><br><br> **小数点** |<br> 已分析选定的 {地平线，延迟，滚动窗口} 个值，但未检测到任何可能的内存不足问题。 <br> <br>已分析选定的 {地平线，延迟，滚动窗口} 值，并且可能会导致实验用尽内存。 已关闭延迟或滚动窗口。

## <a name="run-experiment-and-view-results"></a>运行试验并查看结果

选择 "**启动**" 以运行试验。 试验过程可能需要长达10分钟的时间。 训练作业可能需要额外的2-3 分钟才能完成每个管道的运行。

### <a name="view-experiment-details"></a>실험 세부 정보 보기

>[!NOTE]
> 定期选择 "**刷新**" 以查看运行状态。 

此时将打开 "**运行详细**信息" 屏幕到 "**详细信息**" 选项卡。此屏幕将显示包含**运行状态**的试验运行摘要。 

"**模型**" 选项卡包含按指标分数排序的模型列表。 默认情况下，根据所选指标为最高评分的模型位于列表的顶部。 当训练作业尝试更多模型时，它们将被添加到列表中。 使用此值可以快速比较迄今为止生成的模型的指标。

[![실행 세부 정보 대시보드](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>查看定型运行详细信息

向下钻取任何已完成的模型，以查看定型运行详细信息，例如，在 "**模型详细信息**" 选项卡上运行度量值，或在 "**可视化效果**" 选项卡上查看性能图表。[详细了解图表](how-to-understand-automated-ml.md)

[![迭代详细信息](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>모델 배포

获得最佳模型后，就可以将其部署为 web 服务，以便预测新数据。

自动 ML 可帮助你在不编写代码的情况下部署模型：

1. 部署有几个选项。 

    + 选项1：若要部署最佳模型（根据定义的指标条件），请从 "详细信息" 选项卡中选择 "部署最佳模型"。

    + 选项2：若要从此试验部署特定模型迭代，请向下钻取模型以打开其 "模型详细信息" 选项卡，然后选择 "部署模型"。

1. 填充 "**部署模型**" 窗格。

    필드| 값
    ----|----
    이름| 输入部署的唯一名称。
    Description| 输入说明以更好地识别此部署的用途。
    컴퓨팅 형식| 选择要部署的终结点类型： *Azure Kubernetes Service （AKS）* 或*azure 容器实例（ACI）* 。
    이름| *仅适用于 AKS：* 选择要部署到的 AKS 群集的名称。
    인증 사용 | 选择此项可允许基于令牌或基于密钥的身份验证。
    使用自定义部署资产| 如果要上传自己的评分脚本和环境文件，请启用此功能。 [了解有关评分脚本的详细信息](how-to-deploy-and-where.md#script)。

    >[!Important]
    > 文件名的长度必须为32个字符，并且必须以字母数字开头和结尾。 可能包括短划线、下划线、点和之间的字母数字。 不允许使用空格。

    "*高级*" 菜单提供默认部署功能，如 "[数据收集](how-to-enable-app-insights.md)" 和 "资源使用情况" 设置。 如果希望覆盖这些默认设置，请在此菜单中执行此操作。

1. **배포**를 선택합니다. 部署可能需要大约20分钟才能完成。

现在，你已拥有一个可用于生成预测的操作 web 服务！ 您可以通过从[Power BI 的内置 Azure 机器学习支持](how-to-consume-web-service.md#consume-the-service-from-power-bi)查询服务来测试预测。

## <a name="next-steps"></a>다음 단계

* 尝试[通过 Azure 机器学习创建首次自动 ML 试验](tutorial-first-experiment-automated-ml.md)的端到端教程。 
* [了解有关自动化机器学习](concept-automated-ml.md)和 Azure 机器学习的详细信息。
* [了解自动化机器学习结果](how-to-understand-automated-ml.md)。
* [了解如何使用 web 服务](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)。
