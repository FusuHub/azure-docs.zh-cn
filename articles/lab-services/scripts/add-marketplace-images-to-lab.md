---
title: PowerShell-将 marketplace 映像添加到 Azure 开发测试实验室中的实验室
description: 此 PowerShell 脚本将市场映像添加到 Azure 开发测试实验室中的实验室。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d699a22ed06022c6d9df12ade7f202fb304648c
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166444"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 将市场映像添加到 Azure 开发测试实验室中的实验室

此示例 PowerShell 脚本将市场映像添加到 Azure 开发测试实验室中的实验室。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>必备组件
* 实验室。 此脚本要求拥有现有的实验室。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| Find-AzResource | 基于指定参数搜索资源。 |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 获取资源。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 修改资源。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 创建资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 实验室服务 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 实验室服务 PowerShell 脚本示例。
