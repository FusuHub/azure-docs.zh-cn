---
title: 缩放用于查询和索引工作负荷的容量
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中调整分区和副本计算机资源，其中每个资源以可计费搜索单位定价。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 349587063c528fef1cbdb09d84e61e82443d45d1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906726"
---
# <a name="scale-up-partitions-and-replicas-to-add-capacity-for-query-and-index-workloads-in-azure-cognitive-search"></a>扩展分区和副本，为 Azure 中的查询和索引工作负荷添加容量认知搜索

[选择定价层](search-sku-tier.md)并[预配搜索服务](search-create-service-portal.md)后，下一步是根据需要增加服务使用的副本或分区数。 每个层提供固定数量的计费单位。 本文介绍如何分配这些单元以实现最佳配置，以平衡查询执行、索引和存储的要求。

在[基本层](https://aka.ms/azuresearchbasic)或某个[标准或存储优化层](search-limits-quotas-capacity.md)上设置服务时，可以使用资源配置。 对于这些层级的服务，容量以*搜索单位*（SUs）的增量购买，其中每个分区和副本计为一个 SU。 

使用较少的 SUs 会按比例降低计费。 只要设置了服务，计费就会生效。 如果暂时不使用服务，则避免计费的唯一方法是删除服务，然后在需要时重新创建。

> [!Note]
> 删除服务将删除其上的所有内容。 Azure 认知搜索中没有用于备份和还原持久搜索数据的功能。 若要在新服务上重新部署现有索引，应运行用于创建和加载它的程序。 

## <a name="terminology-replicas-and-partitions"></a>术语：副本和分区
副本和分区是恢复搜索服务的主要资源。

| Resource | 定义 |
|----------|------------|
|*里* | 为读/写操作（例如，重新生成或刷新索引时）提供索引存储和 i/o。|
|*副本* | 搜索服务的实例，主要用于对查询操作进行负载均衡。 每个副本始终承载索引的一个副本。 如果有12个副本，则会在服务上加载每个索引12个副本。|

> [!NOTE]
> 无法直接操作或管理在副本上运行的索引。 每个副本上的每个索引的一个副本都是服务体系结构的一部分。
>


## <a name="how-to-allocate-replicas-and-partitions"></a>如何分配副本和分区
在 Azure 认知搜索中，最初将服务分配到包含一个分区和一个副本的最小级别的资源。 对于支持该服务的层，可以通过增加分区来增量调整计算资源，前提是需要更多的存储和 i/o，或者添加更多的副本用于更大的查询量或提高性能。 单个服务必须具有足够的资源来处理所有工作负荷（索引和查询）。 不能在多个服务之间细分工作负荷。

若要增加或更改副本和分区的分配，建议使用 Azure 门户。 门户对允许的最大限制的组合强制实施限制。 如果需要基于脚本或基于代码的预配方法， [Azure PowerShell](search-manage-powershell.md)或[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/services)是替代解决方案。

通常，搜索应用程序需要比分区更多的副本，尤其是在服务操作倾向于查询工作负荷时。 有关[高可用性](#HA)的部分介绍了原因。

1. 登录到[Azure 门户](https://portal.azure.com/)并选择搜索服务。

2. 在 "**设置**" 中，打开 "**缩放**" 页以修改副本和分区。 

   以下屏幕截图显示了一项预配的标准服务，其中包含一个副本和分区。 底部的公式指示正在使用的搜索单位数（1）。 如果单位价格为 $100 （而不是实际价格），则运行此服务的每月费用将平均为 $100。

   ![显示当前值的缩放页面](media/search-capacity-planning/1-initial-values.png "显示当前值的缩放页面")

3. 使用滑块可以增加或减少分区数。 底部的公式指示正在使用的搜索单位数。

   此示例将容量加倍，其中每个都有两个副本和分区。 请注意搜索单位数;因为计费公式是副本乘以分区（2 x 2），所以现在为四个。 增加容量比运行服务的成本要高得多。 如果搜索单位成本为 $100，则新的月度帐单现在为 $400。

   有关每个层的当前每单位成本，请访问[定价页](https://azure.microsoft.com/pricing/details/search/)。

   ![添加副本和分区](media/search-capacity-planning/2-add-2-each.png "添加副本和分区")

3. 单击 "**保存**" 以确认所做的更改。

   ![确认对缩放和计费的更改](media/search-capacity-planning/3-save-confirm.png "确认对缩放和计费的更改")

   容量更改需要花费几个小时才能完成。 启动进程后，将无法进行取消操作，并且不会对副本和分区调整进行实时监视。 但是，当更改正在进行时，以下消息仍然可见。

   ![门户中的状态消息](media/search-capacity-planning/4-updating.png "门户中的状态消息")


> [!NOTE]
> 预配服务后，无法将其升级到更高版本的 SKU。 你必须在新层中创建搜索服务，并重新加载索引。 若要帮助进行服务预配，请参阅[在门户中创建 Azure 认知搜索服务](search-create-service-portal.md)。
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>分区和副本组合

基本服务只能有一个分区，最多包含三个副本，最大限制为三个。 唯一可调整的资源是副本。 需要至少两个副本才能实现查询的高可用性。

所有标准和存储优化搜索服务都可以根据 36-SU 限制，采用以下副本和分区的组合。 

|   | **1个分区** | **2个分区** | **3个分区** | **4个分区** | **6个分区** | **12个分区** |
| --- | --- | --- | --- | --- | --- | --- |
| **1个副本** |1个 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2个副本** |2 SU |4 SU |6 SU |8个 SU |12 SU |24 SU |
| **3个副本** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4个副本** |4 SU |8个 SU |12 SU |16 SU |24 SU |N/A |
| **5个副本** |5 SU |10个 SU |15个 SU |20个 SU |30个 SU |N/A |
| **6个副本** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **12个副本** |12 SU |24 SU |36 SU |N/A |N/A |N/A |

Azure 网站上详细说明了 SUs、定价和容量。 有关详细信息，请参阅[定价详细](https://azure.microsoft.com/pricing/details/search/)信息。

> [!NOTE]
> 副本和分区数将平均分割为12（具体为1、2、3、4、6、12）。 这是因为，Azure 认知搜索将每个索引预先分割为12个分片，以便可以将其分散到所有分区的相等部分。 例如，如果您的服务有三个分区，而您创建了一个索引，则每个分区将包含四个索引的分片。 Azure 认知搜索分片索引的方式是实现细节，在将来的版本中可能会有所更改。 尽管现在数字为12，但你预计该数字在未来始终为12。
>


<a id="HA"></a>

## <a name="high-availability"></a>高可用性
由于扩展速度非常简单，因此，我们通常建议你从一个分区和一个或两个副本开始，然后随着查询量的生成进行扩展。 查询工作负荷主要在副本上运行。 如果需要更高的吞吐量或高可用性，则可能需要额外的副本。

有关高可用性的一般建议如下：

* 用于只读工作负荷的高可用性的两个副本（查询）

* 用于读取/写入工作负荷的高可用性的三个或更多副本（添加、更新或删除单个文档时的查询和索引）

适用于 Azure 认知搜索的服务级别协议（SLA）面向查询操作和包含添加、更新或删除文档的索引更新。

基本层在一个分区和三个副本之上。 如果希望灵活地立即响应索引和查询吞吐量的需求波动，请考虑标准层之一。  如果发现存储要求的增长速度快于查询吞吐量的增长速度，请考虑采用存储优化的一层。

### <a name="index-availability-during-a-rebuild"></a>重新生成期间索引可用性

Azure 认知搜索的高可用性适用于不涉及重建索引的查询和索引更新。 如果删除字段、更改数据类型或重命名字段，则需要重新生成索引。 若要重新生成索引，必须删除该索引，重新创建该索引，然后重新加载数据。

> [!NOTE]
> 可以将新字段添加到 Azure 认知搜索索引，而无需重新生成索引。 对于索引中已有的所有文档，新字段的值将为 null。

重新生成索引时，会有一段时间将数据添加到新索引。 如果要在此期间继续使旧索引可用，则必须在同一服务上具有具有不同名称的旧索引的副本，或在不同服务上具有相同名称的索引副本，然后在代码中提供重定向或故障转移逻辑。

## <a name="disaster-recovery"></a>灾难恢复
目前没有用于灾难恢复的内置机制。 添加分区或副本将是用于满足灾难恢复目标的错误策略。 最常见的方法是通过在另一个区域中设置另一个搜索服务，在服务级别添加冗余。 与索引重新生成过程中的可用性一样，重定向或故障转移逻辑必须来自你的代码。

## <a name="increase-query-performance-with-replicas"></a>利用副本提高查询性能
查询延迟是指需要其他副本。 通常，提高查询性能的第一步是增加此资源的更多。 添加副本时，会使索引的其他副本联机以支持更大的查询工作负荷，并对多个副本上的请求进行负载均衡。

我们无法为每秒查询（QPS）提供硬估计：查询性能取决于查询和争用工作负载的复杂性。 尽管添加副本可明显提高性能，但结果并不是严格的线性：添加三个副本并不保证三个吞吐量。

有关估计工作负荷的 QPS 的指南，请参阅[Azure 认知搜索性能和优化注意事项](search-performance-optimization.md)。

## <a name="increase-indexing-performance-with-partitions"></a>利用分区提高索引性能
需要近乎实时的数据刷新的搜索应用程序所需的分区比副本要大得多。 添加分区会将读取/写入操作分散到更多的计算资源。 它还提供更多的磁盘空间用于存储其他索引和文档。

查询较大的索引需要更长的时间来进行查询。 因此，你可能会发现，分区中的每个增量增加都需要更小但按比例增加副本。 查询和查询量的复杂性将会影响查询执行的速度。


## <a name="next-steps"></a>后续步骤

[选择 Azure 认知搜索的定价层](search-sku-tier.md)
