---
title: 用 SAS 令牌安全部署模板
description: 使用受 SAS 令牌保护的 Azure 资源管理器模板将资源部署到 Azure。 显示 Azure PowerShell 和 Azure CLI。
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d30e685c35f33b6fc5d3872b9287e45190ad5713
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484280"
---
# <a name="deploy-private-resource-manager-template-with-sas-token"></a>使用 SAS 令牌部署专用 Resource Manager 模板

如果模板位于存储帐户中，可以限制对该模板的访问，以避免公开公开该模板。 可以通过为模板创建共享访问签名（SAS）令牌并在部署过程中提供该令牌来访问受保护的模板。 本文介绍如何使用 Azure PowerShell 或 Azure CLI 来部署具有 SAS 令牌的模板。

## <a name="create-storage-account-with-secured-container"></a>创建具有安全容器的存储帐户

以下脚本将创建一个存储帐户，并关闭公共访问权限的容器。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>将模板上传到存储帐户

现在，你已准备好将模板上传到存储帐户。 提供要使用的模板的路径。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>在部署期间提供 SAS 令牌

要在存储帐户中部署专用模板，请生成 SAS 令牌，并将其包括在模板的 URI 中。 设置到期时间以允许足够的时间来完成部署。

> [!IMPORTANT]
> 只有帐户所有者可以访问包含模板的 blob。 但是，如果为 blob 创建 SAS 令牌，则拥有该 URI 的任何人都可以访问 blob。 如果其他用户截获了该 URI，则此用户可以访问该模板。 SAS 令牌是限制对模板的访问的好方法，但不应直接在模板中包括密码等敏感数据。
>

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

有关将 SAS 令牌与链接模板配合使用的示例，请参阅[将已链接的模版与 Azure 资源管理器配合使用](linked-templates.md)。


## <a name="next-steps"></a>后续步骤
* 有关部署模板的简介，请参阅[使用 Resource Manager 模板和 Azure PowerShell 部署资源](deploy-powershell.md)。
* 若要在模板中定义参数，请参阅[创作模板](template-syntax.md#parameters)。
