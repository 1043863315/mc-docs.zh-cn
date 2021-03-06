---
title: Azure SQL 数据仓库的连接字符串 | Microsoft Docs
description: SQL 数据仓库的连接字符串
services: sql-data-warehouse
author: rockboyfor
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 5793c9b3b2ba44dc90490cff1d6140f71e7cf334
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52662137"
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Azure SQL 数据仓库的连接字符串
用户可以使用多个不同的应用程序协议（例如 [ADO.NET][ADO.NET]、[ODBC][ODBC]、[PHP][PHP] 和 [JDBC][JDBC]）连接到 SQL 数据仓库。 下面是每个协议的连接字符串的一些示例。  可以使用 Azure 门户来生成连接字符串。  若要使用 Azure 门户生成连接字符串，请导航到数据库边栏选项卡，在“概要”下单击“显示数据库连接字符串”。

## <a name="sample-adonet-connection-string"></a>示例 ADO.NET 连接字符串
```csharp
Server=tcp:{your_server}.database.chinacloudapi.cn,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>示例 ODBC 连接字符串
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.chinacloudapi.cn,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>示例 PHP 连接字符串
```PHP
Server: {your_server}.database.chinacloudapi.cn,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.chinacloudapi.cn,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.chinacloudapi.cn,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>示例 JDBC 连接字符串
```Java
jdbc:sqlserver://yourserver.database.chinacloudapi.cn:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.chinacloudapi.cn;loginTimeout=30;
```

> [!NOTE]
> 请考虑将连接超时值设置为 300 秒，以便连接可以经受住短时间内不可用。
> 
> 

## <a name="next-steps"></a>后续步骤
若要开始使用 Visual Studio 和其他应用程序查询数据仓库，请参阅 [使用 Visual Studio 进行查询][Query with Visual Studio]。

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
<!-- Update_Description: update meta properties, wording update -->