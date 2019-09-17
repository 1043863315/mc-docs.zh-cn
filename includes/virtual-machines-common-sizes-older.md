---
title: include 文件
description: include 文件
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 08/15/2019
ms.date: 09/16/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: e9a039e9e2d07ac433c44eb70e28879a334a9c45
ms.sourcegitcommit: 43f569aaac795027c2aa583036619ffb8b11b0b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921002"
---
本部分提供了有关先前几代虚拟机大小的信息。 这些大小仍受支持，但无法接收更多的容量。 有更新的或备用的大小，这些大小已公开发布。 请参阅 [Azure 中 Windows 虚拟机的大小](../articles/virtual-machines/windows/sizes.md)或 [Azure 中 Linux 虚拟机的大小](../articles/virtual-machines/linux/sizes.md)，以便选择最适合自己需求的 VM 大小。  

若要详细了解如何重设 Linux VM 大小，请参阅[使用 Azure CLI 重设 Linux 虚拟机大小](../articles/virtual-machines/linux/change-vm-size.md)。 如果你使用 Windows VM，且首选使用 PowerShell，请参阅[重设 Windows VM 大小](../articles/virtual-machines/windows/resize-vm.md)。  

<br />

### <a name="basic-a"></a>基本 A  

**更新的大小建议**：[Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

高级存储：不支持

高级存储缓存：不支持

基本层大小主要用于开发工作负荷，以及其他不需要负载均衡、自动缩放或内存密集型虚拟机的应用程序。

|大小 - 大小\名称 | vCPU |内存|NIC 数（最大值）|最大临时磁盘大小 |最大 数据磁盘（每个 1023 GB）|最大 IOPS（每个磁盘 300 次）|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1.75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3.5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br />

### <a name="standard-a0---a4-using-cli-and-powershell"></a>使用 CLI 和 PowerShell 的标准 A0 - A4

在经典部署模型中，CLI 和 PowerShell 中的一些 VM 大小名称略有不同：

* Standard_A0 是特小型
* Standard_A1 是小型
* Standard_A2 是中型
* Standard_A3 是大型
* Standard_A4 是超大型

### <a name="a-series"></a>A 系列  

**更新的大小建议**：[Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU：50-100

高级存储：不支持

高级存储缓存：不支持

| 大小 | vCPU | 内存：GiB | 临时存储 (HDD)：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 个 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> A0 大小在物理硬件上过度订阅。 仅针对此特定大小，其他客户部署可能影响正在运行的工作负荷的性能。 以下概述的相对性能为预期的基准，受限于近似变化性的 15%。

<br />

<!--Not Available on ### A-series - compute-intensive instances-->

### <a name="d-series"></a>D 系列  

**更新的大小建议**：[Dv3 系列](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU：160-250 <sup>1</sup>

高级存储：不支持

高级存储缓存：不支持

| 大小         | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000/46/23                                           | 4/4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000/93/46                                           | 8/8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 4000                     |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br />

### <a name="d-series---memory-optimized"></a>D 系列 - 内存优化  

**更新的大小建议**：[Dv3 系列](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU：160-250 <sup>1</sup>

高级存储：不支持

高级存储缓存：不支持

| 大小         | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000/93/46                                           | 8/8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000/750/375                                        | 64/64x500                       | 8 / 8000                |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br />

### <a name="ds-series"></a>DS 系列  

**更新的大小建议**：[DSv3 系列](/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU：160-250 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<sup>1</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br />

### <a name="ds-series---memory-optimized"></a>DS 系列 - 内存优化  

**更新的大小建议**：[DSv3 系列](/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU：160-250 <sup>1,2</sup>

高级存储：支持

高级存储缓存：支持

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> DS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[为实现高性能而设计](../articles/virtual-machines/windows/premium-storage-performance.md)。   
<sup>2</sup> VM 系列可以在下述 CPU 之一上运行：2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 或 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br />

<!--Not Available on ### Ls-series-->
<!--Not Avaialble on ### GS-series -->
<!--Not Available on ### G-series -->

