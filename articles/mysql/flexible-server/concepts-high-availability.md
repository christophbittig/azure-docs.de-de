---
title: Übersicht über zonenredundante Hochverfügbarkeit mit Azure Database for MySQL Flexible Server
description: In diesem Artikel lernen Sie die Konzepte der zonenredundanten Hochverfügbarkeit mit Azure Database for MySQL Flexible Server kennen.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4a8f7d5bd10c21b14c8935bf0a44040501ebeb90
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123109187"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Hochverfügbarkeitskonzepte in Azure Database for MySQL Flexible Server (Vorschau)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Azure Database for MySQL – Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server (Vorschauversion) ermöglicht das Konfigurieren von Hochverfügbarkeit mit automatischem Failover. Die Hochverfügbarkeitslösung ist so konzipiert, dass Daten, für die ein Commit ausgeführt wurde, nie aufgrund von Fehlern verloren gehen und die Datenbank keinen Single Point of Failure (SPOF) in der Softwarearchitektur darstellt. Beim Konfigurieren von Hochverfügbarkeit stellt Flexible Server automatisch ein Standbyreplikat bereit und verwaltet dieses. Es gibt zwei Architekturmodelle für Hochverfügbarkeit:

* **Zonenredundante Hochverfügbarkeit**: Diese Option wird für eine vollständige Isolierung und Redundanz der Infrastruktur über mehrere Verfügbarkeitszonen hinweg empfohlen. Sie bietet den höchsten Verfügbarkeitsgrad, erfordert aber eine zonenübergreifende Konfiguration der Anwendungsredundanz. Zonenredundante Hochverfügbarkeit wird bevorzugt, wenn bei jedem Infrastrukturausfall in der Verfügbarkeitszone die bestmögliche Verfügbarkeit erzielt werden soll und die Latenz in der Verfügbarkeitszone akzeptabel ist. Die zonenredundante Hochverfügbarkeit kann nur zum Zeitpunkt der Servererstellung aktiviert werden. Die zonenredundante Hochverfügbarkeit ist nur in [Azure-Regionen](./overview.md#azure-regions) verfügbar, die mehrere [Verfügbarkeitszonen](../../availability-zones/az-overview.md) und [zonenredundante Premium-Dateifreigaben](../..//storage/common/storage-redundancy.md#zone-redundant-storage) unterstützen.

* **Hochverfügbarkeit in gleicher Zone**: Diese Option wird für Infrastrukturredundanz mit geringerer Netzwerklatenz empfohlen, da sich der Primär- und der Standbyserver in derselben Verfügbarkeitszone befinden. Sie bietet Hochverfügbarkeit ohne die zonenübergreifende Konfiguration der Anwendungsredundanz. Die Option für Hochverfügbarkeit in gleicher Zone wird bevorzugt, wenn Sie die bestmögliche Verfügbarkeit innerhalb einer einzelnen Verfügbarkeitszone mit der geringstmöglichen Netzwerklatenz erreichen möchten. Hochverfügbarkeit in gleicher Zone steht in [allen Azure-Regionen](./overview.md#azure-regions) zur Verfügung, in denen Sie Azure Database for MySQL Flexible Server-Instanzen erstellen können.  

## <a name="zone-redundancy-high-availability-architecture"></a>Architektur mit zonenredundanter Hochverfügbarkeit
 
Wenn Sie einen Server mit zonenredundanter Hochverfügbarkeit bereitstellen, wird in einer Verfügbarkeitszone ein primärer Server und in einer anderen Verfügbarkeitszone derselben Azure-Region ein Standbyreplikatserver erstellt, der die gleiche Konfiguration aufweist wie der primären Server (Compute-Ebene Computegröße, Speichergröße und Netzwerkkonfiguration). Für das primäre Replikat und das Standbyreplikat können Sie eine Verfügbarkeitszone Ihrer Wahl angeben. Durch das Bereitstellen der Standbydatenbankserver und Standbyanwendungen in derselben Zone werden Latenzen reduziert, und Benutzer können sich besser auf Notfallwiederherstellungen und Zonenausfälle vorbereiten.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Zonenredundante Hochverfügbarkeit":::

Die Daten- und Protokolldateien werden in einem [zonenredundanten Speicher (ZRS) gehostet](../../storage/common/storage-redundancy.md#redundancy-in-the-primary-region). Dank der mit ZRS verfügbaren Replikation auf Speicherebene werden die Daten- und Protokolldateien auf den Standbyserver repliziert. Bei einem Failover wird das Standbyreplikat aktiviert, und die binären Protokolldateien des primären Servers werden weiterhin auf den Standbyserver angewendet, um ihn mit der letzten Transaktion auf dem Primärserver online zu schalten, für die ein Commit ausgeführt wurde. Durch die Speicherung von Protokollen im ZRS sind die Protokolle auch dann zugänglich, wenn der primäre Server nicht verfügbar ist. Auf diese Weise wird sichergestellt, dass es nicht zu einem Datenverlust kommt. Sobald das Standbyreplikat aktiviert und die binären Protokolle angewendet wurden, übernimmt der aktuelle Standbyreplikatserver die Funktionen des primären Servers. DNS wird aktualisiert, sodass die Clientverbindungen an den neuen primären Server geleitet werden, wenn der Client erneut eine Verbindung herstellt. Das Failover ist für die Clientanwendung vollständig transparent und benötigt keinerlei Benutzeraktionen. Die Hochverfügbarkeitslösung schaltet dann nach Möglichkeit den alten primären Server wieder online und legt ihn als Standbyserver fest.
 
Anwendungen sind mit dem primären Server über den Datenbankservernamen verbunden. Die Informationen des Standbyreplikats werden nicht für den Direktzugriff verfügbar gemacht. Commit- und Schreibvorgänge werden bestätigt, nachdem die Protokolldateien im zonenredundanten Speicher (ZRS) des primären Servers geleert wurden. Aufgrund der im ZRS verwendeten synchronen Replikationstechnologie muss für Anwendungen mit einer um 5–10 % höheren Latenzzeit für Schreibvorgänge und Commits gerechnet werden.
 
Automatische Sicherungen, sowohl Momentaufnahmen als auch Protokollsicherungen, werden in zonenredundantem Speicher auf dem primären Datenbankserver durchgeführt.

## <a name="same-zone-high-availability-architecture"></a>Architektur mit Hochverfügbarkeit in gleicher Zone
 
Wenn Sie einen Server mit der Option für Hochverfügbarkeit in gleicher Zone bereitstellen, werden innerhalb derselben Zone ein primärer Server und ein Standbyreplikatserver erstellt, die beide die Konfiguration des primären Servers aufweisen (Compute-Ebene Computegröße, Speichergröße und Netzwerkkonfiguration). Der Standbyserver bietet Infrastrukturredundanz durch einen separaten virtuellen Computer (Compute), der die Failoverzeit und Netzwerklatenz zwischen der Benutzeranwendung und dem Datenbankserver aufgrund der Colocation reduziert.

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Hochverfügbarkeit in gleicher Zone":::

Die Daten- und Protokolldateien werden in einem [lokal redundanten Speicher (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage) gehostet. Dank der mit LRS verfügbaren Replikation auf Speicherebene werden die Daten- und Protokolldateien auf den Standbyserver repliziert.

Bei einem Failover wird das Standbyreplikat aktiviert, und die binären Protokolldateien des primären Servers werden weiterhin auf den Standbyserver angewendet, um ihn mit der letzten Transaktion auf dem Primärserver online zu schalten, für die ein Commit ausgeführt wurde. Durch die Speicherung von Protokollen im LRS sind die Protokolle auch dann zugänglich, wenn der primäre Server nicht verfügbar ist. Auf diese Weise wird sichergestellt, dass es nicht zu einem Datenverlust kommt. Sobald das Standbyreplikat aktiviert und die binären Protokolle angewendet wurden, übernimmt das aktuelle Standbyreplikat die Funktionen des primären Servers. DNS wird aktualisiert, um die Verbindungen zum neuen primären Server umzuleiten, wenn der Client erneut eine Verbindung herstellt. Das Failover ist für die Clientanwendung vollständig transparent und benötigt keinerlei Benutzeraktionen. Die Hochverfügbarkeitslösung schaltet dann nach Möglichkeit den alten primären Server wieder online und legt ihn als Standbyserver fest.
 
Anwendungen sind mit dem primären Server über den Datenbankservernamen verbunden. Die Informationen des Standbyreplikats werden nicht für den Direktzugriff verfügbar gemacht. Commit- und Schreibvorgänge werden bestätigt, nachdem die Protokolldateien im lokal redundanten Speicher (LRS) des primären Servers geleert wurden. Da sich das primäre und das Standbyreplikat in derselben Zone befinden, ist die Replikationsverzögerung geringer, und die Latenzzeiten zwischen dem Anwendungsserver und dem Datenbankserver werden verkürzt, wenn sie in derselben Azure-Verfügbarkeitszone platziert werden. Das Setup in derselben Zone bietet keine Hochverfügbarkeit für ein Szenario, in dem abhängige Infrastrukturen für die spezifische Verfügbarkeitszone nicht verfügbar sind.   Es kommt zu einer Downtime, bis alle abhängigen Dienste für diese Verfügbarkeitszone wieder online sind.
 
Automatische Sicherungen – sowohl Momentaufnahmen als auch Protokollsicherungen – werden in einem lokal redundantem Speicher des primären Datenbankservers durchgeführt.

>[!Note]
>Sowohl für zonenredundante Hochverfügbarkeit als auch für Hochverfügbarkeit in gleicher Zone gilt Folgendes:
>* Bei einem Ausfall hängt die Zeit, die das Standbyreplikat benötigt, um die Rolle des primären Servers zu übernehmen, von der Anwendung der Binärprotokolle auf den Standbyserver ab. Es wird daher empfohlen, Primärschlüssel für alle Tabellen zu verwenden, um die für das Failover erforderliche Zeit zu verringern. Die Dauer für Failover befindet sich in der Regel zwischen 60 und 120 Sekunden. 
>* Der Standbyserver ist nicht für Lese- oder Schreibvorgänge verfügbar, fungiert jedoch als passive Reserve, um ein schnelles Failover zu ermöglichen.
>* Verwenden Sie immer den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN), um eine Verbindung zum primären Server herzustellen, und vermeiden Sie die Verwendung der IP-Adresse für die Verbindung. Bei einem Failover kann sich auch der DNS-A-Eintrag ändern, wenn die primäre Serverrolle und die Standbyserverrolle wechseln, sodass die Anwendung keine Verbindung mit dem neuen primären Server herstellen kann, wenn die IP-Adresse in der Verbindungszeichenfolge verwendet wird.

## <a name="failover-process"></a>Failoverprozess 
 
### <a name="planned---forced-failover"></a>Geplant: erzwungenes Failover
 
Das erzwungene Failover von Azure Database for MySQL Flexible Server ermöglicht es Ihnen, ein Failover manuell zu erzwingen, sodass Sie die Funktionalität für Ihre Anwendungsszenarien testen können und auf potenzielle Ausfälle vorbereitet sind. Beim erzwungenen Failover wird der Standbyserver zum primären Server, indem ein Failover ausgelöst wird. Durch das Failover wird das Standbyreplikat aktiviert und als Primärserver mit demselben Datenbankservernamen festgelegt, indem der DNS-Eintrag aktualisiert wird. Der ursprüngliche primäre Server wird neu gestartet und auf das Standbyreplikat umgeschaltet. Clientverbindungen werden getrennt und müssen erneut verbunden werden, um ihre Vorgänge fortsetzen zu können. Je nach der aktuellen Workload und dem letzten Prüfpunkt wird die Failovergesamtdauer gemessen. Im Allgemeinen sollte sie zwischen 60 und 120 Sekunden betragen.
 
### <a name="unplanned--automatic-failover"></a>Ungeplant: automatisches Failover 
 
Eine ungeplante Downtime von Diensten, z. B. durch Softwarefehler oder Infrastrukturprobleme wie etwa Rechen-, Netzwerk- und Speicherfehler oder Stromausfälle haben Auswirkungen auf die Verfügbarkeit der Datenbank. Falls eine Datenbank nicht verfügbar ist, wird die Replikation auf das Standbyreplikat abgebrochen, und das Standbyreplikat wird als primäre Datenbank aktiviert. Das DNS wird aktualisiert, und Clients stellen dann eine neue Verbindung zum Datenbankserver her und setzen ihre Vorgänge fort. Die Failovergesamtdauer beträgt erwartungsgemäß zwischen 60 und 120 Sekunden. Je nach Aktivität auf dem primären Datenbankserver zum Zeitpunkt des Failovers, z. B. große Transaktionen, und abhängig von der Wiederherstellungszeit kann das Failover jedoch auch etwas länger dauern.

## <a name="high-availability-monitoring"></a>Überwachung der Hochverfügbarkeit
Die Integrität der Hochverfügbarkeit wird ununterbrochen überwacht und an die Übersichtsseite gemeldet. Eine Übersicht über die verschiedenen Replikationsstatus finden Sie unten:

| **Status** | **Beschreibung** |
| :----- | :------ |
| <b>NotEnabled | Die zonenredundante Hochverfügbarkeit ist nicht aktiviert. |
| <b>ReplicatingData | Nach Erstellung des Standbyreplikats wird es mit dem primären Server synchronisiert. |
| <b>FailingOver | Der Datenbankserver führt ein Failover vom primären Server auf das Standbyreplikat durch. |
| <b>Healthy | Die zonenredundante Hochverfügbarkeit weist einen stabilen Zustand auf, und die Integrität ist gesichert. |
| <b>RemovingStandby | Auf Grundlage einer Benutzeraktion wird das Standbyreplikat gelöscht.| 

##  <a name="limitations"></a>Einschränkungen 
 
Folgende Überlegungen sollten die Sie bei der Nutzung von Hochverfügbarkeit beachten:
* Zonenredundante Hochverfügbarkeit kann ausschließlich während der Erstellung einer Flexible Server-Instanz festgelegt werden.
* Hochverfügbarkeit wird für burstfähige Computeebenen nicht unterstützt.
* Ein Neustart des primären Datenbankservers für eine Übernahme von Änderungen an statischen Parametern führt auch zu einem Neustart des Standbyreplikats.
* Die Konfiguration von Lesereplikaten für Server mit zonenredundanter Hochverfügbarkeit wird nicht unterstützt.
* Die Datenreplikation wird für Hochverfügbarkeitsserver nicht unterstützt. 
* Der GTID-Modus wird auf EIN festgelegt, weil die Hochverfügbarkeitslösung GTID verwendet. Überprüfen Sie, ob für Ihre Workload [Einschränkungen für die Replikation mit GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-restrictions.html) gelten.  
 
## <a name="frequently-asked-questions-faqs"></a>Häufig gestellte Fragen (FAQs)
 
**Kann ich das Standbyreplikat für Lese- oder Schreibvorgänge verwenden?** </br>
Der Standbyserver ist nicht für Lese- oder Schreibvorgänge verfügbar, fungiert jedoch als passive Reserve, um ein schnelles Failover zu ermöglichen.</br>
**Kommt es bei einem Failover zu Datenverlusten?**</br>
Durch die Speicherung von Protokollen im ZRS sind die Protokolle auch dann zugänglich, wenn der primäre Server nicht verfügbar ist. Auf diese Weise wird sichergestellt, dass es nicht zu einem Datenverlust kommt. Sobald das Standbyreplikat aktiviert und die binären Protokolle angewendet wurden, übernimmt das Standbyreplikat die Funktionen des primären Servers. </br>
**Müssen Benutzer nach dem Failover bestimmte Aktionen ausführen?**</br>
Das Failover ist für die Clientanwendung vollständig transparent und benötigt keinerlei Benutzeraktionen. Die Anwendung sollte lediglich die Wiederholungslogik für ihre Verbindungen implementieren. </br>
**Was geschieht, wenn ich keine spezifische Zone für mein Standbyreplikat auswähle? Kann ich die Zone später ändern?**</br>
Wenn keine Zone ausgewählt ist, wird nach dem Zufallsprinzip eine andere Zone als die für den primären Server verwendete Zone ausgewählt. Um die Zone später zu ändern, können Sie den Hochverfügbarkeitsmodus deaktivieren und dann auf dem Blatt „Hochverfügbarkeit“ die Zonenredundanz mit einer Zone Ihrer Wahl erneut aktivieren.</br>
**Erfolgt die Replikation zwischen dem primären Replikat und dem Standbyreplikat synchron?**</br>
 Die Replikationslösung zwischen dem primären und dem Standbyserver kann ähnlich wie der [semisynchrone Modus](https://dev.mysql.com/doc/refman/5.7/en/replication-semisync.html) in MySQL betrachtet werden. Wenn für eine Transaktion ein Commit ausgeführt wurde, ist kein Commit für das Standbyreplikat erforderlich.  Wenn das primäre Replikat jedoch nicht verfügbar ist, können wir sicherstellen, dass der Standbyserver alle im primären Replikat vorhandenen Datenänderungen replizieren kann, und so gewährleisten, dass es nicht zu einem Datenverlust kommt.</br> 
**Wird bei allen ungeplanten Ausfällen ein Failover auf das Standbyreplikat durchgeführt?**</br>
Wenn eine Datenbank abstürzt oder ein Knoten ausfällt, wird die Flexible Server-VM auf demselben Knoten neu gestartet. Gleichzeitig wird ein automatisches Failover ausgelöst. Wenn die Flexible Server-VM vor Abschluss des Failovers erfolgreich neu gestartet wird, wird der Failovervorgang abgebrochen. Abhängig davon, welche Option weniger Zeit in Anspruch nimmt, wird entschieden, welches Replikat als primäres und welches als Standbyreplikat fungiert.</br>
**Wirkt sich die Verwendung einer Hochverfügbarkeitslösung auf die Leistung aus?**</br>
Mit der zonenredundanten Hochverfügbarkeit kann die Latenz um 5–10 % verringert werden, wenn die Anwendung eine verfügbarkeitszonenübergreifende Verbindung mit dem Datenbankserver herstellt und die Netzwerklatenz in diesen Verfügbarkeitszonen</br> im Vergleich um 2 bis 4 Millisekunden höher liegt. Bei der Option für Hochverfügbarkeit in gleicher Zone befinden sich das primäre und das Standbyreplikat in derselben Zone. Deshalb ist die Replikationsverzögerung geringer, und die Latenzzeiten zwischen dem Anwendungsserver und dem Datenbankserver werden verkürzt, wenn sie in derselben Azure-Verfügbarkeitszone platziert werden.</br>
**Wie wird mein Hochverfügbarkeitsserver gewartet?**</br>
Geplante Ereignisse wie das Skalieren von Computekapazitäten und Upgrades für Nebenversionen werden für den primären Server und das Standbyreplikat zur selben Zeit durchgeführt. Sie können das [Zeitfenster für geplante Wartungen](./concepts-maintenance.md) für Hochverfügbarkeitsserver wie für Flexible Server-Instanzen festlegen. Die Downtime wäre die gleiche wie für eine Azure Database for MySQL Flexible Server-Instanz mit deaktivierter Hochverfügbarkeit. Die Verwendung des Failovermechanismus zur Verringerung der Ausfallzeit für Hochverfügbarkeitsserver ist in unserer Roadmap enthalten, und dieses Feature wird bald zur Verfügung stehen. </br>
**Kann ich eine Zeitpunktwiederherstellung (Point-in-Time Restore, PITR) für meinen Hochverfügbarkeitsserver durchführen?**</br>
Sie können eine [Zeitpunktwiederherstellung](./concepts-backup-restore.md#point-in-time-restore) für Azure Database for MySQL Flexible Server mit aktivierter Hochverfügbarkeit auf eine neue Azure Database for MySQL Flexible Server-Instanz mit deaktivierter Hochverfügbarkeit durchführen. Wenn der Quellserver mit zonenredundanter Hochverfügbarkeit erstellt wurde, können Sie später die zonenredundante Hochverfügbarkeit oder Hochverfügbarkeit in gleicher Zone für den wiederhergestellten Server aktivieren. Wenn der Quellserver mit Hochverfügbarkeit in gleicher Zone erstellt wurde, können Sie für den wiederhergestellten Server nur Hochverfügbarkeit in gleicher Zone aktivieren.</br>
**Kann ich Hochverfügbarkeit für einen Server aktivieren, nachdem dieser erstellt wurde?**</br>
Sie können nach der Servererstellung Hochverfügbarkeit in gleicher Zone aktivieren, aber die zonenredundante Hochverfügbarkeit kann nur zum Zeitpunkt der Erstellung des Servers ausgewählt werden.</br> 
**Kann ich Hochverfügbarkeit für einen Server aktivieren, nachdem dieser erstellt wurde?** </br>
Nach der Erstellung des Servers können Sie die Hochverfügbarkeit deaktivieren, und die Abrechnung wird sofort eingestellt.  </br>
**Wie kann ich die Downtime verkürzen?**</br>
Wenn Sie das Feature für Hochverfügbarkeit nicht nutzen, müssen Sie dennoch in der Lage sein, die Downtime für Ihre Anwendung zu minimieren. Dienstausfälle wie z. B. für geplante Patches, Upgrades von Nebenversionen oder benutzerseitig eingeleitete Vorgänge wie etwa eine Computeskalierung können zur Durchführung innerhalb des Zeitfenster für die geplante Wartung geplant werden. Sie können sich dazu entschließen, von Azure ausgehende Wartungstasks für die Wochentage und die Uhrzeiten zu planen, die sich am wenigsten auf die Anwendung auswirken, um die Beeinträchtigungen möglichst gering zu halten.</br>
**Kann ein Lesereplikat für einen Server mit aktivierter Hochverfügbarkeit verwendet werden?**</br>
Derzeit unterstützen Server mit aktivierter Hochverfügbarkeit kein Lesereplikat. Ein Lesereplikat für Hochverfügbarkeitsserver ist jedoch in unserer Roadmap enthalten, und wir arbeiten daran, dieses Feature bald zur Verfügung zu stellen.</br>
**Kann ich die Datenreplikation für Hochverfügbarkeitsserver nutzen?**</br>
Die Datenreplikation wird für Hochverfügbarkeitsserver derzeit nicht unterstützt. Die Datenreplikation für Hochverfügbarkeitsserver ist jedoch in unserer Roadmap enthalten, und wir arbeiten daran, dieses Feature bald zur Verfügung zu stellen. Wenn Sie das Feature zur Datenreplikation für die Migration nutzen möchten, können Sie vorerst die folgenden Schritte ausführen:
* Erstellen Sie den Server mit aktivierter zonenredundanter Hochverfügbarkeit.
* Deaktivieren Sie die Hochverfügbarkeit.
* Befolgen Sie die Anweisungen im Artikel zur [Einrichtung der Datenreplikation](./concepts-data-in-replication.md) (stellen Sie sicher, dass GTID_Mode auf dem Quell- und dem Zielserver die gleiche Einstellung aufweist).
* Nach der Übernahme wird die Konfiguration der Datenreplikation entfernen.
* Aktivieren Sie die Hochverfügbarkeit.

**Kann ich während eines Serverneustarts oder beim Hoch- bzw. Herunterskalieren ein Failover auf den Standbyserver durchführen, um die Downtime zu verringern?** </br>
Derzeit werden beim Hoch- oder Herunterskalieren der Standby- und der primäre Server gleichzeitig skaliert, sodass ein Failover keinen Nutzen hat. Die Möglichkeit, zuerst den Standbyserver zu skalieren, dann ein Failover durchzuführen und anschließend den primären Server hochzuskalieren, ist in der Roadmap enthalten, wird aber noch nicht unterstützt.</br>


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
- Weitere Informationen zur [zonenredundanten Hochverfügbarkeit](./concepts-high-availability.md)
- Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)