---
title: GPU 优化虚拟机大小
description: 列出 Azure 中适用于 Linux 虚拟机的各种 GPU 优化大小。 针对此系列中的大小列出了 vCPU、数据磁盘和 NIC 的数量，以及存储吞吐量和网络带宽。
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
origin.date: 06/11/2019
ms.date: 02/10/2020
ms.author: v-yeche
ms.openlocfilehash: 53253e6dcb9aa880330515a38144e6f3c8a2105e
ms.sourcegitcommit: ada94ca4685855f58616e4bf1dd5ca757878dfdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77428373"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 优化虚拟机大小

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>支持的分发和驱动程序

若要利用运行 Linux 的 Azure N 系列 VM 的 GPU 功能，必须安装 NVIDIA GPU 驱动程序。 [NVIDIA GPU 驱动程序扩展](../extensions/hpccompute-gpu-linux.md)可在 N 系列 VM 上安装适当的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure CLI 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的分发版和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](../extensions/hpccompute-gpu-linux.md)。 有关 VM 扩展的常规信息，请参阅 [Azure 虚拟机扩展和功能](../extensions/overview.md)。

如果选择手动安装 NVIDIA GPU 驱动程序，请参阅[适用于 Linux 的 N 系列 GPU 驱动程序安装](n-series-driver-setup.md)，了解受支持的分发版、驱动程序以及安装和验证步骤。

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* 不应在 Ubuntu NC VM 上安装 X server 或使用 `Nouveau` 驱动程序的其他系统。 在安装 NVIDIA GPU 驱动程序之前，需要禁用 `Nouveau` 驱动程序。  

## <a name="other-sizes"></a>其他大小
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)

    <!--Not Available on - [Storage optimized](sizes-storage.md)-->
    <!--Not Available on - [High performance compute](sizes-hpc.md)-->
    
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤
了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。

<!--Update_Description: update meta properties -->
