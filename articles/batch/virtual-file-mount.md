---
title: 在池中装载虚拟文件系统 - Azure Batch | Microsoft Docs
description: 了解如何在 Batch 池中装载虚拟文件系统。
services: batch
documentationcenter: ''
author: lingliw
manager: digimobile
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
origin.date: 08/13/2019
ms.date: 09/13/2019
ms.author: v-lingwu
ms.openlocfilehash: 0ba20711a6dd904f41359b8213ebe2f3331b3a3f
ms.sourcegitcommit: b7fe28ec2de92b5befe61985f76c8d0216f23430
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78850199"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>在 Batch 池中装载虚拟文件系统

Azure Batch 现在支持在 Batch 池中的 Windows 或 Linux 计算节点上装载云存储或外部文件系统。 当某个计算节点加入池时，将会装载虚拟文件系统并将其视为该节点上的本地驱动器。

本文介绍如何使用[适用于 .NET 的 Batch 管理库](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)在计算节点池上装载虚拟文件系统。

> [!NOTE]
> 支持在 2019 年 8 月 19 日或之后创建的 Batch 池中装载虚拟文件系统。 在 2019 年 8 月 19 日之前创建的 Batch 池不支持此功能。
> 
> 用于在计算节点上装载文件系统的 API 是 [Batch .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet) 库的一部分。

## <a name="benefits-of-mounting-on-a-pool"></a>在池中装载可获得的优势

将文件系统安装到池中而不是让任务从大型数据集检索自身的数据，可使任务更方便、更高效地访问所需的数据。

假设有多个任务（例如渲染电影）需要访问一组公用数据。 每个任务每次渲染场景文件中的一个或多个帧。 如果装载包含场景文件的驱动器，则计算节点可以更方便地访问共享的数据。 此外，可以根据并发访问数据的计算节点数所需的性能和规模（吞吐量和 IOPS），单独选择和缩放基础文件系统。 Blobfuse 仅在 Linux 节点上可用，但是，[Azure 文件](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/)提供类似的工作流，并可在 Windows 和 Linux 上使用。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>在池中装载虚拟文件系统  

在池中装载虚拟文件系统可使池中的每个计算节点都能使用该文件系统。 当计算节点加入池，或者节点重启或重置映像时，将配置该文件系统。

若要在池中装载文件系统，请创建一个 `MountConfiguration` 对象。 选择适合虚拟文件系统的对象：`AzureBlobFileSystemConfiguration`、`AzureFileShareConfiguration`、`NfsMountConfiguration` 或 `CifsMountConfiguration`。

所有装载配置对象都需要以下基本参数。 某些装载配置具有特定于所用文件系统的参数，代码示例中将更详细地介绍这些参数。

- **帐户名或源**：若要装载虚拟文件共享，需要获取存储帐户或其源的名称。
- **相对装载路径或源**：在计算节点上装载的文件系统的位置，相对于节点上可通过 `AZ_BATCH_NODE_MOUNTS_DIR` 访问的标准 `fsmounts` 目录。 确切的位置根据节点上使用的操作系统而异。 例如，Ubuntu 节点上的物理位置将映射到 `mnt\batch\tasks\fsmounts`，而 CentOS 节点上的物理位置将映射到 `mnt\resources\batch\tasks\fsmounts`。
- **装载选项或 blobfuse 选项**：这些选项描述用于装载文件系统的特定参数。

创建 `MountConfiguration` 对象后，在创建池时将该对象分配到 `MountConfigurationList` 属性。 当某个节点加入池，或者节点重启或重置映像时，将会装载该文件系统。

装载文件系统时，会创建一个指向已装载文件系统的位置的环境变量 `AZ_BATCH_NODE_MOUNTS_DIR`，并创建可用于故障排除和调试的日志文件。 [诊断装载错误](#diagnose-mount-errors)部分更详细地介绍了日志文件。  

> [!IMPORTANT]
> 可在一个池中装载的最大文件系统数目为 10 个。 有关详细信息和其他限制，请参阅 [Batch 服务配额和限制](batch-quota-limit.md#other-limits)。

## <a name="examples"></a>示例

以下代码示例演示如何将各种文件共享装载到计算节点池。

### <a name="azure-files-share"></a>Azure 文件共享

Azure 文件是标准的 Azure 云文件系统产品。 若要详细了解如何获取装载配置代码示例中的任何参数，请参阅[使用 Azure 文件共享](../storage/files/storage-how-to-use-files-windows.md)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob 文件系统

装载 Blob 文件系统需要使用存储帐户的 `AccountKey` 或 `SasKey`。 有关获取这些密钥的信息，请参阅[查看帐户密钥](../storage/common/storage-account-manage.md#view-account-keys-and-connection-string)或[使用共享访问签名 (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 有关使用 blobfuse 的详细信息，请参阅 blobfuse [故障排除常见问题解答](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)。 若要获取对 blobfuse 装载目录的默认访问权限，请以**管理员**身份运行该任务。 blobfuse 在用户空间中装载目录；创建池时，将以 root 身份装载 blobfuse。 在 Linux 中，所有**管理员**任务都以 root 身份运行。 [FUSE 参考页](http://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)中介绍了 FUSE 模块的所有选项。

除了故障排除指南以外，还可以借助“blobfuse 中的问题”GitHub 存储库来检查当前的 blobfuse 问题并获取解决方法。 有关详细信息，请参阅 [blobfuse 问题](https://github.com/Azure/azure-storage-fuse/issues)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>网络文件系统

还可以将网络文件系统 (NFS) 装载到池节点，使 Azure Batch 节点能够轻松访问传统的文件系统。 此类文件系统可能是部署在云中的单个 NFS 服务器，或通过虚拟网络访问的本地 NFS 服务器。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>通用 Internet 文件系统

还可以将通用 Internet 文件系统 (CIFS) 装载到池节点，使 Azure Batch 节点能够轻松访问传统的文件系统。 CIFS 是一种文件共享协议，提供开放的跨平台机制用于请求网络服务器文件和服务。 CIFS 基于用于 Internet 和 Intranet 文件共享的 Microsoft 服务器消息块 (SMB) 协议增强版，用于在 Windows 节点上装载外部文件系统。 若要详细了解 SMB，请参阅[文件服务器和 SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>诊断装载错误

如果某个装载配置失败，则池中的计算节点也会失败，并且节点状态将变为不可用。 若要诊断装载配置失败，请检查 [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性以了解有关错误的详细信息。

若要获取日志文件进行调试，请使用 [OutputFiles](batch-task-output-files.md) 上传 `*.log` 文件。 `*.log` 文件包含有关位于 `AZ_BATCH_NODE_MOUNTS_DIR` 处的文件系统装入点的信息。 每个装入点的装载日志文件格式为 `<type>-<mountDirOrDrive>.log`。 例如，位于名为 `test` 的装载目录中的 `cifs` 装入点将包含名为 `cifs-test.log` 的装载日志文件。

## <a name="supported-skus"></a>支持的 SKU

| 发布者 | 产品/服务 | SKU | Azure 文件共享 | Blobfuse | NFS 装载 | CIFS 装载 |
|---|---|---|---|---|---|---|
| 或批处理 | rendering-centos73 | 呈现 | :heavy_check_mark: <br>注意：与 CentOS 7.7 兼容</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS、18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>注意：与 CentOS 7.4 兼容 </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>注意：支持 A_8 或 A_9 存储</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4、7.3、7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012、2016、2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>后续步骤

- 详细了解如何在 [Windows](../storage/files/storage-how-to-use-files-windows.md) 或 [Linux](../storage/files/storage-how-to-use-files-linux.md) 中装载 Azure 文件共享。
- 了解如何使用和装载 [blobfuse](https://github.com/Azure/azure-storage-fuse) 虚拟文件系统。
- 参阅[网络文件系统概述](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)，了解 NFS 及其应用方案。
- 参阅 [Microsoft SMB 协议和 CIFS 协议概述](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 来详细了解 CIFS。
