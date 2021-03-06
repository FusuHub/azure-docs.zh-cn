---
title: 查看和下载 Microsoft Azure 发票
description: 介绍如何查看和下载 Microsoft Azure 发票。
keywords: 帐单发票, 发票下载, azure 发票, azure 使用情况
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: 0f413d38565202d379c81570b5cb169c2ed8effe
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987819"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>查看和下载 Microsoft Azure 发票

对于大多数订阅，你可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下载发票，也可通过电子邮件发送发票。 如果你是签署了企业协议的 Azure 客户（EA 客户），则无法下载你的组织的发票。 发票将发送给为合约设置的接收发票的人员。

只有特定角色有权查看发票。 例如，帐户管理员或企业管理员。 有关获取对账单信息的访问权限的详细信息，请参阅[使用角色管理对 Azure 账单的访问权限](../manage/manage-billing-access.md)。

如果签订了 Microsoft 客户协议 (MCA)，则必须具有以下角色之一才能获取发票：

- 计费对象信息所有者
- 参与者
- 读取器
- 发票管理者

如果签订了 Microsoft 合作伙伴协议 (MPA)，则必须是合作伙伴组织中的全局管理员或管理员代理，才能查看和下载 Azure 发票。 [检查计费帐户类型](#check-your-billing-account-type)以确定需要哪些权限。

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> 为什么可能无法获取发票

看不到发票的几个可能原因：

- 从订阅 Azure 开始算还不到 30 天。

- 在发票周期结束时，Azure 会向你开具账单。 因此，可能尚未生成发票。 请等到计费周期结束。

- 你无权查看发票。 如果签订了 MCA 或 MPA，则必须是计费对象信息所有者、参与者、读者或发票管理者。 对于其他订阅，如果不是帐户管理员，则可能看不到旧发票。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](../manage/manage-billing-access.md)。

- 如果你的订阅有免费试用或每月信用额度，则仅当超过月度信用额度时才会收到发票。 如果签订了 Microsoft 客户协议或 Microsoft 合作伙伴协议，则始终会收到发票。

## <a name="download-invoices-in-the-azure-portal"></a>可以在 Azure 门户中下载发票

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。
1. 根据你的访问权限，可能需要选择一个计费帐户或计费对象信息。
1. 在左侧菜单中，选择“账单”下的“发票”。
1. 在发票网格中，找到要下载的发票行。
1. 单击行末尾的下载图标或省略号 (`...`)。
1. 在下载菜单中，选择“发票”。

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](review-individual-bill.md)。 如需有关管理成本的帮助，请参阅[通过 Azure 账单和成本管理来防止意外成本](../manage/getting-started.md)。

## <a name="get-your-subscriptions-invoices-in-email"></a>通过电子邮件获取订阅的发票

可以选择加入并配置其他收件人来通过电子邮件接收 Azure 发票。 此功能可能不适用于某些订阅，例如支持套餐、企业协议或 Azure 开放许可。

1. 在[“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上选择订阅。 为拥有的每个订阅执行选择加入操作。 依次单击“发票”、“通过电子邮件发送我的发票”。

    ![显示了“选择加入”流的屏幕截图](./media/download-azure-invoice/invoicesdeeplink01.png)

2. 单击“选择加入”并接受条款。

    ![显示了“选择加入”流步骤 2 的屏幕截图](./media/download-azure-invoice/invoicearticlestep02.png)

3. 接受协议后，即可以配置其他收件人。 删除收件人时，不再存储电子邮件地址。 如果改变主意，则需要重新添加。

    ![显示了“选择加入”流步骤 3 的屏幕截图](./media/download-azure-invoice/invoicearticlestep03.png)

如果执行步骤后未收到电子邮件，请确保[个人资料通讯首选项](https://account.windowsazure.com/profile)中的电子邮件地址正确。

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>选择不通过电子邮件获取订阅的发票

若要选择不通过电子邮件获取发票，请按照前面的步骤进行操作，并单击“选择不通过电子邮件发送发票”。 此选项将删除任何设置用来通过电子邮件接收发票的电子邮件地址。 如果再次选择接收，则可以重新配置收件人。

 ![显示了“选择退出”流的屏幕截图](./media/download-azure-invoice/invoicearticlestep04.png)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>通过电子邮件获取 Microsoft 客户协议发票

如果你有 Microsoft 客户协议的帐单帐户，你可以选择在电子邮件中获取发票。 在帐单配置文件中拥有所有者、参与者、读者或发票经理角色的所有用户都将在电子邮件中获取其发票。 

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 搜索“成本管理 + 计费”。

   ![其中显示了在门户中搜索订阅的屏幕截图](./media/download-azure-invoice/search-cmb.png)

1. 选择左侧的 "**计费配置文件**"。 从 "计费配置文件" 列表中，选择计费配置文件以通过电子邮件获取其发票。

   [显示计费配置文件列表的 ![屏幕快照](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. 选择左侧的 "**属性**"，然后选择 "**更新电子邮件发票首选项**"。

   [显示计费配置文件列表的 ![屏幕快照](./media/download-azure-invoice/mca-select-update-email-preferences.png)](./media/download-azure-invoice/mca-select-update-email-preferences.png#lightbox)

1. 选择 **"选择加入"** ，然后单击 "**更新**"。

   [显示计费配置文件列表的 ![屏幕快照](./media/download-azure-invoice/mca-select-email-opt-in.png)](./media/download-azure-invoice/mca-select-email-opt-in.png#lightbox)

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>选择不通过电子邮件获取 Microsoft 客户协议发票

若要选择不通过电子邮件获取发票，请按照前面的步骤进行操作，并单击 "**选择退出**"。具有所有者、参与者、读者或发票经理角色的所有用户都未选择使用电子邮件获取发票。 

## <a name="give-others-access-to-your-microsoft-customer-agreement-invoices"></a>允许其他人访问你的 Microsoft 客户协议发票

您可以通过为计费配置文件分配发票经理角色，使其他人能够查看、下载和支付发票。 如果已选择使用电子邮件获取发票，则这些用户也会在电子邮件中获取发票。 

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 搜索“成本管理 + 计费”。

   ![其中显示了在门户中搜索订阅的屏幕截图](./media/download-azure-invoice/search-cmb.png)

1. 选择左侧的 "**计费配置文件**"。 从 "计费配置文件" 列表中，选择要为其分配发票经理角色的帐单配置文件。

   [显示计费配置文件列表的 ![屏幕快照](./media/download-azure-invoice/mca-select-profile.png)](./media/download-azure-invoice/mca-select-profile-zoomed-in.png#lightbox)

1. 选择左侧的 "**访问控制（IAM）** "，然后从页面顶部选择 "**添加**"。

   [显示访问控制页面的 ![屏幕快照](./media/download-azure-invoice/mca-select-access-control.png)](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png#lightbox)

1. 在 "角色" 下拉列表中，选择 "**发票管理器**"。 输入要向其授予访问权限的用户的电子邮件地址。 选择“保存”以分配该角色。

   [显示将用户添加为发票管理器 ![屏幕快照](./media/download-azure-invoice/mca-added-invoice-manager.png)](./media/download-azure-invoice/mca-added-invoice-manager.png#lightbox)

## <a name="check-your-billing-account-type"></a>检查计费帐户类型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要详细了解发票和费用，请参阅：

- [查看和下载 Microsoft Azure 使用情况与费用](download-azure-daily-usage.md)
- [了解 Microsoft Azure 账单](review-individual-bill.md)
- [了解有关 Azure 发票的术语](understand-invoice.md)
- [了解 Microsoft Azure 详细使用条款](understand-usage.md)
- [查看组织的 Azure 定价](../manage/ea-pricing.md)

如果你签订了 Microsoft 客户协议，请参阅：

- [了解计费对象信息发票上的费用](review-customer-agreement-bill.md)
- [了解计费对象信息发票上的术语](mca-understand-your-invoice.md)
- [了解计费对象信息的 Azure 使用情况和费用文件](mca-understand-your-usage.md)
- [查看和下载计费对象信息的税单](mca-download-tax-document.md)
- [查看组织的 Azure 定价](../manage/ea-pricing.md)
