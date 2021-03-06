---
title: 配置 Azure AD 身份验证
description: 了解如何将 Azure Active Directory authentication 配置为应用服务应用的标识提供者。
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 69959418c52eb7324efe19ca41481e426b822ab4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842345"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>将应用服务应用配置为使用 Azure AD 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何将 Azure App Service 配置为使用 Azure Active Directory （Azure AD）作为身份验证提供程序。

设置应用和身份验证时，请遵循以下最佳做法：

- 为每个应用服务应用指定自己的权限和许可。
- 将每个应用服务应用程序配置为自己的注册。
- 避免在环境之间通过单独的部署槽位使用单独的应用注册来共享权限。 测试新代码时，这种做法可帮助防止问题影响生产应用。

## <a name="express"></a>用快速设置进行配置

1. 在[Azure 门户]中，搜索并选择 "**应用服务**"，然后选择应用。
2. 从左侧导航栏中选择 "**身份验证/授权** >  **"** 。
3. 选择**Azure Active Directory** > **Express**。

   如果要改为选择现有应用注册：

   1. 选择 "**选择现有 AD 应用**"，然后单击 " **Azure AD 应用**"。
   2. 选择现有应用注册，并单击 **"确定"** 。

3. 选择“确定”，在 Azure Active Directory 中注册应用服务应用。 创建新的应用注册。
   
    ![Azure Active Directory 中的快速设置](./media/configure-authentication-provider-aad/express-settings.png)
   
4. 可有可无默认情况下，应用服务提供身份验证，但不限制对站点内容和 Api 的授权访问。 必须在应用代码中为用户授权。 若要将应用访问限制为仅限通过 Azure Active Directory 进行身份验证的用户访问，请将 "**请求未经身份验证时要执行的操作**" 设置为 " **Azure Active Directory**。 设置此功能时，应用要求对所有请求进行身份验证。 它还将所有未经身份验证的重新定向到 Azure Active Directory 进行身份验证。

    > [!CAUTION]
    > 以这种方式限制访问权限适用于对应用的所有调用，对于具有公开可用主页的应用，与在许多单页应用程序中一样，这可能并不理想。 对于此类应用程序，"**允许匿名请求（无操作）** " 可能是首选，应用程序会手动开始登录。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。
5. 选择“保存”。

## <a name="advanced"></a>配置高级设置

如果要使用不同 Azure AD 租户的应用注册，可以手动配置应用设置。 若要完成此自定义配置：

1. 在 Azure AD 中创建注册。
2. 向应用服务提供一些注册详细信息。

### <a name="register"></a>在应用服务应用的 Azure AD 中创建应用注册

在配置应用服务应用时，你将需要以下信息：

- 客户端 ID
- 租户 ID
- 客户端密码（可选）
- 应用程序 ID URI

执行以下步骤：

1. 登录到[Azure 门户]，搜索并选择 "**应用服务**"，然后选择应用。 记下应用程序的**URL**。 将使用它来配置 Azure Active Directory 应用注册。
1. 选择 " **Azure Active Directory** > **应用注册**" > "**新注册**"。
1. 在 "**注册应用程序**" 页中，输入应用注册的**名称**。
1. 在 "**重定向 URI**" 中，选择 " **Web** "，然后键入 `<app-url>/.auth/login/aad/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback` 。 
1. 选择“创建”。
1. 创建应用注册后，请复制**应用程序（客户端） id**和**目录（租户） id** ，以备稍后之用。
1. 选择“品牌”。 在 "**主页 url**" 中，输入应用服务应用的 url，然后选择 "**保存**"。
1. 选择 "**公开 API** > **集**"。 粘贴到应用服务应用的 URL，然后选择 "**保存**"。

   > [!NOTE]
   > 此值是应用注册的**应用程序 ID URI** 。 如果 web 应用需要访问云中的 API，则在配置云应用服务资源时，需要 web 应用的**应用程序 ID URI** 。 例如，如果你希望云服务显式授予对 web 应用的访问权限，则可以使用此示例。

1. 选择“添加范围”。
   1. 在 "**作用域名称**" 中，输入*user_impersonation*。
   1. 在文本框中，输入希望用户在同意页上看到的许可范围名称和说明。 例如，输入 *"访问我的应用"* 。 
   1. 选择 "**添加作用域**"。
1. 可有可无若要创建客户端密钥，请选择 "**证书" & 机密** > **新的客户端密钥** > "**添加**"。 复制页面中显示的 "客户端机密" 值。 它不会再次显示。
1. 可有可无若要添加多个**回复 url**，请选择 "**身份验证**"。

### <a name="secrets"></a>启用应用服务应用中的 Azure Active Directory

1. 在[Azure 门户]中，搜索并选择 "**应用服务**"，然后选择应用。 
1. 在左窗格中的 "**设置**" 下，选择 "**身份验证/授权** > **打开"** 。
1. 可有可无默认情况下，应用服务身份验证允许未经身份验证的应用访问。 若要强制执行用户身份验证，请**在请求未经过身份验证时**，将操作设置为**使用 Azure Active Directory 进行登录**。
1. 在“身份验证提供程序”下，选择“Azure Active Directory”。
1. 在 "**管理模式**" 中，选择 "**高级**"，并根据下表配置应用服务身份验证：

    |字段|Description|
    |-|-|
    |客户端 ID| 使用应用注册的**应用程序（客户端） ID** 。 |
    |颁发者 ID| 使用 `https://login.microsoftonline.com/<tenant-id>`，并将 *\<租户 id >* 替换为应用注册的**目录（租户） id** 。 |
    |客户端密码（可选）| 使用在应用注册中生成的客户端密码。|
    |允许的令牌受众| 如果这是云应用程序或服务器应用程序，并且你想要允许来自 web 应用的身份验证令牌，请在此处添加 web 应用的**应用程序 ID URI** 。 已配置的**客户端 ID** *始终*被隐式视为允许的受众。 |

2. 选择“确定”，然后选择“保存”。

你现在可以在应用服务应用中使用 Azure Active Directory 进行身份验证。

## <a name="configure-a-native-client-application"></a>配置本机客户端应用程序

你可以注册本机客户端以允许使用客户端库（如**Active Directory 身份验证库**）进行身份验证。

1. 在[Azure 门户]中，选择 " **Active Directory** > **应用注册**" > **新注册**"。
1. 在 "**注册应用程序**" 页中，输入应用注册的**名称**。
1. 在 "**重定向 URI**" 中，选择 "**公用客户端（移动 & 桌面）** "，然后键入 URL `<app-url>/.auth/login/aad/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/aad/callback` 。

    > [!NOTE]
    > 对于 Windows 应用程序，请改用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)作为 URI。
1. 选择“创建”。
1. 创建应用注册后，复制 "**应用程序（客户端） ID**" 的值。
1.  >  > Api**添加权限**，请选择 **"** **api 权限**"。
1. 选择以前为应用服务应用创建的应用注册。 如果看不到应用注册，请确保已在[为应用服务应用 Azure AD 创建应用注册](#register)中添加了**user_impersonation**范围。
1. 选择 " **user_impersonation**"，然后选择 "**添加权限**"。

现已配置可以访问应用服务应用的本机客户端应用程序。

## <a name="related-content"></a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure 门户]: https://portal.azure.com/
