---
title: Sicherung und Wiederherstellung in Azure Database for PostgreSQL – Flexible Server
description: Erfahren Sie mehr über die Konzepte zur Sicherung und Wiederherstellung für Azure Database for PostgreSQL – Flexible Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 8c6f3ebaa72457d93e4b3e491656f494511bd994
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355709"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Sicherung und Wiederherstellung in Azure Database for PostgreSQL – Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – Flexible Server befindet sich in der Vorschau.

Sicherungen sind ein wesentlicher Teil jeder Strategie für Geschäftskontinuität. Sie tragen zum Schutz von Daten vor versehentlicher Beschädigung oder Löschung bei. Azure Database for PostgreSQL – Flexible Server sichert Ihren Server automatisch und bewahrt die Sicherungen für eine Dauer von bis zu 35 Tagen auf. Während der Wiederherstellung können Sie Datum und Uhrzeit für die Wiederherstellung innerhalb des Aufbewahrungszeitraums angeben. Die Gesamtzeit der Wiederherstellung hängt von der Größe der Datenbankdateien und dem Umfang der durchzuführenden Wiederherstellung ab. 

### <a name="backup-process-in-flexible-server"></a>Sicherungsprozess in der Flexible Server-Instanz
Die erste Momentaufnahmensicherung wird unmittelbar nach Erstellung der Flexible Server-Instanz geplant. Anschließend erfolgt eine tägliche Momentaufnahmensicherung der Datendateien. Sicherungen werden in einem zonenredundanten Speicher innerhalb einer Region gespeichert. Transaktionsprotokolle (Write-Ahead-Protokolle, WAL) werden auch fortlaufend archiviert, damit eine Wiederherstellung bis zur letzten Transaktion möglich ist, für die ein Commit erfolgt ist. Dank dieser Daten- und Protokollsicherungen können Sie für einen Server den Stand zu einem beliebigen Zeitpunkt wiederherstellen, der innerhalb Ihres konfigurierten Aufbewahrungszeitraums für Sicherungen liegt. Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

Wenn die Datenbank mit Hochverfügbarkeit konfiguriert ist, werden auf dem primären Server tägliche Momentaufnahmen erstellt. Auf dem Standbyserver werden kontinuierliche Protokollsicherungen durchgeführt.

> [!IMPORTANT]
>Auf angehaltenen Servern werden keine Sicherungen durchgeführt. Die Sicherungen werden jedoch fortgesetzt, wenn die Datenbank entweder automatisch nach 7 Tagen oder vom Benutzer gestartet wird.

Die Sicherungen können nur für Wiederherstellungsvorgänge in der Flexible Server-Instanz verwendet werden. Wenn Sie Daten vom flexiblen Server exportieren oder auf diesem importieren möchten, befolgen Sie die Methodik zur [Sicherung und Wiederherstellung](../howto-migrate-using-dump-and-restore.md).


### <a name="backup-retention"></a>die Aufbewahrung der Sicherung

Sicherungen werden basierend auf der Einstellung für den Aufbewahrungszeitraum der Sicherung für den Server aufbewahrt. Sie können einen Aufbewahrungszeitraum von 7 bis 35 Tagen auswählen. Der Standardaufbewahrungszeitraum ist 7 Tage. Sie können den Aufbewahrungszeitraum bei der Servererstellung festlegen oder ihn zu einem späteren Zeitpunkt ändern. Sicherungen werden auch für angehaltene Server aufbewahrt.

Der Aufbewahrungszeit für Sicherung bestimmt auch, für welchen zurückliegenden Zeitraum eine Zeitpunktwiederherstellung erfolgen kann, da dieser auf verfügbaren Sicherungen basiert. Der Aufbewahrungszeitraum kann auch als Wiederherstellungsfenster im Hinblick auf die Wiederherstellung behandelt werden. Alle Sicherungen, die zum Durchführen einer Zeitpunktwiederherstellung innerhalb des Aufbewahrungszeitraums für die Sicherung erforderlich sind, werden im Sicherungsspeicher aufbewahrt. Wenn der Aufbewahrungszeitraum für Sicherungen z. B. auf sieben Tage festgelegt ist, entspricht das Wiederherstellungsfenster den letzten sieben Tagen. In diesem Szenario werden alle Sicherungen und Protokolle aufbewahrt, die zum Wiederherstellen des Servers in den letzten sieben Tagen erforderlich sind. 


### <a name="backup-storage-cost"></a>Kosten für Sicherungsspeicher

Für die Flexible Server-Instanz werden bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung gestellt. Wenn zusätzlicher Sicherungsspeicher verwendet wird, wird dies in GB pro Monat berechnet. Wenn Sie beispielsweise einen Server mit 250 GiB Speicherkapazität bereitgestellt haben, stehen Ihnen 250 GiB Sicherungsspeicher ohne zusätzliche Kosten zur Verfügung. Wenn die Größe der tägliche Sicherung 25 GiB beträgt, steht Ihnen kostenloser Sicherungsspeicher für bis zu 10 Tage bereit. Die Nutzung des Sicherungsspeichers über 250 GiB hinaus wird Ihnen gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/postgresql/) in Rechnung gestellt.

Sie können die im Azure-Portal verfügbare Metrik für den [verwendeten Sicherungsspeicher](../concepts-monitoring.md) zum Überwachen des von einem Server belegten Sicherungsspeichers verwenden. Die Metrik für den belegten Sicherungsspeicher stellt den gesamten Speicherplatz dar, der von allen Datenbank- und Protokollsicherungen beansprucht wurde, die auf Grundlage des für den Server festgelegten Aufbewahrungszeitraums für Sicherungen aufbewahrt wurden.  Eine hohe Transaktionsaktivität auf dem Server kann dazu führen, dass die Sicherungsspeicherauslastung unabhängig von der Gesamtgröße der Datenbank zunimmt.

Das primäre Mittel zum Steuern der Sicherungsspeicherkosten besteht darin, den geeigneten Aufbewahrungszeitraum festzulegen und die richtige Sicherungsredundanzoptionen auszuwählen, um die gewünschten Wiederherstellungsziele zu erreichen.

> [!IMPORTANT]
> Georedundante Sicherungen werden derzeit für Flexible Server-Instanzen nicht unterstützt.

## <a name="point-in-time-restore-overview"></a>Übersicht für die Zeitpunktwiederherstellung

In einer Flexible Server-Instanz wird bei der Zeitpunktwiederherstellung ein neuer Server aus den Sicherungen der Flexible Server-Instanz in derselben Region wie Ihr Quellserver erstellt. Bei der Erstellung wird die Konfiguration für den Tarif, die Computegeneration, Anzahl virtueller Kerne, Speichergröße, den Aufbewahrungszeitraum für Sicherungen und die Option für Sicherungsredundanz des Quellservers verwendet. Außerdem werden Tags und Einstellungen wie VNET- und Firewalleinstellungen vom Quellserver übernommen. 

 > [!IMPORTANT]
> Wenn Sie eine Flexible Server-Instanz wiederherstellen, die mit zonenredundanter Hochverfügbarkeit konfiguriert ist, wird der wiederhergestellte Server ohne Hochverfügbarkeit und in der gleichen Region wie Ihr primärer Server konfiguriert. 

 ### <a name="restore-process"></a>Wiederherstellungsvorgang

Die physischen Datenbankdateien werden zunächst aus den Momentaufnahmensicherungen am Datenspeicherort des Servers wiederhergestellt. Die entsprechende Sicherung, die vor dem gewünschten Zeitpunkt erstellt wurde, wird automatisch ausgewählt und wiederhergestellt. Anschließend wird ein Wiederherstellungsprozess unter Verwendung von WAL-Dateien eingeleitet, um die Datenbank in einen konsistenten Zustand zu bringen. 

 Angenommen, die Sicherungen werden nachts um 23:00 Uhr durchgeführt. Wenn der Wiederherstellungspunkt der 15. August 2020 um 10:00 Uhr ist, wird die tägliche Sicherung vom 14. August 2020 wiederhergestellt. Die Datenbank wird bis am 25. August 2020 um 10:00 Uhr unter Verwendung der Transaktionsprotokollsicherung zwischen dem 24. August 23:00 Uhr und dem 25. August 10:00 Uhr wiederhergestellt. 

 Weitere Informationen zum Wiederherstellen des Datenbankservers finden Sie [hier](./how-to-restore-server-portal.md).

> [!IMPORTANT]
> Bei Wiederherstellungsvorgängen in der Flexible Server-Instanz wird ein neuer Datenbankserver erstellt und der vorhandene Datenbankserver nicht überschrieben.

Die Point-in-Time-Wiederherstellung ist für viele Szenarien hilfreich. Beispiele hierfür sind Fälle, in denen ein Benutzer versehentlich Daten löscht oder eine wichtige Tabelle oder Datenbank entfernt oder in denen eine Anwendung aufgrund eines Defekts fälschlicherweise Daten durch fehlerhafte Daten überschreibt. Dank der fortlaufenden Sicherung von Transaktionsprotokollen können Sie die letzte Transaktion wiederherstellen.

Sie können zwischen einem spätesten und einem benutzerdefinierten Wiederherstellungspunkt wählen.

-   **Letzter Wiederherstellungspunkt (Jetzt):** Dies ist die Standardoption, mit der Sie den Server auf den aktuellsten Zeitpunkt wiederherstellen können. 

-   **Benutzerdefinierter Wiederherstellungspunkt**: Mithilfe dieser Option können Sie einen beliebigen Zeitpunkt innerhalb des für diese Flexible Server-Instanz definierten Aufbewahrungszeitraums wählen. Standardmäßig wird der späteste Zeitpunkt in UTC automatisch ausgewählt, was nützlich ist, wenn Sie zu Testzwecken die letzte Transaktion wiederherstellen möchten, für die ein Commit durchgeführt wurde. Sie können optional auch andere Tage und Uhrzeiten wählen. 

Die geschätzte Wiederherstellungsdauer hängt von verschiedenen Faktoren ab, z. B. Datenbankgröße, Umfang der zu verarbeitenden Transaktionsprotokolle, Netzwerkbandbreite und der Gesamtanzahl der Datenbanken, die gleichzeitig in derselben Region wiederhergestellt werden müssen. Die gesamte Wiederherstellungsdauer beträgt normalerweise mehrere Minuten bis zu mehrere Stunden.

Wenn Sie Ihren Server in einem VNet konfiguriert haben, können Sie ihn im selben oder einem anderen VNet wiederherstellen. Den öffentlichen Zugriff können Sie jedoch nicht wiederherstellen. Wenn Sie Ihren Server mit öffentlichem Zugriff konfiguriert haben, können Sie keinen privaten Zugriff wiederherstellen.


> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden auch alle Datenbanken gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden. Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren [Verwaltungssperren](../../azure-resource-manager/management/lock-resources.md) nutzen.

## <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nach der Wiederherstellung der Datenbank können Sie die folgenden Aufgaben durchführen, damit Ihre Benutzer und Anwendungen wieder den Betrieb aufnehmen können:

-   Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll.

-   Sicherstellen, dass geeignete Firewallregeln auf Serverebene und VNet-Regeln vorhanden sind, damit Benutzer eine Verbindung herstellen können Diese Regeln werden nicht vom ursprünglichen Server kopiert.
  
-   Die Computekapazität des wiederhergestellten Servers kann nach Bedarf hoch- oder herunterskaliert werden.

-   Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind.

-   Konfigurieren der erforderlichen Warnungen.
  
-  Wenn Sie die mit Hochverfügbarkeit konfigurierte Datenbank wiederhergestellt haben und den wiederhergestellten Server mit Hochverfügbarkeit konfigurieren möchten, können Sie [diese Schritte](./how-to-manage-high-availability-portal.md) befolgen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="backup-related-questions"></a>Fragen zur Sicherung

* **Wie bewerkstelligt Azure die Sicherung meines Servers?**
 
    Azure Database for PostgreSQL ermöglicht standardmäßig automatische Sicherungen Ihres gesamten Servers (einschließlich aller erstellten Datenbanken) mit einer standardmäßigen Aufbewahrungsfrist von 7 Tagen. Eine tägliche inkrementelle Momentaufnahme der Datenbank wird ausgeführt. Die Protokolldateien (WAL) werden kontinuierlich in Azure Blob Storage archiviert.

* **Kann ich diese automatische Sicherung so konfigurieren, dass sie langfristig aufbewahrt wird?**
  
    Nein. Derzeit wird nur eine maximal Aufbewahrungsdauer von 35 Tagen unterstützt. Sie können manuelle Sicherungen durchführen und diese langfristig aufbewahren.

* **Wie führe ich eine manuelle Sicherung meiner Postgres-Server durch?**
  
    Sie können mit dem PostgreSQL-Tool pg_dump manuell eine Sicherung erstellen, wie [hier](https://www.postgresql.org/docs/current/app-pgdump.html)dokumentiert. Beispiele finden Sie in dieser [Dokumentation zu Upgrades/Migration](../howto-migrate-using-dump-and-restore.md), die sich auch für Sicherungen eignen. Wenn Sie Azure Database for PostgreSQL in Blobspeicher sichern möchten, lesen Sie unseren Tech Community-Blog [Backup Azure Database for PostgreSQL to a Blob Storage](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/backup-azure-database-for-postgresql-to-a-blob-storage/ba-p/803343). 

* **Welche Sicherungsfenster gibt es für meinen Server? Kann ich sie anpassen?**
  
    Sicherungsfenster werden grundsätzlich von Azure verwaltet und können nicht angepasst werden. Die erste vollständige Momentaufnahmensicherung wird unmittelbar nach der Erstellung des Servers eingeplant. Nachfolgende Momentaufnahmesicherungen sind inkrementelle Sicherungen, die einmal täglich erfolgen.

* **Werden meine Sicherungen verschlüsselt?**
  
    Ja. Alle Daten, Sicherungen und temporären Dateien für Azure Database for PostgreSQL, die im Zuge der Abfrageausführung erstellt werden, werden mithilfe der AES-256-Bit-Verschlüsselung verschlüsselt. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden. 

* **Kann ich einzelne/einige Datenbanken auf einem Server wiederherstellen?**
  
    Das Wiederherstellen einzelner oder einiger Datenbanken oder Tabellen wird nicht unterstützt. Sie müssen jedoch den gesamten Server auf einem neuen Server wiederherstellen und dann die benötigten Tabellen oder Datenbanken extrahieren und auf Ihren Server importieren.

* **Ist mein Server verfügbar, während die Sicherung erfolgt?**
    Ja. Sicherungen sind Onlinevorgänge unter Verwendung von Momentaufnahmen. Der Momentaufnahmevorgang dauert nur wenige Sekunden und beeinträchtigt keine Produktionsworkloads, sodass Hochverfügbarkeit für den Server sichergestellt ist. 

* **Müssen wir beim Einrichten des Wartungsfensters für den Server das Sicherungsfenster berücksichtigen?**
  
    Nein. Sicherungen werden intern als Teil des verwalteten Diensts ausgelöst und haben keinen Einfluss auf das verwaltete Wartungsfenster.

* **Wo werden meine automatisierten Sicherungen gespeichert, und wie verwalte ich deren Aufbewahrung?**
  
    Azure Database for PostgreSQL erstellt Sicherungen des Servers automatisch und speichert diese automatisch in zonenredundantem Speicher in Regionen, in denen mehrere Zonen unterstützt werden, oder in lokal redundantem Speicher in Regionen, die noch nicht mehrere Zonen unterstützen. Diese Sicherungsdateien können nicht exportiert werden. Sie können mithilfe von Sicherungen Ihren Server nur zu einem bestimmten Zeitpunkt wiederherstellen. Die Standardaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Optional können Sie die Aufbewahrungsdauer für Sicherungen auf bis zu 35 Tage festlegen.

* **Wie werden Sicherungen auf Servern mit aktivierter Hochverfügbarkeit ausgeführt?**
  
    Die Datenvolumes des flexiblen Servers werden mit inkrementellen Momentaufnahmen des primären Servers auf verwalteten Datenträgern gesichert. Die WAL-Sicherung wird entweder vom primären oder Standbyserver ausgeführt.

* **Wie kann ich überprüfen, ob Sicherungen auf meinem Server ausgeführt werden?**

    Die beste Möglichkeit, die Verfügbarkeit gültiger Sicherungen zu überprüfen, ist die regelmäßige zeitpunktbezogene Wiederherstellung und Bestätigung, dass Sicherungen gültig und wiederherstellbar sind. Sicherungsvorgänge oder -dateien werden den Endbenutzern nicht verfügbar gemacht.

* **Wo kann ich die Sicherungsverwendung einsehen?**
  
    Klicken Sie im Azure-Portal unter „Überwachung“ auf „Metriken“. Dort finden Sie die Metrik „Sicherungsverwendung“, in der Sie die gesamte Sicherungsverwendung überwachen können.

* **Was geschieht mit meinen Sicherungen, wenn ich meinen Server lösche?**
  
    Wenn Sie den Server löschen, werden auch alle Sicherungen gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden. Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren Verwaltungssperren nutzen.

* **Wie werden Sicherungen für beendete Server beibehalten?**

    Für beendete Server werden keine neuen Sicherungen ausgeführt. Alle älteren Sicherungen (innerhalb des Aufbewahrungszeitfensters) zum Zeitpunkt des Beendens des Servers werden bis zum Neustart des Servers aufbewahrt, wonach die Aufbewahrung der Sicherungen für den aktiven Server durch dessen Aufbewahrungszeitfenster bestimmt wird.

* **Wie werden meine Sicherungen in Rechnung gestellt und abgerechnet?**
  
    Für die Flexible Server-Instanz werden bis zu 100 % Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher zur Verfügung gestellt. Jeder zusätzlich genutzte Sicherungsspeicher wird gemäß dem Preismodell in GB pro Monat in Rechnung gestellt. Die Abrechnung des Sicherungsspeichers hängt auch von der ausgewählten Aufbewahrungsfrist für Sicherungen und der gewählten Redundanzoption ab – abgesehen von der Transaktionsaktivität auf dem Server, die sich direkt auf den gesamten verwendeten Sicherungsspeicher auswirkt.

* **Wie wird mir ein beendeter Server in Rechnung gestellt?**
  
    Während Ihre Serverinstanz beendet wird, werden keine neuen Sicherungen ausgeführt. Ihnen wird bereitgestellter Speicher und Sicherungsspeicher (Sicherungen, die innerhalb des von Ihnen festgelegten Aufbewahrungszeitraums gespeichert werden) in Rechnung gestellt. Kostenloser Sicherungsspeicher ist auf die Größe Ihrer bereitgestellten Datenbank begrenzt. Überschüssige Sicherungsdaten werden zum Preis für die Sicherung in Rechnung gestellt.

* **Ich habe meinen Server mit zonenredundanter Hochverfügbarkeit konfiguriert. Erstellen Sie zwei Sicherungen, und werden mir zweimal Gebühren in Rechnung gestellt?**
  
    Nein. Unabhängig von Servern mit oder ohne Hochverfügbarkeit wird nur eine Sicherungskopie angelegt, die Ihnen nur einmal in Rechnung gestellt wird.

### <a name="restore-related-questions"></a>Fragen zur Wiederherstellung

* **Wie kann ich meinen Server wiederherstellen?**

    Azure unterstützt die Zeitpunktwiederherstellung (für alle Server), sodass Benutzer mithilfe von Azure-Portal, Azure CLI und API eine Wiederherstellung gemäß dem neuesten oder einem benutzerdefinierten Wiederherstellungspunkt vornehmen können. 

    Um Ihren Server aus manuell mit Tools wie pg_dump erstellten Sicherungen wiederherzustellen, können Sie zunächst einen flexiblen Server erstellen und Ihre Datenbanken mit [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) auf diesem Server wiederherstellen.

* **Ist eine Wiederherstellung in einer anderen Verfügbarkeitszone innerhalb derselben Region möglich?**
  
    Ja. Wenn die Region mehrere Verfügbarkeitszonen unterstützt, wird die Sicherung im ZRS-Konto gespeichert und ermöglicht Ihnen die Wiederherstellung in einer anderen Zone. 

* **Wie lange dauert eine Zeitpunktwiederherstellung? Warum dauert meine Wiederherstellung so lange?**
  
    Der Vorgang zur Datenwiederherstellung aus einer Momentaufnahme hängt nicht von der Größe der Daten ab, jedoch kann der Zeitplan für den Wiederherstellungsprozess, der die Protokolle (Transaktionsaktivitäten zur Wiedergabe) anwendet, abhängig von der vorherigen Sicherung des angeforderten Datums/Zeitpunkts und der Menge der zu verarbeitenden Protokolle variieren. Dies gilt sowohl für die Wiederherstellung innerhalb derselben Zone als auch für eine andere Zone. 
 
* **Wenn ich meinen Server mit Hochverfügbarkeit wiederherstelle, wird der Wiederherstellungsserver automatisch mit Hochverfügbarkeit konfiguriert?**
  
    Nein. Der Server wird als flexibler Einzelinstanzserver wiederhergestellt. Nach Abschluss der Wiederherstellung können Sie den Server optional mit Hochverfügbarkeit konfigurieren.

* **Ich habe meinen Server in einem VNet konfiguriert. Ist eine Wiederherstellung in einem anderen VNet möglich?**
  
    Ja. Wählen Sie zum Zeitpunkt der Wiederherstellung ein anderes VNet aus, in dem die Wiederherstellung erfolgen soll.

* **Kann ich meinen öffentlichen Zugriffsserver in einem VNet wiederherstellen oder umgekehrt?**

    Nein. Die Wiederherstellung von Servern mit öffentlichem und privatem Zugriff wird derzeit nicht unterstützt.

* **Wie kann ich meine Wiederherstellung nachverfolgen?**
  
    Derzeit gibt es keine Möglichkeit, den Wiederherstellungsvorgang nachzuverfolgen. Sie können das Aktivitätsprotokoll überwachen, um zu prüfen, ob der Vorgang in Bearbeitung oder abgeschlossen ist.


## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
-   Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
-   Weitere Informationen zur [Wiederherstellung](./how-to-restore-server-portal.md)