---
title: SSL - Azure Database for PostgreSQL（单一服务器）
description: 有关如何为 Azure Database for PostgreSQL（单一服务器）配置 SSL 连接的说明和信息。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 03/10/2020
ms.date: 03/30/2020
ms.openlocfilehash: 320a6600e94d318785fbf39401b3ced044e9f2bd
ms.sourcegitcommit: 303a16c7117b6f3495ef0493b4ae8ccb67d7dbba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80342422"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>在 Azure Database for PostgreSQL - 单一服务器中配置 SSL 连接

Azure Database for PostgreSQL 倾向于使用安全套接字层 (SSL) 将客户端应用程序连接到 PostgreSQL 服务。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

默认情况下，PostgreSQL 数据库服务配置为需要 SSL 连接。 如果客户端应用程序不支持 SSL 连接，则可以选择禁用 SSL。

## <a name="enforcing-ssl-connections"></a>强制实施 SSL 连接

对于通过 Azure 门户或 CLI 预配的所有 Azure Database for PostgreSQL 服务器，强制实施 SSL 连接是默认启用的。 

同样，在 Azure 门户中，用户服务器的“连接字符串”设置中预定义了连接字符串，该字符串中包含以通用语言使用 SSL 连接到数据库服务器所需的参数。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

## <a name="configure-enforcement-of-ssl"></a>配置强制实施 SSL

（可选）可以禁用强制实施 SSL 连接。 Azure 建议你始终启用“强制实施 SSL 连接”设置，以增强安全性  。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

访问 Azure Database for PostgreSQL 服务器，并单击“连接安全性”  。 使用切换按钮来启用或禁用“强制实施 SSL 连接”  设置。 然后单击“保存”  。

![连接安全性 - 禁用强制实施 SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

可以通过在“概述”  页中查看“SSL 强制实施状态”  指示器来确认设置。

### <a name="using-azure-cli"></a>使用 Azure CLI

可以通过在 Azure CLI 中分别使用 `Enabled` 或 `Disabled` 值来启用或禁用“ssl-enforcement”  参数。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>确保应用程序或框架支持 SSL 连接

某些使用 PostgreSQL 作为其数据库服务的应用程序框架在安装期间默认不启用 SSL。 如果 PostgreSQL 服务器强制实施 SSL 连接，但应用程序未配置 SSL，则应用程序可能无法连接到数据库服务器。 请查阅应用程序文档，了解如何启用 SSL 连接。

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要证书验证才可启用 SSL 连接性的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 请参阅以下步骤获取 .cer 文件，解码证书并将其绑定到应用程序。

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>从证书颁发机构 (CA) 下载证书文件 
可在[此处](https://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt)找到通过 SSL 与 Azure Database for PostgreSQL 服务器通信所需的证书。 本地下载证书文件。

### <a name="install-a-cert-decoder-on-your-machine"></a>在计算机上安装证书解码器 
可以使用 [OpenSSL](https://github.com/openssl/openssl) 来解码应用程序安全连接到数据库服务器所需的证书文件。 若要了解如何安装 OpenSSL，请参阅 [OpenSSL 安装说明](https://github.com/openssl/openssl/blob/master/INSTALL)。 


### <a name="decode-your-certificate-file"></a>解码证书文件
下载的根 CA 文件采用加密格式。 使用 OpenSSL 解码证书文件。 要执行此操作，请运行此 OpenSSL 命令：

```
openssl x509 -inform DER -in DigiCertGlobalRootCA.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>连接到具有 SSL 证书身份验证的 Azure Database for PostgreSQL
现已成功解码证书，可通过 SSL 安全连接到数据库服务器。 要允许服务器证书验证，必须将证书放在用户主目录中的 ~/.postgresql/root.crt 文件中。 （在 Microsoft Windows 上，该文件的名称是 %APPDATA%\postgresql\root.crt.）。 

#### <a name="connect-using-psql"></a>使用 psql 进行连接
以下示例演示如何使用 psql 命令行实用程序成功连接到 PostgreSQL 服务器。 使用创建的 `root.crt` 文件和 `sslmode=verify-ca` 或 `sslmode=verify-full` 选项。

使用 PostgreSQL 命令行接口执行以下命令：
```bash
psql "sslmode=verify-full sslrootcert=root.crt host=mydemoserver.postgres.database.chinacloudapi.cn dbname=postgres user=mylogin@mydemoserver"
```

> [!TIP]
> 确认传递给 `sslrootcert` 的值与你保存的证书的文件路径匹配。

## <a name="tls-connectivity-in-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 单一服务器中的 TLS 连接

对于使用传输层安全性 (TLS) 连接到数据库服务器的客户端，Azure Database for PostgreSQL - 单一服务器支持加密。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现安全的网络连接，使你能够满足合规性要求。

### <a name="tls-settings"></a>TLS 设置

对于连接到 Azure Database for PostgreSQL 单一服务器的客户端，客户现在能够强制为其使用 TLS 版本。 若要使用 TLS 选项，请使用“最低 TLS 版本”选项设置。  此选项设置允许以下值：

|  最低 TLS 设置             | 支持的 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled（默认值） | 不需要 TLS                      |
| TLS1_0                           | TLS 1.0、TLS 1.1、TLS 1.2 及更高版本 |
| TLS1_1                           | TLS 1.1、TLS 1.2 及更高版本          |
| TLS1_2                           | TLS 版本 1.2 及更高版本           |


例如，将此最低 TLS 设置版本设置为 TLS 1.0 意味着服务器将允许使用 TLS 1.0、1.1 和 1.2 + 的客户端进行连接。 或者，将此选项设置为 1.2 意味着仅允许使用 TLS 1.2 的客户端进行连接，并且将拒绝 TLS 1.0 和 TLS 1.1 的所有连接。

> [!Note] 
> Azure Database for PostgreSQL 单一服务器默认对所有新服务器禁用 TLS。
>
> 目前，Azure Database for PostgreSQL 支持的 TLS 版本为 TLS 1.0、1.1 和 1.2。

若要了解如何为 Azure Database for PostgreSQL 单一服务器指定 TLS 设置，请参阅[如何配置 TLS 设置](concepts-ssl-connection-security.md)。

## <a name="next-steps"></a>后续步骤

在 [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)中查看各种应用程序连接选项。
