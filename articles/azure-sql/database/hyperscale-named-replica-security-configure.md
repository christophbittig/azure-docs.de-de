---
title: Konfigurieren der Sicherheit für benannte Replikate, um isolierten Zugriff zu ermöglichen
description: Hier finden Sie Informationen zu den Sicherheitsüberlegungen beim Konfigurieren und Verwalten benannter Replikate, damit ein Benutzer auf das benannte Replikat, aber nicht auf andere Replikate zugreifen kann.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 7/27/2021
ms.openlocfilehash: 88410573dbefdfdedb4628babfffc601b19bed36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355687"
---
# <a name="configure-isolated-access-to-a-hyperscale-named-replica"></a>Konfigurieren von isoliertem Zugriff auf ein benanntes Hyperscale-Replikat
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel erfahren Sie, wie Sie Zugriff auf ein [benanntes Replikat](service-tier-hyperscale-replicas.md) von Azure SQL Hyperscale gewähren, ohne Zugriff auf das primäre Replikat oder auf andere benannte Replikate zu gewähren. Dieses Szenario ermöglicht die Ressourcen- und Sicherheitsisolation eines benannten Replikats, da zum Ausführen des benannten Replikats ein eigener Computeknoten verwendet wird. Dies ist immer dann hilfreich, wenn isolierter schreibgeschützter Zugriff auf eine Azure SQL Hyperscale-Datenbank erforderlich ist. Isoliert bedeutet in diesem Kontext, dass CPU und Arbeitsspeicher nicht gemeinsam vom primären und benannten Replikat genutzt werden. Bei Abfragen, die für das benannte Replikat ausgeführt werden, werden keine Computeressourcen des primären Replikats oder eines anderen Replikats beansprucht, und Prinzipale, die auf das benannte Replikat zugreifen, können nicht auf andere Replikate zugreifen (auch nicht auf das primäre).

## <a name="create-a-login-in-the-master-database-on-the-primary-server"></a>Erstellen einer Anmeldung in der Masterdatenbank auf dem primären Server

Führen Sie in der Datenbank `master` auf dem logischen Server, der die *primäre* Datenbank hostet, Folgendes aus, um eine neue Anmeldung zu erstellen. Verwenden Sie Ihr eigenes sicheres und eindeutiges Kennwort.

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!';
```

Rufen Sie den Hexadezimalwert der SID für die erstellte Anmeldung aus der Systemansicht `sys.sql_logins` ab:

```sql
select sid from sys.sql_logins where name = 'third-party-login';
```

Deaktivieren Sie die Anmeldung. Dadurch wird sichergestellt, dass mit dieser Anmeldung auf keine Datenbank auf dem Server zugegriffen werden kann, auf dem das primäre Replikat gehostet wird.

```sql
alter login [third-party-login] disable;
```

## <a name="create-a-user-in-the-primary-read-write-database"></a>Erstellen eines Benutzers in der primären Datenbank mit Lese-/Schreibzugriff

Stellen Sie nach Erstellung der Anmeldung eine Verbindung mit dem primären Lese-/Schreibreplikat Ihrer Datenbank her – beispielsweise mit „WideWorldImporters“. (Ein Beispielskript für die Wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank in Azure SQL](https://github.com/yorek/azure-sql-db-samples/tree/master/samples/01-restore-database).) Erstellen Sie anschließend einen Datenbankbenutzer für diese Anmeldung:

```sql
create user [third-party-user] from login [third-party-login];
```

Optional können Sie nach dem Erstellen des Datenbankbenutzers die im vorherigen Schritt erstellte Serveranmeldung löschen, um zu verhindern, dass der Anmeldename in irgendeiner Weise reaktiviert werden kann. Stellen Sie eine Verbindung mit der Masterdatenbank auf dem logischen Server her, der die primäre Datenbank hostet, und führen Sie Folgendes aus:

```sql
drop login [third-party-login];
```

## <a name="create-a-named-replica-on-a-different-logical-server"></a>Erstellen eines benannten Replikats auf einem anderen logischen Server

Erstellen Sie einen neuen logischen Azure SQL-Server. Dieser wird verwendet, um den Zugriff auf das benannte Replikat zu isolieren. Gehen Sie dazu wie unter [Erstellen und Verwalten von Servern und Einzeldatenbanken in Azure SQL-Datenbank](single-database-manage.md) beschrieben vor. Wenn Sie ein benanntes Replikat erstellen möchten, muss sich dieser Server in der gleichen Azure-Region befinden wie der Server, der das primäre Replikat hostet.

Verwenden Sie beispielsweise die Azure CLI:

```azurecli
az sql server create -g MyResourceGroup -n MyNamedReplicaServer -l MyLocation --admin-user MyAdminUser --admin-password MyStrongADM1NPassw0rd!
```

Erstellen Sie anschließend ein benanntes Replikat für die primäre Datenbank auf diesem Server. Beispiel mit der Azure CLI:

```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyPrimaryServer --secondary-type Named --partner-database WideWorldImporters_NR --partner-server MyNamedReplicaServer
```

## <a name="create-a-login-in-the-master-database-on-the-named-replica-server"></a>Erstellen einer Anmeldung in der Masterdatenbank auf dem Server des benannten Replikats

Stellen Sie eine Verbindung mit der Datenbank `master` auf dem logischen Server her, der das benannte Replikat hostet, das im vorherigen Schritt erstellt wurde. Fügen Sie die Anmeldung unter Verwendung der SID hinzu, die vom primären Replikat abgerufen wurde:

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!', sid = 0x0...1234;
```

Benutzer und Anwendungen, die `third-party-login` verwenden, können nun eine Verbindung mit dem benannten Replikat herstellen, aber nicht mit dem primären Replikat.

## <a name="grant-object-level-permissions-within-the-database"></a>Erteilen von Berechtigungen auf Objektebene innerhalb der Datenbank

Nachdem Sie die Anmeldeauthentifizierung wie beschrieben eingerichtet haben, können Sie zum Verwalten der Autorisierung reguläre Anweisungen vom Typ `GRANT`, `DENY` und `REVOKE` oder Berechtigungen auf Objektebene innerhalb der Datenbank verwenden. Verweisen Sie in diesen Anweisungen auf den Namen des Benutzers, den Sie in der Datenbank erstellt haben, oder auf eine Datenbankrolle, die diesen Benutzer als Mitglied enthält. Denken Sie daran, diese Befehle für das primäre Replikat auszuführen. Die Änderungen werden an alle sekundären Replikate weitergegeben. Sie sind jedoch nur für das benannte Replikat wirksam, in dem die Anmeldung auf Serverebene erstellt wurde.

Zur Erinnerung: Einem neu erstellten Benutzer werden standardmäßig nur minimale Berechtigungen erteilt. (Er kann beispielsweise nicht auf Benutzertabellen zugreifen.) Wenn Sie für `third-party-user` das Lesen von Daten in einer Tabelle zulassen möchten, müssen Sie explizit die Berechtigung `SELECT` erteilen:

```sql
grant select on [Application].[Cities] to [third-party-user];
```

Anstatt Berechtigungen einzeln für jede Tabelle zu erteilen, können Sie den Benutzer auch der [Datenbankrolle](/sql/relational-databases/security/authentication-access/database-level-roles) `db_datareaders` hinzufügen, um ihm Lesezugriff auf alle Tabellen zu gewähren. Eine weitere Möglichkeit ist die Verwendung von [Schemas](/sql/relational-databases/security/authentication-access/create-a-database-schema) zum [Zulassen des Zugriffs](/sql/t-sql/statements/grant-schema-permissions-transact-sql) auf alle vorhandenen und neuen Tabellen in einem Schema.

## <a name="test-access"></a>Testen des Zugriffs

Sie können diese Konfiguration mit einem beliebigen Clienttool testen und versuchen, eine Verbindung mit dem primären und dem benannten Replikat herzustellen. Mit `sqlcmd` können Sie beispielsweise versuchen, unter Verwendung des Benutzers `third-party-login` eine Verbindung mit dem primären Replikat herzustellen:

```
sqlcmd -S MyPrimaryServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters
```

Dies führt zu einem Fehler, da der Benutzer keine Verbindung mit dem Server herstellen darf:

```
Sqlcmd: Error: Microsoft ODBC Driver 13 for SQL Server : Login failed for user 'third-party-login'. Reason: The account is disabled.
```

Die Verbindungsherstellung mit dem benannten Replikat ist erfolgreich:

```
sqlcmd -S MyNamedReplicaServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters_NR
```

Es werden keine Fehler zurückgegeben, und Abfragen können gemäß den erteilten Berechtigungen auf Objektebene für das benannte Replikat ausgeführt werden.

Weitere Informationen finden Sie unter:

* Logische Azure SQL-Server: [Was ist ein logischer SQL-Server in Azure SQL-Datenbank und Azure Synapse?](logical-servers.md)
* Verwalten von Datenbankzugriff und Anmeldungen: [Autorisieren des Datenbankzugriffs für Azure SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics](logins-create-manage.md)
* Berechtigungen für die Datenbank-Engine: [Berechtigungen (Datenbank-Engine)](/sql/relational-databases/security/permissions-database-engine) 
* Erteilen von Objektberechtigungen: [GRANT (Objektberechtigungen) (Transact-SQL)](/sql/t-sql/statements/grant-object-permissions-transact-sql)



