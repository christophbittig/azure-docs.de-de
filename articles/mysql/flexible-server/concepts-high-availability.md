---
title: Übersicht über zonenredundante Hochverfügbarkeit mit Azure Database for MySQL Flexible Server
description: In diesem Artikel lernen Sie die Konzepte der zonenredundanten Hochverfügbarkeit mit Azure Database for MySQL Flexible Server kennen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: d78bb5aeb111411641508b38bd49a6f5bcbf5374
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122340552"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Hochverfügbarkeitskonzepte in Azure Database for MySQL Flexible Server (Vorschau)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Azure Database for MySQL – Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

In Azure Database for MySQL Flexible Server (Vorschauversion) können Sie die Hochverfügbarkeit mit automatischem Failover konfigurieren. Wenn Hochverfügbarkeit konfiguriert ist, stellt Flexible Server automatisch ein Standbyreplikat mit zwei verschiedenen Optionen bereit und verwaltet dieses.

* **Zonenredundante Hochverfügbarkeit:** Diese Option wird für die vollständige Isolation und Redundanz der Infrastruktur für mehrere Verfügbarkeitszonen bevorzugt. Sie bietet den höchsten Verfügbarkeitsgrad, erfordert aber eine zonenübergreifende Konfiguration der Anwendungsredundanz. Zonenredundante Hochverfügbarkeit wird bevorzugt, wenn bei jedem Infrastrukturausfall in der Verfügbarkeitszone die bestmögliche Verfügbarkeit erzielt werden soll und die Latenz in der Verfügbarkeitszone akzeptabel ist. Zonenredundante Hochverfügbarkeit ist nur in [Azure-Regionen](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) verfügbar, die mehrere Verfügbarkeitszonen und zonenredundante Hochverfügbarkeit unterstützen.

* **Hochverfügbarkeit in gleicher Zone:** Diese Option wird für Infrastrukturredundanz mit geringerer Netzwerklatenz bevorzugt, da sich sowohl der primäre Server als auch der Standbyserver in derselben Verfügbarkeitszone befinden. Sie bietet Hochverfügbarkeit ohne die zonenübergreifende Konfiguration der Anwendungsredundanz. Die Hochverfügbarkeit in gleicher Zone wird bevorzugt, wenn Sie die bestmögliche Verfügbarkeit innerhalb einer einzelnen Verfügbarkeitszone mit der geringstmöglichen Netzwerklatenz erreichen möchten. Die Hochverfügbarkeit in gleicher Zone ist in allen [Azure-Regionen](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) verfügbar, in denen Flexible Server verfügbar ist.  

## <a name="zone-redundant-high-availability"></a>Zonenredundante Hochverfügbarkeit

Wenn der flexible Server mit aktivierter zonenredundanter Hochverfügbarkeit erstellt wird, werden die Daten- und Protokolldateien in einem [zonenredundanten Speicher (ZRS)](../../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) gehostet. Dank der mit ZRS verfügbaren Replikation auf Speicherebene werden die Daten- und Protokolldateien synchron auf den Standbyserver repliziert, um Datenverlust auszuschließen. Das Failover ist für die Clientanwendung vollständig transparent und benötigt keinerlei Benutzeraktionen. Die Wiederherstellung des Onlinestatus eines Standbyservers während des Failovers hängt von der Binärprotokollanwendung auf diesem Server ab. Es wird daher empfohlen, Primärschlüssel für alle Tabellen zu verwenden, um die Failoverzeit zu reduzieren. Der Standbyserver ist nicht für Lese- oder Schreibvorgänge verfügbar, fungiert jedoch als passive Reserve, um ein schnelles Failover zu ermöglichen. Die Dauer für Failover befindet sich in der Regel zwischen 60 und 120 Sekunden.

> [!Note]
> Die zonenredundante Hochverfügbarkeit kann zu einem Latenzabfall von 5 bis 10 % führen, wenn die Anwendung eine verfügbarkeitszonenübergreifende Verbindung mit dem Datenbankserver herstellt und die Netzwerklatenz in diesen Verfügbarkeitszonen im Vergleich um 2 bis 4 Millisekunden höher liegt. 

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Zonenredundante Hochverfügbarkeit":::

### <a name="zone-redundancy-architecture"></a>Architektur der Zonenredundanz

Der primäre Server wird in der Region und in einer bestimmten Verfügbarkeitszone bereitgestellt. Wenn die Hochverfügbarkeit ausgewählt wird, wird automatisch ein Standbyreplikatserver mit derselben Konfiguration wie der primäre Server in der angegebenen Verfügbarkeitszone bereitgestellt, einschließlich identischer Computeebene, Computegröße, Speichergröße und Netzwerkkonfiguration. Die Protokolldaten werden synchron mit dem Standbyreplikat repliziert, um dafür zu sorgen, dass es bei Fehlern zu keinerlei Datenverlust kommt. Automatische Sicherungen, sowohl Momentaufnahmen als auch Protokollsicherungen, werden in zonenredundantem Speicher auf dem primären Datenbankserver durchgeführt.

### <a name="standby-zone-selection"></a>Auswahl der Standbyzone
In einem Szenario mit zonenredundanter Hochverfügbarkeit können Sie den Standort der Standbyserverzone beliebig auswählen. Durch das Bereitstellen der Standby-Datenbankserver und Standbyanwendungen in derselben Zone werden Latenzen reduziert, und Benutzer können sich besser auf Notfallwiederherstellungen und Zonenausfälle vorbereiten.

## <a name="same-zone-high-availability"></a>Hochverfügbarkeit in gleicher Zone

Wenn der flexible Server mit Hochverfügbarkeit in gleicher Zone erstellt wird, werden die Daten- und Protokolldateien in einem [lokal redundanten Speicher (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage) gehostet. Dank der mit LRS verfügbaren Replikation auf Speicherebene werden die Daten- und Protokolldateien synchron auf den Standbyserver repliziert, um Datenverlust auszuschließen. Der Standbyserver bietet Infrastrukturredundanz durch einen separaten virtuellen Computer (Compute), der die Failoverzeit und Netzwerklatenz zwischen der Benutzeranwendung und dem Datenbankserver aufgrund der Colocation reduziert. Das Failover ist für die Clientanwendung vollständig transparent und benötigt keinerlei Benutzeraktionen. Die Wiederherstellung des Onlinestatus eines Standbyservers während des Failovers hängt von der Binärprotokollanwendung auf diesem Server ab. Es wird daher empfohlen, Primärschlüssel für alle Tabellen zu verwenden, um die Failoverzeit zu reduzieren. Der Standbyserver ist nicht für Lese- oder Schreibvorgänge verfügbar, fungiert jedoch als passive Reserve, um ein schnelles Failover zu ermöglichen. Die Dauer für Failover befindet sich in der Regel zwischen 60 und 120 Sekunden.

Die Hochverfügbarkeit in gleicher Zone ermöglicht es Benutzern, einen Standbyserver in derselben Zone wie der primäre Server bereitzustellen, wodurch die Replikationsverzögerung zwischen dem primären Server und dem Standbyserver reduziert wird. Dadurch entstehen auch niedrigere Latenzen zwischen dem Anwendungsserver und dem Datenbankserver, wenn diese in derselben Azure-Verfügbarkeitszone bereitgestellt werden.

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Hochverfügbarkeit in gleicher Zone":::

## <a name="high-availability-monitoring"></a>Überwachung der Hochverfügbarkeit
Die Integrität der Hochverfügbarkeit wird ununterbrochen überwacht und an die Übersichtsseite gemeldet. Eine Übersicht über die verschiedenen Replikationsstatus finden Sie unten:

| **Status** | **Beschreibung** |
| :----- | :------ |
| <b>NotEnabled | Die zonenredundante Hochverfügbarkeit ist nicht aktiviert. |
| <b>ReplicatingData | Nach Erstellung des Standbyreplikats wird es mit dem primären Server synchronisiert. |
| <b>FailingOver | Der Datenbankserver führt ein Failover vom primären Server auf das Standbyreplikat durch. |
| <b>Healthy | Die zonenredundante Hochverfügbarkeit weist einen stabilen Zustand auf, und die Integrität ist gesichert. |
| <b>RemovingStandby | Auf Grundlage einer Benutzeraktion wird das Standbyreplikat gelöscht.| 

## <a name="advantages"></a>Vorteile

Unten finden Sie einige Vorteile der Verwendung des Features für zonenredundante Hochverfügbarkeit: 

- Standbyreplikate werden hinsichtlich Anzahl virtueller Kerne, Speicher und Netzwerkeinstellungen (virtuelles Netzwerk, Firewall) mit der exakt gleichen VM-Konfiguration wie das primäre Replikat bereitgestellt.
- Es besteht die Möglichkeit, Standbyreplikate zu entfernen, indem die Hochverfügbarkeit deaktiviert wird.
- Automatische Sicherungen basieren auf Momentaufnahmen, werden vom primären Datenbankserver ausgeführt und je nach Hochverfügbarkeitsoption in einem zonenredundanten oder lokal redundanten Speicher gespeichert.
- Bei einem Failover führt der flexible Azure Database for MySQL-Server automatisch ein Failover zum Standbyreplikat aus, wenn Hochverfügbarkeit aktiviert ist. Das Hochverfügbarkeitssetup überwacht den primären Server und schaltet ihn wieder online.
- Clients stellen immer eine Verbindung mit dem primären Datenbankserver her.
- Wenn eine Datenbank abstürzt oder ein Knoten ausfällt, wird die Flexible Server-VM auf demselben Knoten neu gestartet. Gleichzeitig wird ein automatisches Failover ausgelöst. Wenn die Flexible Server-VM vor Abschluss des Failovers erfolgreich neu gestartet wird, wird der Failovervorgang abgebrochen.
- Es besteht die Möglichkeit, den Server neu zu starten, sodass alle Änderungen an statischen Serverparametern übernommen werden.

## <a name="steady-state-operations"></a>Vorgänge mit stabilem Zustand

Anwendungen sind mit dem primären Server über den Datenbankservernamen verbunden. Die Informationen des Standbyreplikats werden nicht für den Direktzugriff verfügbar gemacht. Commit- und Schreibvorgänge werden bestätigt, nachdem die Protokolldateien im zonenredundanten Speicher (ZRS) des primären Servers geleert wurden. Aufgrund der synchronen Replikationstechnologie, die im ZRS verwendet wird, können Anwendungen eine niedrige Latenz für Schreib- und Commitvorgänge erwarten.

## <a name="failover-process"></a>Failoverprozess 
Zur Gewährleistung einer Geschäftskontinuität müssen Sie über einen Failoverprozess für geplante und ungeplante Ereignisse verfügen. 

>[!NOTE]
> Verwenden Sie immer den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN), um eine Verbindung zum primären Server herzustellen, und vermeiden Sie die Verwendung der IP-Adresse für die Verbindung. Bei einem Failover kann sich auch der DNS-A-Eintrag ändern, wenn die primäre Serverrolle und die Standbyserverrolle wechseln, sodass die Anwendung keine Verbindung mit dem neuen primären Server herstellen kann, wenn die IP-Adresse in der Verbindungszeichenfolge verwendet wird. 

### <a name="planned-events---forced-failover"></a>Geplante Ereignisse: Erzwungenes Failover

Das erzwungene Failover von Azure Database for MySQL ermöglicht es Ihnen, ein Failover manuell zu erzwingen, sodass Sie die Funktionalität bei Ihren Anwendungsszenarios testen und bei irgendwelchen Ausfällen bereit sein können. Durch erzwungenes Failover wird der Standbyserver zum primären Server, indem ein Failover ausgelöst wird. Dadurch wird das Standbyreplikat aktiviert und – durch Aktualisierung des DNS-Eintrags – zum primären Server mit demselben Datenbankservernamen. Der ursprüngliche primäre Server wird neu gestartet und auf das Standbyreplikat umgeschaltet. Clientverbindungen werden getrennt und müssen erneut verbunden werden, um ihre Vorgänge fortsetzen zu können. Je nach der aktuellen Workload und dem letzten Prüfpunkt wird die Failovergesamtdauer gemessen. Normalerweise sollte sie zwischen 60 und 120 Sekunden betragen. 

### <a name="failover-process---unplanned-events"></a>Failoverprozess: nicht geplante Ereignisse
Zu nicht geplanten Dienstdowntimes gehören Softwarefehler oder Infrastrukturausfälle wie bei Compute-, Netzwerk- oder Speicherfehlern oder Stromausfälle, die die Verfügbarkeit der Datenbank beeinträchtigen. Falls eine Datenbank nicht verfügbar ist, wird die Replikation auf das Standbyreplikat abgebrochen, und das Standbyreplikat wird als primäre Datenbank aktiviert. Das DNS wird aktualisiert, und Clients stellen dann eine neue Verbindung zum Datenbankserver her und setzen ihre Vorgänge fort. Die Failovergesamtdauer beträgt erwartungsgemäß zwischen 60 und 120 Sekunden. Je nach Aktivität auf dem primären Datenbankserver zum Zeitpunkt des Failovers, z. B. große Transaktionen, und abhängig von der Wiederherstellungszeit kann das Failover jedoch auch etwas länger dauern.

## <a name="schedule-maintenance-window"></a>Zeitplan für Wartungsfenster 

Flexible Server bieten die Möglichkeit, die Wartung zu planen. Dabei können Sie ein dreißigminütiges Zeitfenster an einem Tag Ihrer Wahl auswählen, während dem Wartungsarbeiten in Azure wie Patchimplementierungen oder Nebenversionsupgrades durchgeführt werden. Für Ihre flexiblen Server mit konfigurierter Hochverfügbarkeit werden diese Wartungsaktivitäten zuerst auf dem Standbyreplikat durchgeführt. 

## <a name="point-in-time-restore"></a>Wiederherstellung bis zu einem bestimmten Zeitpunkt 
Da Flexible Server mit konfigurierter Hochverfügbarkeit Daten synchron repliziert, sind Standbyserver und primärer Server immer auf dem gleichen aktuellen Stand. Fehler durch Benutzer auf dem primären Replikat, z. B. das versehentliche Löschen einer Tabelle oder fehlerhafte Aktualisierungen, werden identisch für den Standbyserver übernommen. Folglich können Standbyserver nicht dazu verwendet werden, Wiederherstellungen für solche logischen Fehler durchzuführen. Zur Wiederherstellung solcher logischen Fehler müssen Sie eine Zeitpunktwiederherstellung für einen Zeitpunkt durchführen, der vor dem Auftreten des Fehlers liegt. Mithilfe der Zeitpunktwiederherstellungsfunktion von Flexible Server wird beim Wiederherstellen einer Datenbank mit Hochverfügbarkeit ein neuer Datenbankserver als neuer Flexible Server mit einem vom Benutzer angegebenen Namen wiederhergestellt. Sie können dann das Objekt vom Datenbankserver exportieren und in Ihren Produktionsdatenbankserver importieren. Wenn Sie Ihren Datenbankserver für Test- und Entwicklungszwecke klonen möchten oder wenn Sie ihn aus anderen Gründen wiederherstellen möchten, können Sie in ähnlicher Weise entweder den neuesten Wiederherstellungspunkt oder einen benutzerdefinierten Wiederherstellungspunkt auswählen. Beim Wiederherstellungsvorgang wird ein flexibler Server mit einzelner Zone erstellt.

## <a name="mitigate-downtime"></a>Minimieren von Downtime 
Wenn Sie nicht das Feature für zonenredundante Hochverfügbarkeit nutzen, müssen Sie dennoch in der Lage sein, die Downtime für Ihre Anwendung zu minimieren. Dienstdowntimes, z. B. im Rahmen geplanter Patches, Upgrades für Nebenversionen oder von Benutzern ausgehenden Vorgängen, können innerhalb geplanter Wartungsfensters eingeschlossen werden. Geplante Dienstdowntimes, z. B. geplante Patches, Upgrades für Nebenversionen oder von Benutzern ausgehende Vorgänge wie die Skalierung von Computekapazitäten, sorgen für Downtime. Sie können sich dazu entschließen, von Azure ausgehende Wartungstasks für die Wochentage und die Uhrzeiten zu planen, die sich am wenigsten auf die Anwendung auswirken, um die Beeinträchtigungen möglichst gering zu halten. 

Während ungeplanter Downtimes, beispielsweise als Folge eines Datenbankabsturzes oder einem Serverausfall, wird der beeinträchtigte Server innerhalb derselben Zone neu gestartet. Wenn eine gesamte Zone betroffen sein sollte, was selten vorkommt, können Sie die Datenbank in einer anderen Zone derselben Region wiederherstellen. 

## <a name="things-to-know-with-zone-redundancy"></a>Wichtige Informationen zur Zonenredundanz 

Unten finden Sie einige Hinweise, die Sie beachten sollten, wenn Sie die zonenredundante Hochverfügbarkeit nutzen: 

- Hochverfügbarkeit kann **ausschließlich** während der Erstellung eines flexiblen Servers festgelegt werden.
- Hochverfügbarkeit wird für burstfähige Computeebenen nicht unterstützt.
- Aufgrund der synchronen Replikation in eine andere Verfügbarkeitszone kann es beim primären Datenbankserver zu einer erhöhten Latenz für Schreib- und Commitvorgänge kommen.
- Standbyreplikate können nicht für schreibgeschützte Abfragen verwendet werden.
- Je nach Aktivität des primären Servers zum Zeitpunkt eines Failovers kann es zwischen 60 und 120 Sekunden oder länger dauern, bis das Failover abgeschlossen ist.
- Ein Neustart des primären Datenbankservers für eine Übernahme von Änderungen an statischen Parametern führt auch zu einem Neustart des Standbyreplikats.
- Die Konfiguration von Lesereplikaten für Server mit zonenredundanter Hochverfügbarkeit wird nicht unterstützt.
- Das Konfigurieren von Verwaltungsaufgaben, die vom Kunden ausgehen, kann während eines verwalteten Wartungsfensters nicht automatisiert werden.
- Geplante Ereignisse wie das Skalieren von Computekapazitäten und Upgrades für Nebenversionen werden für den primären Server und das Standbyreplikat zur selben Zeit durchgeführt. 


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
- Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
- Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)