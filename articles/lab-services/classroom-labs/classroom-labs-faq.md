---
title: Azure 实验室服务中的教室实验室-常见问题解答 |Microsoft Docs
description: 本文提供了有关 Azure 实验室服务中的教室实验室的常见问题（FAQ）的解答。
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 551167cda28a2bb6007e66c1b4b458a0a7b2e396
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718028"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure 实验室服务中的教室实验室—常见问题（FAQ）
获取有关 Azure 实验室服务中的教室实验室的一些最常见问题的解答。 

## <a name="quotas"></a>할당량

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>每个用户的配额或每周的配额，还是每周的实验室持续时间？ 
为实验室设置的配额是针对实验室的整个持续时间的每个学生。 而且， [vm 的计划运行时间](how-to-create-schedules.md)不会计为分配给用户的配额。 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다.  有关配额的详细信息，请参阅为[用户设置配额](how-to-configure-student-usage.md#set-quotas-for-users)。

## <a name="schedules"></a>일정

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>设置计划后，实验室中的所有 Vm 是否自动启动？ 
아닙니다. 并非所有 Vm。 仅按计划分配给用户的 Vm。 未分配给用户的 Vm 不会自动启动。 这是由设计决定的。 

## <a name="lab-accounts"></a>랩 계정

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>由于地址范围不可用，为什么无法创建实验室？ 
在 Azure 门户中创建实验室帐户时，教室实验室可以在指定的 IP 地址范围内创建实验室 Vm。 提供地址范围时，为实验室 Vm 分配 512 IP 地址后创建的每个实验室。 实验室帐户的地址范围必须足够大，以适应要在实验室帐户下创建的所有实验室。 

例如，如果你的块为/19-10.0.0.0/19，则此地址范围可容纳8192个 IP 地址和16个实验室（8192/512 = 16 个实验）。 在这种情况下，实验室创建在 17 lab 创建时失败。

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我应在组织的防火墙设置上打开哪些端口范围才能通过 RDP/SSH 连接到实验室虚拟机？

端口为：49152–65535。 教室实验室位于负载均衡器的后面，因此实验室中的所有虚拟机都有单个 IP 地址，并且实验室中的每个虚拟机都有一个唯一的端口。 每次重新发布实验室时，端口号和公共 IP 地址可能会更改。

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我应在组织的防火墙设置上打开哪些公共 IP 地址范围，以便通过 RDP/SSH 连接到实验室虚拟机？
请参阅[AZURE IP 范围和服务标记—公有云](https://www.microsoft.com/download/details.aspx?id=56519)，它为 Azure 中的数据中心提供公共 IP 地址范围。 可以打开实验室帐户所在区域的 IP 地址。

## <a name="users"></a>사용자

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>教室实验室中可以有多少用户？
最多可以向一个教室实验室添加400个用户。 

## <a name="blog-post"></a>博客文章
订阅[Azure 实验室服务博客](https://azure.microsoft.com/blog/tag/azure-lab-services/)。

## <a name="update-notifications"></a>更新通知
订阅[实验室服务更新](https://azure.microsoft.com/updates/?product=lab-services)，随时了解实验室服务中的新增功能。

## <a name="general"></a>일반
### <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?
如果你的问题未在此处列出，请告知我们，以便我们可以帮助你找到答案。

- 이 FAQ의 끝에 질문을 게시합니다. 
- 若要访问更广大的受众，请在[Azure 实验室服务（Stack Overflow 论坛）](https://stackoverflow.com/questions/tagged/azure-lab-services)上发布问题。 
- 对于功能请求，请将请求和想法提交到[Azure 实验室服务-用户语音](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)。

