---
title: Azure Monitor의 모니터링 사용량 및 예상 비용
description: Azure Monitor의 모니터링 사용량 및 예상 비용 프로세스 개요 페이지
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 3fb51a9dc3f607d89934f6962588195e0f5c83f5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715772"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Azure Monitor의 모니터링 사용량 및 예상 비용

> [!NOTE]
> 本文介绍如何在多个 Azure 监视功能中查看使用情况和估计成本。 Azure Monitor 的特定组件的相关文章包括：
> - [使用 Azure Monitor 日志管理使用情况和成本](manage-cost-storage.md)介绍了如何通过更改数据保持期来控制成本，以及如何分析和警报数据的使用情况。
> - [管理 Application Insights 的使用情况和成本](../../azure-monitor/app/pricing.md)介绍如何分析 Application Insights 中的数据使用情况。

## <a name="azure-monitor-pricing-model"></a>Azure Monitor 定价模型

基本 Azure Monitor 计费模型是一种基于云的、基于消耗的定价（"即用即付"）。 사용한 만큼만 요금을 지불합니다. 定价详细信息适用于[警报、指标、通知](https://azure.microsoft.com/pricing/details/monitor/)、 [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)和[Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)。 

除了用于日志数据的即用即付模型外，Log Analytics 还提供了容量预留，这使你可以将其与即用即付价格相比最多保存25%。 产能预留价格使你可以购买起价 100 GB/天的保留。 将按现用现付费率对超出预订级别的任何使用量进行计费。 [了解](https://azure.microsoft.com/pricing/details/monitor/)有关容量保留定价的详细信息。

某些客户将有权访问[旧式 Log Analytics 定价](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)层和[旧的企业 Application Insights 定价层](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier)。 

## <a name="understanding-your-azure-monitor-costs"></a>了解 Azure Monitor 成本

了解成本有两个阶段。 第一种方法是考虑将 Azure Monitor 用作监视解决方案。 

### <a name="estimating-the-costs-to-manage-your-environment"></a>估计管理环境的成本

如果尚未使用 Azure Monitor 日志，可以使用[Azure Monitor 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估计使用 Azure Monitor 的成本。 首先在搜索框中输入 "Azure Monitor"，然后单击生成的 Azure Monitor 磁贴。 向下滚动到 "Azure Monitor"，然后从 "类型" 下拉列表中选择其中一个选项：

- 指标查询和警报  
- Log Analytics
- Application Insights

在上述每种情况下，定价计算器都可帮助你根据预期利用率估计可能的成本。

例如，通过 Log Analytics 可以输入要从每个 VM 收集的 Vm 数和 GB 数据。 通常，从典型的 Azure VM 引入的数据月份是 1 GB 到 3 GB。 如果已在评估 Azure Monitor 的日志，则可以使用自己的环境中的数据统计信息。 请参阅下面有关如何确定[受监视 vm 的数目](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)以及[工作区引入的数据量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)。

与 Application Insights 同样，如果启用 "基于应用程序活动的估计数据量" 功能，则可以提供有关应用程序的输入（每月的请求数和每月的页面视图，以防您收集客户端遥测），然后，计算器会告诉您类似应用程序收集的中间值和90% 的数据量。 这些应用程序在 Application Insights 配置范围内（例如，某些应用程序具有默认采样、有些采样没有采样等），因此您仍然可以使用采样来减少在中间级别下引入的数据量。 但这是一种开始了解其他同类客户看到的情况。 [详细了解](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application)如何估算 Application Insights 的成本。

### <a name="understanding-your-usage-and-estimated-costs"></a>了解你的使用情况和估计成本

使用 Azure Monitor 了解和跟踪使用情况很重要，并且有一组丰富的工具可帮助实现此功能。 

Azure 在[Azure 成本管理 + 计费](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心提供了大量有用的功能。 打开**Azure 成本管理 + 计费**中心后，单击 "**成本管理**"，然后选择[范围](https://docs.microsoft.com/azure/cost-management/understand-work-scopes)（要调查的资源集）。 

然后，若要查看最近30天的 Azure Monitor 成本，请单击 "**每日成本**" 磁贴，选择 "相对日期" 下的 "过去30天"，然后添加一个选择服务名称的筛选器：

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insight and Analytics

这将生成如下所示的视图：

![Azure 成本管理屏幕截图](./media/usage-estimated-costs/010.png)

从这里，您可以从累积成本汇总中深化，以获取 "按资源成本" 视图中的更详细信息。 在当前定价层中，将根据 Log Analytics 或 Application Insights 中的一组计量器对 Azure 日志数据收费。 若要将成本与 Log Analytics 或 Application Insights 使用量隔离开来，你可以在**资源类型**上添加筛选器。 若要查看所有 Application Insights 开销，请将资源类型筛选为 "microsoft.operationalinsights/components"，并为 Log Analytics 成本筛选资源类型。 

通过[从 Azure 门户下载您的使用](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)情况，可以获得更多详细信息。 在下载的电子表格中，可以查看每天每个 Azure 资源的使用情况。 在此 Excel 电子表格中，可以通过 "计量类别" 列中的第一个筛选来找到 Application Insights 资源的使用情况，以便显示 "Application Insights" 和 "Log Analytics"，然后在 "Instance ID" 列中添加一个筛选器，该筛选器为 "包含 microsoft Insights/组件"。  由于所有 Azure Monitor 组件都有一个登录后端，因此，大多数 Application Insights 使用情况都是在计量器类别为 Log Analytics 的情况下报告的。  对于旧定价层和多步骤 web 测试 Application Insights 资源，将使用 Application Insights 的计量类别进行报告。  使用情况显示在 "已消耗数量" 列中，每个条目的单位显示在 "度量单位" 列中。  更多详细信息可帮助你[了解 Microsoft Azure 帐单](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

> [!NOTE]
> 使用**Azure 成本管理 + 计费**中心中的**成本管理**是广泛了解监视成本的首选方法。  [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs)和[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs)的**使用情况和估计成本**经验为 Azure Monitor 的每个部分提供更深入的见解。

查看 Azure Monitor 使用情况的另一种方法是监视集线器中的 "**使用情况和估计成本**" 页。 这会显示核心监视功能的使用情况，例如[警报、指标、通知](https://azure.microsoft.com/pricing/details/monitor/)、 [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)和[Azure 应用程序见解](https://azure.microsoft.com/pricing/details/application-insights/)。 2018년 4월 이전 제공된 요금제 고객의 경우 Insights 및 Analytics 제안을 통해 구매한 Log Analytics 사용량도 여기에 포함됩니다.

사용자는 이 페이지에서 구독별로 집계된 지난 31일 간의 리소스 사용량을 볼 수 있습니다. `Drill-ins` 显示31天内的使用趋势。 예상치를 내기 위해서는 상당한 데이터를 취합해야 하므로 페이지 로드에 다소 시간이 소요됩니다.

이 예에서는 모니터링 사용량과 그에 따른 비용의 추정치를 보여 줍니다.

![사용량 및 예상 비용 포털 스크린 샷](./media/usage-estimated-costs/001.png)

월별 사용량 열의 링크를 클릭하면 지난 31일 간의 사용량 추세를 보여 주는 차트가 열립니다. 

![노드당 포함 가로 막대형 차트 스크린 샷](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite 订阅权利

Microsoft Operations Management Suite E1 및 E2를 구매한 고객은 [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) 및 [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing)에 대한 노드별 데이터 주입 자격이 있습니다. 지정된 구독에서 Log Analytics 작업 영역 또는 Application Insights 리소스에 대해 이러한 자격을 얻으려면: 

- Log Analytics 작업 영역에서 "노드당(OMS)" 가격 책정 계층을 사용해야 합니다.
- Application Insights 资源应使用 "企业" 定价层。

根据你的组织购买的套件的节点数，将某些订阅转移到即用即付（每 GB）定价层可能会很有利，但这需要仔细考虑。

> [!WARNING]
> 如果你的组织具有当前 Microsoft Operations Management Suite E1 和 E2，通常最好将 Log Analytics 工作区保存在 "每节点（OMS）" 定价层和 "企业" 定价层中的 Application Insights 资源。 
>
