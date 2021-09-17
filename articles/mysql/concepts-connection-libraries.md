---
title: Datenverbindungsbibliotheken für Azure Database for MySQL
description: In diesem Artikel werden die einzelnen Bibliotheken bzw. Treiber aufgelistet, mit denen Clientprogramme Verbindungen zu Azure-Datenbank für MySQL herstellen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 8/3/2020
ms.openlocfilehash: 04353f49acababbd9b7d4a164abe2d744c7052a0
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639770"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Datenverbindungsbibliotheken für Azure-Datenbank für MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

In diesem Artikel werden die einzelnen Bibliotheken bzw. Treiber aufgelistet, mit denen Clientprogramme Verbindungen zu Azure-Datenbank für MySQL herstellen können.

## <a name="client-interfaces"></a>Clientschnittstellen
MySQL bietet standardmäßige Datenbanktreiberkonnektivität zur Verwendung von MySQL mit Anwendungen und Tools, die mit den Branchenstandards ODBC und JDBC kompatibel sind. Alle Systeme, die mit ODBC oder JDBC kompatibel sind, können MySQL verwenden.

| **Sprache** | **Plattform** | **Zusätzliche Ressourcen** | **Download** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Systemeigener MySQL-Treiber für PHP – mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Download](https://secure.php.net/downloads.php) |
| ODBC | Windows-, Linux-, macOS X- und Unix-Plattformen | [MySQL-Connector-/ODBC-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-odbc/en/) | [Download](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL-Connector-/Net-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-net/en/) | [Download](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Plattformunabhängig | [MySQL-Connector-/J 5.1-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Download](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, macOS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Download](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, macOS X | [MySQL-Connector-/Python-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-python/en/) | [Download](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, macOS X | [MySQL-Connector-/C++-Entwicklerhandbuch](https://dev.mysql.com/doc/connector-cpp/en/) | [Download](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, macOS X | [MySQL-Connector-/C-Entwicklerhandbuch](https://dev.mysql.com/doc/c-api/8.0/en/) | [Download](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows-, Linux-, macOS X- und Unix-Plattformen | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Download](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Nächste Schritte
Lesen Sie diese Schnellstarts, in denen beschrieben wird, wie Azure Database for MySQL mit der gewünschten Sprache verbunden und abgefragt wird:

- [PHP](./connect-php.md)
- [Java](./connect-java.md)
- [.NET (C#)](./connect-csharp.md)
- [Python](./connect-python.md)
- [Node.JS](./connect-nodejs.md)
- [Ruby](./connect-ruby.md)
- [C++](connect-cpp.md)
- [Go](./connect-go.md)
