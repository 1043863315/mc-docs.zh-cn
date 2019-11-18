---
title: 将 Azure VM 添加到现有可用性集的可支持性 | Azure
description: 将 Azure VM 添加到现有可用性集的可支持性。
services: virtual-machines-linux
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
origin.date: 06/15/2018
ms.date: 11/11/2019
ms.author: v-yeche
ms.openlocfilehash: dfe1d223bfd48b4eab1cbcd4369a780071fb6334
ms.sourcegitcommit: 1fd822d99b2b487877278a83a9e5b84d9b4a8ce7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116876"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>将 Azure VM 添加到现有可用性集的可支持性

将新的虚拟机 (VM) 添加到现有可用性集时，可能偶尔会遇到限制。 以下图表详细说明了可以在同一可用性集中混合的 VM 系列。

以下是混合不同类型的 VM 的可支持性矩阵：

<!--PENDING FOR Dv3 GA ANOUNCEMENT -->

系列和可用性集|第二个 VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|第一个 VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

<!-- Checking the last Row and Coloumn in the above matrix -->
<!--PENDING FOR Dv3 GA ANOUNCEMENT -->

所有其他系列都不能在同一可用性集中，因为它们需要特定的硬件。

<!--Not Available on A8/A9 -->

<!--Update_Description: update meta properties -->
