---
title: Neuerungen in Azure Database for MySQL Single Server
description: Hier erfahren Sie mehr zu Azure Database for MySQL Single Server, einem relationalen Datenbankdienst in der Microsoft-Cloud, der auf MySQL Community Edition basiert.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: ef710559cb8d51b21e859bb08c50c5a2ed07b74c
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894201"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Neuerungen in Azure Database for MySQL Single Server

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL ist ein relationaler Datenbankdienst in der Microsoft-Cloud. Der Dienst basiert auf der [Datenbank-Engine MySQL Community Edition](https://www.mysql.com/products/community/) (verfügbar unter der GPLv2-Lizenz) und unterstützt die Versionen 5.6 (eingestellt), 5.7 und 8.0. Bei [Azure Database for MySQL Single Server](./overview.md#azure-database-for-mysql---single-server) handelt es sich um einen Bereitstellungsmodus, der einen vollständig verwalteten Datenbankdienst mit minimalen Anforderungen für die Anpassung der Datenbank bietet. Die Single Server-Plattform kann die meisten Funktionen zur Datenbankverwaltung bereitstellen, z. B. Patching, Sicherungen, Hochverfügbarkeit sowie Sicherheit, alle mit minimaler Benutzerkonfiguration und -steuerung.

In diesem Artikel werden neue Releases und Features in Azure Database for MySQL Single Server ab Januar 2021 zusammengefasst. Auflistungen werden in umgekehrter chronologischer Reihenfolge angezeigt, die neuesten Updates zuerst.

## <a name="october-2021"></a>Oktober 2021

- **Bekannte Probleme**

Der MySQL-8.0.27-Client ist nicht kompatibel mit der Azure Datenbank für MySQL-Einzelserver. Alle Verbindungen vom MySQL-8.0.27-Clienten, die entweder über mysql.exe oder der Workbench erstellt wurden, führen zu einer Fehlermeldung. Erwägen Sie als Problemumgehung die Verwendung einer früheren Version des Clienten (vor MySQL 8.0.27) oder das Erstellen einer Instanz von [Azure Datenbank für MySQL-flexible Server](https://docs.microsoft.com/azure/mysql/flexible-server/overview).

## <a name="june-2021"></a>Juni 2021
  
Dieses Release von Azure Database for MySQL Single Server enthält die folgenden Updates.

- **Möglichkeit zum Ändern des Serverparameters `activate_all_roles_on_login` über Portal/Befehlszeilenschnittstelle für MySQL 8.0 aktiviert**

  Benutzer können jetzt den Wert des Parameters „activate_all_roles_on_login“ über das Azure-Portal und die Befehlszeilenschnittstelle ändern. Mit diesem Parameter können Sie konfigurieren, ob die automatische Aktivierung aller gewährten Rollen aktiviert werden soll, wenn sich Benutzer beim Server anmelden. Weitere Informationen finden Sie unter [Serversystemvariablen](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html).

- **MySQL-Communityfehler 29596969 und 94668 behoben**

  Dieses Release behebt ein Problem, bei dem der Standardausdruck in einer CREATE TABLE-Abfrage ignoriert wird, wenn das Feld als PRIMÄRSCHLÜSSEL für MySQL 8.0 markiert wurde. (MySQL-Communityfehler 29596969, Fehler 94668). Weitere Informationen finden Sie unter [MySQL Bugs: #94668: Expression Default is made NULL during CREATE TABLE query, if field is made PK](https://bugs.mysql.com/bug.php?id=94668) (MySQL-Fehler 94668: Standardwert für Ausdruck ist NULL bei CREATE TABLE-Abfrage, wenn das Feld der Primärschlüssel ist).

- **Ein Problem mit doppelten Tabellennamen in der SHOW TABLE-Abfrage behoben**

  Es wurde eine neue Funktion eingeführt, die während des Tabellenvorgangs eine differenzierte Steuerung des Tabellencaches ermöglicht. Aufgrund eines Codefehlers in der neuen Funktion kann der Eintrag im Verzeichniscache falsch konfiguriert oder hinzugefügt werden und zu unerwartetem Verhalten führen, z. B. der Rückgabe von zwei Tabellen mit dem gleichen Namen. Der Verzeichniscache funktioniert nur für die SHOW TABLE-Abfrage. Er wirkt sich nicht auf DML- oder DDL-Abfragen aus. Dieses Problem wurde in diesem Release vollständig behoben.

- **Standardwert für Serverparameter `max_heap_table_size` erhöht, um Überläufe der temporären Tabelle auf dem Datenträger zu reduzieren**

  Mit diesem Release wurde der maximal zulässige Wert für den Parameter `max_heap_table_size` in „8589934592“ für Speicher der Ebene „Universell“ mit 64 virtuellen Kernen und der Ebene „Arbeitsspeicheroptimiert“ mit 32 virtuellen Kernen geändert.

- **Problem beim Festlegen des Werts des Parameters `sql_require_primary_key` über das Portal behoben**

  Benutzer können jetzt den Wert des Parameters `sql_require_primary_key` direkt über das Azure-Portal ändern.

- **Allgemeine Verfügbarkeit von Benachrichtigungen zu geplanten Wartungen**

  Mit diesem Release werden Benachrichtigungen zu geplanten Wartungen in Azure Database for MySQL – Einzelserver allgemein verfügbar gemacht. Weitere Informationen finden Sie im Artikel [Benachrichtigungen zu geplanten Wartungen](concepts-planned-maintenance-notification.md).

- **Parameter `redirect_enabled` standardmäßig aktiviert**

  Mit diesem Release wird der Parameter `redirect_enabled` standardmäßig aktiviert. Mithilfe der Umleitung soll die Netzwerklatenz zwischen Clientanwendungen und MySQL-Servern dadurch verringert werden, dass Anwendungen eine direkte Verbindung mit Back-End-Serverknoten herstellen dürfen. Unterstützung für die Umleitung in PHP-Anwendungen ist über die von Microsoft entwickelte [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)-Erweiterung verfügbar. Weitere Informationen finden Sie im Artikel [Herstellen einer Verbindung mit Azure Database for MySQL mit Umleitung](howto-redirection.md).

>[!Note]
> * Die Umleitung funktioniert bei der Einrichtung von Private Link nicht. Wenn Sie Private Link für Azure Database for MySQL verwenden, tritt möglicherweise ein Verbindungsproblem auf. Um das Problem zu beheben, stellen Sie sicher, dass der Parameter „redirect_enabled“ auf „OFF“ festgelegt ist und die Clientanwendung neu gestartet wurde.</br>
> * Wenn Sie eine PHP-Anwendung mit dem Umleitungstreiber mysqlnd_azure verwenden, um eine Verbindung mit Azure Database for MySQL herzustellen (wobei die Umleitung standardmäßig aktiviert ist), kann es zu einem Datencodierungsproblem kommen, das sich auf Ihre Einfügetransaktionen auswirken kann.</br>
> Führen Sie zum Beheben dieses Problems eine der folgenden Aktionen aus:
>    - Deaktivieren Sie im Azure-Portal die Umleitung, indem Sie den Parameter „redirect_enabled“ auf „OFF“ festlegen, und starten Sie die PHP-Anwendung neu, um den Treibercache nach der Änderung zu löschen.
>     - Legen Sie die zeichensatzbezogenen Parameter explizit auf Sitzungsebene und basierend auf Ihren Einstellungen fest, nachdem die Verbindung hergestellt wurde (z. B. „set names utf8mb4“).

## <a name="february-2021"></a>Februar 2021

Dieses Release von Azure Database for MySQL Single Server enthält die folgenden Updates.

- Neue gespeicherte Prozeduren wurden hinzugefügt, um den globalen Transaktionsbezeichner (GTID) für eingehende Daten für den Großspeicherserver der Version 5.7 und 8.0 zu unterstützen.
- Mit dem Update werden die MySQL-Versionen bis 5.6.50 und 5.7.32 unterstützt.

## <a name="january-2021"></a>Januar 2021

Dieses Release von Azure Database for MySQL Single Server enthält die folgenden Updates.

- „Kennwort zurücksetzen“ wurde aktiviert, um die erste Administratorberechtigung automatisch zu korrigieren.
- Der Serverparameter `auto_increment_increment/auto_increment_offset` und `session_track_gtids` wurden verfügbar gemacht.
- Neue gespeicherte Prozeduren zum Steuern der Sicherung/Wiederherstellung des InnoDB-Pufferpools wurden hinzugefügt.
- Der InnoDB-Aufwärmparameter für einen großen Speicherserver wurde verfügbar gemacht.

## <a name="contacts"></a>Kontakte

Wenn Sie Fragen oder Vorschläge zur Arbeit mit Azure Database for MySQL haben, wenden Sie sich an das Azure Database for MySQL-Team ([@AskAzure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Bei dieser E-Mail-Adresse handelt es sich nicht um einen Alias für technischen Support.

Weitere Kontaktmöglichkeiten:

- Wenn Sie den Azure-Support kontaktieren möchten, [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den [Preisen für Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/server/).
- Suchen Sie in der [öffentlichen Dokumentation](./single-server/index.yml) nach Azure Database for MySQL Single Server.
- Lesen Sie Details zur [Problembehandlung für häufige Fehler](./howto-troubleshoot-common-errors.md).
