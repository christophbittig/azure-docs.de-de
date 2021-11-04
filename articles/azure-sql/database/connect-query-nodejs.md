---
title: Abfragen einer Datenbank mithilfe von Node.js
description: Erfahren Sie, wie Sie unter Verwendung von Node.js ein Programm erstellen, das eine Verbindung mit einer Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance herstellt und diese mithilfe von T-SQL-Anweisungen abfragt.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: connect
ms.devlang: nodejs
ms.topic: quickstart
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma, v-masebo
ms.date: 05/19/2021
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-js
ms.openlocfilehash: d9d6ea8cd3ef13fe38ca67caffb01beb64fbb982
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012281"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Schnellstart: Abfragen einer Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance mithilfe von Node.js
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In dieser Schnellstartanleitung verwenden Sie Node.js, um eine Verbindung mit einer Datenbank herzustellen und Daten abzufragen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung dieses Schnellstarts benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  | Aktion | SQL-Datenbank | Verwaltete SQL-Instanz | SQL Server auf Azure-VMs |
  |:--- |:--- |:---|:---|
  | Erstellen| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [BEFEHLSZEILENSCHNITTSTELLE (CLI)](scripts/create-and-configure-database-cli.md) | [BEFEHLSZEILENSCHNITTSTELLE (CLI)](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurieren | [IP-Firewallregel auf Serverebene](firewall-create-server-level-portal-quickstart.md)| [Verbindung von einem virtuellen Computer](../managed-instance/connect-vm-instance-configure.md)|
  |||[Verbindungen von lokalen Computern](../managed-instance/point-to-site-p2s-configure.md) | [Herstellen einer Verbindung mit einer SQL Server-Instanz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Laden der Daten|Laden von Adventure Works gemäß Schnellstartanleitung|[Wiederherstellen von Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Wiederherstellen von Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Wiederherstellen oder Importieren von Adventure Works über eine [BACPAC-Datei](database-import.md) von [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Wiederherstellen oder Importieren von Adventure Works über eine [BACPAC-Datei](database-import.md) von [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||


- [Node.js](https://nodejs.org)-bezogene Software

  # <a name="macos"></a>[macOS](#tab/macos)

  Installieren Sie Homebrew, Node.js und den ODBC-Treiber und SQLCMD mithilfe der Schritte **1.2** und **1.3** des Artikels [Erstellen von Node.js-Apps mit SQL Server unter macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installieren Sie Node.js, den ODBC-Treiber und SQLCMD mithilfe der Schritte **1.2** und **1.3** des Artikels [Erstellen von Node.js-Apps mit SQL Server unter Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installieren Sie Chocolatey, Node.js und den ODBC-Treiber und SQLCMD mithilfe der Schritte **1.2** und **1.3** des Artikels [Erstellen von Node.js-Apps mit SQL Server unter Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Die Skripts in diesem Artikel wurden für die Datenbank **Adventure Works** geschrieben.

> [!NOTE]
> Optional kann Azure SQL Managed Instance verwendet werden.
>
> Verwenden Sie zum Erstellen und Konfigurieren das [Azure-Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) oder die [Befehlszeilenschnittstelle](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), und richten Sie anschließend die Konnektivität ([lokal](../managed-instance/point-to-site-p2s-configure.md) oder [VM](../managed-instance/connect-vm-instance-configure.md)) ein.
>
> Informationen zum Laden von Daten finden Sie unter [Schnellstart: Importieren einer BACPAC-Datei in eine Datenbank in Azure SQL-Datenbank](database-import.md) (mit der Datei für [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)) bzw. unter [Schnellstart: Wiederherstellen einer Datenbank in einer verwalteten Instanz](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-server-connection-information"></a>Ermitteln von Serververbindungsinformationen

Ermitteln Sie die Verbindungsinformationen, die Sie zum Herstellen einer Verbindung mit der Datenbank in Azure SQL-Datenbank benötigen. In den weiteren Verfahren benötigen Sie den vollqualifizierten Server- oder Hostnamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wechseln Sie zur Seite **SQL-Datenbanken** oder **Verwaltete SQL-Instanzen**.

3. Auf der Seite **Übersicht** finden Sie den vollqualifizierten Servernamen für eine Datenbank in Azure SQL-Datenbank neben **Servername** oder den vollqualifizierten Servernamen (oder die IP-Adresse) für Azure SQL Managed Instance bzw. für SQL Server auf einem virtuellen Azure-Computer neben **Host**. Um den Namen des Servers oder Hosts zu kopieren, zeigen Sie darauf, und wählen Sie das Symbol **Kopieren** aus.

> [!NOTE]
> Verbindungsinformationen für SQL Server auf einem virtuellen Azure-Computer finden Sie unter [Herstellen einer Verbindung mit SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-the-project"></a>Erstellen des Projekts

Öffnen Sie eine Eingabeaufforderung, und erstellen Sie einen Ordner namens *sqltest*. Öffnen Sie den erstellten Ordner, und führen Sie den folgenden Befehl aus:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>Hinzufügen von Code zum Abfragen der Datenbank

1. Erstellen Sie in Ihrem bevorzugten Text-Editor eine neue Datei namens *sqltest.js*.

1. Ersetzen Sie ihren Inhalt durch den folgenden Code. Fügen Sie anschließend die entsprechenden Werte für Server, Datenbank, Benutzer und Kennwort hinzu.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };
    
    /* 
        //Use Azure VM Managed Identity to connect to the SQL database
        const config = {
            server: process.env["db_server"],
            authentication: {
                type: 'azure-active-directory-msi-vm',
            },
            options: {
                database: process.env["db_database"],
                encrypt: true,
                port: 1433
            }
        };
        
        //Use Azure App Service Managed Identity to connect to the SQL database
        const config = {
            server: process.env["db_server"],
            authentication: {
                type: 'azure-active-directory-msi-app-service',
            },
            options: {
                database: process.env["db_database"],
                encrypt: true,
                port: 1433
            }
        });

    */

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });
    
    connection.connect();

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });

      connection.execSql(request);
    }
    ```

> [!NOTE]
> Weitere Informationen zur Verwendung der verwalteten Identität für die Authentifizierung finden Sie im Tutorial zum [Zugreifen auf Daten über die verwaltete Identität](../../app-service/tutorial-connect-msi-sql-database.md).

> [!NOTE]
> Im Codebeispiel wird die Beispieldatenbank **AdventureWorksLT** in Azure SQL-Datenbank verwendet.

## <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie das Programm an der Eingabeaufforderung aus.

    ```bash
    node sqltest.js
    ```

1. Vergewissern Sie sich, dass die ersten 20 Zeilen zurückgegeben werden, und schließen Sie das Anwendungsfenster.

## <a name="next-steps"></a>Nächste Schritte

- [Microsoft Node.js Driver for SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server) (Microsoft Node.js-Treiber für SQL Server)

- Herstellen einer Verbindung und Abfragen unter Windows/Linux/macOS mit [.NET Core](connect-query-dotnet-core.md), [Visual Studio Code](connect-query-vscode.md) oder [SSMS](connect-query-ssms.md) (nur Windows)

- [Erste Schritte mit .NET Core unter Windows/Linux/Mac OS unter Verwendung der Befehlszeile](/dotnet/core/tutorials/using-with-xplat-cli)

- Entwerfen Ihrer ersten Datenbank in Azure SQL-Datenbank mithilfe von [.NET](design-first-database-csharp-tutorial.md) oder [SSMS](design-first-database-tutorial.md)
