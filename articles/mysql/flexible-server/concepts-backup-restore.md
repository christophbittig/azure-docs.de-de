---
title: Sicherung und Wiederherstellung in Azure Database for MySQL Flexible Server
description: Erfahren Sie mehr über die Konzepte der Sicherung und Wiederherstellung mit Azure Database for MySQL Flexible Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7564db786084964fa0aca38c6ce8b722b0f3a95a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355432"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Sicherung und Wiederherstellung in Azure Database for MySQL Flexible Server (Vorschau)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server erstellt automatisch Serversicherungen und speichert diese sicher in lokalen redundanten Speichern innerhalb der Azure-Region. Sicherungen können verwendet werden, um für Ihren Server den Stand zu einem bestimmten Zeitpunkt wiederherzustellen. Sicherungen und Wiederherstellungen sind wesentliche Bestandteile jeder Strategie für Geschäftskontinuität, da Ihre Daten so vor versehentlichen Beschädigungen und Löschungen geschützt werden.

## <a name="backup-overview"></a>Übersicht über Azure Backup

Flexible Server erstellt Momentaufnahmensicherungen der Datendateien und speichert sie in einem lokalen redundanten Speicher. Der Server führt auch eine Sicherung der Transaktionsprotokolle durch und speichert diese ebenfalls in einem lokalen redundanten Speicher. Dank dieser Sicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Sie können die Sicherung der Datenbank optional von 1 bis 35 Tagen konfigurieren. Alle Sicherungen werden mithilfe der AES 256-Bit-Verschlüsselung für die ruhenden Daten verschlüsselt.

Diese Sicherungsdateien können nicht exportiert werden. Die Sicherungen können nur für Wiederherstellungsvorgänge in Flexible Server verwendet werden. Sie können auch [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) von einem MySQL-Client aus verwenden, um eine Datenbank zu kopieren.

## <a name="backup-frequency"></a>Sicherungshäufigkeit

Sicherungen auf flexiblen Servern basieren auf Momentaufnahmen. Die erste Momentaufnahmensicherung ist für unmittelbar nach Erstellung des Servers geplant. Momentaufnahmesicherungen werden einmal täglich erstellt. Transaktionsprotokollsicherungen finden alle fünf Minuten statt.

## <a name="backup-retention"></a>Sicherungsaufbewahrung

Datenbanksicherungen werden in einem lokal redundanten Speicher gespeichert, der innerhalb einer Region in drei Kopien gespeichert wird. Sicherungen werden basierend auf der Einstellung für den Aufbewahrungszeitraum der Sicherung auf dem Server beibehalten. Sie können einen Aufbewahrungszeitraum von 1 bis 35 Tagen auswählen, wobei die standardmäßige Beibehaltungsdauer sieben Tage beträgt. Sie können den Aufbewahrungszeitraum bei der Servererstellung oder später festlegen, indem Sie die Sicherungskonfiguration mithilfe des Azure-Portals aktualisieren.

Der Aufbewahrungszeitraum für Sicherungen legt fest, wie weit zurück in der Zeit ein Zeitpunktwiederherstellungsvorgang durchgeführt werden kann, da er auf verfügbaren Sicherungen basiert. Der Aufbewahrungszeitraum kann auch als Wiederherstellungsfenster im Hinblick auf die Wiederherstellung behandelt werden. Alle Sicherungen, die zum Durchführen einer Zeitpunktwiederherstellung innerhalb des Aufbewahrungszeitraums für die Sicherung erforderlich sind, werden im Sicherungsspeicher beibehalten. Wenn der Aufbewahrungszeitraum für Sicherungen z. B. auf sieben Tage festgelegt ist, entspricht das Wiederherstellungsfenster einer Dauer von sieben Tagen. In diesem Szenario bleiben alle Sicherungen erhalten, die zum Wiederherstellen des Servers in den letzten sieben Tagen erforderlich sind. Bei einem Fenster für die Aufbewahrung von Sicherungen von sieben Tagen werden Datenbankmomentaufnahmen und Transaktionsprotokollsicherungen für die letzten acht Tage (ein Tag vor dem Fenster) gespeichert.

## <a name="backup-storage-cost"></a>Kosten für Sicherungsspeicher

Für die Flexible Server-Instanz werden bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung gestellt. Wenn zusätzlicher Sicherungsspeicher verwendet wird, wird dies in GB pro Monat berechnet. Beispiel: Wenn Sie einen Server mit 250 GB bereitgestellt haben, verfügen Sie über 250 GB an Speicher, der ohne zusätzliche Kosten für Serversicherungen zur Verfügung steht. Wenn die tägliche Sicherheitsauslastung 25 GB beträgt, können Sie für bis zu 10 Tage über kostenlosen Sicherungsspeicher verfügen. Der für Sicherungen verwendete Speicher über 250 GB wird gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/mysql/) abgerechnet.

Sie können die im Azure-Portal in Azure Monitor verfügbare Metrik für den [verwendeten Sicherungsspeicher](./concepts-monitoring.md) zum Überwachen des von einem Server genutzten Sicherungsspeichers verwenden. Die Metrik für den belegten **Sicherungsspeicher** stellt den gesamten Speicherplatz dar, der von allen Datenbank- und Protokollsicherungen beansprucht wurde, die auf Grundlage des für den Server festgelegten Aufbewahrungszeitraums für Sicherungen aufbewahrt wurden. Eine hohe Transaktionsaktivität auf dem Server kann dazu führen, dass die Sicherungsspeicherauslastung unabhängig von der Gesamtgröße der Datenbank zunimmt.

Das primäre Mittel zur Kostenkontrolle bei der Speicherung von Sicherungen ist die Festlegung eines angemessenen Aufbewahrungszeitraums für Sicherungen. Sie können einen Aufbewahrungszeitraum zwischen 1 und 35 Tagen auswählen.

> [!IMPORTANT]
> Sicherungen von einem Datenbankserver, der in einer zonenredundanten Hochverfügbarkeitskonfiguration konfiguriert ist, erfolgen vom primären Datenbankserver, da der Mehraufwand bei Momentaufnahmensicherungen minimal ist.

> [!IMPORTANT]
> Georedundante Sicherungen werden derzeit für Flexible Server nicht unterstützt.

## <a name="point-in-time-restore"></a>Wiederherstellung bis zu einem bestimmten Zeitpunkt

In Azure Database for MySQL Flexible Server wird bei der Zeitpunktwiederherstellung ein neuer Server aus den Sicherungen des flexiblen Servers in derselben Region wie Ihr Ausgangsserver erstellt. Bei der Erstellung werden die Konfiguration für die Computeebene, die Anzahl der virtuellen Kerne, die Speichergröße, den Aufbewahrungszeitraum für Sicherungen und die Option für Sicherungsredundanz des ursprünglichen Servers verwendet. Außerdem werden Tags und Einstellungen wie „Virtuelles Netzwerk“ und „Firewall“ vom Quellserver übernommen. Die Compute- und Speicherebene, die Konfiguration und die Sicherheitseinstellungen des wiederhergestellten Servers können nach Abschluss der Wiederherstellung geändert werden.

> [!NOTE]
> Es gibt zwei Serverparameter, die nach dem Wiederherstellungsvorgang auf Standardwerte zurückgesetzt werden (und nicht vom primären Server übernommen werden)
> *   time_zone: Dieser Wert wird auf den DEFAULT-Wert SYSTEM festgelegt.
> *   event_scheduler: Dieser Wert wird auf dem wiederhergestellten Server auf OFF festgelegt.

Die Point-in-Time-Wiederherstellung ist für viele Szenarien hilfreich. Einige der üblichen Anwendungsfälle sind:
-   Wenn ein Benutzer versehentlich Daten in der Datenbank löscht
-   Der Benutzer löscht eine wichtige Tabelle oder Datenbank
-   Eine Benutzeranwendung überschreibt aufgrund eines Anwendungsfehlers versehentlich gültige Daten mit ungültigen Daten

Sie können über das [Azure-Portal](how-to-restore-server-portal.md) zwischen einem letzten Wiederherstellungspunkt und einem benutzerdefinierten Wiederherstellungspunkt auswählen.

-   **Letzter Wiederherstellungspunkt**: Mit der Option „letzter Wiederherstellungspunkt“ können Sie den Server auf den Zeitstempel zurücksetzen, zu dem der Wiederherstellungsvorgang ausgelöst wurde. Diese Option ist hilfreich, um den Server schnell auf den aktuellsten Stand zu bringen.
-   **Benutzerdefinierter Wiederherstellungspunkt**: Dadurch können Sie einen beliebigen Zeitpunkt innerhalb des für diesen flexiblen Server definierten Aufbewahrungszeitraums wählen. Diese Option ist nützlich, um den Server genau zu dem Zeitpunkt wiederherzustellen, zu dem ein Benutzerfehler aufgetreten ist.

Die geschätzte Wiederherstellungszeit hängt von mehreren Faktoren ab, einschließlich der Datenbankgrößen, der Größe der Sicherung des Transaktionsprotokolls, der Computegröße der SKU und auch der Zeit der Wiederherstellung. Die Wiederherstellung des Transaktionsprotokolls ist der zeitaufwendigste Teil des Wiederherstellungsprozesses. Wenn die Wiederherstellungszeit näher am Zeitplan der Momentaufnahmensicherung gewählt wird, erfolgen die Wiederherstellungsabläufe schneller, da die Anwendung des Transaktionsprotokolls minimal ist. Um die genaue Wiederherstellungszeit für Ihren Server zu schätzen, wird dringend empfohlen, ihn in Ihrer Umgebung zu testen, da er zu viele umgebungsspezifische Variablen aufweist.

> [!IMPORTANT]
> Wenn Sie einen flexiblen Server wiederherstellen, der mit zonenredundanter Hochverfügbarkeit konfiguriert ist, wird der wiederhergestellte Server in derselben Region und Zone wie Ihr primärer Server konfiguriert und als einzelner flexibler Server in einem Modus ohne Hochverfügbarkeit bereitgestellt. Weitere Informationen finden Sie unter [Zonenredundante Hochverfügbarkeit](concepts-high-availability.md) für flexible Server.

> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden auch alle Datenbanken gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden. Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren [Verwaltungssperren](../../azure-resource-manager/management/lock-resources.md) nutzen.

## <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nach beiden Wiederherstellungsverfahren (**neuester Wiederherstellungspunkt** oder **Benutzerdefinierter Wiederherstellungspunkt**) sollten Sie die folgenden Aufgaben durchführen, um Ihre Benutzer und Anwendungen wieder in den betriebsbereiten Zustand zu versetzen:

-   Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll.
-   Sicherstellen, dass geeignete Firewallregeln auf Serverebene und Regeln von virtuellen Netzwerken vorhanden sind, damit Benutzer eine Verbindung herstellen können.
-   Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind.
-   Konfigurieren der erforderlichen Warnungen.

## <a name="frequently-asked-questions-faqs"></a>Häufig gestellte Fragen (FAQs)

### <a name="backup-related-questions"></a>Fragen zur Sicherung

- **Wie sichere ich meinen Server?**
Azure Database for MySQL ermöglicht standardmäßig automatische Sicherungen Ihres gesamten Servers (einschließlich aller erstellten Datenbanken) mit einer standardmäßigen Aufbewahrungsfrist von 7 Tagen. Manuelle Sicherungen können, nur mit Community-Tools wie mysqldump ([hier](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) dokumentiert) oder mydumper ([hier](../concepts-migrate-mydumper-myloader.md#create-a-backup-using-mydumper) dokumentiert) erstellt werden. Wenn Sie Azure Database for MySQL in einem Blobspeicher sichern möchten, lesen Sie unseren Tech Community-Blog [Sichern von Azure Database for MySQL auf Blob Storage](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/backup-azure-database-for-mysql-to-a-blob-storage/ba-p/803830). 

- **Kann ich automatische Sicherungen so konfigurieren, dass sie langfristig aufbewahrt werden?**
Nein, derzeit unterstützen wir nur eine automatisierte Sicherungsaufbewahrung von maximal 35 Tagen. Sie können manuelle Sicherungen zur langfristigen Aufbewahrung durchführen.

- **Was sind die Sicherungsfenster für meinen Server? Kann ich sie anpassen?**
Die erste Momentaufnahmensicherung ist für unmittelbar nach Erstellung des Servers geplant. Momentaufnahmesicherungen werden einmal täglich erstellt. Transaktionsprotokollsicherungen finden alle fünf Minuten statt. Sicherungsfenster werden grundsätzlich von Azure verwaltet und können nicht angepasst werden.

- **Werden meine Sicherungen verschlüsselt?**
Alle Daten, Sicherungen und temporären Dateien für Azure Database for MySQL, die im Zuge der Abfrageausführung erstellt werden, werden mithilfe der AES-256-Bit-Verschlüsselung verschlüsselt. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden. 

- **Kann ich einzelne oder mehrere Datenbanken wiederherstellen?**
Das Wiederherstellen einzelner oder mehrerer Datenbanken oder Tabellen wird nicht unterstützt. Falls Sie bestimmte Datenbanken wiederherstellen möchten, führen Sie eine Point-in-Time-Wiederherstellung durch und extrahieren Sie dann die benötigten Tabellen oder Datenbanken.

- **Ist mein Server während des Sicherungsfensters verfügbar?**
Ja. Sicherungen sind Onlinevorgänge und momentaufnahmebasiert. Der Momentaufnahmevorgang dauert nur wenige Sekunden und beeinträchtigt keine Produktionsworkloads, sodass die Hochverfügbarkeit des Servers sichergestellt ist.

- **Müssen wir beim Einrichten des Wartungsfensters für den Server das Sicherungsfenster berücksichtigen?**
Neun, Sicherungen werden intern als Teil des verwalteten Diensts ausgelöst und haben keinen Einfluss auf das verwaltete Wartungsfenster.

- **Wo werden meine automatisierten Sicherungen gespeichert, und wie verwalte ich deren Aufbewahrung?**
Azure Database for MySQL erstellt Serversicherungen automatisch und speichert sie in einem vom Benutzer konfigurierten lokal redundanten oder georedundanten Speicher. Diese Sicherungsdateien können nicht exportiert werden. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Sie können die Sicherung der Datenbank optional von 1 bis 35 Tagen konfigurieren.

- **Wie kann ich meine Sicherungen validieren?**
Die beste Möglichkeit, die Verfügbarkeit gültiger Sicherungen zu validieren, ist es regelmäßig Point-in-Time-Wiederherstellung durchzuführen und zu und bestätigen, dass die Sicherungen gültig und wiederherstellbar sind. Sicherungsvorgänge oder -dateien sind für Endbenutzer nicht zugänglich.

- **Wo kann ich den Speicherverbrauch für Sicherungen einsehen?**
Im Azure-Portal finden Sie auf der Registerkarte „Überwachung“ im Abschnitt „Metriken“ die Metrik [Verwendeter Sicherungsspeicher](./concepts-monitoring.md), die den gesamten Verbrauch für Sicherungen anzeigt.

- **Was geschieht mit meinen Sicherungen, wenn ich meinen Server lösche?**
Wenn Sie den Server löschen, werden auch alle Sicherungen gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden. Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren [Verwaltungssperren](../../azure-resource-manager/management/lock-resources.md) nutzen.

- **Wie wird meine Verwendung von Sicherungen in Rechnung gestellt?**
Für die Flexible Server-Instanz werden bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung gestellt. Jeder zusätzlich genutzte Sicherungsspeicher wird gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/mysql/server/) in GB pro Monat in Rechnung gestellt. Die Abrechnung des Sicherungsspeichers hängt auch von der ausgewählten Aufbewahrungsfrist für Sicherungen und der gewählten Redundanzoption ab, abgesehen von der Transaktionsaktivität auf dem Server, die sich direkt auf den gesamten verwendeten Sicherungsspeicher auswirkt.

- **Wie werden Sicherungen für angehaltene Server beibehalten?**
Für angehaltene Server werden keine neuen Sicherungen ausgeführt. Alle älteren Sicherungen (innerhalb des Aufbewahrungszeitfensters) zum Zeitpunkt des Anhaltens des Servers werden bis zum erneuten Starten des Servers aufbewahrt, wonach die Aufbewahrung der Sicherungen für den aktiven Server durch dessen Aufbewahrungszeitfenster bestimmt wird.

- **Wie werden mir Sicherungen angehaltener Server in Rechnung gestellt?**
Während Ihre Serverinstanz angehalten wird, werden Ihnen bereitgestellter Speicher (einschließlich bereitgestellter IOPS) und Sicherungsspeicher (Sicherungen, die im angegebenen Aufbewahrungszeitfenster gespeichert sind) in Rechnung gestellt. Der kostenlose Sicherungsspeicher ist auf die Größe Ihrer bereitgestellten Datenbank beschränkt und gilt nur für aktive Server. 

### <a name="restore-related-questions"></a>Fragen zur Wiederherstellung

- **Wie kann ich meinen Server wiederherstellen?**
Das Azure-Portal unterstützt die Point-in-Time-Wiederherstellung (für alle Server), sodass Benutzer den neuesten oder einen benutzerdefinierten Wiederherstellungspunkt wiederherstellen können. Informationen zum manuellen Wiederherstellen Ihres Servers aus den Sicherungen, die von mysqldump/myDumper erstellt wurden, finden Sie unter [Wiederherstellen Ihrer Datenbank mit myLoader.](../concepts-migrate-mydumper-myloader.md#restore-your-database-using-myloader)

- **Warum dauert meine Wiederherstellung so lange?**
Die geschätzte Zeit für die Wiederherstellung des Servers ist von mehreren Faktoren abhängig: 
   - Größe der Datenbanken Im Rahmen des Wiederherstellungsprozesses muss die Datenbank mit Daten aus der letzten physischen Sicherung aufgefüllt werden. Daher ist der Zeitaufwand der Wiederherstellung proportional zur Größe der Datenbank.
   - Der aktive Teil der Transaktionsaktivität, der zur Wiederherstellung erneut durchgeführt werden muss Die Wiederherstellung kann abhängig von der zusätzlichen Transaktionsaktivität seit dem letzten erfolgreichen Prüfpunkt länger dauern.
   - Netzwerkbandbreite, sofern die Wiederherstellung in einer anderen Region erfolgt 
   - Anzahl der gleichzeitigen Wiederherstellungsanforderungen, die aktuell in der Zielregion verarbeitet werden 
   - Vorhandensein eines Primärschlüssels in den Tabellen in der Datenbank. Um die Wiederherstellung zu beschleunigen, sollten Sie für alle Tabellen in der Datenbank einen Primärschlüssel hinzufügen.  


## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
-   Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
-   Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)
