---
title: 使用 Azure Site Recovery 中的 PowerShell 将 VMware VM 复制和故障转移到 Azure | Azure
description: 了解如何使用 Azure Site Recovery 中的 PowerShell 设置 VMware VM 到 Azure 的复制和故障转移。
services: site-recovery
author: rockboyfor
ms.service: site-recovery
origin.date: 07/06/2018
ms.date: 07/23/2018
ms.topic: conceptual
ms.author: v-yeche
ms.openlocfilehash: 46de52aa0982d4a7b3e006056b5a4e2a2d946586
ms.sourcegitcommit: f7ff09be9f3be5e3eb795e383c0c670f480f233d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39169025"
---
# <a name="replicate-and-fail-over-vmware-vms-to-azure-with-powershell"></a>使用 PowerShell 将 VMware VM 复制和故障转移到 Azure

本文介绍如何使用 Azure PowerShell 将 VMware 虚拟机复制和故障转移到 Azure。 

你将学习如何执行以下操作：

> [!div class="checklist"]
> - 创建恢复服务保管库并设置保管库上下文。
> - 在保管库中验证服务器注册。
> - 设置复制，包括复制策略。 添加 vCenter Server，并发现 VM。 > - 添加 vCenter Server，并发现 
> - 创建存储帐户以保存复制数据，并复制 VM。
> - 执行故障转移。 配置故障转移设置，执行复制虚拟机的 e 设置。

## <a name="prerequisites"></a>先决条件

开始之前：

- 请确保了解[方案体系结构和组件](vmware-azure-architecture.md)。
- 查看所有组件的[支持要求](site-recovery-support-matrix-to-azure.md)。
- 已安装 5.0.1 或更高版本的 AzureRm PowerShell 模块。 如需安装或升级 Azure PowerShell，请参阅 [Azure PowerShell 安装和配置指南](https://docs.microsoft.com/powershell/azureps-cmdlets-docs)。

## <a name="log-into-azure"></a>登录到 Azure

使用 Connect-AzureRmAccount -Environment AzureChinaCloud cmdlet 登录到 Azure 订阅：

```azurepowershell
Connect-AzureRmAccount -Environment AzureChinaCloud
```
选择要将 VMware 虚拟机复制到的 Azure 订阅。 使用 Get-AzureRmSubscription cmdlet 获取有权访问的 Azure 订阅列表。 使用 Select-AzureRmSubscription cmdlet 选择要使用的 Azure 订阅。

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>设置恢复服务保管库

1. 创建要在其中创建恢复服务保管库的资源组。 在以下示例中，资源组名为 VMwareDRtoAzurePS，它是在中国东部区域创建的。

    ```azurepowershell
    New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "China East"
    ```
    ```
    ResourceGroupName : VMwareDRtoAzurePS
    Location          : chinaeast
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
    ```

2. 创建恢复服务保管库。 在以下示例中，恢复服务保管库名为 VMwareDRToAzurePs，它是在中国东部区域创建的，并位于上一步骤创建的资源组中。

    ```azurepowershell
    New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "China East" -ResourceGroupName "VMwareDRToAzurePs"
    ```
    ```
    Name              : VMwareDRToAzurePs
    ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
    Type              : Microsoft.RecoveryServices/vaults
    Location          : chinaeast
    ResourceGroupName : VMwareDRToAzurePs
    SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
    ``` 

3. 下载保管库的保管库注册密钥。 保管库注册密钥用于将本地配置服务器注册到此保管库。 注册属于配置服务器软件安装过程的一部分。

    ```azurepowershell
    #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
    $vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

    #Download vault registration key to the path C:\Work
    Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
    ```
    ```
    FilePath
    --------
    C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
    ```

4. 使用下载的保管库注册密钥并遵循下面所列文章中的步骤，完成配置服务器的安装和注册。
    - [选择保护目标](vmware-azure-set-up-source.md#choose-your-protection-goals)
    - [设置源环境](vmware-azure-set-up-source.md#set-up-the-configuration-server) 

### <a name="set-the-vault-context"></a>设置保管库上下文

使用 Set-ASRVaultContext cmdlet 设置保管库上下文。 设置后，PowerShell 会话中的后续 Azure Site Recovery 操作将在所选保管库的上下文中执行。

> [!TIP]
> Azure Site Recovery PowerShell 模块（AzureRm.RecoveryServices.SiteRecovery 模块）为大多数 cmdlet 提供了易用的别名。 模块中的 cmdlet 采用 *\<Operation>-**AzureRmRecoveryServicesAsr**\<Object>* 形式，并具有采用 *\<Operation>-**ASR**\<Object>* 形式的等效别名。 本文使用 cmdlet 别名以便于阅读。

以下示例使用 $vault 变量中的保管库详细信息为 PowerShell 会话指定保管库上下文。

    ```azurepowershell
    Set-ASRVaultContext -Vault $vault
    ```
    ```
    ResourceName      ResourceGroupName ResourceNamespace          ResouceType
    ------------      ----------------- -----------------          -----------
    VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
    ```

除了使用 Set-ASRVaultContext cmdlet 之外，还可以使用 Import-AzureRmRecoveryServicesAsrVaultSettingsFile cmdlet 设置保管库上下文。 将保管库注册密钥文件所在位置的路径指定为 Import-AzureRmRecoveryServicesAsrVaultSettingsFile cmdlet 的路径参数。 例如：

    ```azurepowershell
    Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
    Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
    ```
本文的后续部分假设已为 Azure Site Recovery 操作设置保管库上下文。

## <a name="validate-vault-registration"></a>验证保管库注册

对于此示例，我们具备以下先决条件：

- 配置服务器 (**ConfigurationServer**) 已注册到此保管库。
- 其他进程服务器 (**ScaleOut-ProcessServer**) 已注册到 ConfigurationServer
- 配置服务器上已设置了帐户（**vCenter_account**、**WindowsAccount**、**LinuxAccount**）。 这些帐户用于添加 vCenter Server 以发现虚拟机，以及在要复制的 Windows 和 Linux 服务器上推送安装移动服务软件。

1. 已注册的配置服务器由 Site Recovery 中的结构对象表示。 获取保管库中结构对象的列表，并标识配置服务器。

    ```azurepowershell
    # Verify that the Configuration server is successfully registered to the vault
    $ASRFabrics = Get-ASRFabric
    $ASRFabrics.count
    ```
    ```
    1
    ```
    ```azurepowershell
    #Print details of the Configuration Server
    $ASRFabrics[0]
    ```
    ```
    Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
    FriendlyName          : ConfigurationServer
    ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                            /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
    Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
    FabricType            : VMware
    SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
    FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
    ```

2. 标识可用于复制计算机的进程服务器。

    ```azurepowershell
    $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
    for($i=0; $i -lt $ProcessServers.count; $i++) {
     "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
    }
    ```
    ```
    0     ScaleOut-ProcessServer
    1     ConfigurationServer
    ```

   在上面的输出中，***$ProcessServers[0]*** 对应于 *ScaleOut ProcessServer*，***$ProcessServers[1]*** 对应于 *ConfigurationServer* 上的进程服务器角色

3. 标识已在配置服务器上设置的帐户。

    ```azurepowershell
    $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
    #Print the account details
    $AccountHandles
    ```
    ```
    AccountId AccountName
    --------- -----------
    1         vCenter_account
    2         WindowsAccount
    3         LinuxAccount
    ```

   从上面的输出中，***$AccountHandles[0]*** 对应于帐户 *vCenter_account*，***$AccountHandles[1]*** 对应于帐户 *WindowsAccount*，***$AccountHandles[2]*** 对应于帐户 *LinuxAccount*

## <a name="create-a-replication-policy"></a>创建复制策略

此步骤创建两个复制策略。 一个策略将 VMware 虚拟机复制到 Azure，另一个策略将 Azure 中运行的故障转移虚拟机复制回到本地 VMware 站点。

> [!NOTE]
> 大多数 Azure Site Recovery 操作都是以异步方式执行的。 启动某个操作时，会提交一个 Azure Site Recovery 作业并返回一个作业跟踪对象。 此作业跟踪对象可用于监视操作状态。

1. 创建名为 *ReplicationPolicy* 的复制策略，以使用指定的属性将 VMware 虚拟机复制到 Azure。

    ```azurepowershell
    $Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

    # Track Job status to check for completion
    while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){ 
            sleep 10; 
            $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
    }

    #Display job status
    $Job_PolicyCreate
    ```
    ```
    Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
    ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                       9-479f-430d-b76b-6bc7eb2d0b3e
    Type             :
    JobType          : AddProtectionProfile
    DisplayName      : Create replication policy
    ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
    State            : Succeeded
    StateDescription : Completed
    StartTime        : 11/24/2017 2:49:24 AM
    EndTime          : 11/24/2017 2:49:23 AM
    TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
    TargetObjectType : ProtectionProfile
    TargetObjectName : ReplicationPolicy
    AllowedActions   :
    Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
    Errors           : {}
    ```

2. 创建用于从 Azure 故障回复到本地 VMware 站点的复制策略。

    ```azurepowershell
    $Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
    ```

    使用 *$Job_FailbackPolicyCreate* 中的作业详细信息来跟踪操作，直到其完成。

    * 创建保护容器映射，用于在配置服务器上映射复制策略。

    ```azurepowershell
    #Get the protection container corresponding to the Configuration Server
    $ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

    #Get the replication policies to map by name.
    $ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
    $FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

    # Associate the replication policies to the protection container corresponding to the Configuration Server. 

    $Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

    # Check the job status
    while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
            sleep 10; 
            $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
    }
    $Job_AssociatePolicy.State

    <# In the protection container mapping used for failback (replicating failed over virtual machines 
       running in Azure, to the primary VMware site.) the protection container corresponding to the 
       Configuration server acts as both the Primary protection container and the recovery protection
       container
    #>
     $Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

    # Check the job status
    while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
            sleep 10; 
            $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
    }
    $Job_AssociateFailbackPolicy.State

    ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>添加 vCenter 服务器，并发现 VM

根据 IP 地址或主机名添加 vCenter 服务器。 **-port** 参数指定 vCenter 服务器上要连接到的端口，**-Name** 参数指定用于 vCenter 服务器的友好名称，**-Account** 参数指定配置服务器上用于发现 vCenter 服务器所管理的虚拟机的帐户句柄。

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) { 
        sleep 30; 
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>创建用于复制的存储帐户

此步骤创建用于复制的存储帐户。 稍后将使用这些存储帐户来复制虚拟机。 请务必在保管库所在的同一个 Azure 区域中创建存储帐户。 如果打算使用现有的存储帐户进行复制，则可以跳过此步骤。

> [!NOTE]
> 将本地虚拟机复制到高级存储帐户时，需要额外指定一个标准存储帐户（日志存储帐户）。 在可以在高级存储目标上应用日志之前，日志存储帐户以中间存储的形式保存复制日志。
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "China East" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "China East" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "China East" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>复制 VMware VM

发现 vCenter 服务器中的虚拟机大约需要 15 到 20 分钟时间。 发现后，将在 Azure Site Recovery 中为每个发现的虚拟机创建一个可保护项对象。 此步骤将三个已发现的虚拟机复制到上一步骤中创建的 Azure 存储帐户。

需要使用以下详细信息来保护已发现的虚拟机：

* 要复制的可保护项。
* 要将虚拟机复制到的存储帐户。 此外，需要使用日志存储在高级存储帐户中保护虚拟机。
* 用于复制的进程服务器。 已检索可用进程服务器列表并将其保存在 ***$ProcessServers[0]*** *(ScaleOut-ProcessServer)* 和 ***$ProcessServers[1]*** *(ConfigurationServer)* 变量中。
* 用于在计算机上推送安装移动服务软件的帐户。 已检索可用帐户列表并将其存储在 ***$AccountHandles*** 变量中。
* 用于复制的复制策略的保护容器映射。
* 故障转移时必须在其中创建虚拟机的资源组。
* （可选）故障转移的虚拟机应连接到的 Azure 虚拟网络和子网。

现在，请使用下表中指定的设置来复制以下虚拟机

|虚拟机  |进程服务器        |存储帐户              |日志存储帐户  |策略           |用于安装移动服务的帐户|目标资源组  | 目标虚拟网络  |目标子网  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| 不适用                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| 不适用                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   

```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableRepication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableRepication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableRepication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

成功完成启用复制作业后，将对虚拟机启动初始复制。 初始复制可能要花费一段时间，具体时间取决于要复制的数据量和复制时可用的带宽。 初始复制完成后，虚拟机会进入受保护状态。 虚拟机进入受保护状态后，你可以针对该虚拟机执行测试故障转移、将其添加到恢复计划，等等。

可以使用 Get-ASRReplicationProtectedItem cmdlet 检查虚拟机的复制状态和复制运行状况。

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>配置故障转移设置

可以使用 Set-ASRReplicationProtectedItem cmdlet 更新受保护计算机的故障转移设置。 可通过此 cmdlet 更新的部分设置包括：
* 故障转移时要创建的虚拟机的名称
* 故障转移时要创建的虚拟机的 VM 大小
* 故障转移时虚拟机的 NIC 应连接到的 Azure 虚拟网络和子网
* 故障转移到托管磁盘
* 应用 Azure 混合使用权益
* 在目标虚拟网络中，分配一个要在故障转移时分配给虚拟机的静态 IP 地址。

在此示例中，我们将更新在故障转移虚拟机 *Win2K12VM1* 时要创建的虚拟机的 VM 大小，并指定该虚拟机应在故障转移时使用托管磁盘。

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>运行测试故障转移

1. 按如下所述运行 DR 演练（测试故障转移）：

    ```azurepowershell
    #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

    #Get details of the test failover virtual network to be used
    TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

    #Start the test failover operation
    $TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
    ```
2. 成功完成测试故障转移作业后，可以看到，Azure 中创建了一个虚拟机，其名称带有后缀 *-Test*（在本例中为 Win2K12VM1-Test）。
3. 现在，可以连接到测试故障转移虚拟机并验证测试故障转移。
4. 使用 Start-ASRTestFailoverCleanupJob cmdlet 清理测试故障转移。 此操作会删除执行测试故障转移操作过程中创建的虚拟机。

    ```azurepowershell
    $Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
    ```

## <a name="fail-over-to-azure"></a>故障转移到 Azure

在此步骤中，我们要将虚拟机 Win2K12VM1 故障转移到特定恢复点。

1. 获取要用于故障转移的可用恢复点的列表：
    ```azurepowershell
    # Get the list of available recovery points for Win2K12VM1
    $RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
    "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
    ```
    ```
    CrashConsistent 11/24/2017 5:28:25 PM
    ```
    ```azurepowershell

    #Start the failover job
    $Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
    do {
            $Job_Failover = Get-ASRJob -Job $Job_Failover;
            sleep 60;
    } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
    $Job_Failover.State
    ```
    ```
    Succeeded
    ```

2. 故障转移成功后，可提交故障转移操作并设置从 Azure 到本地 VMware 站点的反向复制。

## <a name="next-steps"></a>后续步骤
了解如何使用 [Azure Site Recovery PowerShell 参考](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery)自动执行更多任务。
<!-- Update_Description: update meta properties， update link, wording update -->
