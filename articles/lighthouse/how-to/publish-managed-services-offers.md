---
title: 将托管服务产品发布到 Azure 市场
description: 了解如何发布将客户载入到 Azure 委派资源管理的托管服务产品。
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 841cb52791709be5649d66b72f5c18ef35b740ef
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155241"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>将托管服务产品发布到 Azure 市场

本文介绍如何使用[云合作伙伴门户](https://cloudpartner.azure.com/)将公共或专用托管服务产品发布到 [Azure 市场](https://azuremarketplace.microsoft.com)，从而使购买产品的客户能够加入 Azure 委托资源管理的资源。

> [!NOTE]
> 若要创建和发布这些产品/服务，需要拥有有效的[合作伙伴中心帐户](../../marketplace/partner-center-portal/create-account.md)。 如果还没有帐户，[注册过程](https://aka.ms/joinmarketplace)将引导你按照步骤在合作伙伴中心创建帐户并注册商业市场计划。 你的 Microsoft 合作伙伴网络 (MPN) ID 将[自动关联](../../billing/billing-partner-admin-link-started.md)你发布的产品/服务，以跟踪你在客户参与中的影响。
>
> 如果不想将产品/服务发布到 Azure 市场，可使用 Azure 资源管理器模板手动载入客户。 有关详细信息，请参阅[将客户载入到 Azure 委派资源管理](onboard-customer.md)。

与将其他任何类型的产品/服务发布到 Azure 市场相比，发布托管服务产品的过程很类似。 要了解此过程，请参阅 [Azure 市场和 AppSource 发布指南](../../marketplace/marketplace-publishers-guide.md)和[管理 Azure 和 AppSource 市场产品/服务](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md)。 还应查看[商业市场认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies)，特别是[托管服务](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)部分。

客户添加你的产品/服务后，他们将能够委托一个或多个特定订阅或资源组，然后将[加入这些订阅或资源组以进行 Azure 委托资源管理](#the-customer-onboarding-process)。 请注意，在加入订阅（或订阅中的资源组）之前，必须通过手动注册 **Microsoft.ManagedServices** 资源提供程序来对订阅进行加入授权。

> [!IMPORTANT]
> 托管服务产品中的每个计划都有一个“清单详细信息”部分，你在此处定义租户中将有权访问委派资源组和/或购买该计划的客户的订阅的 Azure Active Directory (Azure AD) 实体。 请注意，此处包括的任何组（或用户/服务主体）将对购买此计划的每位客户具有相同的权限。 要分配不同的组来应对每位客户，需要发布单独的专门针对每位用户的计划。

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>在云合作伙伴门户中创建产品/服务

1. 登录[云合作伙伴平台](https://cloudpartner.azure.com/)。
2. 从左侧导航菜单中，选择“新建产品/服务”，然后选择“托管服务”。
3. 你将看到你的产品/服务的 "**编辑**" 部分，其中包含四个部分用于填写：**提供设置**、**计划**、 **Marketplace**和**支持**。 请继续阅读，了解如何填写这些部分。

## <a name="enter-offer-settings"></a>输入产品/服务设置

在“产品/服务设置”部分中，提供以下信息：

|字段  |Description  |
|---------|---------|
|**产品/服务 ID**     | （发布者配置文件内）产品/服务的唯一标识符。 此 ID 只能包含小写字母数字字符、短划线和下划线，且不得超过 50 个字符。 请记住，客户可能会在产品 URL 和计费报表等位置看到产品/服务 ID。 一旦发布产品/服务，即无法更改此值。        |
|**发布者 ID**     | 将与产品/服务关联的发布者 ID。 如果拥有多个发布者 ID，可选择要用于此产品/服务的 ID。       |
|**名称**     | 你的产品/服务将在 Azure 市场和 Azure 门户中向客户显示的名称（不超过 50 个字符）。 请使用客户能理解的可识别的品牌名称 - 如果正在通过自己的网站推广此产品/服务，请确保在此处使用完全相同的名称。        |

完成后，选择“保存”。 现在，可继续转到“计划”部分。

## <a name="create-plans"></a>创建计划

每个产品/服务都必须具有一个或多个计划（有时也称为 SKU）。 可添加多个计划，以不同的价格支持不同的功能集，也可为有限数量的特定客户自定义特定计划。 客户可在父级产品/服务中查看可供其使用的计划。

在“计划”部分中，选择“新建计划”。 然后，输入计划 ID。 此 ID 只能包含小写字母数字字符、短划线和下划线，且不得超过 50 个字符。 客户可能会在产品 URL 和计费报表等位置看到计划 ID。 一旦发布产品/服务，即无法更改此值。

### <a name="plan-details"></a>计划详细信息

完成“计划详细信息”部分中的以下部分：

|字段  |Description  |
|---------|---------|
|**标题**     | 要显示的计划的易记名称。 最大长度为 50 个字符。        |
|**摘要**     | 标题下显示的计划的简洁说明。 最大长度为 100 个字符。        |
|**说明**     | 更详细地介绍计划的说明文本。         |
|**计费模式**     | 此处显示两种计费模式，但必须为托管服务产品选择“自带许可”。 这意味着你将直接向客户收取与此产品/服务相关的费用，而 Microsoft 不向你收取任何费用。   |
|**这是专用计划吗？**     | 指示该 SKU 是专用还是公共 SKU。 默认值为“否”（即公共 SKU）。 如果保留此选择，则你的计划将不仅限于特定客户（或特定数量的客户）；发布公用计划后，无法再将其更改为专用计划。 要使此计划仅对特定客户可用，请选择“是”。 执行此操作时，需要提供客户的订阅 ID 来确定客户身份。 可逐一输入（最多 10 个订阅），也可上传一个 .csv 文件（最多 20,000 个订阅）。 请确保在此处包含你自己的订阅，以便测试和验证该产品/服务。 有关详细信息，请参阅[专用 SKU 和计划](../../marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)。  |

> [!IMPORTANT]
> 一旦某个计划作为公共计划发布，则不能将其更改为 "专用"。 若要控制哪些客户可以接受您的产品/服务和委托资源，请使用私有计划。 使用公用计划，您不能将可用性限制为特定客户或甚至是特定数量的客户（不过，如果您选择这样做，您可以完全停止销售计划）。 仅当你在发布产品/服务时，如果你在将**角色定义**设置为 "[托管服务注册分配](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)" "角色 **"，则**可以在客户接受产品/服务后[删除对委派的访问权限](onboard-customer.md#remove-access-to-a-delegation)。 你还可以联系客户并要求他们[删除你的访问权限](view-manage-service-providers.md#add-or-remove-service-provider-offers)。

### <a name="manifest-details"></a>清单详细信息

完成计划的“清单详细信息”部分。 这会创建一个清单，其中包含用于管理客户资源的授权信息。 若要启用 Azure 委派资源管理，必须使用此信息。

> [!NOTE]
> 如上所述，“授权”条目中的用户和角色将应用于每个购买了计划的客户。 如果想要限制特定客户的访问权限，需发布专用计划供其专用。

首先，请提供清单的版本。 使用 n.n.n 格式（例如 1.2.5）。

然后，输入租户 ID。 这是与你组织的 Azure Active Directory 租户 ID 关联的 GUID（即你将在其中管理客户资源的租户）。 如果没有此信息，可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来找到它。

最后，将一个或多个授权条目添加到计划中。 授权定义了哪些实体可访问购买计划的客户的资源和订阅，并分配了授予特定访问级别的角色。

> [!TIP]
> 在大多数情况下，需要将权限分配给 Azure AD 用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。 有关其他建议，请参阅 [Azure Lighthouse 方案中的租户、角色和用户](../concepts/tenants-users-roles.md)。

对于每个授权，你需要提供以下信息。 然后，可根据需要多次选择“新授权”，以添加更多用户和角色定义。

- **Azure AD 对象 ID**：用户、用户组或应用程序的 Azure AD 标识符，将向客户的资源授予特定权限（如角色定义所述）。
- **Azure AD 对象显示名称**：一个友好名称，可帮助客户了解此授权的用途。 在委派资源时，客户将看到此名称。
- **角色定义**：从列表中选择一个可用 Azure AD 内置角色。 此角色将确定“Azure AD 对象 ID”字段中的用户对客户资源拥有的权限。 有关这些角色的说明，请参阅[Azure 委托资源管理](../concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)的[内置角色](../../role-based-access-control/built-in-roles.md)和角色支持。
  > [!NOTE]
  > 将适用的新内置角色添加到 Azure 时，它们将在此处可用，但可能会出现一些延迟后出现。
- 可**分配角色**：只有在此授权的**角色定义**中选择了 "用户访问管理员" 时，才需要执行此操作。 如果是这样，则必须在此处添加一个或多个可分配角色。 “Azure AD 对象 ID”字段中的用户能够将这些“可分配角色”分配给[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，这是[部署可修正的策略](deploy-policy-remediation.md)所必需的。 请注意，通常与用户访问管理员角色关联的其他权限均不适用于此用户。 如果未在此处选择一个或多个角色，则你的提交将无法通过认证。 （如果未为此用户的角色定义选择用户访问管理员，则此字段无效。）

> [!TIP]
> 若要确保在需要时可以[删除对委派的访问权限](onboard-customer.md#remove-access-to-a-delegation)，请包括将**角色定义**设置为[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的**授权**。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

完成这些信息后，可以根据需要多次选择“新建计划”以创建其他计划。 完成后，选择“保存”，然后继续填写“市场”部分。

## <a name="provide-marketplace-text-and-images"></a>提供市场文本和图像

可在“市场”部分提供客户要在 Azure 市场和 Azure 门户看到的文本和图像。

在“概述”部分中填写以下字段：

|字段  |Description  |
|---------|---------|
|**标题**     |  套餐的标题，通常是较长的正式名称。 此标题将在市场中突出显示。 最大长度为 50 个字符。 大多数情况下，此名称应与你在“产品/服务设置”部分输入的名称相同。       |
|**摘要**     | 产品/服务用途或功能的简要说明。 通常显示在标题下方。 最大长度为 100 个字符。        |
|**长摘要**     | 产品/服务用途或功能的详细摘要。 最大长度为 256 个字符。        |
|**说明**     | 有关产品/服务的详细信息。 此字段不得超过 3000 个字符，且支持简单的 HTML 格式。 必须在说明中的某个位置包含“托管服务”字样。       |
|**营销标识符**     | 唯一的 URL 友好标识符。 此标识符只能包含小写字母数字字符和短划线。 它将用于此产品/服务的 Marketplace Url。 例如，如果发布者 ID 为 contoso，营销标识符为 sampleApp，则产品/服务在 Azure 市场中的 URL 为 https://azuremarketplace.microsoft.com/marketplace/apps/contoso-sampleApp。        |
|**预览订阅 ID**     | 添加 1 到 100 个订阅标识符。 在产品/服务上线之前，与这些订阅关联的客户将可在 Azure 市场中查看此产品/服务。 建议在此处包含你自己的订阅，以便在向客户提供产品/服务之前，可预览其在 Azure 市场中的显示方式。  （在此预览期间，Microsoft 支持和工程团队也将能够查看你的产品/服务。）   |
|**有用链接**     | 与你的产品/服务相关的 URL，例如文档、发行说明、常见问题解答等等。        |
|**建议的类别（最多 5 个）**     | 适用于你的产品/服务的一个或多个类别（最多 5 个）。 这些类别可帮助客户在 Azure 市场和 Azure 门户中发现你的产品/服务。        |

在“营销项目”部分，可上传徽标和其他资产，使其与产品/服务一起显示。 可选择性地上传屏幕截图或视频链接，帮助客户了解你的产品/服务。

需要四种徽标大小：**小（40x40）** 、**中（90x90）** 、**大（115x115）** 和**宽（255x115）** 。 请遵守徽标适用的下述准则：

- Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
- 门户的主题颜色为白色和黑色。 请勿将这些颜色用作徽标的背景色。 使用可使徽标在门户中更为突出的颜色。 建议使用简单的主颜色。
- 如果使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
- 徽标的外观应平整，并且应避免渐变。 不要在徽标上使用渐变背景。
- 不要在徽标上放置文本，即使是公司或品牌名称也不可以。
- 确保徽标未被拉伸。

特大 (815x290) 徽标可选，但建议使用它。 如果包含了特大徽标，请遵循以下准则：

- 特大徽标中不得包含任何文本，且务必在徽标右侧留出 415 像素的空白空间。 目的是有空间以编程方式嵌入发布者显示名称、计划标题和产品/服务详细摘要等文本元素。
- 特大徽标的背景不得为黑色、白色或透明。 请确保背景色较深，因为嵌入的文本将显示为白色。
- 一旦使用特大图标发布产品/服务，便不能将其删除（但如果需要，可使用其他版本进行更新）。

可在“潜在顾客管理”部分中，选择将存储潜在顾客的 CRM 系统。 请注意，根据[托管服务认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)，需要**潜在顾客目标**。

最后，在“法律”部分提供隐私策略 URL 和使用条款。 还可在此处指定是否对此产品/服务使用[标准协定](../../marketplace/standard-contract.md)。

转到“支持”部分之前，请务必保存更改。

## <a name="add-support-info"></a>添加支持信息

在“支持”部分中，提供工程联系人和客户支持联系人的姓名、电子邮件和电话号码。 还需要提供支持 URL。 当 Microsoft 需要就业务和支持问题与你联系时，我们可能会使用此信息。

添加此信息后，请选择“保存”。

## <a name="publish-your-offer"></a>发布产品

完成所有部分后，下一步就是将产品/服务发布到 Azure 市场。 选择“发布”按钮启动产品/服务上线过程。 有关此过程的详细信息，请参阅[发布 Azure 市场和 AppSource 产品/服务](../../marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer.md)。

你可以随时[发布产品/服务](../../marketplace/cloud-partner-portal/manage-offers/cpp-update-offer.md)的更新版本。 例如，你可能想要向以前发布的产品/服务添加新的角色定义。 当你执行此操作时，已添加该产品/服务的客户会在 Azure 门户中的[服务提供程序](view-manage-service-providers.md)页面上看到一个图标，他们可通过图标知道更新可用。 每个客户都可以[查看更改](view-manage-service-providers.md#update-service-provider-offers)并决定是否要更新到新版本。 

## <a name="the-customer-onboarding-process"></a>客户加入过程

客户添加你的产品/服务后，他们将能够[委托一个或多个特定订阅或资源组](view-manage-service-providers.md#delegate-resources)，然后将加入这些订阅或资源组以进行 Azure 委托资源管理。 如果客户已接受产品/服务但尚未委托任何资源，则他们会在 Azure 门户[“服务提供商”](view-manage-service-providers.md)页中“提供商产品/服务”部分的顶部看到备注。

> [!IMPORTANT]
> 委托必须由客户租户中的非来宾帐户完成，该帐户对于正在载入的订阅（或包含正在载入的资源组的订阅）拥有[所有者内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)。 若要查看所有可以委托订阅的用户，客户租户中的用户可以在 Azure 门户中选择订阅，打开“访问控制(IAM)”，然后[查看具有“所有者”角色的所有用户](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

在客户委托订阅（或订阅中的一个或多个资源组）后，将为该订阅注册 Microsoft.ManagedServices资源提供程序，租户中的用户将能够根据产品/服务中的授权访问委托的资源。

> [!NOTE]
> 此时，如果订阅使用 Azure Databricks，则无法委托订阅（或订阅中的资源组）。 同样，如果已委托订阅（或订阅中的资源组），则当前无法在该订阅中创建 Databricks 工作区。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。
