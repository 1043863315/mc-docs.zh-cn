---
title: 部署 SQL Server 资源提供程序
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 上部署 SQL Server 资源提供程序。
author: WenJason
ms.topic: article
origin.date: 10/02/2019
ms.date: 02/24/2020
ms.lastreviewed: 03/18/2019
ms.author: v-jay
ms.reviewer: xiaofmao
ms.openlocfilehash: 78f23e5b88cf6e7ff6406c1153d362f9b4bde1ee
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79290911"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack-hub"></a>在 Azure Stack Hub 上部署 SQL Server 资源提供程序

可以使用 Azure Stack Hub SQL Server 资源提供程序来将 SQL 数据库公开为 Azure Stack Hub 服务。 SQL 资源提供程序以服务的形式在 Windows Server 2016 Server Core 虚拟机 (VM) 上运行。

> [!IMPORTANT]
> 只有资源提供程序才能在托管 SQL 或 MySQL 的服务器上创建项目。 如果在不是由资源提供程序创建的主机服务器上创建项目，则此类项目可能导致状态不匹配。

## <a name="prerequisites"></a>先决条件

需要先实施几个先决条件，然后才能部署 Azure Stack Hub SQL 资源提供程序。 若要满足这些要求，请在可访问特权终结点 VM 的计算机上完成以下步骤：

- 向 Azure [注册 Azure Stack Hub](azure-stack-registration.md)（如果尚未这样做），以便可以下载 Azure 市场项。

- 下载 **Windows Server 2016 Datacenter - 服务器核心**映像，将所需的 Windows Server 核心 VM 添加到 Azure Stack Hub 市场。

- 下载 SQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序具有相应的最低 Azure Stack Hub 版本。

  |最低 Azure Stack Hub 版本|SQL RP 版本|
  |-----|-----|
  |版本 1910 (1.1910.0.58)|[SQL RP 版本 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|
  |版本 1808 (1.1808.0.97)|[SQL RP 版本 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |版本 1808 (1.1808.0.97)|[SQL RP 版本 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |版本 1804 (1.0.180513.1)|[SQL RP 版本 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

> [!IMPORTANT]
> 在部署 SQL 资源提供程序版本 1.1.47.0 之前，应该将 Azure Stack Hub 系统升级到 1910 更新或更高版本。 以前不支持的 Azure Stack Hub 版本上的 SQL 资源提供程序版本 1.1.47.0 无法正常工作。

- 请确保满足数据中心集成先决条件：

    |先决条件|参考|
    |-----|-----|
    |正确设置了条件性 DNS 转发。|[Azure Stack Hub 数据中心集成 - DNS](azure-stack-integrate-dns.md)|
    |资源提供程序的入站端口处于打开状态。|[Azure Stack Hub 数据中心集成 - 入站端口和协议](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |正确设置了 PKI 证书使用者和 SAN。|[Azure Stack Hub 部署必备 PKI 先决条件](azure-stack-pki-certs.md#mandatory-certificates)<br>[Azure Stack Hub 部署 PaaS 证书先决条件](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

在离线场景中，请完成以下步骤下载所需的 PowerShell 模块，并手动注册存储库。

1. 登录到已建立 Internet 连接的计算机，使用以下脚本下载 PowerShell 模块。

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

2. 然后，将下载的包复制到 USB 设备。

3. 登录到已断开连接的工作站，将包从 USB 设备复制到工作站中的某个位置。

4. 将此位置注册为本地存储库。

```powershell
# requires -Version 5
# requires -RunAsAdministrator
# requires -Module PowerShellGet
# requires -Module PackageManagement

$SourceLocation = "C:\temp\azs1.6.0"
$RepoName = "azs1.6.0"

Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

New-Item -Path $env:ProgramFiles -name "SqlMySqlPsh" -ItemType "Directory"
```

### <a name="certificates"></a>证书

_仅适用于集成系统安装_。 必须提供 [Azure Stack Hub 部署 PKI 要求](./azure-stack-pki-certs.md#optional-paas-certificates)中的“可选 PaaS 证书”部分所述的 SQL PaaS PKI 证书。 将 .pfx 文件放在 **DependencyFilesLocalPath** 参数指定的位置。 对于 ASDK 系统，请不要提供证书。

## <a name="deploy-the-sql-resource-provider"></a>部署 SQL 资源提供程序

安装所有必备组件后，在可访问 Azure Stack Hub 管理员 Azure 资源管理终结点和特权终结点的计算机中运行 **DeploySqlProvider.ps1** 脚本，以部署 SQL 资源提供程序。 DeploySqlProvider.ps1 脚本是从针对 Azure Stack Hub 版本下载的 SQL 资源提供程序二进制文件中提取的。

 > [!IMPORTANT]
 > 在部署资源提供程序之前，请查看发行说明，了解新功能、修补程序以及任何可能影响部署的已知问题。

若要部署 SQL 资源提供程序，请打开一个权限提升的 PowerShell（不是 PowerShell ISE）**新**窗口，并切换到解压缩后的 SQL 资源提供程序二进制文件所在的目录。 我们建议使用新的 PowerShell 窗口，以避免已加载的 PowerShell 模块造成问题。

运行 DeploySqlProvider.ps1 脚本，以完成以下任务：

- 将证书和其他项目上传到 Azure Stack Hub 上的存储帐户。
- 发布库包，以便可以使用库部署 SQL 数据库。
- 发布用于部署宿主服务器的库包。
- 使用下载的 Windows Server 2016 核心映像部署 VM，然后安装 SQL 资源提供程序。
- 注册映射到资源提供程序 VM 的本地 DNS 记录。
- 将资源提供程序注册到操作员帐户的本地 Azure 资源管理器。

> [!NOTE]
> 当 SQL 资源提供程序部署开始时，将创建 **system.local.sqladapter** 资源组。 可能需要花费多达 75 分钟才能完成此资源组的必需部署。 不要将任何其他资源置于 **system.local.sqladapter** 资源组中。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 参数

可在命令行中指定以下参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack Hub 服务管理员帐户的凭据。 使用部署 Azure Stack Hub 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **AzureEnvironment** | 用于部署 Azure Stack Hub 的服务管理员帐户的 Azure 环境。 仅对于 Azure AD 部署是必需的。 受支持的环境名称是 **AzureChinaCloud**。 | AzureChinaCloud |
| **DependencyFilesLocalPath** | 对于集成系统，必须将证书 .pfx 文件放在此目录中。 还可以在此处复制一个 Windows Update MSU 包。 | _可选_（对于集成系统为强制的  ） |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>使用自定义脚本部署 SQL 资源提供程序

如果要部署 SQL 资源提供程序版本 1.1.33.0 或更早版本，则需要在 PowerShell 中安装特定版本的 AzureRm.BootStrapper 和 Azure Stack Hub 模块。 如果要部署 SQL 资源提供程序版本 1.1.47.0，则部署脚本会自动下载所需的 PowerShell 模块并将其安装到路径 C:\Program Files\SqlMySqlPsh。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> 在断开连接的情况下，需要下载所需的 PowerShell 模块并手动注册存储库，这是先决条件。

若要在部署资源提供程序时消除任何手动配置，可以自定义以下脚本。 更改 Azure Stack Hub 部署所需的默认帐户信息和密码。

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter is AzureChinaCloud depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.partner.onmschina.cn"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local admin account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

资源提供程序安装脚本完成后，刷新浏览器以确保能够看到最新的更新，然后关闭当前 PowerShell 会话。

## <a name="verify-the-deployment-using-the-azure-stack-hub-portal"></a>使用 Azure Stack Hub 门户验证部署

可以使用以下步骤来验证是否已成功部署 SQL 资源提供程序。

1. 以服务管理员身份登录到管理员门户。
2. 选择“资源组”  。
3. 选择“system.\<位置\>.sqladapter”资源组。 
4. 在资源组概述摘要页上，应当没有失败的部署。

    ![在 Azure Stack Hub 管理员门户中验证 SQL 资源提供程序的部署](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. 最后，在管理员门户中选择“虚拟机”  ，以验证 SQL 资源提供程序 VM 是否已成功创建且正在运行。

## <a name="next-steps"></a>后续步骤

[添加宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)
