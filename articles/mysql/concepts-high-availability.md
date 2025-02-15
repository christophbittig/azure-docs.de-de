---
title: Hochverfügbarkeit (Azure Database for MySQL)
description: Dieser Artikel bietet Informationen zur Hochverfügbarkeit in Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 921e4cd8885dcbad5cd477e7f2283c9b11c445fc
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639701"
---
# <a name="high-availability-in-azure-database-for-mysql"></a>Hochverfügbarkeit in Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Der Dienst Azure Database for MySQL bietet eine garantierte hohe Verfügbarkeit mit der finanziell abgesicherten Vereinbarung zum Servicelevel (Service Level Agreement, SLA) über eine Uptime von [99,99 %](https://azure.microsoft.com/support/legal/sla/mysql). Azure Database for MySQL bietet Hochverfügbarkeit sowohl bei geplanten Ereignissen wie benutzerseitig initiierten Skalierungscomputevorgängen als auch bei ungeplanten Ereignissen wie Ausfällen von zugrunde liegender Hardware oder Software oder Netzwerkausfällen. Der Dienst kann auch nach höchst kritischen Situationen schnell wiederhergestellt werden – so ist gewährleistet, dass für Anwendungen so gut wie keine Downtime auftritt.

Azure Database for MySQL eignet sich zum Ausführen von unternehmenskritischen Datenbanken, die eine hohe Uptime erfordern. Der Dienst basiert auf der Azure-Architektur und verfügt über inhärente Hochverfügbarkeit, Redundanz und Resilienz, um die Datenbankdowntime aufgrund von geplanten und ungeplanten Ausfällen zu minimieren, ohne dass Sie zusätzliche Komponenten konfigurieren müssen. 

## <a name="components-in-azure-database-for-mysql"></a>Komponenten in Azure Database for MySQL

| **Komponente** | **Beschreibung**|
| ------------ | ----------- |
| <b>MySQL-Datenbankserver | Azure Database for MySQL bietet Sicherheit, Isolation und Ressourcenschutzvorrichtungen und ermöglicht einen schnellen Neustart von Datenbankservern. Diese Funktionen ermöglichen es, nach einem Ausfall innerhalb von 60 bis 120 Sekunden (abhängig von der Transaktionsaktivität in der Datenbank) Vorgänge wie die Skalierung oder die Wiederherstellung des Datenbankservers auszuführen. <br/> Änderungen an Daten auf dem Datenbankserver finden in der Regel im Rahmen von Datenbanktransaktionen statt. Alle Änderungen an der Datenbank werden synchron in Form von Write-Ahead-Protokollen (ib_log) im Azure Storage-Dienst aufgezeichnet, der an den Datenbankserver angefügt ist. Während des [Prüfpunktprozesses](https://dev.mysql.com/doc/refman/5.7/en/innodb-checkpoints.html) für die Datenbank werden außerdem Datenseiten aus dem Arbeitsspeicher des Datenbankservers in den Speicher geleert. |
| <b>Remotespeicher | Alle physischen MySQL-Datendateien und Protokolldateien werden in Azure Storage gespeichert. Dieser Dienst ist so konzipiert, dass drei Kopien der Daten in einer Region gespeichert werden, um die Redundanz, Verfügbarkeit und Zuverlässigkeit der Daten sicherzustellen. Außerdem ist die Speicherebene unabhängig vom Datenbankserver. Sie kann von einem ausgefallenen Datenbankserver getrennt und innerhalb von 60 Sekunden an einen neuen Datenbankserver angefügt werden. Darüber hinaus prüft Azure Storage die Daten kontinuierlich auf Speicherfehler. Wenn eine Blockbeschädigung erkannt wird, wird diese automatisch durch Instanziieren einer neuen Speicherkopie behoben. |
| <b>Gateway | Das Gateway fungiert als Datenbankproxy und leitet alle Clientverbindungen an den Datenbankserver weiter. |

## <a name="planned-downtime-mitigation"></a>Minimierung von geplanter Downtime
Azure Database for MySQL ist so konzipiert, dass bei einer geplanten Downtime weiterhin Hochverfügbarkeit gewährleistet ist. 

:::image type="content" source="./media/concepts-high-availability/elastic-scaling-mysql-server.png" alt-text="Übersicht über die elastische Skalierung in Azure Database for MySQL":::

Hier finden Sie einige Szenarien mit geplanter Wartung:

| **Szenario** | **Beschreibung**|
| ------------ | ----------- |
| <b>Hoch-/Herunterskalieren von Computeressourcen | Wenn ein Benutzer den Vorgang zum Hoch- bzw. Herunterskalieren von Computeressourcen ausführt, wird ein neuer Datenbankserver mit der skalierten Computekonfiguration bereitgestellt. Auf dem alten Datenbankserver können aktive Prüfpunkte abgeschlossen werden, Clientverbindungen werden entladen, alle Transaktionen ohne Commit werden abgebrochen, und anschließend wird der Server heruntergefahren. Der Speicher wird dann vom alten Datenbankserver getrennt und an den neuen Datenbankserver angefügt. Wenn die Clientanwendung versucht, die Verbindung wiederherzustellen oder eine neue Verbindung herzustellen, leitet das Gateway die Verbindungsanforderung an den neuen Datenbankserver weiter.|
| <b>Hochskalieren des Speichers | Das Hochskalieren des Speichers ist ein Onlinevorgang, bei dem der Datenbankserver nicht unterbrochen wird.|
| <b>Bereitstellung neuer Software (Azure) | Rollouts von neuen Features oder Fehlerbehebungen werden automatisch im Rahmen der geplanten Wartung des Diensts durchgeführt. Weitere Informationen finden Sie in der [Dokumentation](concepts-monitoring.md#planned-maintenance-notification) und im [Portal](https://aka.ms/servicehealthpm).|
| <b>Upgrades auf Nebenversionen | Azure Database for MySQL patcht Datenbankserver automatisch auf die von Azure festgelegte Nebenversion. Dies erfolgt im Rahmen der geplanten Wartung des Diensts. Während einer geplanten Wartung können Datenbankserver neu gestartet oder Failover ausgeführt werden. Dies kann dazu führen, dass die Datenbankserver kurzzeitig nicht für Endbenutzer verfügbar sind. Azure Database for MySQL-Server werden in Containern ausgeführt, sodass Neustarts von Datenbankservern meist schnell abgeschlossen sind – in der Regel innerhalb von 60-120 Sekunden. Das gesamte geplante Wartungsereignis, einschließlich der einzelnen Serverneustarts, wird vom Technikerteam sorgfältig überwacht. Die Zeit für Serverfailover hängt von der Wiederherstellungszeit der Datenbank ab. Es kann daher länger dauern, die Datenbank wieder online zu schalten, wenn während des Failovers auf dem Server extrem viele Transaktionsaktivitäten auftreten. Um längere Neustarts zu vermeiden, empfiehlt es sich, zeitintensive Transaktionen (Massenladen) während geplanter Wartungsereignisse zu vermeiden. Weitere Informationen finden Sie in der [Dokumentation](concepts-monitoring.md#planned-maintenance-notification) und im [Portal](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Minimierung von ungeplanter Downtime

Ungeplante Downtime kann aufgrund von unvorhergesehenen Fehlern auftreten, darunter Fehler in der zugrunde liegenden Hardware, Netzwerkprobleme und Softwarefehler. Wenn der Datenbankserver unerwartet ausfällt, wird automatisch innerhalb von 60 bis 120 Sekunden ein neuer Datenbankserver bereitgestellt. Der Remotespeicher wird automatisch an den neuen Datenbankserver angefügt. Die MySQL-Engine führt den Wiederherstellungsvorgang mithilfe von WAL-Dateien und Datenbankdateien durch und öffnet den Datenbankserver, sodass Clients eine Verbindung herstellen können. Transaktionen ohne Commit gehen verloren und müssen von der Anwendung erneut ausgeführt werden. Ungeplante Downtime lässt sich zwar nicht vollständig vermeiden, wird aber von Azure Database for MySQL durch automatisches Ausführen von Wiederherstellungsvorgängen sowohl auf Ebene der Datenbankserver als auch des Speichers minimiert, ohne dass ein Eingreifen durch einen Benutzer erforderlich ist. 


:::image type="content" source="./media/concepts-high-availability/availability-for-mysql-server.png" alt-text="Übersicht über Hochverfügbarkeit in Azure Database for MySQL":::

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Ungeplante Downtime: Fehlerszenarien und Dienstwiederherstellung
Im Folgenden finden Sie einige Fehlerszenarien und die Aktionen von Azure Database for MySQL zur automatischen Wiederherstellung:

| **Szenario** | **Automatische Wiederherstellung** |
| ---------- | ---------- |
| <B>Ausfall des Datenbankservers | Wenn der Datenbankserver aufgrund eines Fehlers an der zugrunde liegenden Hardware ausfällt, werden die aktiven Verbindungen getrennt, und alle Transaktionen, die gerade ausgeführt werden, werden abgebrochen. Ein neuer Datenbankserver wird automatisch bereitgestellt, und der Remotedatenspeicher wird an den neuen Datenbankserver angefügt. Sobald die Datenbank wiederhergestellt wurde, können Clients über das Gateway eine Verbindung mit dem neuen Datenbankserver herstellen. <br /> <br /> Anwendungen, die die MySQL-Datenbanken verwenden, müssen so konzipiert sein, dass sie getrennte Verbindungen und Transaktionsfehler erkennen und entsprechende Wiederholungsversuche durchführen.  Wenn die Anwendung einen Wiederholungsversuch durchführt, leitet das Gateway die Verbindung transparent an den neu erstellten Datenbankserver um. |
| <B>Speicherfehler | Speicherbezogene Probleme wie der Ausfall eines Datenträgers oder physische Blockbeschädigungen haben keine Auswirkungen auf Anwendungen. Da drei Kopien der Daten gespeichert werden, wird eine Kopie der Daten vom verbleibenden Speicher bereitgestellt. Blockbeschädigungen werden automatisch behoben. Wenn eine Kopie der Daten verloren geht, wird automatisch eine neue erstellt. |

Im Folgenden finden Sie einige Fehlerszenarien, bei denen für die Wiederherstellung eine Benutzeraktion erforderlich ist:

| **Szenario** | **Plan für die Wiederherstellung** |
| ---------- | ---------- |
| <b> Regionsausfall | Regionen fallen nur selten aus. Wenn Sie jedoch Schutz vor einem Regionsausfall benötigen, können Sie ein oder mehrere Lesereplikate in anderen Regionen für die Notfallwiederherstellung konfigurieren. (Weitere Informationen finden Sie in [diesem Artikel](howto-read-replicas-portal.md) zum Erstellen und Verwalten von Lesereplikaten.) Bei einem Ausfall auf Regionsebene können Sie das in der anderen Region konfigurierte Lesereplikat manuell zum Produktionsdatenbankserver höher stufen. |
| <b> Logische Fehler/Benutzerfehler | Die Wiederherstellung nach Benutzerfehlern, z. B. versehentlich gelöschten Tabellen oder falsch aktualisierten Daten, umfasst das Durchführen einer [Zeitpunktwiederherstellung](concepts-backup.md), bei der die Daten auf einen Zeitpunkt kurz vor dem Fehler wiederhergestellt werden.<br> <br>  Wenn Sie anstelle aller Datenbanken auf dem Datenbankserver nur einen Teil der Datenbanken oder bestimmte Tabellen wiederherstellen möchten, können Sie den Datenbankserver in einer neuen Instanz wiederherstellen, die Tabellen über [mysqldump](concepts-migrate-dump-restore.md) exportieren und sie dann über [restore](concepts-migrate-dump-restore.md#restore-your-mysql-database-using-command-line-or-mysql-workbench) in Ihrer Datenbank wiederherstellen. |



## <a name="summary"></a>Zusammenfassung

Azure Database for MySQL bietet einen schnellen Neustart von Datenbankservern, redundanten Speicher und effizientes Routing über das Gateway. Um Ihre Daten zusätzlich zu schützen, können Sie Sicherungen so konfigurieren, dass sie georepliziert werden, und ein oder mehrere Lesereplikate in anderen Regionen bereitstellen. Mithilfe der inhärenten Hochverfügbarkeitsfunktionen schützt Azure Database for MySQL Ihre Datenbanken vor den häufigsten Arten von Ausfällen und bietet eine branchenführende, finanziell abgesicherte [SLA über eine Uptime von 99,99 %](https://azure.microsoft.com/support/legal/sla/mysql). Dank all dieser Verfügbarkeits- und Zuverlässigkeitsfunktionen ist Azure die ideale Plattform für Ihre unternehmenskritischen Anwendungen.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Azure-Regionen](../availability-zones/az-overview.md).
- Erfahren Sie mehr über [Behandlung vorübergehender Verbindungsfehler](concepts-connectivity.md)
- Erfahren Sie, wie Sie [Ihre Daten mit Lesereplikaten replizieren](howto-read-replicas-portal.md)
