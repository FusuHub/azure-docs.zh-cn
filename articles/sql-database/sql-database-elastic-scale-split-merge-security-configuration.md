---
title: 拆分/合并安全配置
description: 使用拆分/合并服务设置用于加密的 x409 证书以实现弹性缩放。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a916645f153f73a98e7fc5d4046bdf557e8acf2b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823528"
---
# <a name="split-merge-security-configuration"></a>拆分/合并安全配置

若要使用拆分/合并服务，必须正确配置安全性。 该服务是 Microsoft Azure SQL 数据库弹性扩展功能的一部分。 有关详细信息，请参阅[弹性缩放拆分和合并服务教程](sql-database-elastic-scale-configure-deploy-split-and-merge.md)。

## <a name="configuring-certificates"></a>配置证书

通过两种方式配置证书。 

1. [配置 SSL 证书](#to-configure-the-ssl-certificate)
2. [配置客户端证书](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>获取证书

可从公共证书颁发机构 (CA) 或 [Windows 证书服务](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx)中获取证书。 这些方法是获取证书的首选方法。

如果这些选项不可用，可以生成**自签名证书**。

## <a name="tools-to-generate-certificates"></a>用于生成证书的工具

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>运行工具

* 有关适用于 Visual Studio 的开发人员命令提示符，请参阅 [Visual Studio 命令提示符](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    如果已安装工具，请转到：
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* 从 [Windows 8.1：下载工具包和工具](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)获取 WDK

## <a name="to-configure-the-ssl-certificate"></a>配置 SSL 证书

若要对通信进行加密并对服务器进行身份验证，需要使用 SSL 证书。 从下面的三个方案中选择最适合的方案，并执行其所有步骤：

### <a name="create-a-new-self-signed-certificate"></a>创建新的自签名证书

1. [创建自签名证书](#create-a-self-signed-certificate)
2. [为自签名 SSL 证书创建 PFX 文件](#create-pfx-file-for-self-signed-ssl-certificate)
3. [将 SSL 证书上传到云服务](#upload-ssl-certificate-to-cloud-service)
4. [在服务配置文件中更新 SSL 证书](#update-ssl-certificate-in-service-configuration-file)
5. [导入 SSL 证书颁发机构](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>使用证书存储中的现有证书
1. [从证书存储中导出 SSL 证书](#export-ssl-certificate-from-certificate-store)
2. [将 SSL 证书上传到云服务](#upload-ssl-certificate-to-cloud-service)
3. [在服务配置文件中更新 SSL 证书](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>在 PFX 文件中使用现有证书
1. [将 SSL 证书上传到云服务](#upload-ssl-certificate-to-cloud-service)
2. [在服务配置文件中更新 SSL 证书](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>配置客户端证书
若要对服务请求进行身份验证，需要使用客户端证书。 从下面的三个方案中选择最适合的方案，并执行其所有步骤：

### <a name="turn-off-client-certificates"></a>禁用客户端证书
1. [禁用基于客户端证书的身份验证](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>颁发新的自签名客户端证书
1. [创建自签名证书颁发机构](#create-a-self-signed-certification-authority)
2. [将 CA 证书上传到云服务](#upload-ca-certificate-to-cloud-service)
3. [在服务配置文件中更新 CA 证书](#update-ca-certificate-in-service-configuration-file)
4. [颁发客户端证书](#issue-client-certificates)
5. [为客户端证书创建 PFX 文件](#create-pfx-files-for-client-certificates)
6. [导入客户端证书](#import-client-certificate)
7. [复制客户端证书指纹](#copy-client-certificate-thumbprints)
8. [在服务配置文件中配置允许的客户端](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>使用现有客户端证书
1. [查找 CA 公钥](#find-ca-public-key)
2. [将 CA 证书上传到云服务](#upload-ca-certificate-to-cloud-service)
3. [在服务配置文件中更新 CA 证书](#update-ca-certificate-in-service-configuration-file)
4. [复制客户端证书指纹](#copy-client-certificate-thumbprints)
5. [在服务配置文件中配置允许的客户端](#configure-allowed-clients-in-the-service-configuration-file)
6. [配置客户端证书吊销检查](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>允许的 IP 地址
可将对服务终结点的访问限制为特定范围的 IP 地址。

## <a name="to-configure-encryption-for-the-store"></a>为存储配置加密
若要加密存储在元数据存储中的凭据，需要使用证书。 从下面的三个方案中选择最适合的方案，并执行其所有步骤：

### <a name="use-a-new-self-signed-certificate"></a>使用新的自签名证书
1. [创建自签名证书](#create-a-self-signed-certificate)
2. [为自签名加密证书创建 PFX 文件](#create-pfx-file-for-self-signed-ssl-certificate)
3. [将加密证书上传到云服务](#upload-encryption-certificate-to-cloud-service)
4. [在服务配置文件中更新加密证书](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>使用证书存储中的现有证书
1. [从证书存储中导出加密证书](#export-encryption-certificate-from-certificate-store)
2. [将加密证书上传到云服务](#upload-encryption-certificate-to-cloud-service)
3. [在服务配置文件中更新加密证书](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>在 PFX 文件中使用现有证书
1. [将加密证书上传到云服务](#upload-encryption-certificate-to-cloud-service)
2. [在服务配置文件中更新加密证书](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>默认配置
默认配置拒绝对 HTTP 终结点的所有访问。 这是推荐的设置，因为对这些终结点的请求可能包含敏感信息，如数据库凭据。
默认配置允许对 HTTPS 终结点的所有访问。 可能会进一步限制此设置。

### <a name="changing-the-configuration"></a>更改配置
所应用的访问控制规则的组和终结点是在**服务配置文件\<的** **EndpointAcls>** 部分中配置的。

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

访问控制组中的规则是在服务配置文件的 \<AccessControl name=""> 部分中配置的。 

在网络访问控制列表文档中对格式进行了说明。
例如，要仅允许范围 100.100.0.0 到 100.100.255.255 中的 IP 访问 HTTPS 终结点，规则将如下所示：

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>防止拒绝服务
可使用两种受支持的不同机制检测和防止拒绝服务攻击：

* 限制每台远程主机的并发请求数（默认为禁用）
* 限制每台远程主机的访问率（默认为启用）

这些机制还基于在 IIS 的动态 IP 安全中记录的功能。 更改此配置时，请注意以下因素：

* 通过远程主机信息的代理和网络地址转换设备的行为
* 考虑对 Web 角色中任何资源的每个请求（例如，加载脚本、图像等）

## <a name="restricting-number-of-concurrent-accesses"></a>限制并发访问数
配置此行为的设置如下：

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

将 DynamicIpRestrictionDenyByConcurrentRequests 更改为 true 以启用此保护。

## <a name="restricting-rate-of-access"></a>限制访问率
配置此行为的设置如下：

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>配置对拒绝请求的响应
以下设置将配置对拒绝请求的响应：

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

有关其他受支持的值，请参考 IIS 中动态 IP 安全文档。

## <a name="operations-for-configuring-service-certificates"></a>用于配置服务证书的操作
本主题仅供参考。 请遵循以下部分中概括的配置步骤：

* 配置 SSL 证书
* 配置客户端证书

## <a name="create-a-self-signed-certificate"></a>创建自签名证书
执行：

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

自定义：

* -n，带有服务 URL。 通配符 ("CN=*.cloudapp.net") 和替代名称 ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") 均受支持。
* -e，带有证书过期日期创建强密码并在提示时指定它。

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>为自签名 SSL 证书创建 PFX 文件
执行：

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

输入密码，并使用以下选项导出证书：

* 是，导出私钥
* 导出所有扩展属性

## <a name="export-ssl-certificate-from-certificate-store"></a>从证书存储中导出 SSL 证书
* 查找证书
* 依次单击“操作”->“所有任务”->“导出...”
* 使用以下选项将证书导出到 .PFX 文件中：
  * 是，导出私钥
  * 包括证书路径中的所有证书（如果可能）*导出所有扩展属性

## <a name="upload-ssl-certificate-to-cloud-service"></a>将 SSL 证书上传到云服务
使用带有 SSL 密钥对的现有或生成的 .PFX 文件上传证书：

* 输入用于保护私钥信息的密码

## <a name="update-ssl-certificate-in-service-configuration-file"></a>在服务配置文件中更新 SSL 证书
在服务配置文件中，使用已上传到云服务的证书指纹更新以下设置的指纹值：

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>导入 SSL 证书颁发机构
在将与该服务通信的所有帐户/计算机中，按照以下步骤进行操作：

* 在 Windows 资源管理器中，双击 .CER 文件
* 在“证书”对话框中，单击“安装证书...”
* 将证书导入到“受信任的根证书颁发机构”存储中

## <a name="turn-off-client-certificate-based-authentication"></a>禁用基于客户端证书的身份验证
仅支持基于客户端证书的身份验证，禁用它即可公开访问服务终结点，除非使用了其他机制（例如 Microsoft Azure 虚拟网络）。

在服务配置文件中，将这些设置更改为 false 以关闭该功能：

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

然后，复制与 CA 证书设置中 SSL 证书相同的指纹：

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>创建自签名证书颁发机构
执行以下步骤来创建自签名证书，以充当证书颁发机构：

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

对其进行自定义

* -e，带有证书到期日期

## <a name="find-ca-public-key"></a>查找 CA 公钥
所有客户端证书都必须由服务信任的证书颁发机构颁发。 为了将证书上传到云服务，需要查找颁发了客户端证书（用于身份验证）的证书颁发机构提供的公钥。

如果具有公钥的文件不可用，则将其从证书存储中导出：

* 查找证书
  * 搜索同一证书颁发机构颁发的客户端证书
* 双击该证书。
* 在“证书”对话框中选择“证书路径”选项卡。
* 双击路径中的 CA 条目。
* 记下证书属性。
* 关闭“证书”对话框。
* 查找证书
  * 搜索前面记下的 CA。
* 依次单击“操作”->“所有任务”->“导出...”
* 使用以下选项将证书导出到 .CER 中：
  * “否，不导出私钥”
  * 包括证书路径中的所有证书（如果可能）。
  * 导出所有扩展属性。

## <a name="upload-ca-certificate-to-cloud-service"></a>将 CA 证书上传到云服务
使用带有 CA 公钥的现有或生成的 .CER 文件上传证书。

## <a name="update-ca-certificate-in-service-configuration-file"></a>在服务配置文件中更新 CA 证书
在服务配置文件中，使用已上传到云服务的证书指纹更新以下设置的指纹值：

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

使用同一指纹更新以下设置的值：

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>颁发客户端证书
授予了访问服务权限的每个用户都应具有一个颁发的客户端证书供其独占使用，并且应选择自己的强密码来保护其私钥。 

必须在生成和存储了自签名 CA 证书的同一计算机上执行以下步骤：

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

自定义：

* -n，带有将使用此证书进行身份验证的客户端的 ID
* -e，带有证书过期日期
* MyID.pvk 和 MyID.cer，带有用于此客户端证书的唯一文件名

此命令会提示创建密码，然后使用一次该密码。 使用强密码。

## <a name="create-pfx-files-for-client-certificates"></a>为客户端证书创建 PFX 文件
针对每个生成的客户端证书，执行：

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

自定义：

    MyID.pvk and MyID.cer with the filename for the client certificate

输入密码，并使用以下选项导出证书：

* 是，导出私钥
* 导出所有扩展属性
* 将向其颁发此证书的单个用户应选择导出密码

## <a name="import-client-certificate"></a>导入客户端证书
为其颁发了客户端证书的每个用户都应将密钥对导入到用于与服务通信的计算机中：

* 在 Windows 资源管理器中，双击 .PFX 文件
* 至少使用以下选项将证书导入到个人存储中：
  * 包括选中的所有扩展属性

## <a name="copy-client-certificate-thumbprints"></a>复制客户端证书指纹
每个已颁发客户端证书的用户都必须遵循以下步骤，才能获取将添加到服务配置文件的证书的指纹：

* 运行 certmgr.exe
* 选择“个人”选项卡
* 双击要用于身份验证的客户端证书
* 在打开的“证书”对话框中，选择“详细信息”选项卡
* 确保“显示”可显示全部内容
* 选择列表中名为“Thumbprint”的字段
* 复制指纹的值
  * 删除第一个数字前的不可见 Unicode 字符
  * 删除所有空格

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>在服务配置文件中配置允许的客户端
在服务配置文件中，使用以逗号分隔的客户端证书（允许访问服务）的指纹列表更新以下设置的值：

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>配置客户端证书吊销检查
默认设置不会通过证书颁发机构检查客户端证书吊销状态。 若要启用检查，请在颁发了客户端证书的证书颁发机构支持此类检查时，使用在 X509RevocationMode 枚举中定义的值之一更改以下设置：

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>为自签名加密证书创建 PFX 文件
对于加密证书，请执行：

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

自定义：

    MyID.pvk and MyID.cer with the filename for the encryption certificate

输入密码，并使用以下选项导出证书：

* 是，导出私钥
* 导出所有扩展属性
* 将证书上传到云服务时，需要密码。

## <a name="export-encryption-certificate-from-certificate-store"></a>从证书存储中导出加密证书
* 查找证书
* 依次单击“操作”->“所有任务”->“导出...”
* 使用以下选项将证书导出到 .PFX 文件中： 
  * 是，导出私钥
  * 包括证书路径中的所有证书（如果可能） 
* 导出所有扩展属性

## <a name="upload-encryption-certificate-to-cloud-service"></a>将加密证书上传到云服务
使用带有加密密钥对的现有或生成的 .PFX 文件上传证书：

* 输入用于保护私钥信息的密码

## <a name="update-encryption-certificate-in-service-configuration-file"></a>在服务配置文件中更新加密证书
在服务配置文件中，使用已上传到云服务的证书指纹更新以下设置的指纹值：

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>公用证书操作
* 配置 SSL 证书
* 配置客户端证书

## <a name="find-certificate"></a>查找证书
执行以下步骤:

1. 运行 mmc.exe。
2. “文件”->“添加/删除管理单元...”
3. 选择“证书”。
4. 单击“添加”。
5. 选择证书存储位置。
6. 单击“完成”。
7. 单击 **“确定”** 。
8. 展开“证书”。
9. 展开证书存储节点。
10. 展开证书子节点。
11. 在列表中选择某个证书。

## <a name="export-certificate"></a>导出证书
在**证书导出向导**中：

1. 单击“下一步”。
2. 选择“是”，并选择“导出私钥”。
3. 单击“下一步”。
4. 选择所需的输出文件格式。
5. 选中所需的选项。
6. 选中“密码”。
7. 输入强密码并进行确认。
8. 单击“下一步”。
9. 在证书的存储位置键入或浏览文件名（使用 .PFX 扩展名）。
10. 单击“下一步”。
11. 单击“完成”。
12. 单击 **“确定”** 。

## <a name="import-certificate"></a>导入证书
在证书导入向导中：

1. 选择存储位置。
   
   * 如果只有在当前用户下运行的进程将访问该服务，请选择“当前用户”
   * 如果此计算机中的其他进程将访问该服务，请选择“本地计算机”
2. 单击“下一步”。
3. 如果要从文件中导入，请确认文件路径。
4. 如果要导入 .PFX 文件，请执行以下操作：
   1. 输入用于保护私钥的密码
   2. 选择导入选项
5. 选择“将证书放入以下存储”
6. 单击“浏览”。
7. 选择所需的存储。
8. 单击“完成”。
   
   * 如果已选中“受信任的根证书颁发机构”存储，请单击“是”。
9. 在所有对话框窗口上单击“确定”。

## <a name="upload-certificate"></a>上传证书
在 [Azure 门户](https://portal.azure.com/)中

1. 选择“云服务”。
2. 选择云服务。
3. 单击顶部菜单上的“证书”。
4. 在底部栏上，单击“上传”。
5. 选择证书文件。
6. 如果是 .PFX 文件，则输入私钥密码。
7. 完成操作后，从列表中的新条目复制证书指纹。

## <a name="other-security-considerations"></a>其他安全注意事项
使用 HTTPS 终结点时，本文档中介绍的 SSL 设置将对服务及其客户端之间的通信进行加密。 这一点很重要，因为该通信中包含了数据库访问凭据以及其他可能的敏感信息。 但是，请注意，该服务会将内部状态（包括凭据）保存在其内部表中，该表位于在 Microsoft Azure 订阅中为元数据存储提供的 Microsoft Azure SQL 数据库中。 在服务配置文件（.CSCFG 文件）中，该数据库已定义为以下设置的一部分： 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

对此数据库中存储的凭据进行加密。 但是，最佳做法是，确保服务部署的 Web 角色和辅助角色保持最新且是安全的，因为它们都有权访问元数据数据库和用于加密和解密存储凭据的证书。 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

