---
title: Beheben von Datenbankbeschädigungen – Azure Database for MySQL
description: In diesem Artikel erfahren Sie, wie Sie Datenbankbeschädigungs-Probleme in Azure Database for MySQL beheben können.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d9b79ba83613a854610eae8fee6392d6e12ef935
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639722"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Beheben von Datenbankbeschädigungen in Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Datenbankbeschädigungen können eine Downtime Ihrer Anwendung verursachen. Auch um Datenverluste zu vermeiden, ist es wichtig, Beschädigungsprobleme rechtzeitig zu beheben. Wenn eine Datenbankbeschädigung auftritt, wird der folgende Fehler in Ihren Serverprotokollen angezeigt: `InnoDB: Database page corruption on disk or a failed.`

In diesem Artikel erfahren Sie, wie Sie Datenbank- oder Tabellenbeschädigungsprobleme beheben. Azure Database for MySQL verwendet die InnoDB-Engine. Sie enthält automatisierte Überprüfungs- und Reparaturvorgänge. InnoDB wendet auf jede gelesene Seite Prüfsummen an, um beschädigte Seiten zu ermitteln. Bei einer Prüfsummenabweichung wird der MySQL-Server automatisch beendet.

Verwenden Sie die folgenden Optionen, um Datenbankbeschädigungs-Probleme schnell zu beheben.

## <a name="restart-your-mysql-server"></a>Neustarten des MySQL-Servers

In der Regel stellen Sie fest, dass eine Datenbank oder Tabelle beschädigt ist, wenn Ihre Anwendung auf die Datenbank oder Tabelle zugreift. InnoDB bietet einen Mechanismus zur Wiederherstellung nach einem Absturz, der die meisten Probleme beheben kann, wenn der Server neu gestartet wird. Daher kann ein Neustart des Servers dem Server helfen, sich von einem Absturz zu erholen, der zu einem fehlerhaften Zustand der Datenbank geführt hat.

## <a name="use-the-dump-and-restore-method"></a>Verwenden der Sicherungs- und Wiederherstellungsmethode

Sie sollten Beschädigungsprobleme mit einer *Sicherungs- und Wiederherstellungsmethode* lösen. Diese Methode umfasst Folgendes:
1. Zugreifen auf die beschädigte Tabelle.
1. Verwenden des mysqldump-Hilfsprogramms, um eine logische Sicherung der Tabelle zu erstellen. Bei der Sicherung werden die Tabellenstruktur und die darin enthaltenen Daten beibehalten.
1. Erneutes Laden der Tabelle in die Datenbank.

### <a name="back-up-your-database-or-tables"></a>Sichern der Datenbank oder Tabellen

> [!Important]
> - Stellen Sie sicher, dass Sie eine Firewallregel konfiguriert haben, um von Ihrem Clientcomputer aus auf den Server zuzugreifen. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL mithilfe des Azure-Portals](howto-manage-firewall-using-portal.md) und [Verschlüsselte Konnektivität mit der Transport Layer Security (TLS 1.2) in Azure Database for MySQL – Flexible Server](flexible-server/how-to-connect-tls-ssl.md).
> - Verwenden Sie die SSL-Option `--ssl-cert` für „mysqldump“, wenn SSL aktiviert ist.

Erstellen Sie eine Sicherungsdatei über die Befehlszeile mit „mysqldump“. Verwenden Sie diesen Befehl:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parameterbeschreibungen:
- `[ssl-cert=/path/to/pem]`: Der Pfad zum SSL-Zertifikat. Laden Sie das SSL-Zertifikat auf Ihren Clientcomputer herunter, und geben Sie im Befehl den Pfad darin an. Verwenden Sie diesen Parameter nicht, wenn SSL deaktiviert ist.
- `[host]`: Ihr Azure Database for MySQL-Server.
- `[uname]`: Ihr Serveradministrator-Benutzername.
- `[pass]`: Das Kennwort für Ihren Administratorbenutzer.
- `[dbname]`: Der Name Ihrer Datenbank.
- `[backupfile.sql]`: Der Dateiname der Datenbanksicherung.

> [!Important]
> - Verwenden Sie für einzelne Server das Format `admin-user@servername`, um `myserveradmin` in den nachstehenden Befehlen zu ersetzen.
> - Verwenden Sie für flexible Server das Format `admin-user`, um `myserveradmin` in den nachstehenden Befehlen zu ersetzen.

Wenn eine bestimmte Tabelle beschädigt ist, wählen Sie bestimmte Tabellen in Ihrer Datenbank zur Sicherung aus:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Um eine oder mehrere Datenbanken zu sichern, verwenden Sie den Schalter `--database`, und listen Sie die Datenbanknamen durch Leerzeichen getrennt auf:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Wiederherstellen der Datenbank oder Tabellen

Die folgenden Schritte zeigen Ihnen, wie Sie Ihre Datenbank oder Tabellen wiederherstellen können. Nachdem Sie die Sicherungsdatei erstellt haben, können Sie die Tabellen oder Datenbanken mithilfe des mysql-Hilfsprogramms wiederherstellen. Führen Sie den folgenden Befehl aus:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Hier folgt ein Beispiel für die Wiederherstellung von `testdb` aus einer Sicherungsdatei, die mit „mysqldump“ erstellt wurde: 

> [!Important]
> - Verwenden Sie für einzelne Server das Format `admin-user@servername`, um `myserveradmin` im nachstehenden Befehl zu ersetzen.
> - Verwenden Sie für flexible Server das Format ```admin-user```, um `myserveradmin` im nachstehenden Befehl zu ersetzen. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Nächste Schritte
Wenn die oben genannten Schritte nicht zur Lösung des Problems beitragen, können Sie jederzeit den gesamten Server wiederherstellen:
- [Sichern und Wiederherstellen eines Servers in Azure Database for MySQL mit dem Azure-Portal](howto-restore-server-portal.md)
- [Zeitpunktwiederherstellung von Azure Database for MySQL – Flexible Server (Vorschau)](flexible-server/how-to-restore-server-portal.md)



