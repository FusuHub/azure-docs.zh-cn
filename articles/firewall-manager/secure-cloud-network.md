---
title: 教程：配合使用 Azure 防火墙管理器预览版和 Azure 门户以保护云网络
description: 在本教程中，你将了解如何配合使用 Azure 防火墙管理器和 Azure 门户以保护云网络。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: victorh
ms.openlocfilehash: d2ebfd6003c0bc2b47636be1e38f47e554cc6988
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510031"
---
# <a name="tutorial-secure-your-cloud-network-with-azure-firewall-manager-preview-using-the-azure-portal"></a>教程：配合使用 Azure 防火墙管理器预览版和 Azure 门户以保护云网络

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

使用 Azure 防火墙管理器预览版，可以创建安全中心来保护发往专用 IP 地址、Azure PaaS 和 Internet 的云网络流量。 到防火墙的流量路由是自动的，因此无需创建用户定义的路由 (UDR)。

![保护云网络](media/secure-cloud-network/secure-cloud-network.png)

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 必须使用 `Register-AzProviderFeature` PowerShell 命令显式启用 Azure 防火墙管理器预览版。

从 PowerShell 命令提示符运行以下命令：

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
功能注册最多需要 30 分钟即可完成。 运行以下命令来检查注册状态：

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="create-a-hub-and-spoke-architecture"></a>创建中心和辐射体系结构

首先，创建一个可放置服务器的辐射 VNet。

### <a name="create-a-spoke-vnet-and-subnets"></a>创建辐射 VNet 和子网

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在“网络”下，选择“虚拟网络”。  
4. 对于“名称”  ，请键入“Spoke-01”  。
5. 对于“地址空间”，请键入 **10.0.0.0/16**。 
6. 对于“订阅”，请选择自己的订阅。 
7. 对于“资源组”  ，选择“新建”  ，键入“FW-Manager”  作为名称，然后选择“确定”  。
8. 对于“位置”  ，请选择“(美国)美国东部”  。
9. 在“子网”  下，为“名称”  键入“Workload-SN”  。
10. 对于“地址范围”，请键入 **10.0.1.0/24**。 
11. 接受其他默认设置，然后选择“创建”。 

接下来，创建用于跳转服务器的子网。

1. 在 Azure 门户主页上，选择“资源组”   > “FW-Manager”  。
2. 选择“Spoke-01”  虚拟网络。
3. 选择“子网”   > “+子网”  。
4. 对于“名称”  ，键入“Jump-SN”  。
5. 对于“地址范围”，请键入 **10.0.2.0/24**。 
6. 选择“确定”  。

### <a name="create-the-secured-virtual-hub"></a>创建安全虚拟中心

使用防火墙管理器创建安全虚拟中心。

1. 在 Azure 门户主页上，选择“所有服务”。 
2. 在搜索框中，键入“防火墙管理器”  并选择“防火墙管理器”  。
3. 在“防火墙管理器”  页上，选择“创建安全虚拟中心”  。
4. 在“新建安全虚拟中心”  页上，选择你的订阅和“FW-Manager”  资源组。
5. 对于“安全虚拟中心名称”  ，键入“Hub-01”  。
6. 对于“位置”，请选择“美国东部”。  
7. 对于“中心地址空间”  ，请键入“10.1.0.0/16”  。
8. 对于新的 vWAN 名称，请键入“vwan-01”  。
9. 使“包含 VPN 网关以启用受信任的安全合作伙伴”  复选框处于清除状态。
10. 选择“下一步:Azure 防火墙”  。
11. 接受默认的“Azure 防火墙”  的“启用”  设置，然后选择“下一步:受信任的安全合作伙伴”  。
12. 接受默认的“受信任的安全合作伙伴”  的“禁用”  设置，然后选择“下一步:  查看 + 创建”。
13. 选择“创建”  。 这将耗时大约 30 分钟进行部署。

### <a name="connect-the-hub-and-spoke-vnets"></a>连接中心和辐射 VNet

现在，可以将中心和辐射 VNet 对等互连。

1. 选择“FW-Manager”  资源组，然后选择“vwan-01”  虚拟 WAN。
2. 在“连接”  下，选择“虚拟网络连接”  。
3. 选择“添加连接”  。
4. 对于“连接名称”  ，键入“hub-spoke”  。
5. 对于“中心”  ，选择“Hub-01”  。
6. 对于“虚拟网络”  ，选择“Spoke-01”  。
7. 选择“确定”  。

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>创建防火墙策略并保护中心

防火墙策略定义规则集合，以在一个或多个安全虚拟中心上定向流量。 你将创建防火墙策略，然后保护你的中心。

1. 从防火墙管理器中，选择“创建 Azure 防火墙策略”  。
2. 选择订阅，然后选择“FW-Manager”  资源组。
3. 在“策略详细信息”  下，针对“名称”  键入“Policy-01”  并针对“区域”  选择“美国东部”  。
4. 选择“下一步:规则”  。
5. 在“规则”  选项卡上，选择“添加规则集合”  。
6. 在“添加规则集合”  页上，针对“名称”  键入“RC-01”  。
7. 对于“规则集合类型”  ，选择“应用程序”  。
8. 对于“优先级”，请键入 **100**。 
9. 确保“规则集合操作”  设置为“允许”  。
10. 对于规则的“名称”  ，键入“Allow-msft”  。
11. 对于“源地址”  键入“\*”  。
12. 对于“协议”  ，键入“http,https”  。
13. 确保**目标类型是“FQDN”  。
14. 对于“目标”  ，键入“.microsoft.com” **\*** 。
15. 选择 **添加** 。
16. 在完成时选择“下一步:**安全虚拟中心**。
17. 在“安全虚拟中心”  选项卡上，选择“Hub-01”  。
19. 选择“查看 + 创建”  。
20. 选择“创建”  。

这可能需要 5 分钟或更长时间才能完成。

## <a name="route-traffic-to-your-hub"></a>将流量路由到中心

现在必须确保通过防火墙路由到网络流量。

1. 从防火墙管理器中，选择“安全虚拟中心”  。
2. 选择“Hub-01”  。
3. 在“设置”  下，选择“路由设置”  。
4. 在“Internet 流量”  下的“来自虚拟网络的流量”  中，选择“通过 Azure 防火墙发送”  。
5. 在“Azure 专用流量”  下的“来自虚拟网络的流量”  中，选择“通过 Azure 防火墙发送”  。
6. 选择“编辑 IP 地址前缀”  。
7. 选择“添加 IP 地址前缀”  。
8. 键入“10.0.1.0/24”  作为工作负载子网的地址，然后选择“保存”  。
9. 在“设置”  下，选择“连接”  。
10. 选择“hub-spoke”  连接，然后选择“保护 Internet 流量”  ，然后选择“确定”  。


## <a name="test-your-firewall"></a>测试防火墙

若要测试防火墙规则，将需要部署几个服务器。 你将在“Workload-SN”子网中部署 Workload-Srv 以测试防火墙规则和 Jump-Srv，以便你可以使用远程桌面从 Internet 进行连接，然后连接到 Workload-Srv。

### <a name="deploy-the-servers"></a>部署服务器

1. 在 Azure 门户中，选择“创建资源”。 
2. 在“常用”列表中选择“Windows Server 2016 Datacenter”   。
3. 输入虚拟机的以下值：

   |设置  |值  |
   |---------|---------|
   |Resource group     |**FW-Manager**|
   |虚拟机名称     |**Jump-Srv**|
   |区域     |**(美国)美国东部**|
   |管理员用户名     |**azureuser**|
   |密码     |**Azure123456!** -|

4. 在“入站端口规则”  下，对于 **“公共入站端口”** ，请选择“允许所选端口”  。
5. 对于“选择入站端口”，请选择“RDP (3389)”。  

6. 接受其他默认值，然后选择“下一步:**磁盘”** 。
7. 接受磁盘默认值，然后选择“下一步:  网络”。
8. 请确保选择“Spoke-01”  作为虚拟网络，子网是“Jump-SN”  。
9. 对于“公共 IP”  ，请接受默认的新公共 IP 地址名称 (Jump-Srv-ip)。
11. 接受其他默认值，然后选择“下一步:**管理”** 。
12. 选择“关闭”  以禁用启动诊断。 接受其他默认值，然后选择“查看 + 创建”  。
13. 检查摘要页上的设置，然后选择“创建”。 

使用下表中的信息配置名为“Workload-Srv”  的另一个虚拟机。 剩余的配置与 Srv-Work 虚拟机相同。

|设置  |值  |
|---------|---------|
|子网|**Workload-SN**|
|公共 IP|无 |
|公共入站端口|无 |

### <a name="add-a-route-table-and-default-route"></a>添加路由表和默认路由

若要允许通过 Internet 连接到 Jump-Srv，你必须从“Jump-SN”  子网创建路由表和到 Internet 的默认网关路由。

1. 在 Azure 门户中，选择“创建资源”。 
2. 在搜索框中键入“路由表”  ，然后选择“路由表”  。
3. 选择“创建”  。
4. 对于“名称”  ，键入“RT-01”  。
5. 选择你的订阅，并选择“FW-Manager”  资源组和“(美国)美国东部”  区域。
6. 选择“创建”  。
7. 部署完成后，选择“RT-01”  路由表。
8. 依次选择“路由”、“添加”   。
9. 对于“路由名称”  ，键入“jump-to-inet”  。
10. 对于“地址前缀”  键入“0.0.0.0/0”  。
11. 对于“下一跃点类型”  ，选择“Internet”  。
12. 选择“确定”  。
13. 部署完成后，选择“子网”  ，然后选择“关联”  。
14. 对于“虚拟网络”  ，选择“Spoke-01”  。
15. 对于“子网”  ，请选择“Jump-SN”  。
16. 选择“确定”  。

### <a name="test-the-rules"></a>测试规则

现在，测试防火墙以确认它可按预期工作。

1. 在 Azure 门户中，查看“Workload-Srv”  虚拟机的网络设置并记下专用 IP 地址。
2. 将远程桌面连接到“Jump-Srv”  虚拟机，然后登录。 在这里，打开与“Workload-Srv”  专用 IP 地址建立的远程桌面连接。

3. 打开 Internet Explorer 并浏览到 https://www.microsoft.com 。
4. 出现 Internet Explorer 安全警报时，请选择“确定” > “关闭”。  

   应会看到 Microsoft 主页。

5. 浏览到 https://www.google.com 。

   防火墙应会阻止你访问。

现已验证防火墙规则可正常工作：

* 可以浏览到一个允许的 FQDN，但不能浏览到其他任何 FQDN。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解受信任的安全合作伙伴](trusted-security-partners.md)
