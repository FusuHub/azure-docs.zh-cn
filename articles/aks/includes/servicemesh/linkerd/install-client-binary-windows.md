---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 2a1249d134c23f47f939913fa1c9887d2a8e1f63
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600842"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下载并安装 Linkerd Linkerd 客户端二进制文件

在 Windows 上基于 PowerShell 的 shell 中，使用 `Invoke-WebRequest` 下载 Linkerd 版本，如下所示：

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

@No__t_0 客户端二进制文件在客户端计算机上运行，并允许与 Linkerd 服务网格交互。 使用以下命令在 Windows 上基于 PowerShell 的 shell 中安装 Linkerd `linkerd` 客户端二进制文件。 这些命令将 `linkerd` 的客户端二进制文件复制到 Linkerd 文件夹，然后通过 `PATH` 使其立即可供使用（在当前 shell 中）和永久（跨 shell 重启）。 运行这些命令不需要提升的（管理员）权限，并且无需重新启动 shell。

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```