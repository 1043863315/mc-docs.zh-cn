---
title: 第 2 代 VM 的 Azure 支持（预览版）| Azure
description: 第 2 代 VM 的 Azure 支持概述
services: virtual-machines-windows
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
origin.date: 10/29/2019
ms.date: 11/11/2019
ms.author: v-yeche
ms.openlocfilehash: e814873989b997b273e77d902d329e3c0b6aa8dd
ms.sourcegitcommit: 73715ebbaeb96e80046142b8fe5bbc117d85b317
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2019
ms.locfileid: "74592431"
---
<!--Verify sucessfully-->
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Azure 上的第 2 代 VM 支持（预览版）

> [!IMPORTANT]
> 第 2 代 VM 的 Azure 支持目前以预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅[适用于 Azure 预览版的补充使用条款](https://www.azure.cn/support/legal/subscription-agreement/)。

Azure 中现已推出第 2 代虚拟机 (VM) 支持预览版。 创建虚拟机后无法更改其代系，因此，在选择代系之前，请先查看本页上的注意事项。 

第 2 代 VM 支持第 1 代 VM 所不支持的某些关键特性。 这些特性包括更大的内存、Intel Software Guard Extensions (Intel SGX) 和虚拟化持久性内存 (vPMEM)。 第 2 代 VM 还具有 Azure 中不支持的某些特性。 有关详细信息，请参阅[特性和功能](#features-and-capabilities)部分。

第 2 代 VM 使用新的基于 UEFI 的启动体系结构，而不是使用第 1 代 VM 所用的基于 BIOS 的体系结构。 与第 1 代 VM 相比，第 2 代 VM 的启动时间和安装时间可能有所改善。 有关第 2 代 VM 的概述以及第 1 代与第 2 代之间的某些差异，请参阅[应在 Hyper-V 中创建第 1 代还是第 2 代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第 2 代 VM 的大小

Azure 中的所有 VM 大小都支持第 1 代 VM。 Azure 目前为以下选定 VM 系列提供第 2 代预览版支持：

* [B 系列](/virtual-machines/windows/b-series-burstable)
* [Dsv2 系列](/virtual-machines/windows/sizes-general#dsv2-series)和 [Dsv3 系列](/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 系列](/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 系列](/virtual-machines/windows/sizes-compute#fsv2-series-1)

    <!--Not Available on * [GS-series](/virtual-machines/windows/sizes-previous-gen#gs-series)-->
    <!--Not Available on * [Ls-series](/virtual-machines/windows/sizes-previous-gen#ls-series)-->
    <!--Not Available on * [Lsv2-series](/virtual-machines/windows/sizes-storage#lsv2-series)-->
    <!--Not Available on * [Mv2-series](/virtual-machines/windows/sizes-memory#mv2-series)-->
    
* [NCv3 系列](/virtual-machines/windows/sizes-gpu#ncv3-series)

    <!--Not Available on * [NCv2-series](/virtual-machines/windows/sizes-gpu#ncv2-series)-->
    <!--Not Available on * [ND-series](/virtual-machines/windows/sizes-gpu#nd-series)-->
    
<!--Not Available [!NOTE] due to Mv2 series not available on Azure China Cloud-->

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure 市场中的第 2 代 VM 映像

第 2 代 VM 支持以下市场映像：

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

<!--MOONCAKE: CUSTOMIZE-->

> [!NOTE]
> 可将 `(Gen2)` 后缀追加到映像说明，然后在市场中搜索相应的映像。<br />
> 例如：`Windows Server 2019 Datacenter (Gen2)`

<!--MOONCAKE: CUSTOMIZE-->

## <a name="on-premises-vs-azure-generation-2-vms"></a>本地与Azure 第 2 代 VM

Azure 目前不支持本地 Hyper-V 对第 2 代 VM 所支持的某些特性。

| 第 2 代的特性                | 本地 Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| 安全启动                         | :heavy_check_mark:  | :x:   |
| 屏蔽 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 基于虚拟化的安全性 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特性和功能

### <a name="generation-1-vs-generation-2-features"></a>第 1 代和第 2 代的特性

| 功能 | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| 启动             | PCAT                      | UEFI                               |
| 磁盘控制器 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 仅限支持高级存储的 VM |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 代与第 2 代的功能

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁盘 > 2 TB                    | :x:                | :heavy_check_mark: |
| 自定义磁盘/映像/交换 OS         | :heavy_check_mark: | :heavy_check_mark: |
| 虚拟机规模集支持 | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :x:                |
| 备份/还原                    | :heavy_check_mark: | :heavy_check_mark: |
| 共享映像库              | :heavy_check_mark: | :heavy_check_mark: |
| Azure 磁盘加密             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>创建第 2 代 VM

### <a name="marketplace-image"></a>市场映像

在 Azure 门户或 Azure CLI 中，可以从支持 UEFI 启动的市场映像创建第 2 代 VM。

#### <a name="azure-portal"></a>Azure 门户

Windows 和 SLES 的第 2 代映像包括在与第 1 代映像相同的服务器产品中。 从流的角度来看，这意味着你可以从门户中为 VM 选择产品/服务和 SKU。 如果 SKU 同时支持第 1 代和第 2 代映像，则可以从 VM 创建流程的“高级”  选项卡中选择“创建第 2 代 VM”。

目前，以下 SKU 同时支持第 1 代映像和第 2 代映像：

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

当你选择 Windows Server SKU 作为产品时，在“高级”  选项卡中，可以选择创建**第 1 代** (BIOS) 或**第 2 代** (UEFI) VM。 如果选择“第 2 代”  ，请确保[第 2 代 VM 支持](#generation-2-vm-sizes)在“基本信息”  选项卡中选择的 VM 大小。

![选择“第 1 代”或“第 2 代”VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

也可以使用 PowerShell 通过直接引用第 1 代或第 2 代 SKU 来创建 VM。

例如，使用以下 PowerShell cmdlet 获取 `WindowsServer` 产品中的 SKU 列表。

```powershell
Get-AzVMImageSku -Location chinanorth2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

如果要创建使用 Windows Server 2012 作为操作系统的 VM，则将选择第 1 代 (BIOS) 或第 2 代 (UEFI) VM SKU，如下所示：

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

有关最新的受支持市场映像列表，请参阅[特性和功能](#features-and-capabilities)部分。

### <a name="managed-image-or-managed-disk"></a>托管映像或托管磁盘

可以从托管映像或托管磁盘创建第 2 代 VM，就像创建第 1 代 VM 一样，

### <a name="virtual-machine-scale-sets"></a>虚拟机规模集

还可以使用虚拟机规模集创建第 2 代 VM。 在 Azure CLI 中使用 Azure 规模集创建第 2 代 VM。

## <a name="frequently-asked-questions"></a>常见问题

* **第 2 代 VM 是否已在所有 Azure 区域中推出？**  
    是的。 但是，并非所有[第 2 代 VM 大小](#generation-2-vm-sizes)都已在每个区域中推出。 第 2 代 VM 的可用性取决于 VM 大小的可用性。

* **第 1 代与第 2 代 VM 的价格是否有差别？**  
    否。

* **我有一个来自本地第 2 代 VM 的 .vhd 文件。我可以使用该 .vhd 文件在 Azure 中创建第 2 代 VM 吗？**
    
    是的，你可以将第 2 代 .vhd 文件带到 Azure，并使用该文件创建第 2 代 VM。 请使用以下步骤来执行该操作：
    
    1. 将 .vhd 上传到你要创建 VM 的同一区域中的存储帐户。
    1. 从 .vhd 文件创建托管磁盘。 将“Hyper-V Generation”属性设置为 V2。 以下 PowerShell 命令在创建托管磁盘时设置“Hyper-V Generation”属性。

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.chinacloudapi.cn/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 磁盘可用后，请通过附加此磁盘来创建 VM。 创建的 VM 将是第 2 代 VM。
    创建第 2 代 VM 时，可以选择通用化此 VM 的映像。 通过通用化映像，可以使用它来创建多个 VM。

* **如何增大 OS 磁盘的大小？*    *  
    大于 2 TB 的 OS 磁盘是第 2 代 VM 的新配置。 默认情况下，第 2 代 VM 的 OS 磁盘小于 2 TB。 可将磁盘大小增大至 4 TB（建议的最大大小）。 使用 Azure CLI 或 Azure 门户增大 OS 磁盘大小。 有关如何以编程方式扩展磁盘的信息，请参阅[调整磁盘大小](expand-os-disk.md)。

    若要在 Azure 门户中增大 OS 磁盘大小：

    1. 在 Azure 门户中，转到 VM 属性页。
    1. 若要关闭并解除分配 VM，请选择“停止”按钮。 
    1. 在“磁盘”部分，选择要增大的 OS 磁盘。 
    1. 在“磁盘”部分，选择“配置”并将“大小”更新为所需的值。   
    1. 返回到 VM 属性页并**启动** VM。

    你可能会看到一条警告，指出 OS 磁盘大于 2 TB。 该警告不适用于第 2 代 VM。 但是，不建议使用大于 4 TB 的 OS 磁盘大小。 

* **第 2 代 VM 是否支持加速网络？**  
    是的。 有关详细信息，请参阅[创建具有加速网络的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **第 2 代是否支持 VHDX？**  
    否，第 2 代 VM 仅支持 VHD。

* **第2代 VM 是否支持 Azure 超级磁盘存储？**  
    是的。

* **是否可将 VM 从第 1 代迁移到第 2 代？**  
    否，创建 VM 后无法更改其代系。 如果需要在 VM 代系之间切换，请创建一个不同代系的新 VM。

## <a name="next-steps"></a>后续步骤

* 了解 [Hyper-V 中的第 2 代虚拟机](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 了解如何[准备 VHD](prepare-for-upload-vhd-image.md) 以从本地系统上传到 Azure。

<!-- Update_Description: wording update -->
