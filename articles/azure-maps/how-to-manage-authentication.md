---
title: 管理身份验证 |Microsoft Azure 映射
description: 您可以使用 Azure 门户在 Microsoft Azure 映射中管理身份验证。
author: walsehgal
ms.author: v-musehg
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1f7f128898089292a8ccd92686af5d68fe328f3c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766062"
---
# <a name="manage-authentication-in-azure-maps"></a>在 Azure Maps 中管理身份验证

创建 Azure Maps 帐户后，将创建客户端 ID 和密钥以支持 Azure Active Directory （Azure AD）和共享密钥身份验证。

## <a name="view-authentication-details"></a>查看身份验证详细信息

创建 Azure Maps 帐户后，将生成主密钥和辅助密钥。 使用主密钥作为订阅密钥，有时这些名称可互换使用。 辅助密钥可用于诸如滚动密钥更改这样的方案。 无论采用哪种方式，都需要使用密钥才能调用 Azure Maps。 此过程称为[共享密钥身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)。 若要详细了解共享密钥和 Azure AD 身份验证，请参阅[通过 Azure Maps 进行身份验证](https://aka.ms/amauth)。

你可以在 Azure 门户上查看身份验证详细信息。 请在 "**设置**" 菜单中转到你的帐户并选择 "**身份验证**"。

![身份验证详细信息](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>设置 Azure AD 应用注册

创建 Azure Maps 帐户后，需要在 Azure AD 租户与 Azure Maps 资源之间建立链接。

1. 从门户菜单中选择 " **Azure Active Directory** "。 提供注册的名称。 单击 "**应用注册**"，然后单击 "**新建注册**"。 在 "**重定向 URI** " 框中，提供 web 应用的主页。 例如， https://localhost/ 。 如果已注册应用，请执行步骤2。

    ![应用注册](./media/how-to-manage-authentication/app-registration.png)

    ![应用注册详细信息](./media/how-to-manage-authentication/app-create.png)

2. 若要将委派的 API 权限分配到 Azure Maps，请在 "**应用注册**下访问应用程序，然后选择" **API 权限**"。 选择 "**添加权限**"。 搜索并选择 "**选择 API**" 下的**Azure Maps** 。

    ![应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

3. 在 "**选择权限**" 下，选中 "**用户模拟**" 框，然后单击底部的 "**选择**" 按钮。

    ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

4. 根据身份验证方法完成步骤 a 或 b。

    1. 如果你的应用程序对 Azure Maps Web SDK 使用用户令牌身份验证，请启用 `oauth2AllowImplicitFlow`，方法是在你的应用注册的清单部分中将其设置为 true。
    
       ![应用部件清单](./media/how-to-manage-authentication/app-manifest.png)

    2. 如果你的应用程序使用服务器/应用程序身份验证，请在应用注册中中转到 "**证书" & 机密**"边栏选项卡，并创建密码或将公钥证书上传到应用注册。 如果创建密码，请安全地存储它以供以后使用。 你将使用此密码从 Azure AD 获取令牌。

       ![应用密钥](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>向 Azure Maps 授予基于角色的访问控制（RBAC）

将 Azure Maps 帐户与 Azure AD 租户关联后，可以授予访问控制权限。 通过将用户、组或应用程序分配给一个或多个 Azure Maps 访问控制角色来授予访问控制权限。

1. 中转到你的**Azure Maps 帐户**。 选择 "**访问控制（IAM）** "，然后选择 "**角色分配**"。

    ![授予 RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 "**角色分配**" 窗口中的 "**角色**" 下，选择 " **Azure Maps 日期读取者（预览版）** "。 在 "**分配访问权限**" 下，选择**Azure AD 用户、组或服务主体**。 选择用户或应用程序。 选择“保存”。

    ![添加角色分配](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>查看可用 Azure Maps RBAC 角色

若要查看可用于 Azure Maps 的基于角色的访问控制（RBAC）角色，请访问 "**访问控制（IAM）** "，选择 "**角色**"，然后搜索 "以**Azure Maps**开头的角色。 这些角色是可向其授予访问权限的角色。

![查看可用的角色](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>查看 Azure Maps RBAC

RBAC 提供粒度访问控制。

若要查看已为 Azure Maps 授予 RBAC 的用户和应用，请访问 "**访问控制（IAM）** "，选择 "**角色分配**"，然后按**Azure Maps**进行筛选。

![查看已授予 RBAC 的用户和应用](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>请求用于 Azure Maps 的令牌

注册应用并将其与 Azure Maps 相关联后，可以请求访问令牌。

* 如果你的应用程序对 Azure Maps Web SDK 使用用户令牌身份验证，则需要使用 Azure Maps 的客户端 ID 和 Azure AD 应用 ID 来配置 HTML 页面。

* 如果你的应用程序使用服务器/应用程序身份验证，则需要使用 Azure AD 资源 ID `https://atlas.microsoft.com/`、Azure Maps 的客户端 ID、Azure AD 的应用程序 ID 以及 Azure AD 的应用注册密码或证书，从 Azure AD 令牌终结点 `https://login.microsoftonline.com` 请求令牌。

| Azure 环境   | Azure AD 令牌终结点 | Azure 资源 ID |
| --------------------|-------------------------|-------------------|
| Azure Public        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

若要详细了解如何从 Azure AD 请求访问令牌，请参阅针对用户和服务主体的[身份验证 Azure AD 方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。


## <a name="next-steps"></a>后续步骤

若要了解有关 Azure AD 身份验证和 Azure Maps Web SDK 的详细信息，请参阅 [Azure AD 和 Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。

了解如何查看 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"] 
> [查看使用情况指标](how-to-view-api-usage.md)

有关演示如何将 Azure Active Directory （AAD）与 Azure Maps 集成的示例列表，请参阅：

> [!div class="nextstepaction"]
> [Azure AD 身份验证示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
