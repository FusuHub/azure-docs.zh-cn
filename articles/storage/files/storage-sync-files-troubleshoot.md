---
title: Azure 文件同步疑难解答 |Microsoft Docs
description: 排查 Azure 文件同步的常见问题。
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 887c10097187f193f55c6e301be3e739a16d6bf7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906923"
---
# <a name="troubleshoot-azure-file-sync"></a>排查 Azure 文件同步
使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本文旨在帮助你排查和解决 Azure 文件同步部署时可能遇到的问题。 如果需要对问题进行更深入的调查，我们还将介绍如何从系统收集重要日志。 如果看不到你的问题的答案，可以通过以下渠道联系我们（按提升顺序）：

1. [Azure 存储论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。
2. [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。
3. Microsoft 支持部门。 若要创建新的支持请求，请在 "Azure 门户中的"**帮助**"选项卡上，选择"**帮助 + 支持**"按钮，然后选择"**新建支持请求**"。

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>服务器上的 Azure 文件同步（同步、云分层等）出现问题。 是否应删除并重新创建服务器终结点？
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>代理安装和服务器注册
<a id="agent-installation-failures"></a>**代理安装失败故障排除**  
如果 Azure 文件同步代理安装失败，请在提升的命令提示符下运行以下命令，以在安装代理期间启用日志记录：

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

查看安装程序 .log，确定安装失败的原因。

<a id="agent-installation-on-DC"></a>**Active Directory 域控制器上的代理安装失败**  
如果尝试在 Active Directory 域控制器上安装同步代理，而 PDC 角色所有者在 Windows Server 2008 R2 或更低版本的操作系统版本上，则可能会遇到同步代理安装失败的问题。

若要解决此问题，请将 PDC 角色转移到另一个运行 Windows Server 2012 R2 或更高的域控制器，然后安装同步。

<a id="parameter-is-incorrect"></a>**访问 Windows Server 2012 R2 上的卷失败，出现错误：参数不正确**  
在 Windows Server 2012 R2 上创建服务器终结点后，访问卷时出现以下错误：

驱动器号： \不可访问。  
参数不正确。

若要解决此问题，请安装 Windows Server 2012 R2 的最新更新，然后重新启动服务器。

<a id="server-registration-missing-subscriptions"></a>**服务器注册不列出所有 Azure 订阅**  
当使用 ServerRegistration 注册服务器时，当你单击 "Azure 订阅" 下拉箭头时，订阅将丢失。

之所以出现此问题，是因为 ServerRegistration 目前不支持多租户环境。 此问题将在将来的 Azure 文件同步代理更新中解决。

若要解决此问题，请使用以下 PowerShell 命令注册服务器：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**服务器注册显示以下消息： "缺少必备组件"**  
如果未在 PowerShell 5.1 上安装 Az 或 AzureRM PowerShell 模块，则会出现此消息。 

> [!Note]  
> ServerRegistration 不支持 PowerShell 1.x。 你可以使用 PowerShell 1.x 上的 AzStorageSyncServer cmdlet 注册服务器。

若要在 PowerShell 5.1 上安装 Az 或 AzureRM 模块，请执行以下步骤：

1. 在提升的命令提示符下键入**powershell** ，然后按 enter。
2. 按照文档的说明安装最新 Az 或 AzureRM 模块：
    - [Az module （需要 .NET 4.7.2）](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [AzureRM 模块]( https://go.microsoft.com/fwlink/?linkid=856959)
3. 运行 ServerRegistration，并完成向导以将服务器注册到存储同步服务。

<a id="server-already-registered"></a>**服务器注册显示以下消息： "此服务器已注册"** 

!["服务器已注册" 错误消息的服务器注册对话框屏幕截图](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果以前向存储同步服务注册了服务器，则会显示此消息。 若要从当前存储同步服务注销服务器，然后向新的存储同步服务注册，请完成[使用 Azure 文件同步注销服务器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步骤。

如果存储同步服务中的 "**已注册服务器**" 下未列出该服务器，请在想要注销的服务器上运行以下 PowerShell 命令：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 如果该服务器是群集的一部分，则可以使用可选的*StorageSyncServer-CleanClusterRegistration*参数来删除该群集注册。

<a id="web-site-not-trusted"></a>**当我注册服务器时，我看到了许多 "网站不受信任" 的响应。为什么?**  
如果在服务器注册过程中启用了**增强的 Internet Explorer 安全**策略，则会出现此问题。 有关如何正确禁用**增强的 Internet Explorer 安全**策略的详细信息，请参阅[准备 Windows Server 以使用 Azure 文件同步](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

<a id="server-registration-missing"></a>**服务器未在 Azure 门户中的 "已注册服务器" 下列出**  
如果存储同步服务的 "**已注册的服务器**" 下列出了服务器：
1. 登录到要注册的服务器。
2. 打开文件资源管理器，然后切换到存储同步代理安装目录（默认位置为 C:\Program Files\Azure\StorageSyncAgent）。 
3. 运行 ServerRegistration，并完成向导以将服务器注册到存储同步服务。

## <a name="sync-group-management"></a>同步组管理
<a id="cloud-endpoint-using-share"></a>**云终结点创建失败，出现以下错误： "指定的 Azure 文件共享已被其他 CloudEndpoint 使用"**  
如果 Azure 文件共享已被其他云终结点使用，则会出现此错误。 

如果你看到此消息，并且 Azure 文件共享当前未被云终结点使用，请完成以下步骤清除 Azure 文件共享上的 Azure 文件同步元数据：

> [!Warning]  
> 删除云终结点当前正在使用的 Azure 文件共享上的元数据会导致 Azure 文件同步操作失败。 

1. 在 Azure 门户中，请参阅 Azure 文件共享。  
2. 右键单击 "Azure 文件共享"，然后选择 "**编辑元数据**"。
3. 右键单击**SyncService**，然后选择 "**删除**"。

<a id="cloud-endpoint-authfailed"></a>**云终结点创建失败，出现以下错误： "AuthorizationFailed"**  
如果你的用户帐户没有足够的权限来创建云终结点，则会发生此错误。 

若要创建云终结点，你的用户帐户必须具有下列 Microsoft 授权权限：  
* 读取：获取角色定义
* 写入：创建或更新自定义角色定义
* 读取：获取角色分配
* 写入：创建角色分配

以下内置角色具有所需的 Microsoft 授权权限：  
* Owner
* 用户访问管理员

若要确定你的用户帐户角色是否具有所需的权限：  
1. 在 Azure 门户中，选择 "**资源组**"。
2. 选择存储帐户所在的资源组，然后选择 "**访问控制（IAM）** "。
3. 选择 "**角色分配**" 选项卡。
4. 选择用户帐户的**角色**（例如，所有者或参与者）。
5. 在**资源提供程序**列表中，选择 " **Microsoft 授权**"。 
    * **角色分配**应具有**读取**和**写入**权限。
    * **角色定义**应该具有**读取**和**写入**权限。

<a id="-2134375898"></a>**服务器终结点创建失败，出现以下错误： "MgmtServerJobFailed" （错误代码：-2134375898 或0x80c80226）**  
如果服务器终结点路径在系统卷上并且启用了云分层，则会发生此错误。 系统卷上不支持云分层。 若要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。

<a id="-2147024894"></a>**服务器终结点创建失败，出现以下错误： "MgmtServerJobFailed" （错误代码：-2147024894 或0x80070002）**  
如果指定的服务器终结点路径无效，则会发生此错误。 验证指定的服务器终结点路径是否为本地连接的 NTFS 卷。 请注意，Azure 文件同步不支持将映射的驱动器作为服务器终结点路径。

<a id="-2134375640"></a>**服务器终结点创建失败，出现以下错误： "MgmtServerJobFailed" （错误代码：-2134375640 或0x80c80328）**  
如果指定的服务器终结点路径不是 NTFS 卷，则会出现此错误。 验证指定的服务器终结点路径是否为本地连接的 NTFS 卷。 请注意，Azure 文件同步不支持将映射的驱动器作为服务器终结点路径。

<a id="-2134347507"></a>**服务器终结点创建失败，出现以下错误： "MgmtServerJobFailed" （错误代码：-2134347507 或0x80c8710d）**  
之所以发生此错误，是因为 Azure 文件同步不支持具有压缩系统卷信息文件夹的卷上的服务器终结点。 若要解决此问题，请解压缩系统卷信息文件夹。 如果系统卷信息文件夹是卷上唯一压缩的文件夹，请执行以下步骤：

1. 下载[PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)工具。
2. 在提升的命令提示符下运行以下命令，启动在系统帐户下运行的命令提示符： **PsExec-i-s-d cmd**
3. 在系统帐户下运行的命令提示符下，键入以下命令并按 enter：   
    **cd/d "驱动器号： \ 系统卷信息"**  
    **compact/u/s**

<a id="-2134376345"></a>**服务器终结点创建失败，出现以下错误： "MgmtServerJobFailed" （错误代码：-2134376345 或0x80C80067）**  
如果达到每个服务器的服务器终结点限制，则会出现此错误。 Azure 文件同步当前支持每个服务器最多30个服务器终结点。 有关详细信息，请参阅[Azure 文件同步缩放目标](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets)。

<a id="-2134376427"></a>**服务器终结点创建失败，出现以下错误： "MgmtServerJobFailed" （错误代码：-2134376427 或0x80c80015）**  
如果另一个服务器终结点已经同步指定的服务器终结点路径，则会发生此错误。 Azure 文件同步不支持同步同一目录或卷的多个服务器终结点。

<a id="-2160590967"></a>**服务器终结点创建失败，出现以下错误： "MgmtServerJobFailed" （错误代码：-2160590967 或0x80c80077）**  
如果服务器终结点路径包含孤立的分层文件，则会发生此错误。 如果最近删除了服务器终结点，请等待，直到孤立的分层文件清理完成。 启动孤立分层文件清理后，会将事件 ID 6662 记录到遥测事件日志中。 当孤立分层文件清理完成并且可以使用该路径重新创建服务器终结点后，将记录事件 ID 6661。 如果在记录事件 ID 6661 后服务器终结点创建失败，请通过执行 "[删除服务器终结点后在服务器上无法访问](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)" 一节中所述的步骤，删除孤立的分层文件。

<a id="-2134347757"></a>**服务器终结点删除失败，并出现以下错误： "MgmtServerJobExpired" （错误代码：-2134347757 或0x80c87013）**  
如果服务器处于脱机状态或没有网络连接，则会发生此错误。 如果服务器不再可用，请在门户中取消注册服务器终结点的服务器。 若要删除服务器终结点，请按照[使用 Azure 文件同步注销服务器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步骤进行操作。

<a id="server-endpoint-provisioningfailed"></a>**无法打开服务器终结点属性页或更新云分层策略**  
如果服务器终结点上的管理操作失败，则可能出现此问题。 如果未在 Azure 门户中打开服务器终结点属性页，则从服务器使用 PowerShell 命令更新服务器终结点可能会解决此问题。 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**服务器终结点的运行状况状态为 "无活动" 或 "挂起"，并且 "已注册的服务器" 边栏选项卡上的 "服务器状态" 显示为 "脱机"**  

如果存储同步监视器进程（AzureStorageSyncMonitor）未运行或服务器无法访问 Azure 文件同步服务，则可能出现此问题。

在门户中显示为 "脱机" 的服务器上，查看遥测事件日志中的事件 ID 9301 （位于事件查看器中的 "应用程序" 和 "Services\Microsoft\FileSync\Agent"），以确定服务器无法访问 Azure 文件同步的原因服务. 

- 如果**GetNextJob 已完成，状态为： 0** ，则服务器可以与 Azure 文件同步服务通信。 
    - 打开服务器上的任务管理器并验证存储同步监视器（AzureStorageSyncMonitor）进程是否正在运行。 如果该进程未运行，请首先尝试重新启动服务器。 如果重新启动服务器无法解决该问题，请升级到最新的 Azure 文件同步[代理版本](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes)。 

- 如果**GetNextJob 已完成，状态为：-2134347756** ，则服务器无法与 Azure 文件同步服务通信，因为防火墙或代理。 
    - 如果服务器位于防火墙后面，请验证是否允许端口443出站。 如果防火墙将流量限制到特定域，请确认防火墙[文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall)中列出的域是可访问的。
    - 如果服务器位于代理后面，请按照代理[文档](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)中的步骤配置计算机范围或特定于应用的代理设置。
    - 使用 StorageSyncNetworkConnectivity cmdlet 检查到服务终结点的网络连接。 若要了解详细信息，请参阅[测试到服务终结点的网络连接](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)。

- 如果**GetNextJob 已完成，状态为：-2134347764** ，则服务器无法与 Azure 文件同步服务通信，因为证书已过期或已删除。  
    - 在服务器上运行以下 PowerShell 命令，以重置用于身份验证的证书：
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**服务器终结点的运行状况状态为 "无活动"，且 "已注册的服务器" 边栏选项卡上的服务器状态为 "联机"**  

服务器终结点运行状况状态为 "无活动" 表示服务器终结点在过去两小时内未记录同步活动。

若要检查服务器上的当前同步活动，请参阅[如何实现监视当前同步会话的进度？](#how-do-i-monitor-the-progress-of-a-current-sync-session)。

由于 bug 或系统资源不足，服务器终结点可能不会记录同步活动几个小时。 验证是否安装了最新的 Azure 文件同步[代理版本](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes)。 如果问题仍然存在，请打开支持请求。

> [!Note]  
> 如果 "已注册的服务器" 边栏选项卡上的 "服务器状态" 显示为 "脱机"，请执行服务器终结点中记录的步骤的[运行状况状态为 "无活动" 或 "挂起"，并且 "已注册的服务器" 边栏选项卡上的 "服务器状态" 部分为 "脱机"](#server-endpoint-noactivity) 。

## <a name="sync"></a>同步
<a id="afs-change-detection"></a>**如果我通过 SMB 或门户在 Azure 文件共享中直接创建了一个文件，该文件同步到同步组中的服务器需要多长时间？**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**服务器终结点运行状况处于挂起状态长达几个小时**  
如果创建云终结点并使用包含数据的 Azure 文件共享，则会出现此问题。 在 Azure 文件共享中扫描更改的更改枚举作业必须先完成，然后文件才能在云和服务器终结点之间进行同步。 完成作业的时间取决于 Azure 文件共享中命名空间的大小。 更改枚举作业完成后，服务器终结点运行状况应该会更新。

### <a id="broken-sync"></a>如何实现监视同步运行状况？
# <a name="portaltabportal1"></a>[端口](#tab/portal1)
在每个同步组中，你可以向下钻取到其各自的服务器终结点，以查看上次完成的同步会话的状态。 绿色运行状况列和未同步值0的文件指示同步按预期方式工作。 如果不是这种情况，请参阅下面有关常见同步错误的列表，以及如何处理未同步的文件。 

![Azure 门户的屏幕截图](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[服务](#tab/server)
请访问服务器的遥测日志，该日志可在 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`的事件查看器中找到。 事件9102对应于已完成的同步会话;有关同步的最新状态，请查找 ID 为9102的最新事件。 SyncDirection 会告诉您此会话是上传还是下载。 如果 HResult 为0，则同步会话已成功。 非零 HResult 表示在同步过程中出错;有关常见错误的列表，请参阅下文。 如果 PerItemErrorCount 大于0，则表示某些文件或文件夹未正确同步。 可以使 HResult 为0，但 PerItemErrorCount 大于0。

下面是一个成功上载的示例。 为了简洁起见，下面列出了每个9102事件中包含的一些值。 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

相反，上载失败可能如下所示：

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

有时，同步会话会整体失败或具有非零 PerItemErrorCount，但仍会继续进行，而某些文件则同步成功。 此操作可以在应用的 * 字段（AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes）中看到，这会告诉你会话的成功程度。 如果在某个行中发现多个同步会话出现故障，但应用了已增加的 * 计数，则应在打开支持票证之前提供同步时间以重试。

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>如何实现监视当前同步会话的进度？
# <a name="portaltabportal1"></a>[端口](#tab/portal1)
在同步组中，请访问相关的服务器终结点，查看同步活动部分，查看在当前同步会话中上载或下载的文件的计数。 请注意，此状态将延迟约5分钟，并且如果你的同步会话足够小，可以在此时间段内完成，则它可能不会在门户中报告。 

# <a name="servertabserver"></a>[服务](#tab/server)
查看服务器上遥测日志中的最新9302事件（在事件查看器中，请参阅应用程序和服务 Logs\Microsoft\FileSync\Agent\Telemetry）。 此事件指示当前同步会话的状态。 TotalItemCount 表示要同步的文件数，AppliedItemCount 迄今为止已同步的文件数，以及 PerItemErrorCount 无法同步的文件数（请参阅下面有关如何处理此情况的详细说明）。

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>如何实现知道我的服务器是否彼此同步？
# <a name="portaltabportal1"></a>[端口](#tab/portal1)
对于给定同步组中的每个服务器，请确保：
- 最近一次上载和下载尝试同步的时间戳是最新的。
- 对于上传和下载，状态为绿色。
- "同步活动" 字段显示的文件很少或没有剩余的同步操作。
- 对于上传和下载，"未同步的文件" 字段为0。

# <a name="servertabserver"></a>[服务](#tab/server)
查看在每个服务器的遥测事件日志中标记为9102事件的已完成同步会话（在事件查看器中，中转到 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`）。 

1. 在任何给定的服务器上，需要确保最近的上传和下载会话已成功完成。 若要执行此操作，请检查 HResult 和 PerItemErrorCount 对于上传和下载均为0（SyncDirection 字段指示给定会话是上传还是下载会话）。 请注意，如果你看不到最近完成的同步会话，则很可能当前正在进行同步会话，如果你刚添加或修改了大量数据，就会出现这种情况。
2. 当服务器与云完全更新并且没有任何一个方向的同步更改时，你将看到空的同步会话。 其中的所有 Sync * 字段（SyncFileCount、SyncDirCount、SyncTombstoneCount 和 SyncSizeBytes）都为零，这意味着没有要同步的内容。请注意，这些空的同步会话可能不会出现在高改动服务器上，因为始终有新的同步操作。如果没有同步活动，则每隔30分钟就会发生一次。 
3. 如果所有服务器都是最新的云，即最近的上传和下载会话为空的同步会话，你可以合理地确信系统整体处于同步状态。 
    
请注意，如果直接在 Azure 文件共享中进行更改，Azure 文件同步将不会在更改枚举运行之前检测到此更改，这种情况每24小时发生一次。 当某个服务器确实缺少直接在 Azure 文件共享中所做的最新更改时，它可能会指出它是最新的。 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>如何实现查看是否存在未同步的特定文件或文件夹？
如果对于任何给定的同步会话，在该服务器上的 PerItemErrorCount 或在门户中同步的文件数不是0，则意味着某些项未能同步。文件和文件夹可能有阻止它们同步的特征。 这些特性可以是永久性的，并且需要显式操作才能恢复同步，例如，从文件或文件夹名称中删除不受支持的字符。 它们也可以是暂时性的，这意味着文件或文件夹将自动恢复同步;例如，当文件关闭时，带有打开的句柄的文件会自动恢复同步。 当 Azure 文件同步引擎检测到这样的问题时，将生成一个错误日志，可对其进行分析以列出当前未正确同步的项。

若要查看这些错误，请运行**FileSyncErrorsReport** PowerShell 脚本（位于 Azure 文件同步 agent 的代理安装目录中），以标识由于打开句柄、不支持的字符或其他问题而无法同步的文件。 项路径字段告诉您文件相对于根同步目录的位置。 有关更正步骤，请参阅下面的常见同步错误列表。

> [!Note]  
> 如果 FileSyncErrorsReport 脚本返回 "找不到任何文件错误" 或不列出同步组的每个项的错误，则原因为：
>
>- 原因1：最后完成的同步会话没有每项错误。 门户应该会立即更新，以显示0个未同步的文件。 
>   - 检查遥测事件日志中的[事件 ID 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) ，确认 PerItemErrorCount 是否为0。 
>
>- 原因2：服务器上的 ItemResults 事件日志由于每个项目的错误太多而被包装，事件日志不再包含此同步组的错误。
>   - 若要避免此问题，请增加 ItemResults 事件日志的大小。 在事件查看器中的 "应用程序和服务 Logs\Microsoft\FileSync\Agent" 下可以找到 ItemResults 事件日志。 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>每个文件/目录同步错误的疑难解答
**ItemResults 每项日志同步错误数**  

| HRESULT | HRESULT （decimal） | 错误字符串 | 问题 | 修正 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | 服务器上的分层文件无法访问。 如果在删除服务器终结点之前未重新调用该分层文件，则会出现此问题。 | 若要解决此问题，请参阅[删除服务器终结点后，服务器上的分层文件无法访问](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 由于尚未同步依赖文件夹，因此无法同步文件或目录更改。 依赖更改同步后，此项将同步。 | 无需任何操作。 |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | 文件或目录更改尚未同步，因为尚未同步依赖文件夹，同步会话失败。 依赖更改同步后，此项将同步。 | 无需任何操作。 如果错误仍然存在，请调查同步会话失败。 |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | 文件或目录名称无效。 | 重命名有问题的文件或目录。 有关详细信息，请参阅[处理不支持的字符](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)。 |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | 文件或目录名称无效。 | 重命名有问题的文件或目录。 有关详细信息，请参阅[处理不支持的字符](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)。 |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | 无法同步文件，因为该文件正在使用中。 当该文件不再使用时，将同步该文件。 | 无需任何操作。 Azure 文件同步在服务器上每天创建一个临时 VSS 快照，以便同步具有打开句柄的文件。 |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | 此文件已更改，但同步未检测到此更改。检测到此更改后，同步将恢复。 | 无需任何操作。 |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | 该文件已被删除，同步不能识别此更改。 | 无需任何操作。 一旦更改检测检测到该文件已被删除，同步将停止记录此错误。 |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | 无法同步删除文件或目录，因为该项目已在目标中被删除，并且同步不知道此项更改。 | 无需任何操作。 在目标上运行更改检测并检测到已删除该项后，同步将停止记录此错误。 |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | 已跳过文件或目录，但会在下一个同步会话期间进行同步。 如果在下载项时报告此错误，则文件或目录的名称可能会无效。 | 如果在上传文件时报告此错误，则无需执行任何操作。 如果在下载文件时报告错误，请重命名相关文件或目录。 有关详细信息，请参阅[处理不支持的字符](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)。 |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | 无法同步文件或目录的创建，因为目标中已存在该项并且同步不知道更改。 | 无需任何操作。 在目标上运行更改检测并检测到此新项后，同步将停止记录此错误。 |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | 由于已达到 Azure 文件共享限制，无法同步该文件。 | 若要解决此问题，请参阅故障排除指南中[的 "Azure 文件共享存储限制"](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810)部分。 |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | 此文件由不受支持的解决方案（如 NTFS EFS）进行加密。 | 解密文件并使用受支持的加密解决方案。 有关支持解决方案的列表，请参阅规划指南中的[加密解决方案](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions)部分。 |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | 此文件位于 DFS R 只读复制文件夹中。 | 文件位于 DFS R 只读复制文件夹中。 Azure 文件同步不支持 DFS 只读复制文件夹中的服务器终结点。 有关详细信息，请参阅[规划指南](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs)。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 文件具有 "删除挂起" 状态。 | 无需任何操作。 关闭所有打开的文件句柄后，文件将被删除。 |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | 由于启用了存储帐户上的防火墙和虚拟网络设置并且服务器无权访问存储帐户，因此无法同步该文件。 | 按照部署指南中的[配置防火墙和虚拟网络设置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)部分所述的步骤，添加服务器 IP 地址或虚拟网络。 |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | 此文件无法同步，因为安全描述符大小超过了 64 KiB 的限制。 | 若要解决此问题，请删除文件上的访问控制项（ACE）以减少安全描述符大小。 |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | 由于出现意外错误，无法同步该文件。 | 如果错误持续几天，请打开支持案例。 |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | 无法同步文件，因为该文件正在使用中。 当该文件不再使用时，将同步该文件。 | 无需任何操作。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 在同步过程中更改了文件，因此需要再次同步该文件。 | 无需任何操作。 |
| 0x80070017 | -2147024873 | ERROR_CRC | 由于 CRC 错误，无法同步该文件。 如果在删除服务器终结点之前未重新调用某个分层文件或该文件已损坏，则会出现此错误。 | 若要解决此问题，请参阅[删除服务器终结点后，服务器上的分层文件无法访问](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)，以删除孤立的分层文件。 如果在删除已删除孤立分层文件后仍然出现错误，请在卷上运行[chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) 。 |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | 由于已达到冲突文件的最大数量，无法同步该文件。 Azure 文件同步支持每个文件100个冲突文件。 若要了解有关文件冲突的详细信息，请参阅 Azure 文件同步[常见问题解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution)。 | 若要解决此问题，请减少冲突文件的数目。 冲突文件数小于100后，文件将同步。 |

#### <a name="handling-unsupported-characters"></a>处理不受支持的字符
如果**FileSyncErrorsReport** PowerShell 脚本显示由于不支持的字符（错误代码0x8007007b 或0x80c80255）导致的失败，则应从相应的文件名中删除或重命名出现错误的字符。 PowerShell 可能会将这些字符打印为问号或空矩形，因为大多数这些字符都没有标准的视觉编码。 [评估工具](storage-sync-files-planning.md#evaluation-cmdlet)可用于识别不支持的字符。

下表包含 Azure 文件同步尚不支持的所有 unicode 字符。

| 字符集 | 字符计数 |
|---------------|-----------------|
| <ul><li>0x0000009D （.osc 操作系统命令）</li><li>0x00000090 （dc 设备控制字符串）</li><li>0x0000008F （ss3）</li><li>0x00000081 （高位字节预设）</li><li>0x0000007F （删除删除）</li><li>0x0000008D （ri 反向换行）</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF （阿拉伯语显现形式-a） | 32 |
| 0x0000FFF0-0x0000FFFF （特惠） | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 （非字符）</li><li>0x0002FFFE-0x0002FFFF = 2 （非字符）</li><li>0x0003FFFE-0x0003FFFF = 2 （非字符）</li><li>0x0004FFFE-0x0004FFFF = 2 （非字符）</li><li>0x0005FFFE-0x0005FFFF = 2 （非字符）</li><li>0x0006FFFE-0x0006FFFF = 2 （非字符）</li><li>0x0007FFFE-0x0007FFFF = 2 （非字符）</li><li>0x0008FFFE-0x0008FFFF = 2 （非字符）</li><li>0x0009FFFE-0x0009FFFF = 2 （非字符）</li><li>0x000AFFFE-0x000AFFFF = 2 （非字符）</li><li>0x000BFFFE-0x000BFFFF = 2 （非字符）</li><li>0x000CFFFE-0x000CFFFF = 2 （非字符）</li><li>0x000DFFFE-0x000DFFFF = 2 （非字符）</li><li>0x000EFFFE-0x000EFFFF = 2 （未定义）</li><li>0x000FFFFE-0x000FFFFF = 2 （辅助专用使用区域）</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>常见同步错误
<a id="-2147023673"></a>**同步会话已取消。**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT （decimal）** | -2147023673 | 
| **错误字符串** | ERROR_CANCELLED |
| **需要修正** | No |

同步会话可能会因各种原因而失败，其中包括重新启动或更新的服务器、VSS 快照等。尽管此错误看起来像需要跟进，但忽略此错误是安全的，除非它在几个小时内保持不变。

<a id="-2147012889"></a>**无法建立与服务的连接。**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT （decimal）** | -2147012889 | 
| **错误字符串** | WININET_E_NAME_NOT_RESOLVED |
| **需要修正** | 用户帐户控制 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**服务已限制用户请求。**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT （decimal）** | -2134376372 |
| **错误字符串** | ECS_E_USER_REQUEST_THROTTLED |
| **需要修正** | No |

不需要执行任何操作;服务器将重试。 如果此错误持续几个小时，请创建支持请求。

<a id="-2134364043"></a>**同步被阻止，直到更改检测完成还原后**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT （decimal）** | -2134364043 |
| **错误字符串** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **需要修正** | No |

无需任何操作。 使用 Azure 备份还原文件或文件共享（云终结点）后，同步会被阻止，直到在 Azure 文件共享上完成更改检测。 一旦还原完成且持续时间基于文件共享中的文件数，更改检测会立即运行。

<a id="-2147216747"></a>**同步失败，因为同步数据库已卸载。**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT （decimal）** | -2147216747 |
| **错误字符串** | SYNC_E_METADATA_INVALID_OPERATION |
| **需要修正** | No |

此错误通常发生在备份应用程序创建 VSS 快照并且卸载了同步数据库时。 如果此错误持续几个小时，请创建支持请求。

<a id="-2134364065"></a>**同步无法访问云终结点中指定的 Azure 文件共享。**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT （decimal）** | -2134364065 |
| **错误字符串** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **需要修正** | 用户帐户控制 |

发生此错误的原因是 Azure 文件同步代理无法访问 Azure 文件共享，这可能是因为 Azure 文件共享或托管该文件的存储帐户不再存在。 要解决此错误，可以执行以下步骤：

1. [验证存储帐户是否存在。](#troubleshoot-storage-account)
2. [确保 Azure 文件共享存在。](#troubleshoot-azure-file-share)
3. [确保 Azure 文件同步有权访问存储帐户。](#troubleshoot-rbac)
4. [验证是否正确配置了存储帐户上的防火墙和虚拟网络设置（如果已启用）](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**由于请求无权执行此操作，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT （decimal）** | -2134351804 |
| **错误字符串** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **需要修正** | 用户帐户控制 |

发生此错误的原因是 Azure 文件同步代理无权访问 Azure 文件共享。 要解决此错误，可以执行以下步骤：

1. [验证存储帐户是否存在。](#troubleshoot-storage-account)
2. [确保 Azure 文件共享存在。](#troubleshoot-azure-file-share)
3. [验证是否正确配置了存储帐户上的防火墙和虚拟网络设置（如果已启用）](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [确保 Azure 文件同步有权访问存储帐户。](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**无法解析所用的存储帐户名称。**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT （decimal）** | -2134364064 |
| **错误字符串** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **需要修正** | 用户帐户控制 |

1. 检查是否可以从服务器解析存储 DNS 名称。

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [验证存储帐户是否存在。](#troubleshoot-storage-account)
3. [验证是否正确配置了存储帐户上的防火墙和虚拟网络设置（如果已启用）](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**访问存储帐户时出现未知错误。**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT （decimal）** | -2134364022 |
| **错误字符串** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **需要修正** | 用户帐户控制 |

1. [验证存储帐户是否存在。](#troubleshoot-storage-account)
2. [验证是否正确配置了存储帐户上的防火墙和虚拟网络设置（如果已启用）](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**由于存储帐户被锁定，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT （decimal）** | -2134364014 |
| **错误字符串** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **需要修正** | 用户帐户控制 |

之所以发生此错误，是因为存储帐户具有只读[资源锁](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 若要解决此问题，请删除存储帐户上的只读资源锁。 

<a id="-1906441138"></a>**同步失败，因为同步数据库出现问题。**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT （decimal）** | -1906441138 |
| **错误字符串** | JET_errWriteConflict |
| **需要修正** | 用户帐户控制 |

如果 Azure 文件同步使用的内部数据库存在问题，则会出现此错误。发生此问题时，请创建支持请求，我们将与你联系以帮助你解决此问题。

<a id="-2134364053"></a>**服务器上安装的 Azure 文件同步代理版本不受支持。**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT （decimal）** | -2134364053 |
| **错误字符串** | ECS_E_AGENT_VERSION_BLOCKED |
| **需要修正** | 用户帐户控制 |

如果在服务器上安装的 Azure 文件同步代理版本不受支持，则会出现此错误。 若要解决此问题，请[升级]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths)到[受支持的代理版本]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)。

<a id="-2134351810"></a>**已达到 Azure 文件共享存储限制。**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT （decimal）** | -2134351810 |
| **错误字符串** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **需要修正** | 用户帐户控制 |

此错误发生在已达到 Azure 文件共享存储限制的情况下，如果 Azure 文件共享应用了配额或使用超出 Azure 文件共享的限制，则可能会发生此错误。 有关详细信息，请参阅[Azure 文件共享的当前限制](storage-files-scale-targets.md)。

1. 导航到存储同步服务中的同步组。
2. 选择同步组中的云终结点。
3. 请注意打开的窗格中的 Azure 文件共享名称。
4. 选择 "链接的存储帐户"。 如果此链接失败，则引用的存储帐户已被删除。

    ![显示 "云终结点详细信息" 窗格的屏幕截图，其中包含指向存储帐户的链接。](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. 选择 "**文件**" 以查看文件共享列表。
6. 针对云终结点引用的 Azure 文件共享，单击行末尾的三个点。
7. 验证**使用情况**是否低于**配额**。 注意除非指定了备用配额，否则配额将匹配[Azure 文件共享的最大大小](storage-files-scale-targets.md)。

    ![Azure 文件共享属性的屏幕截图。](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

如果共享已满并且未设置配额，则修复此问题的一种可行方法是将当前服务器终结点的每个子文件夹置于其自己的单独同步组中的服务器终结点。 这样，每个子文件夹就会同步到各个 Azure 文件共享。

<a id="-2134351824"></a>**找不到 Azure 文件共享。**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT （decimal）** | -2134351824 |
| **错误字符串** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **需要修正** | 用户帐户控制 |

当无法访问 Azure 文件共享时，会出现此错误。 若要解决此问题：

1. [验证存储帐户是否存在。](#troubleshoot-storage-account)
2. [确保 Azure 文件共享存在。](#troubleshoot-azure-file-share)

如果删除了 Azure 文件共享，则需要创建新的文件共享，然后重新创建同步组。 

<a id="-2134364042"></a>**此 Azure 订阅挂起时暂停同步。**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT （decimal）** | -2134364042 |
| **错误字符串** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **需要修正** | 用户帐户控制 |

当 Azure 订阅挂起时，会发生此错误。 恢复 Azure 订阅后，将重新启用同步。 请参阅[为什么禁用我的 Azure 订阅，以及如何重新激活它？](../../cost-management-billing/manage/subscription-disabled.md)以获取详细信息。

<a id="-2134364052"></a>**存储帐户配置了防火墙或虚拟网络。**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT （decimal）** | -2134364052 |
| **错误字符串** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **需要修正** | 用户帐户控制 |

当由于存储帐户防火墙或存储帐户属于虚拟网络而无法访问 Azure 文件共享时，会出现此错误。 验证是否正确配置了存储帐户上的防火墙和虚拟网络设置。 有关详细信息，请参阅[配置防火墙和虚拟网络设置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)。 

<a id="-2134375911"></a>**同步失败，因为同步数据库出现问题。**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT （decimal）** | -2134375911 |
| **错误字符串** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **需要修正** | No |

此错误通常会自行解决，并且可能会发生以下情况：

* 同步组中的服务器上的文件更改量很多。
* 单个文件和目录中有大量错误。

如果此错误持续超过数小时，请创建支持请求，我们将与你联系以帮助你解决此问题。

<a id="-2146762487"></a>**服务器无法建立安全连接。云服务收到意外的证书。**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT （decimal）** | -2146762487 |
| **错误字符串** | CERT_E_UNTRUSTEDROOT |
| **需要修正** | 用户帐户控制 |

如果你的组织正在使用 SSL 终止代理，或者如果恶意实体正在截获服务器与 Azure 文件同步服务之间的流量，则会发生此错误。 如果你确定这是预期的（因为你的组织使用的是 SSL 终止代理），则会跳过使用注册表替代的证书验证。

1. 创建 SkipVerifyingPinnedRootCertificate 注册表值。

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. 重新启动已注册的服务器上的同步服务。

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

通过设置此注册表值，在服务器和云服务之间传输数据时，Azure 文件同步代理将接受任何本地受信任的 SSL 证书。

<a id="-2147012894"></a>**无法建立与服务的连接。**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT （decimal）** | -2147012894 |
| **错误字符串** | WININET_E_TIMEOUT |
| **需要修正** | 用户帐户控制 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**由于身份验证问题，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT （decimal）** | -2134375680 |
| **错误字符串** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **需要修正** | 用户帐户控制 |

发生此错误的原因通常是服务器时间不正确。 如果服务器在虚拟机中运行，请验证主机上的时间是否正确。

<a id="-2134364040"></a>**由于证书过期，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT （decimal）** | -2134364040 |
| **错误字符串** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **需要修正** | 用户帐户控制 |

之所以发生此错误，是因为用于身份验证的证书已过期。

若要确认证书已过期，请执行以下步骤：  
1. 打开 "证书" MMC 管理单元，选择 "计算机帐户"，然后导航到 "证书（本地计算机）" \Personal\Certificates。
2. 检查客户端身份验证证书是否已过期。

如果客户端身份验证证书已过期，请执行以下步骤来解决此问题：

1. 验证是否安装了 Azure 文件同步代理版本4.0.1.0 或更高版本。
2. 在服务器上运行以下 PowerShell 命令：

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**由于找不到身份验证证书，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT （decimal）** | -2134375896 |
| **错误字符串** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **需要修正** | 用户帐户控制 |

发生此错误的原因是找不到用于身份验证的证书。

若要解决此问题，请执行以下步骤：

1. 验证是否安装了 Azure 文件同步代理版本4.0.1.0 或更高版本。
2. 在服务器上运行以下 PowerShell 命令：

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**由于找不到身份验证标识，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT （decimal）** | -2134364039 |
| **错误字符串** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **需要修正** | 用户帐户控制 |

之所以发生此错误，是因为服务器终结点删除失败，终结点现在处于部分删除状态。 若要解决此问题，请重试删除服务器终结点。

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**服务器终结点所在的卷的磁盘空间不足。**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT （decimal）** | -1906441711 |
| **错误字符串** | JET_errLogDiskFull |
| **需要修正** | 用户帐户控制 |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT （decimal）** | -2134375654 |
| **错误字符串** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **需要修正** | 用户帐户控制 |

之所以发生此错误，是因为卷已满。 出现此错误的原因通常是服务器终结点外的文件占用了卷上的空间。 通过添加更多的服务器终结点、将文件移动到其他卷或增加服务器终结点所在的卷的大小，释放卷上的空间。

<a id="-2134364145"></a><a id="replica-not-ready"></a>**服务尚未准备好与此服务器终结点进行同步。**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT （decimal）** | -2134364145 |
| **错误字符串** | ECS_E_REPLICA_NOT_READY |
| **需要修正** | No |

之所以发生此错误，是因为云终结点是在 Azure 文件共享中已存在内容的情况下创建的。 Azure 文件同步必须先扫描 Azure 文件共享中的所有内容，然后服务器终结点才能继续进行初始同步。

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**由于多个单独文件存在问题，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT （decimal）** | -2134375877 |
| **错误字符串** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **需要修正** | 用户帐户控制 |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT （decimal）** | -2134375908 |
| **错误字符串** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **需要修正** | 用户帐户控制 |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT （decimal）** | -2134375853 |
| **错误字符串** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **需要修正** | 用户帐户控制 |

如果有多个文件同步错误，同步会话可能会开始失败。 <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure 文件同步在服务器上每天创建一个临时 VSS 快照，以便同步具有打开句柄的文件。

<a id="-2134376423"></a>**由于服务器终结点路径出现问题，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT （decimal）** | -2134376423 |
| **错误字符串** | ECS_E_SYNC_INVALID_PATH |
| **需要修正** | 用户帐户控制 |

请确保该路径存在、位于本地 NTFS 卷上，并且不是重新分析点或现有服务器终结点。

<a id="-2134375817"></a>**同步失败，因为筛选器驱动程序版本与代理版本不兼容**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT （decimal）** | -2134375817 |
| **错误字符串** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **需要修正** | 用户帐户控制 |

之所以发生此错误，是因为加载的云分层筛选器驱动程序（Storagesync.sys）版本与存储同步代理（FileSyncSvc）服务不兼容。 如果 Azure 文件同步代理已升级，请重新启动服务器以完成安装。 如果错误继续出现，请卸载代理，重新启动服务器，然后重新安装 Azure 文件同步代理。

<a id="-2134376373"></a>**服务当前不可用。**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT （decimal）** | -2134376373 |
| **错误字符串** | ECS_E_SERVICE_UNAVAILABLE |
| **需要修正** | No |

发生此错误的原因是 Azure 文件同步服务不可用。 当 Azure 文件同步服务再次可用时，此错误将自动解决。

<a id="-2146233088"></a>**由于出现异常，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT （decimal）** | -2146233088 |
| **错误字符串** | COR_E_EXCEPTION |
| **需要修正** | No |

发生此错误的原因是由于出现异常，同步失败。 如果错误持续几个小时，请创建支持请求。

<a id="-2134364045"></a>**同步失败，因为存储帐户已故障转移到另一个区域。**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT （decimal）** | -2134364045 |
| **错误字符串** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **需要修正** | 用户帐户控制 |

发生此错误的原因是存储帐户已故障转移到另一个区域。 Azure 文件同步不支持存储帐户故障转移功能。 在 Azure 文件同步中，包含用作云终结点的 Azure 文件共享的存储帐户不应进行故障转移。 执行此操作将导致同步停止工作，并且还可能导致新分层的文件出现意外数据丢失。 若要解决此问题，请将存储帐户移到主要区域。

<a id="-2134375922"></a>**同步失败，因为同步数据库存在暂时性问题。**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT （decimal）** | -2134375922 |
| **错误字符串** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **需要修正** | No |

发生此错误的原因是同步数据库存在内部问题。 同步重试时，此错误将自动解决。 如果此错误持续延长一段时间，请创建支持请求，我们将与你联系以帮助你解决此问题。

<a id="-2134364024"></a>**由于 Azure Active Directory 租户中的更改，同步失败**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT （decimal）** | -2134364024 | 
| **错误字符串** | ECS_E_INVALID_AAD_TENANT |
| **需要修正** | 用户帐户控制 |

之所以发生此错误，是因为 Azure 文件同步当前不支持将订阅移到其他 Azure Active Directory 租户。
 
若要解决此问题，请执行下列选项之一：

- **选项1（推荐）** ：将订阅移回原始 Azure Active Directory 租户
- **选项 2**：删除并重新创建当前同步组。 如果已在服务器终结点上启用云分层，请删除该同步组，然后执行在重新创建同步组之前删除孤立分层文件的[云分层部分]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)中所述的步骤。 

<a id="-2134364010"></a>**由于防火墙和虚拟网络例外，同步失败-未配置**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT （decimal）** | -2134364010 | 
| **错误字符串** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **需要修正** | 用户帐户控制 |

如果在存储帐户上启用了防火墙和虚拟网络设置，但未选中 "允许受信任的 Microsoft 服务访问此存储帐户" 异常，则会出现此错误。 若要解决此问题，请遵循部署指南中的[配置防火墙和虚拟网络设置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)部分中所述的步骤。

<a id="-2147024891"></a>**由于系统卷信息文件夹上的权限不正确，同步失败。**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT （decimal）** | -2147024891 |
| **错误字符串** | ERROR_ACCESS_DENIED |
| **需要修正** | 用户帐户控制 |

如果 NT AUTHORITY\SYSTEM 帐户对服务器终结点所在的卷上的系统卷信息文件夹没有权限，则会发生此错误。 请注意，如果单个文件无法与 ERROR_ACCESS_DENIED 同步，请执行 "[每个文件/目录同步错误疑难解答](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors)" 一节中所述的步骤。

若要解决此问题，请执行以下步骤：

1. 下载[Psexec](https://docs.microsoft.com/sysinternals/downloads/psexec)工具。
2. 在提升的命令提示符下运行以下命令，以使用系统帐户启动命令提示符： **PsExec-i-s-d cmd** 
3. 在系统帐户下运行的命令提示符下，运行以下命令以确认 NT AUTHORITY\SYSTEM 帐户无权访问系统卷信息文件夹： **cacls "驱动器号： \ 系统卷信息"/T/c**
4. 如果 NT AUTHORITY\SYSTEM 帐户没有对系统卷信息文件夹的访问权限，请运行以下命令： **cacls "drive #： \ SYSTEM Volume Information"/T/E/g "NT AUTHORITY\SYSTEM： F"**
    - 如果步骤 #4 由于拒绝访问而失败，请运行以下命令获取系统卷信息文件夹的所有权，然后重复步骤 #4： **takeown/A/R/f "drive： \ 系统卷信息"**

<a id="-2134375810"></a>**同步失败，因为已删除并重新创建 Azure 文件共享。**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT （decimal）** | -2134375810 |
| **错误字符串** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **需要修正** | 用户帐户控制 |

之所以发生此错误，是因为 Azure 文件同步不支持在同一个同步组中删除并重新创建 Azure 文件共享。 

若要解决此问题，请通过执行以下步骤来删除并重新创建同步组：

1. 删除同步组中的所有服务器终结点。
2. 删除云终结点。 
3. 删除同步组。
4. 如果已在服务器终结点上启用云分层，则可以通过执行 "[删除服务器终结点后无法在服务器上访问](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)" 一节中所述的步骤，在服务器上删除孤立的分层文件。
5. 重新创建同步组。

<a id="-2145844941"></a>**同步失败，因为已重定向 HTTP 请求**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT （decimal）** | -2145844941 |
| **错误字符串** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **需要修正** | 用户帐户控制 |

发生此错误的原因是 Azure 文件同步不支持 HTTP 重定向（3xx 状态代码）。 若要解决此问题，请在代理服务器或网络设备上禁用 HTTP 重定向。

<a id="-2134364027"></a>**脱机数据传输期间发生超时，但仍在进行中。**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT （decimal）** | -2134364027 |
| **错误字符串** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **需要修正** | No |

当数据引入操作超过超时值时，会出现此错误。 如果正在进行同步，则可以忽略此错误（AppliedItemCount 大于0）。 请参阅[如何实现监视当前同步会话的进度？](#how-do-i-monitor-the-progress-of-a-current-sync-session)。

### <a name="common-troubleshooting-steps"></a>常见的故障排除步骤
<a id="troubleshoot-storage-account"></a>**验证存储帐户是否存在。**  
# <a name="portaltabazure-portal"></a>[端口](#tab/azure-portal)
1. 导航到存储同步服务中的同步组。
2. 选择同步组中的云终结点。
3. 请注意打开的窗格中的 Azure 文件共享名称。
4. 选择 "链接的存储帐户"。 如果此链接失败，则引用的存储帐户已被删除。
    ![显示 "云终结点详细信息" 窗格的屏幕截图，其中包含指向存储帐户的链接。](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**确保 Azure 文件共享存在。**  
# <a name="portaltabazure-portal"></a>[端口](#tab/azure-portal)
1. 单击左侧目录中的 "**概述**"，返回到 "主存储帐户" 页。
2. 选择 "**文件**" 以查看文件共享列表。
3. 验证云终结点引用的文件共享出现在文件共享列表中（在上面的步骤1中应记下此项）。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**确保 Azure 文件同步有权访问存储帐户。**  
# <a name="portaltabazure-portal"></a>[端口](#tab/azure-portal)
1. 单击左侧目录中的 "**访问控制（IAM）** "。
1. 单击 "**角色分配**" 选项卡，列出有权访问你的存储帐户的用户和应用程序（*服务主体*）。
1. 使用 "读取者"**和 "数据访问**" 角色验证**混合文件同步服务**是否出现在列表中。 

    ![存储帐户的 "访问控制" 选项卡中混合文件同步服务主体的屏幕截图](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    如果未在列表中显示**混合文件同步服务**，请执行以下步骤：

    - 单击 "**添加**"。
    - 在 "**角色**" 字段中，选择 "**读取器和数据访问**"。
    - 在 "**选择**" 字段中，键入**混合文件同步服务**，选择角色，然后单击 "**保存**"。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>如何实现阻止用户在服务器上创建包含不支持的字符的文件？
您可以使用[文件服务器资源管理器（FSRM）文件屏蔽](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management)来阻止其名称中不支持的字符在服务器上创建的文件。 你可能需要使用 PowerShell 来执行此操作，因为大多数不支持的字符不可打印，因此你需要将它们的十六进制表示形式首先转换为个字符。

首先，使用[FsrmFileGroup cmdlet](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup)创建 FSRM 文件组。 此示例将组定义为仅包含两个不受支持的字符，但你可以根据需要在文件组中包含任意多个字符。

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

定义 FSRM 文件组后，可以使用 FsrmFileScreen cmdlet 创建 FSRM 文件屏蔽。

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> 请注意，文件屏蔽应仅用于阻止创建 Azure 文件同步不支持的字符。如果在其他情况下使用文件屏蔽，则同步将继续尝试将文件从 Azure 文件共享下载到服务器，并将因文件屏蔽而被阻止，导致数据传出较高。 

## <a name="cloud-tiering"></a>云分层 
云分层中的故障有两个路径：

- 文件可能无法分层，这意味着 Azure 文件同步尝试将文件分层到 Azure 文件时失败。
- 文件可能无法撤回，这意味着，当用户尝试访问已分层的文件时，Azure 文件同步文件系统筛选器（Storagesync.sys）无法下载数据。

可以通过以下两种故障路径来实现故障的主要类别：

- 云存储故障
    - *暂时性存储服务可用性问题*。 有关详细信息，请参阅[Azure 存储的服务级别协议（SLA）](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)。
    - *无法访问 Azure 文件共享*。 如果 Azure 文件共享仍为同步组中的云终结点，则通常会发生此故障。
    - *无法访问存储帐户*。 当你删除存储帐户时，通常会发生此故障，因为该帐户的 Azure 文件共享是同步组中的云终结点。 
- 服务器故障 
  - *未加载 Azure 文件同步文件系统筛选器（storagesync.sys）* 。 为了响应分层/召回请求，必须加载 Azure 文件同步文件系统筛选器。 无法加载筛选器的原因有多种，但最常见的原因是管理员手动卸载了该筛选器。 必须始终加载 Azure 文件同步文件系统筛选器，才能正常运行 Azure 文件同步。
  - *缺少或损坏的重新分析点*。 重新分析点是文件中由两部分组成的特殊数据结构：
    1. 重新分析标记，向操作系统指示 Azure 文件同步文件系统筛选器（Storagesync.sys）可能需要对文件执行 IO 操作。 
    2. 重新分析数据，指示文件系统筛选关联的云终结点（Azure 文件共享）上文件的 URI。 
        
       重新分析点可能会损坏的最常见方法是：管理员尝试修改标记或其数据。 
  - *网络连接问题*。 为了对文件进行分层或撤回，服务器必须具有 internet 连接。

以下部分说明如何排查云分层问题，并确定问题是云存储问题还是服务器问题。

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>如何监视服务器上的分层活动  
若要监视服务器上的分层活动，请使用遥测事件日志中的事件 ID 9003、9016和9029（位于事件查看器中的 "应用程序" 和 "Services\Microsoft\FileSync\Agent" 下）。

- 事件 ID 9003 提供服务器终结点的错误分布。 例如，"错误总数"、"错误代码" 等。请注意，每个错误代码将记录一个事件。
- 事件 ID 9016 提供卷的幻像结果。 例如，可用空间百分比是：会话中幻像的文件数、失败的文件数，等等。
- 事件 ID 9029 提供服务器终结点的幻像会话信息。 例如，会话中尝试的文件数、会话中分层的文件数、已分层的文件数，等等。

### <a name="how-to-monitor-recall-activity-on-a-server"></a>如何监视服务器上的撤回活动
若要监视服务器上的撤回活动，请使用遥测事件日志中的事件 ID 9005、9006、9009和9059（位于事件查看器中的 "应用程序" 和 "Services\Microsoft\FileSync\Agent" 下）。

- 事件 ID 9005 为服务器终结点提供召回可靠性。 例如，访问的唯一文件总数、访问失败的唯一文件总数等。
- 事件 ID 9006 为服务器终结点提供撤回错误分布。 例如，失败的请求总数、错误代码，等等。请注意，每个错误代码将记录一个事件。
- 事件 ID 9009 提供服务器终结点的撤回会话信息。 例如，DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed 等。
- 事件 ID 9059 提供服务器终结点的应用程序回调分布。 例如，"ShareId"、"Application Name" 和 "TotalEgressNetworkBytes"。

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>如何对未能分层的文件进行故障排除
如果文件无法分层到 Azure 文件：

1. 在事件查看器中，查看 "应用程序和 Services\microsoft\filesync\agent" 下的遥测、操作和诊断事件日志。 
   1. 验证文件是否存在于 Azure 文件共享中。

      > [!NOTE]
      > 必须先将文件同步到 Azure 文件共享，然后才能对其进行分层。

   2. 验证服务器是否具有 internet 连接。 
   3. 验证 Azure 文件同步筛选器驱动程序（Storagesync.sys 和 Storagesyncguard.sys）是否正在运行：
       - 在提升的命令提示符下，运行 `fltmc`。 验证是否列出了 Storagesync.sys 和 Storagesyncguard.sys 文件系统筛选器驱动程序。

> [!NOTE]
> 如果文件无法进行层级（每个错误代码记录一个事件），则会在遥测事件日志中一小时记录一次事件 ID 9003。 请查看[分层错误和修正](#tiering-errors-and-remediation)部分，以查看是否列出了更正步骤来查找错误代码。

### <a name="tiering-errors-and-remediation"></a>分层错误和修正

| HRESULT | HRESULT （decimal） | 错误字符串 | 问题 | 修正 |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | 文件无法进行层级，因为它正在使用中。 | 无需任何操作。 当文件不再使用时，将对其进行分层。 |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | 文件无法进行层级，因为它已被同步排除。 | 无需任何操作。 同步排除列表中的文件不能分层。 |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | 由于在服务器上找不到该文件，因此无法将其分层。 | 无需任何操作。 如果错误仍然存在，请检查服务器上是否存在该文件。 |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | 由于在 Azure 文件共享中删除了该文件，因此无法将其分层。 | 无需任何操作。 当下一个下载同步会话运行时，应在服务器上删除该文件。 |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | 由于网络问题，文件无法进行层级。 | 无需任何操作。 如果错误仍然存在，请检查与 Azure 文件共享之间的网络连接。 |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | 由于网络问题，文件无法进行层级。 | 无需任何操作。 如果错误仍然存在，请检查与 Azure 文件共享之间的网络连接。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 由于访问被拒绝错误，文件失败。 如果文件位于 DFS R 只读复制文件夹中，则会发生此错误。 | Azure 文件同步不支持 DFS 只读复制文件夹中的服务器终结点。 有关详细信息，请参阅[规划指南](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs)。 |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | 由于网络问题，文件无法进行层级。 | 无需任何操作。 如果错误仍然存在，请检查与 Azure 文件共享之间的网络连接。 |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | 文件无法进行层级，因为文件大小小于支持的大小。 | 如果代理版本低于9.0，则支持的最小文件大小为64kb。 如果代理版本为9.0 和更高版本，则支持的最小文件大小取决于文件系统群集大小（双文件系统群集大小）。 例如，如果文件系统群集大小为4kb，则最小文件大小为8kb。 |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | 由于 Azure 存储问题，文件未能分层。 | 如果错误仍然存在，请打开支持请求。 |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | 由于此文件是同时回调的，因此无法将其分层。 | 无需任何操作。 撤回完成并且文件不再使用时，将会对文件进行分层。 |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | 文件无法进行分层，因为它尚未同步到 Azure 文件共享。 | 无需任何操作。 该文件在同步到 Azure 文件共享后将层级。 |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | 由于云分层筛选器驱动程序（storagesync.sys）未运行，导致文件无法分层。 | 若要解决此问题，请打开提升的命令提示符，并运行以下命令： `fltmc load storagesync`<br>如果在运行 fltmc 命令时无法加载 storagesync.sys 筛选器驱动程序，请卸载 Azure 文件同步代理，重新启动服务器，然后重新安装 Azure 文件同步代理。 |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 由于服务器终结点所在的卷上的磁盘空间不足，导致文件无法分层。 | 若要解决此问题，请在服务器终结点所在的卷上至少释放 100 MB 的磁盘空间。 |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | 文件无法进行分层，因为它尚未同步到 Azure 文件共享。 | 无需任何操作。 该文件在同步到 Azure 文件共享后将层级。 |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | 文件无法进行层级，因为它是不受支持的重新分析点。 | 如果该文件是重复数据删除重新分析点，请按照[规划指南](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication)中的步骤启用重复数据删除支持。 不支持重分析点为重复数据删除的文件，也不会对其进行分层。  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | 文件无法进行层级，因为已对其进行了修改。 | 无需任何操作。 当修改后的文件同步到 Azure 文件共享时，文件将进行层级。 |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | 文件无法进行分层，因为它尚未同步到 Azure 文件共享。 | 无需任何操作。 该文件在同步到 Azure 文件共享后将层级。 |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | 由于网络问题，文件无法进行层级。 | 无需任何操作。 如果错误仍然存在，请检查与 Azure 文件共享之间的网络连接。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 文件无法进行层级，因为已对其进行了修改。 | 无需任何操作。 当修改后的文件同步到 Azure 文件共享时，文件将进行层级。 |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 由于系统资源不足，文件无法进行层级。 | 如果错误仍然存在，请调查哪些应用程序或内核模式驱动程序耗尽系统资源。 |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>如何对未能召回的文件进行故障排除  
如果文件无法撤回：
1. 在事件查看器中，查看 "应用程序和 Services\microsoft\filesync\agent" 下的遥测、操作和诊断事件日志。
    1. 验证文件是否存在于 Azure 文件共享中。
    2. 验证服务器是否具有 internet 连接。 
    3. 打开服务 MMC 管理单元，然后验证存储同步代理服务（FileSyncSvc）是否正在运行。
    4. 验证 Azure 文件同步筛选器驱动程序（Storagesync.sys 和 Storagesyncguard.sys）是否正在运行：
        - 在提升的命令提示符下，运行 `fltmc`。 验证是否列出了 Storagesync.sys 和 Storagesyncguard.sys 文件系统筛选器驱动程序。

> [!NOTE]
> 如果文件无法撤回（每个错误代码记录一个事件），则会在遥测事件日志中每小时记录一次事件 ID 9006。 检查[撤回错误和修正](#recall-errors-and-remediation)部分，以查看是否为错误代码列出了更正步骤。

### <a name="recall-errors-and-remediation"></a>撤回错误和修正

| HRESULT | HRESULT （decimal） | 错误字符串 | 问题 | 修正 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | 由于 i/o 超时，文件无法撤回。 此问题可能是由以下几个原因导致的：服务器资源约束、网络连接差或 Azure 存储问题（例如，限制）。 | 无需任何操作。 如果错误持续几个小时，请打开支持案例。 |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | 由于网络问题，文件无法撤回。  | 如果错误仍然存在，请检查与 Azure 文件共享之间的网络连接。 |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | 由于已删除服务器终结点，因此文件无法撤回。 | 若要解决此问题，请参阅[删除服务器终结点后，服务器上的分层文件无法访问](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 由于访问被拒绝错误，文件无法撤回。 如果存储帐户上的防火墙和虚拟网络设置已启用并且服务器无权访问存储帐户，则可能出现此问题。 | 若要解决此问题，请遵循部署指南中的[配置防火墙和虚拟网络设置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)部分中所述的步骤来添加服务器 IP 地址或虚拟网络。 |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | 由于在 Azure 文件共享中无法访问该文件，因此该文件无法撤回。 | 若要解决此问题，请验证文件是否存在于 Azure 文件共享中。 如果文件存在于 Azure 文件共享中，请升级到最新的 Azure 文件同步[代理版本](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)。 |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | 由于对存储帐户的授权失败，文件无法撤回。 | 若要解决此问题，请验证[Azure 文件同步是否有权访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac)。 |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | 由于无法访问 Azure 文件共享，该文件未能撤回。 | 验证文件共享存在并且可访问。 如果删除并重新创建了文件共享，请执行同步失败中所述的步骤，[因为已删除并重新创建 Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810)部分以删除并重新创建同步组。 |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 由于系统资源不足，文件无法撤回。 | 如果错误仍然存在，请调查哪些应用程序或内核模式驱动程序耗尽系统资源。 |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | 由于 insuffcient 内存，文件无法重新调用。 | 如果错误仍然存在，请调查是哪个应用程序或内核模式驱动程序导致内存不足的情况。 |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 由于磁盘空间不足，文件无法撤回。 | 若要解决此问题，请通过将文件移动到不同的卷，增加卷的大小，或者使用 StorageSyncCloudTiering cmdlet 将文件强制转换为级别，从而释放卷上的空间。 |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>删除服务器终结点后，无法在服务器上访问分层文件
如果在删除服务器终结点之前未重新调用这些文件，则服务器上的分层文件将无法访问。

如果分层文件不可访问，则记录错误
- 同步文件时，错误代码-2147942467 （ERROR_BAD_NET_NAME 0x80070043）记录在 ItemResults 事件日志中
- 撤回文件时，错误代码-2134376393 （ECS_E_SYNC_SHARE_NOT_FOUND 0x80c80037）记录在 RecallResults 事件日志中

如果满足以下条件，则可以还原对分层文件的访问权限：
- 已在过去30天内删除服务器终结点
- 未删除云终结点 
- 未删除文件共享
- 未删除同步组

如果满足上述条件，则可以通过在同一同步组中的同一同步组内的同一路径上重新创建服务器终结点来还原对服务器上文件的访问权限。 

如果未满足上述条件，则无法还原访问，因为服务器上的这些分层文件现在已孤立。 请按照下面的说明删除孤立的分层文件。

**说明**
- 当分层文件在服务器上不可访问时，如果直接访问 Azure 文件共享，则完整文件应仍可访问。
- 若要防止将来使用孤立的分层文件，请按照删除服务器终结点时[删除服务器终结](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)点中所述的步骤进行操作。

<a id="get-orphaned"></a>**如何获取孤立分层文件的列表** 

1. 验证是否安装了 Azure 文件同步 agent 版本5.1 或更高版本。
2. 运行以下 PowerShell 命令以列出孤立的分层文件：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. 保存 OrphanTieredFiles 输出文件，以防文件在删除后需要从备份还原。

<a id="remove-orphaned"></a>**如何删除孤立的分层文件** 

*选项1：删除孤立的分层文件*

此选项将删除 Windows Server 上的孤立分层文件，但需要删除服务器终结点（如果该终结点由于30天后重新使用而存在），或者连接到不同的同步组。 如果在重新创建服务器终结点之前在 Windows Server 或 Azure 文件共享上更新了文件，则会发生文件冲突。

1. 验证是否安装了 Azure 文件同步 agent 版本5.1 或更高版本。
2. 备份 Azure 文件共享和服务器终结点位置。
3. 按照[删除服务器终结点](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)中所述的步骤操作，在同步组中删除服务器终结点（如果存在）。

> [!Warning]  
> 如果在使用 StorageSyncOrphanedTieredFiles cmdlet 之前未删除服务器终结点，则在服务器上删除孤立的分层文件将删除 Azure 文件共享中的完整文件。 

4. 运行以下 PowerShell 命令以列出孤立的分层文件：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. 保存 OrphanTieredFiles 输出文件，以防文件在删除后需要从备份还原。
6. 运行以下 PowerShell 命令以删除孤立的分层文件：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**说明** 
- 在未同步到 Azure 文件共享的服务器上修改的分层文件将被删除。
- 可访问（而不是孤立）的分层文件将不会被删除。
- 非分层文件将保留在服务器上。

7. 可选：如果在步骤3中删除服务器终结点，请重新创建该终结点。

*选项2：装载 Azure 文件共享并将本地文件复制到服务器上的孤立文件*

此选项不需要删除服务器终结点，但需要足够的磁盘空间来本地复制完整文件。

1. 在具有孤立分层文件的 Windows 服务器上[装载](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)Azure 文件共享。
2. 运行以下 PowerShell 命令以列出孤立的分层文件：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. 使用 OrphanTieredFiles 输出文件标识服务器上的孤立分层文件。
4. 通过将完整文件从 Azure 文件共享复制到 Windows Server 来覆盖孤立的分层文件。

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>如何对服务器上意外召回的文件进行故障排除  
防病毒软件、备份和读取大量文件的其他应用程序会导致意外的撤回，除非它们遵循 skip 脱机属性并跳过读取这些文件的内容。 对于支持此选项的产品，跳过脱机文件可帮助避免在执行防病毒扫描或备份作业时出现意外的召回。

请咨询软件供应商，了解如何配置其解决方案以跳过读取脱机文件。

在其他情况下（如在文件资源管理器中浏览文件时）也可能发生意外的撤回。 在服务器上的文件资源管理器中打开包含云分层文件的文件夹可能会导致意外的撤回。 如果在服务器上启用了防病毒解决方案，则更有可能出现这种情况。

> [!NOTE]
>使用遥测事件日志中的事件 ID 9059 来确定哪些应用程序导致了撤回。 此事件提供了服务器终结点的应用程序回调分发，并且每小时记录一次。

## <a name="general-troubleshooting"></a>常规故障排除
如果在服务器上遇到与 Azure 文件同步有关的问题，请先完成以下步骤：
1. 在事件查看器中，查看遥测、操作和诊断事件日志。
    - 同步、分层和召回问题记录在应用程序和 Services\microsoft\filesync\agent 下的遥测、诊断和操作事件日志中。
    - 与管理服务器相关的问题（例如，配置设置）记录在应用程序和 Services\Microsoft\FileSync\Management. 下的操作和诊断事件日志中。
2. 验证服务器上是否正在运行 Azure 文件同步服务：
    - 打开服务 MMC 管理单元，并验证存储同步代理服务（FileSyncSvc）是否正在运行。
3. 验证 Azure 文件同步筛选器驱动程序（Storagesync.sys 和 Storagesyncguard.sys）是否正在运行：
    - 在提升的命令提示符下，运行 `fltmc`。 验证是否列出了 Storagesync.sys 和 Storagesyncguard.sys 文件系统筛选器驱动程序。

如果问题未得到解决，请运行 AFSDiag 工具：
1. 创建将在其中保存 AFSDiag 输出的目录（例如，C:\Output）。
    > [!NOTE]
    >AFSDiag 会在收集日志之前删除输出目录中的所有内容。 指定不包含数据的输出位置。
2. 打开提升的 PowerShell 窗口，并运行以下命令（在每个命令后面按 Enter）：

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. 对于 "Azure 文件同步内核模式" 跟踪级别，输入**1** （除非另有指定，以创建更详细的跟踪），然后按 enter。
4. 对于 Azure 文件同步用户模式跟踪级别，输入**1** （除非另有指定，以创建更详细的跟踪），然后按 enter。
5. 再现问题。 完成后，输入**D**。
6. 包含日志和跟踪文件的 .zip 文件将保存到指定的输出目录中。

## <a name="see-also"></a>另请参阅
- [监视 Azure 文件同步](storage-sync-files-monitoring.md)
- [Azure 文件常见问题](storage-files-faq.md)
- [排查 Windows 中的 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)
- [排查 Linux 中的 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)
