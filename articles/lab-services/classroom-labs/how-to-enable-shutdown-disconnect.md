---
title: 在断开 Azure 实验室服务的连接时启用 Vm 关闭 |Microsoft Docs
description: 了解在断开远程桌面连接时，如何启用或禁用 Vm 的自动关闭。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: e615170952ea2987639a0bfc269ad5a1692e1e59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480796"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>断开连接时启用 Vm 自动关闭
本文介绍如何在断开远程桌面连接后启用或禁用**Windows 10**实验室 vm （模板或学生）的自动关闭。 你还可以指定 Vm 在自动关机之前应等待用户重新连接的时间。

实验室帐户管理员可以为您在其中创建实验室的实验室帐户配置此设置。 有关详细信息，请参阅[在 "断开连接时自动关闭 vm" 作为实验室帐户](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)。 作为实验室所有者，你可以在创建实验室时或在创建实验室后重写该设置。 

## <a name="configure-when-creating-a-lab"></a>创建实验室时进行配置
在 "实验室创建向导" 的 "步骤 3" 页上，可以启用或禁用此功能，还可以指定 VM 在自动关机之前应等待用户重新连接的时间。 

![创建实验室时进行配置](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>创建实验室后进行配置
你可以在 "**设置**" 页上配置此设置，如下图所示： 

![创建实验室后进行配置](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [用于课堂实验室的仪表板](use-dashboard.md)