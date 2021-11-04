---
title: 'Behandeln häufiger Fehler: Azure Database for MySQL'
description: Erfahren Sie, wie Sie häufige Migrationsfehler bei neuen Benutzern des Azure Database for MySQL-Diensts behandeln.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 5/21/2021
ms.openlocfilehash: e66940b74110e5870acfffb255e7de4942ad1b71
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453400"
---
# <a name="commonly-encountered-errors-during-or-post-migration-to-azure-database-for-mysql"></a>Häufig während oder nach der Migration zum Azure Database for MySQL-Dienst auftretende Fehler

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL ist ein vollständig verwalteter Dienst, der auf der Communityversion von MySQL basiert. Die MySQL-Umgebung in einer verwalteten Dienstumgebung kann sich von der MySQL-Ausführung in Ihrer eigenen Umgebung unterscheiden. Dieser Artikel enthält Informationen zu einigen der häufigen Fehler, die bei Benutzern auftreten können, die zu Azure Database for MySQL migrieren oder zum ersten Mal mit Azure Database for MySQL entwickeln.

## <a name="common-connection-errors"></a>Allgemeine Verbindungsfehler

### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>FEHLER 1184 (08S01): Aborted connection 22 to db: 'db-name' user: 'user' host: 'hostIP' (init_connect command failed) (Verbindung 22 mit DB "<Datenbankname>" abgebrochen. Benutzer: <Benutzer>, Host: <Host-IP-Adresse>. (Fehler beim Befehl "init_connect".))

Der obige Fehler tritt nach erfolgreicher Anmeldung, aber vor Ausführung eines Befehls nach Einrichtung der Sitzung auf. Die obige Meldung gibt an, dass ein falscher Wert für den Serverparameter `init_connect` festgelegt wurde, was dazu führt, dass die Sitzung nicht erfolgreich initialisiert werden kann.

Es gibt einige Serverparameter wie etwa `require_secure_transport`, die auf Sitzungsebene nicht unterstützt werden. Daher kann der Versuch, die Werte dieser Parameter mithilfe von `init_connect` zu ändern, bei der Verbindungsherstellung mit dem MySQL-Server zum Fehler 1184 führen:

mysql> show databases; ERROR 2006 (HY000): MySQL server has gone away No connection. Trying to reconnect... Connection id:    64897 Current database: *** NONE *** ERROR 1184 (08S01): Aborted connection 22 to db: 'db-name' user: 'user' host: 'hostIP' (init_connect command failed) (mysql&gt; show databases; FEHLER 2006 (HY000): MySQL-Server nicht mehr verfügbar. Keine Verbindung. Es wird versucht, die Verbindung wiederherzustellen... Verbindungs-ID: 64897 Aktuelle Datenbank: *** KEINE *** FEHLER 1184 (08S01): Verbindung 22 mit DB "&lt;Datenbankname&gt;" abgebrochen. Benutzer: &lt;Benutzer&gt;, Host: &lt;Host-IP-Adresse&gt;. (Fehler beim Befehl "init_connect".))

**Lösung**: Setzen Sie den Wert von `init_connect` im Azure-Portal auf der Registerkarte „Serverparameter“ zurück, und legen Sie mit init_connect nur unterstützte Serverparameter fest.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Fehler aufgrund fehlender SUPER-Berechtigung und DBA-Rolle

Die Berechtigung SUPER und die Rolle DBA werden für den Dienst nicht unterstützt. Folglich können allgemeine Fehler wie die folgenden auftreten:

### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>FEHLER 1419: Sie verfügen nicht über die SUPER-Berechtigung, und die binäre Protokollierung ist aktiviert (es *kann* ratsam sein, die weniger sichere Variable „log_bin_trust_function_creators“ zu verwenden).

Der obige Fehler kann auftreten, wenn Sie eine Funktion erstellen, etwas wie unten beschrieben auslösen oder ein Schema importieren. Die DDL-Anweisungen wie „CREATE FUNCTION“ oder „CREATE TRIGGER“ werden in das binäre Protokoll geschrieben, sodass sie vom sekundären Replikat ausgeführt werden können. Der SQL-Replikatthread verfügt über vollständige Berechtigungen, die ausgenutzt werden können, um Berechtigungen zu erhöhen. Um Server mit aktivierter binärer Protokollierung vor dieser Gefahr zu schützen, erfordert die MySQL-Engine, dass Ersteller gespeicherter Funktionen neben der üblichen erforderlichen Berechtigung CREATE ROUTINE auch über die Berechtigung SUPER verfügen.

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Lösung:** Legen Sie `log_bin_trust_function_creators` auf dem Blatt [Serverparameter](howto-server-parameters.md) des Portals auf 1 fest, führen Sie die DDL-Anweisungen aus, oder importieren Sie das Schema, um die gewünschten Objekte zu erstellen. Sie können `log_bin_trust_function_creators` für Ihren Server auf 1 festgelegt lassen, um den Fehler in Zukunft zu vermeiden. Wir empfehlen, `log_bin_trust_function_creators` festzulegen. Das in der [Dokumentation der MySQL Community](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) angegebene Sicherheitsrisiko in Azure DB for MySQL ist minimal, da das binäre Protokoll keinen Bedrohungen ausgesetzt ist.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>FEHLER 1227 (42000) in Zeile 101: Zugriff verweigert. Für diesen Vorgang benötigen Sie (mindestens) eine SUPER-Berechtigung. Fehler für Vorgang mit Exitcode 1

Der obige Fehler kann auftreten, wenn Sie eine Dumpdatei importieren oder eine Prozedur erstellen, die Elemente vom Typ [DEFINER](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html) enthält.

**Lösung:**  Zur Behebung dieses Fehlers kann der Administratorbenutzer durch Ausführen eines GRANT-Befehls Berechtigungen zum Erstellen oder Ausführen von Prozeduren erteilen, wie in den folgenden Beispielen gezeigt:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```

Alternativ können Sie die DEFINER-Elemente durch den Namen des Administratorbenutzers ersetzen, der den Importvorgang ausführt, wie hier zu sehen.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>FEHLER 1227 (42000) in Zeile 295: Zugriff verweigert. Für diesen Vorgang benötigen Sie (mindestens) eine SUPER- oder SET_USER_ID-Berechtigung.

Der obige Fehler kann beim Ausführen von CREATE VIEW mit DEFINER-Anweisungen im Rahmen des Imports einer Dumpdatei oder der Ausführung eines Skripts auftreten. Azure Database for MySQL lässt für Benutzer weder nicht die Berechtigungen SUPER und SET_USER_ID zu.

**Lösung**:

* Verwenden Sie, sofern möglich, den DEFINER-Benutzer, um CREATE VIEW auszuführen. Wahrscheinlich gibt es viele Ansichten mit unterschiedlichen DEFINER-Elementen und unterschiedlichen Berechtigungen, sodass dieser Vorgang eventuell nicht möglich ist.  ODER
* Bearbeiten Sie die Dumpdatei oder das CREATE VIEW-Skript, und entfernen Sie die DEFINER=-Anweisung aus der Dumpdatei. ODER 
* Bearbeiten Sie die Dumpdatei oder das CREATE VIEW-Skript, und ersetzen Sie die DEFINER-Werte durch einen Benutzer mit Administratorberechtigungen, der den Import vornimmt oder die Skriptdatei ausführt.

> [!Tip]
> Verwenden Sie „sed“ oder „perl“ zum Ändern einer Dumpdatei oder eines SQL-Skripts und zum Ersetzen der DEFINER=-Anweisung.

#### <a name="error-1227-42000-at-line-18-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation"></a>FEHLER 1227 (42000) in Zeile 18: Zugriff verweigert. Für diesen Vorgang benötigen Sie (mindestens) eine SUPER-Berechtigung.

Dieser Fehler kann auftreten, wenn Sie versuchen, die Dumpdatei vom MySQL-Server mit aktiviertem GTID auf den Azure Database for MySQL-Zielserver zu importieren. Mysqldump fügt einer Dumpdatei von einem Server, auf dem GTIDs verwendet werden, die Anweisung „SET @@SESSION.sql_log_bin=0“ hinzu. Dadurch wird beim erneuten Laden der Dumpdatei die binäre Protokollierung deaktiviert.

**Lösung:** Um diesen Fehler beim Importieren zu beheben, entfernen Sie die unten aufgeführten Zeilen in der mysqldump-Datei, oder kommentieren Sie sie aus. Führen Sie anschließend den Import erneut aus, um sicherzustellen, dass er erfolgreich war.

SET @MYSQLDUMP_TEMP_LOG_BIN = @@SESSION.SQL_LOG_BIN; SET @@SESSION.SQL_LOG_BIN= 0; SET @@GLOBAL.GTID_PURGED=''; SET @@SESSION.SQL_LOG_BIN = @MYSQLDUMP_TEMP_LOG_BIN;

## <a name="common-connection-errors-for-server-admin-sign-in"></a>Häufige Verbindungsfehler bei der Serveradministratoranmeldung

Beim Erstellen eines Azure Database for MySQL-Servers wird vom Endbenutzer bei der Servererstellung eine Serveradministratoranmeldung bereitgestellt. Mit der Serveradministratoranmeldung können Sie neue Datenbanken erstellen, neue Benutzer hinzufügen und Berechtigungen erteilen. Werden die Serveradministratoranmeldung gelöscht, die zugehörigen Berechtigungen widerrufen oder das Kennwort geändert, werden beim Herstellen von Verbindungen möglicherweise Verbindungsfehler in Ihrer Anwendung angezeigt. Im Anschluss finden Sie einige häufige Fehler:

### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>FEHLER 1045 (28000): Der Zugriff auf den Benutzer „Benutzername“@„IP-Adresse“ (mit Kennwort: JA) wurde verweigert

Der obige Fehler tritt bei folgenden Bedingungen auf:

* Der Benutzername ist nicht vorhanden.
* Der Benutzername wurde gelöscht.
* Das Kennwort wurde geändert oder zurückgesetzt

**Lösung**:

* Überprüfen Sie, ob „Benutzername“ als gültiger Benutzer auf dem Server vorhanden ist oder versehentlich gelöscht wurde. Sie können die folgende Abfrage ausführen, indem Sie sich beim Azure Database for MySQL-Benutzer anmelden:

  ```sql
  select user from mysql.user;
  ```

* Wenn Sie sich nicht bei MySQL anmelden können, um die oben genannte Abfrage auszuführen, empfiehlt es sich, [das Administratorkennwort im Azure-Portal zurückzusetzen](howto-create-manage-server-portal.md). Mithilfe der Option „Kennwort zurücksetzen“ im Azure-Portal können Sie den Benutzer erneut erstellen, das Kennwort zurücksetzen und die Administratorberechtigungen wiederherstellen. Dadurch können Sie sich als Serveradministrator anmelden und weitere Vorgänge ausführen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die gesuchte Antwort nicht gefunden haben, ziehen Sie die folgenden Optionen in Betracht:

* Stellen Sie Ihre Frage auf der [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-database-mysql.html) oder in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
* Senden Sie eine E-Mail an das Azure Database for MySQL-Team: [@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Bei dieser E-Mail-Adresse handelt es sich nicht um einen Alias für technischen Support.
* Wenn Sie den Azure-Support kontaktieren möchten, [erstellen Sie ein Ticket im Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
* Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).
