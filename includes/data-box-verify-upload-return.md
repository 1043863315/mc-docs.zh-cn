---
author: WenJason
ms.service: databox
ms.subservice: pod
ms.topic: include
origin.date: 09/19/2019
ms.date: 12/02/2019
ms.author: v-jay
ms.openlocfilehash: 91240237009362ea1351ec232be82f5fe45e7208
ms.sourcegitcommit: 481542df432d52b7d4823811cef94772e4e0f192
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "80634483"
---
当 Microsoft 收到并扫描该设备时，订单状态将更新为“已接收”。  然后，该设备将经受物理验证，以确定是否存在损坏或篡改迹象。

验证完成后，Data Box 将连接到 Azure 数据中心的网络。 数据复制将自动开始。 根据数据大小，复制操作可能需要几小时到几天的时间才能完成。 可以在门户中监视复制作业的进度。

复制完成后，订单状态将更新为“已完成”。 

从源中删除数据之前，请验证数据是否已上传到 Azure。 数据可能位于以下位置：

- Azure 存储帐户。 将数据复制到 Data Box 时，会根据类型将数据将上传到 Azure 存储帐户中的以下路径之一。

  - 对于块 blob 和页 blob：`https://<storage_account_name>.blob.core.chinacloudapi.cn/<containername>/files/a.txt`
  - 对于 Azure 文件：`https://<storage_account_name>.file.core.chinacloudapi.cn/<sharename>/files/a.txt`

    或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

- 托管磁盘资源组。 创建托管磁盘时，会将 VHD 作为页 Blob 上传，然后将其转换为托管磁盘。 托管磁盘会附加到在创建订单时指定的资源组。 

    - 如果已成功复制到 Azure 中的托管磁盘，则可转到  Azure 门户中的“订单详细信息”，记下为托管磁盘指定的资源组。

        ![标识托管磁盘资源组](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        转到记下的资源组，找到托管磁盘。

        ![附加到资源组的托管磁盘](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - 如果复制了 VHDX 或动态/差分 VHD，则可以将 VHDX/VHD 作为页 Blob 上传到临时存储帐户，但无法将 VHD 转换为托管磁盘。 转到临时存储帐户 >“Blob”，然后选择适当的容器 - 标准 SSD、标准 HDD 或高级 SSD。  VHD 会作为页 Blob 上传到临时存储帐户中。


## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。


## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 先决条件
> * 准备交付
> * 将 Data Box 寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box 中擦除数据

请转到以下文章，了解如何通过本地 Web UI 管理 Data Box。

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Azure Data Box](../articles/databox/data-box-local-web-ui-admin.md)