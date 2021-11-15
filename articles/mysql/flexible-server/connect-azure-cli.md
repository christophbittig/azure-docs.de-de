---
title: 'Schnellstart: Herstellen einer Verbindung unter Verwendung der Azure CLI: Azure Database for MySQL Flexible Server'
description: In dieser Schnellstartanleitung erhalten Sie Informationen zu verschiedenen Möglichkeiten, mithilfe der Azure CLI eine Verbindung mit Azure Database for MySQL Flexible Server herzustellen.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: 26c25afc997ee86f0fe23f944ae5afad34269e92
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468117"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Schnellstart: Verbinden und Abfragen mit der Azure CLI mit Azure Database for MySQL Flexible Server

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In dieser Schnellstartanleitung wird gezeigt, wie Sie sich mit der Azure CLI über ```az mysql flexible-server connect``` mit einer Instanz von Azure Database for MySQL Flexible Server verbinden und mit dem Befehl ```az mysql flexible-server execute``` eine einzelne Abfrage oder SQL-Datei ausführen. Mit diesem Befehl können Sie die Konnektivität mit dem Datenbankserver testen und Abfragen ausführen. Sie können mithilfe des interaktiven Modus auch mehrere Abfragen ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- Installieren der aktuellen Version der [Azure CLI](/cli/azure/install-azure-cli) (mindestens 2.20.0)
- Anmelden unter Verwendung der Azure CLI mit dem Befehl ```az login```
- Aktivieren der Parameterpersistenz mit ```az config param-persist on```. Mithilfe der Parameterpersistenz können Sie den lokalen Kontext verwenden, ohne zahlreiche Argumente wie Ressourcengruppe, Speicherort usw. wiederholen zu müssen.

## <a name="create-a-mysql-flexible-server"></a>Erstellen einer MySQL Flexible Server-Instanz

Das erste, was wir erstellen, ist ein verwalteter MySQL-Server. Führen Sie in [Azure Cloud Shell](https://shell.azure.com/) das folgende Skript aus, und notieren Sie sich den **Servernamen**, den **Benutzernamen** und das **Kennwort**, die mit diesem Befehl generiert werden.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Sie können zur Anpassung zusätzliche Argumente für diesen Befehl angeben. Sehen Sie sich alle Argumente für [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create) an.

## <a name="create-a-database"></a>Erstellen einer Datenbank
Führen Sie den folgenden Befehl aus, um eine Datenbank (**newdatabase**) zu erstellen, wenn Sie noch keine erstellt haben.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Anzeigen aller Argumente
Sie können alle Argumente für diesen Befehl mit dem Argument ```--help``` anzeigen.

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testen der Verbindung des Datenbankservers
Führen Sie das folgende Skript aus, um die Verbindung mit der Datenbank aus Ihrer Entwicklungsumgebung zu testen und zu überprüfen:

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Beispiel:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Für eine erfolgreiche Verbindung sollte die folgende Ausgabe angezeigt werden:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Konnte die Verbindung nicht hergestellt werden, versuchen Sie Folgendes:
- Überprüfen Sie, ob Port 3306 auf dem Clientcomputer geöffnet ist.
- Überprüfen Sie, ob Benutzername und Kennwort des Serveradministrators korrekt sind.
- Überprüfen Sie, ob die Firewallregel für Ihren Clientcomputer konfiguriert wurde.
- Wenn Sie Ihren Server mit privatem Zugriff in einem virtuellen Netzwerk konfiguriert haben, vergewissern Sie sich, dass sich Ihr Clientcomputer im gleichen virtuellen Netzwerk befindet.

## <a name="run-multiple-queries-using-interactive-mode"></a>Ausführen mehrerer Abfragen mithilfe des interaktiven Modus
Sie können mehrere Abfragen mithilfe des **interaktiven** Modus ausführen. Führen Sie zum Aktivieren des interaktiven Modus den folgenden Befehl aus:

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Beispiel:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Die **MySQL**-Shelloberfläche wird wie folgt angezeigt:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

## <a name="run-single-query"></a>Ausführen einer einzelnen Abfrage
Führen Sie den folgenden Befehl aus, um eine einzelne Abfrage mit dem ```--querytext```-Argument ```-q``` auszuführen.

```azurecli
az mysql flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Beispiel:**
```azurecli
az mysql flexible-server execute -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Die folgende Ausgabe sollte angezeigt werden:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-sql-file"></a>Ausführen einer SQL-Datei
Sie können eine SQL-Datei mit dem Befehl mit dem ```--file-path```-Argument ```-q``` ausführen.

```azurecli
az mysql flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --file-path "<file-path>"
```

**Beispiel:**
```azurecli
az mysql flexible-server execute -n mysqldemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -f "./test.sql"
```

Die folgende Ausgabe sollte angezeigt werden:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Running sql file '.\test.sql'...
Successfully executed the file.
Closed the connection to mysqldemoserver.
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
* [Herstellen einer Verbindung mit Azure Database for MySQL – Flexible Server mit verschlüsselten Verbindungen](how-to-connect-tls-ssl.md)
* [Verwalten des Servers](./how-to-manage-server-cli.md)

