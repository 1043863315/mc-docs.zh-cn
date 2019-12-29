---
title: Azure PowerShell 脚本示例 - 将应用程序证书添加到群集 | Azure
description: Azure PowerShell 脚本示例 - 将应用程序证书添加到 Service Fabric 群集。
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
origin.date: 01/18/2018
ms.date: 12/09/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 0f59fe1604067f98b1141e3808684b70ab830a80
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75335714"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>将应用程序证书添加到 Service Fabric 群集

此示例脚本演示如何在 Key Vault 中创建证书，然后将其部署到运行群集的虚拟机规模集之一。 此方案不直接使用 Service Fabric，而是取决于 Key Vault 和虚拟机规模集。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount -Environment AzureChinaCloud` 创建与 Azure 的连接。 

## <a name="create-a-certificate-in-key-vault"></a>在 Key Vault 中创建证书

```powershell
Connect-AzAccount -Environment AzureChinaCloud

$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>或将现有证书上传到 Key Vault

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $PathToPFX, $CertPassword

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>通过证书更新虚拟机规模集配置文件

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore
$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($certConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>更新虚拟机规模集
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> [!NOTE]
> 如果希望将证书放置在群集中的多个节点上，应针对每个应具有证书的节点类型重复此脚本的第二个和第三部分。

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzKeyVaultCertificatePolicy](https://docs.microsoft.com/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | 创建表示证书的内存中策略 |
| [Add-AzKeyVaultCertificate](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| 将策略部署到 Key Vault |
| [New-AzVmssVaultCertificateConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | 创建表示 VM 中证书的内存中配置 |
| [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssSecret) | 将证书添加到虚拟机规模集的内存中定义 |
| [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss) | 部署虚拟机规模集的新定义 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Azure Powershell 示例。

<!-- Update_Description: update meta properties, wording update, update link -->