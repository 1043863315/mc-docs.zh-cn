---
title: 管理 Azure Stack Hub 中的存储容量
description: 了解如何在 Azure Stack Hub 中监视和管理存储容量与可用性。
author: WenJason
ms.topic: conceptual
origin.date: 1/22/2020
ms.date: 02/24/2020
ms.author: v-jay
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: a052cad412ad5570d16d8a80d3960dcf35d0155e
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79292194"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>管理 Azure Stack Hub 的存储容量

本文帮助 Azure Stack Hub 云操作员监视并管理其 Azure Stack Hub 部署的存储容量。 Azure Stack Hub 存储基础结构分配 Azure Stack Hub 部署的一部分总存储容量作为*存储服务*。 存储服务将租户的数据存储在卷上对应于部署节点的共享中。

云操作员可以使用的存储量有限。 实施的解决方案定义了存储量。 使用多节点解决方案时，解决方案由 OEM 供应商提供，或由安装 Azure Stack 开发工具包 (ASDK) 的硬件提供。

由于 Azure Stack Hub 不支持存储容量扩展，因此必须[监视](#monitor-shares)可用存储，确保保持操作的效率。

当共享的剩余可用容量有限时，请规划[管理可用空间](#manage-available-space)以免共享的容量不足。

用于管理容量的选项包括：
- 回收容量。
- 迁移容器。

当共享利用率达到 100% 时，不再能够针对该共享运行存储服务。 若要获取共享还原操作的帮助，请联系 Azure 支持部门。

## <a name="understand-volumes-and-shares-containers-and-disks"></a>了解卷、共享、容器和磁盘
### <a name="volumes-and-shares"></a>卷和共享
存储服务将可用的存储分区成相等的独立卷，这些卷分配用于保存租户数据。  卷的数目等于 Azure Stack Hub 部署中的节点数目：

- 在包含四个节点的部署中，有四个卷。 每个卷有单个共享。 在多节点部署中，如果某个节点被删除或出现故障，共享数目不会减少。
- 如果使用 ASDK，则有一个包含单个共享的卷。

由于存储服务共享专供存储服务使用，因此不得直接在共享中修改、添加或删除任何文件。 只能由存储服务处理这些卷中存储的文件。

卷上的共享保存租户数据。 租户数据包括页 Blob、块 Blob、追加 Blob、表、队列、数据库和相关的元数据存储。 由于存储对象（Blob 等）各自包含在单个共享中，因此每个对象的大小上限不能超过共享大小。 新对象的大小上限取决于创建新对象时共享中仍未使用的空间容量。

当共享的可用空间不足且[回收](#reclaim-capacity)空间的操作不成功或不可用时，Azure Stack Hub 云操作人员可以将 Blob 容器从一个共享迁移到另一个共享。

有关租户用户如何使用 Azure Stack Hub 中的 Blob 存储的详细信息，请参阅 [Azure Stack Hub 存储服务](/azure-stack/user/azure-stack-storage-overview)。

### <a name="containers"></a>容器
租户用户可以创建容器用于存储 Blob 数据。 用户可以确定要在哪个容器中放置 Blob，而存储服务使用算法来确定要在哪个卷中放置容器。 此算法通常选择具有最多可用空间的卷。  

将 Blob 置于容器中后，该 Blob 可以增长以使用更多空间。 随着新 Blob 的添加和现有 Blob 的增长，卷中保存该容器的可用空间会不断缩减。  

容器不限于单个共享。 当容器中合并的 Blob 数据增长为使用 80% 或更多可用空间时，容器将进入溢出模式。  当容器处于溢出模式时，在该容器中创建的任何新 Blob 将分配到具有足够空间的其他卷。 一段时间后，处于溢出模式的容器可将 Blob 分布到多个卷。

达到卷中 80% 到 90% 的可用空间时，系统会在 Azure Stack Hub 管理员门户中引发警报。 云操作员应查看可用的存储容量，并规划重新均衡内容。 到达磁盘的 100% 容量时，存储服务将停止运行，但不会引发其他警报。

### <a name="disks"></a>磁盘
虚拟机 (VM) 磁盘由租户添加到容器，包括操作系统磁盘。 VM 还可能包含一个或多个数据磁盘。 这两种类型的磁盘存储为页 Blob。 租户最好是将每个磁盘放入不同的容器，以改善 VM 性能。

- VM 中保存磁盘的每个容器（或页 Blob）被视为拥有此磁盘的 VM 上的附加容器。
- 不保存 VM 中任何磁盘的容器被视为可用容器。

用于在附加容器中释放空间的选项有限制。 有关详细信息，请参阅[移动 VM 磁盘](#move-vm-disks)。

>[!TIP]  
> 云操作员不会直接管理已附加到可能由租户添加到容器的 VM 上的磁盘。 但是，在规划如何管理存储共享上的空间时，最好是了解磁盘与容器和共享之间的关系。

## <a name="monitor-shares"></a>监视共享
使用 Azure PowerShell 或管理员门户来监视共享，以便了解可用空间何时受限。 使用门户时，会收到有关共享空间不足的警报。

### <a name="use-powershell"></a>使用 PowerShell
云操作员可以使用 PowerShell `Get-AzsStorageShare` cmdlet 来监视共享的存储容量。 该 cmdlet 返回每个共享中总计、已分配和可用的空间（以字节为单位）。

![示例：返回共享的可用空间](media/azure-stack-manage-storage-shares/free-space.png)

- **总容量**：共享中可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
- **已用容量**：存储租户数据和相关元数据的文件中所有盘区使用的数据量（以字节为单位）。

### <a name="use-the-administrator-portal"></a>使用管理员门户
云操作员可以使用管理员门户来查看所有共享的存储容量。

1. 登录到[管理员门户](https://adminportal.local.azurestack.external)。
2. 选择“所有服务”   > “存储”   > “文件共享”  打开文件共享列表，可以在其中查看使用情况信息。

    ![示例：Azure Stack Hub 管理员门户中的存储文件共享](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total**：共享中可用的总空间（以字节为单位）。 此空间用于存储服务维护的数据和元数据。
   - **已用**：存储租户数据和相关元数据的文件中所有盘区使用的数据量（以字节为单位）。

### <a name="storage-space-alerts"></a>存储空间警报
使用管理员门户时，会收到有关共享空间不足的警报。

> [!IMPORTANT]
> 云操作员应该避免共享达到用完状态。 当共享利用率达到 100% 时，不再能够针对该共享运行存储服务。 若要在共享利用率达到 100% 时恢复可用空间和执行还原操作，必须联系支持部门。

* **警告**：当文件共享利用率超过 80% 时，管理员门户中会显示“警告”警报  ：

  ![示例：Azure Stack Hub 管理员门户中的警告性警报](media/azure-stack-manage-storage-shares/alert-warning.png)

* **严重**：当文件共享利用率超过 90% 时，管理员门户中会显示“严重”警报  ：

  ![示例：Azure Stack Hub 管理员门户中的严重警报](media/azure-stack-manage-storage-shares/alert-critical.png)

* **查看详细信息**：在管理员门户中，可以打开警报的详细信息来查看缓解选项：

  ![示例：在 Azure Stack Hub 管理员门户中查看警报详细信息](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>管理可用空间
需要释放共享中的可用空间时，请先使用破坏性最低的方法。 例如，先尝试回收空间，然后再选择迁移容器。  

### <a name="reclaim-capacity"></a>回收容量
此选项适用于多节点部署和 Azure Stack 开发工具包。 

可以回收已删除的租户帐户使用的容量。 当数据达到[保留期](azure-stack-manage-storage-accounts.md#set-the-retention-period)时，系统会自动回收此容量，或者，你也可以立即回收此容量。

有关详细信息，请参阅[管理 Azure Stack Hub 存储帐户](azure-stack-manage-storage-accounts.md#reclaim)的“回收容量”部分。

### <a name="migrate-a-container-between-volumes"></a>在卷之间迁移容器
此选项仅适用于 Azure Stack Hub 集成系统。 

由于租户使用模式方面的原因，某些租户共享使用的空间比其他共享要多。 这可能会导致某些共享在其他相对用得极少的共享之前就遇到空间不足的情况。

可将某些 Blob 容器手动迁移到不同的共享，在过度使用的共享中释放空间。 可将多个较小容器迁移到单个共享，前提是该共享可以提供足够的容量来保存所有这些容器。 使用迁移操作来移动可用容器。  可用容器是不包含 VM 磁盘的容器。

迁移过程会在新的共享中合并容器的所有 Blob。

- 如果容器进入溢出模式且 Blob 已放置在其他卷上，则新共享必须提供足够的容量来保存迁移的容器的所有 Blob。 这包括位于其他共享中的 Blob。

- PowerShell cmdlet `Get-AzsStorageContainer` 只识别容器的初始卷上的已用空间。 此 cmdlet 不识别已置于其他卷上的 Blob 使用的空间。 因此，容器的完整大小可能不明显。 新共享中的容器整合可能会使该共享进入溢出状态，以便将数据置于其他共享。 因此，可能需要重新均衡共享。

- 如果你缺少对特定资源组的权限，且无法使用 PowerShell 来查询溢出数据的其他卷，请配合这些资源组和容器的所有者，在迁移数据之前了解要迁移的总数据量。  

> [!IMPORTANT]
> 迁移容器的 Blob 是一项脱机操作，需要用到 PowerShell。 在迁移完成之前，要迁移的容器的所有 Blob 会保持脱机状态且不可使用。 还应该避免在所有现有迁移操作完成之前升级 Azure Stack Hub。

#### <a name="migrate-containers-by-using-powershell"></a>使用 PowerShell 迁移容器
1. 确认已[安装并配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。 有关详细信息，请参阅[使用 Azure PowerShell 管理 Azure 资源](/azure-resource-manager/management/manage-resources-powershell)。
2. 检查容器，了解要迁移的共享中包含哪种数据。 若要识别卷中可迁移的最佳候选容器，请使用 `Get-AzsStorageContainer` cmdlet：

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   然后检查 $containers：

   ```powershell
   $containers
   ```

   ![示例：$containers](media/azure-stack-manage-storage-shares/containers.png)

3. 识别用于保存要迁移的容器的最佳目标共享：

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   然后检查 $destinationshares：

   ```powershell 
   $destinationshares
   ```

   ![示例：$destination shares](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. 开始迁移容器。 迁移是异步操作。 如果在首次迁移完成之前开始迁移其他容器，请使用作业 ID 来跟踪每个容器的状态。

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   然后检查 $jobId。 在以下示例中，请将 *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* 替换为要检查的作业 ID：

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. 使用作业 ID 检查迁移作业的状态。 容器迁移完成后，**MigrationStatus** 会设置为 *Complete*。

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![示例：迁移状态](media/azure-stack-manage-storage-shares/migration-status1.png)

6. 可以取消正在进行的迁移作业。 系统会以异步方式处理已取消的迁移作业。 可以使用 $jobid 跟踪取消操作：

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![示例：回退状态](media/azure-stack-manage-storage-shares/rollback.png)

7. 可以再次运行步骤 6 中的命令，直到迁移状态为 *Canceled*：  

    ![示例：“已取消”状态](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>移动 VM 磁盘
此选项仅适用于 Azure Stack Hub 集成系统。 

用于管理空间的最极端方法涉及到移动 VM 磁盘。 由于移动附加容器（包含 VM 磁盘的容器）的过程很复杂，请在 Azure 支持部门的帮助下完成此操作。

## <a name="next-steps"></a>后续步骤
若要详细了解如何向用户提供 VM，请参阅[管理 Azure Stack Hub 的存储容量](azure-stack-tutorial-tenant-vm.md)。
