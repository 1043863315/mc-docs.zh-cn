---
title: include 文件
description: include 文件
services: virtual-wan
author: rockboyfor
ms.service: virtual-wan
ms.topic: include
origin.date: 11/04/2019
ms.date: 03/02/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 9a78836bd4aba26a5ec6e3f79ec4bc0a5520cf63
ms.sourcegitcommit: 69cadf1fa0ed81751c48fbce919a6bb44b1053ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209170"
---
1. 找到创建的虚拟 WAN。 在虚拟 WAN 页上的“连接”部分下，选择“中心”   。
2. 在“中心”页上，选择“+ 新建中心”以打开“创建虚拟中心”页   。

    ![基础知识](./media/virtual-wan-tutorial-hub-include/basics.png "基础知识")
3. 在“创建虚拟中心”页上的“基本”选项卡上，请填写以下字段   ：

    **项目详细信息**

    * 区域（之前称为位置）
    * 名称
    * 中心专用地址空间。 用于创建中心的最小地址空间是 /24，这表示在创建过程中从 /25 到 /32 的任何范围都将产生错误。
4. 在完成时选择“下一步:  站点到站点”。

    ![站点到站点](./media/virtual-wan-tutorial-hub-include/site-to-site.png "站点到站点")

5. 在“站点到站点”选项卡上填写以下字段： 

    * 选择“是”以创建站点到站点 VPN。 
    * 暂时无法在虚拟中心编辑“AS 编号”字段。
    * 从下拉列表中选择“网关缩放单元”  。 缩放单元允许选择在虚拟中心内创建的、要将站点连接到的 VPN 网关的聚合吞吐量。 如果选择 1 个 500 Mbps 的缩放单元，则表示会创建两个实例以实现冗余，每个实例的最大吞吐量为 500 Mbps。 例如，如果你有 5 个分支，每个分支执行 10 Mbps 的 IO，则前端的聚合吞吐量需要达到 50 Mbps。 应在评估支持中心的分支数量所需的容量后，再规划 Azure VPN 网关的聚合容量。
6. 选择“查看 + 创建”以进行验证  。
7. 选择“创建”以创建中心  。 30 分钟后，“刷新”以在“中心”页上查看该中心   。 选择“转到资源”导航到资源  。

<!-- Update_Description: update meta properties, wording update, update link -->