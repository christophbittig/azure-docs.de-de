---
title: Übersicht über zonenredundante Hochverfügbarkeit für Azure Database for PostgreSQL – Flexible Server (Vorschau)
description: In diesem Artikel lernen Sie die Konzepte zonenredundanter Hochverfügbarkeit für Azure Database for PostgreSQL – Flexible Server kennen.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: 80b0d18eec8bbc37eda407d07873786f4155f4be
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422613"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Hochverfügbarkeitskonzepte für Azure Database for PostgreSQL – Flexible Server



Azure Database for PostgreSQL – Flexible Server bietet eine Konfiguration mit Hochverfügbarkeit und automatischer Failoverfunktion mithilfe einer **zonenredundanten** Serverbereitstellung. Wenn Flexible Server in einer zonenredundanten Konfiguration bereitgestellt wird, wird in einer anderen Verfügbarkeitszone automatisch ein Standbyreplikat bereitgestellt und verwaltet. Mithilfe der Streamingreplikation von PostgreSQL werden die Daten im **synchronen** Modus auf den Standbyreplikatserver repliziert. 

Eine zonenredundante Konfiguration ermöglicht ein automatisches Failover ohne Datenverlust während geplanter Ereignisse wie von Benutzern ausgehende Vorgänge zum Skalieren von Computekapazitäten sowie ungeplanter Ereignisse wie Fehler bei der zugrunde liegenden Hardware und Software, Netzwerkausfälle und Ausfälle von Verfügbarkeitszonen. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Zonenredundante Hochverfügbarkeit"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Zonenredundante Hochverfügbarkeitsarchitektur

Die zonenredundante Bereitstellung ermöglicht eine hohe Verfügbarkeit des flexiblen Servers. Sie können die Region und Verfügbarkeitszone auswählen, um den primären Datenbankserver bereitzustellen. Ein Standbyreplikatserver wird **automatisch**  in einer anderen Verfügbarkeitszone in derselben Region mit derselben Compute-, Speicher- und Netzwerkkonfiguration wie der primäre Server bereitgestellt und verwaltet. Daten- und Transaktionsprotokolldateien (Write-Ahead-Logs oder WAL) werden in lokal redundantem Speicher innerhalb jeder Verfügbarkeitszone gespeichert, in dem automatisch drei Datenkopien gespeichert werden. Dies ermöglicht eine physische Isolierung des gesamten Stapels zwischen primären und Standbyservern.  

Wenn die Anwendung Schreib- oder Commitvorgänge durchführt, werden bei der PostgreSQL-Streamingreplikation Transaktionsprotokolle (Write-Ahead-Logs, WAL) auf den lokalen Datenträger geschrieben und auch im *synchronen* Modus in das Standbyreplikat repliziert. Nachdem die Protokolle persistent im Standbyreplikat gespeichert wurden, wird die Anwendung über die Schreib- oder Commitvorgänge informiert. Der Standbyserver befindet sich im Wiederherstellungsmodus, in dem die Protokolle weiterhin angewendet werden, aber der primäre Server wartet nicht, bis das Anwenden abgeschlossen ist.

Der primäre Datenbankserver führt regelmäßig automatische Sicherungen durch, während die Transaktionsprotokolle vom Standbyreplikat kontinuierlich im Sicherungsspeicher archiviert werden.

Die Integrität des primären und Standbyservers wird ständig überwacht, und es werden geeignete Maßnahmen ergriffen, um Probleme zu beheben, einschließlich des Auslösens eines Failover auf den Standbyserver. Die Status zonenredundanter Hochverfügbarkeit sind wie folgt:

| **Status** | **Beschreibung** |
| ------- | ------ |
| <b> Wird initialisiert | Ein neuer Standbyserver wird gerade erstellt. |
| <b> Daten werden repliziert | Nach Erstellung des Standbyservers wird er mit dem primären Server synchronisiert. |
| <b> Fehlerfrei | Die Replikation ist stabil und fehlerfrei. |
| <b> Failover wird ausgeführt | Der Datenbankserver führt ein Failover auf den Standbyserver durch. |
| <b> Standby wird entfernt | Der Standbyserver wird gerade gelöscht. | 
| <b> Nicht aktiviert | Zonenredundante Hochverfügbarkeit ist nicht aktiviert.  |

>[!NOTE]
> Sie können Hochverfügbarkeit auch während der Servererstellung oder zu einem späteren Zeitpunkt aktivieren. Wenn Sie Hochverfügbarkeit in der Phase nach der Erstellung aktivieren oder deaktivieren, empfiehlt es sich, den Vorgang durchzuführen, wenn die Aktivität auf dem primären Server gering ist.

## <a name="steady-state-operations"></a>Vorgänge mit stabilem Zustand

PostgreSQL-Clientanwendungen sind mit dem primären Server über den Namen des Datenbankservers verbunden. Lesevorgänge der Anwendung werden direkt vom primären Server ermöglicht, während Commit- und Schreibvorgänge in der Anwendung erst dann bestätigt werden, nachdem die Protokolldaten sowohl auf dem primären Server als auch auf dem Standbyreplikatserver persistent gespeichert wurden. Aufgrund dieses zusätzlichen Roundtrips ist bei Anwendungen mit einer höheren Latenz für Schreib- und Commitvorgänge zu rechnen. Sie können die Integrität der Hochverfügbarkeit im Portal überwachen.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="Zonenredundante Hochverfügbarkeit: stabiler Zustand"::: 

1. Clients stellen eine Verbindung mit der Flexible Server-Instanz her und führen Schreibvorgänge aus.
2. Änderungen werden an den Standbystandort repliziert.
3. Der primäre Server empfängt eine Bestätigung.
4. Schreib-/Commitvorgänge werden bestätigt.

## <a name="failover-process---planned-downtimes"></a>Failoverprozess: geplante Ausfallzeiten

Zu den geplanten Ausfallzeiten zählen regelmäßige geplante Softwareupdates und Upgrades von Nebenversionen in Azure. Bei konfigurierter Hochverfügbarkeit erfolgen diese Vorgänge zunächst für das Standbyreplikat, während die Anwendungen weiterhin auf den primären Server zugreifen. Nachdem das Standbyreplikat aktualisiert wurde, werden die Verbindungen des primären Servers ausgeglichen. Dabei wird ein Failover ausgelöst, durch das das Standbyreplikat als primärer Server mit identischem Datenbankservernamen aktiviert wird. Clientanwendungen müssen sich mit dem gleichen Namen des Datenbankservers wieder mit dem neuen primären Server verbinden und können ihren Betrieb fortsetzen. In der Zone des ehemaligen primären Servers wird ein neuer Standbyserver erstellt. 

Bei anderen vom Benutzer eingeleiteten Vorgängen, wie z. B. Skalieren der Compute- oder Speicherkapazität, erfolgen die Änderungen zuerst auf dem Standby- und dann auf dem primären Server. Derzeit erfolgt kein Failover des Diensts auf den Standbyserver. Während der Skalierungsvorgang auf dem primären Server erfolgt, kommt es daher bei Anwendungen zu einer kurzen Ausfallzeit.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Verkürzen geplanter Ausfallzeiten mit verwaltetem Wartungsfenster

Beim flexiblen Server können Sie optional von Azure eingeleitete Wartungsaktivitäten planen, indem Sie ein 30-minütiges Fenster für einen gewünschten Tag wählen, an dem die Aktivitäten in den Datenbanken voraussichtlich gering sein werden. Azure-Wartungsaufgaben wie Patchen oder Upgrades von Nebenversionen erfolgen dann in diesem Wartungszeitfenster. Wenn Sie kein benutzerdefiniertes Fenster auswählen, wird für Ihren Server ein vom System zugeteiltes einstündiges Fenster zwischen 23:00 Uhr und 07:00 Uhr Ortszeit ausgewählt. 
 
Bei flexiblen Servern, die mit Hochverfügbarkeit konfiguriert sind, erfolgen diese Wartungsaufgaben zuerst im Standbyreplikat. Für den Dienst erfolgt ein Failover auf das Standbyreplikat, mit dem sich Anwendungen wieder verbinden können.

## <a name="failover-process---unplanned-downtimes"></a>Failoverprozess: ungeplante Ausfallzeiten

Zu den ungeplanten Ausfällen gehören Softwarefehler oder Ausfälle von Infrastrukturkomponenten, die die Verfügbarkeit der Datenbank beeinträchtigen. Falls der primäre Server nicht mehr verfügbar sein sollte, wird dies vom Überwachungssystem erkannt, das daraufhin einen Failoverprozess einleitet.  Der Vorgang umfasst einige Sekunden Wartezeit, um sicherzustellen, dass es sich nicht um ein falsch positives Ergebnis handelt. Die Replikation mit dem Standbyreplikat wird unterbrochen, und das Standbyreplikat wird als primärer Datenbankserver aktiviert. Dies schließt den Standbymodus ein, um alle verbleibenden WAL-Dateien wiederherstellungsbereit zu machen. Nach der vollständigen Wiederherstellung wird das DNS für denselben Endpunkt mit der IP-Adresse des Standbyservers aktualisiert. Clients können sich dann mit der gleichen Verbindungszeichenfolge wieder mit dem Datenbankserver verbinden und ihren Betrieb fortsetzen. 

>[!NOTE]
> Flexible Server, die mit zonenredundanter Hochverfügbarkeit konfiguriert sind, bieten das Recovery Point Objective (RPO) **0** (kein Datenverlust). Das Recovery Time Objective (RTO) sollte in typischen Fällen **weniger als 120 Sekunden** betragen. Abhängig von der Aktivität auf dem primären Datenbankserver zum Zeitpunkt des Failovers kann das Failover jedoch länger dauern. 

Nach dem Failover können sich Anwendungen, während ein neuer Standbyserver bereitgestellt wird, weiterhin mit dem primären Server verbinden und ihre Lese-/Schreibvorgänge fortsetzen. Nachdem der Standbyserver eingerichtet wurde, beginnt er mit der Wiederherstellung der Protokolle, die nach dem Failover generiert wurden. 

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="Zonenredundante Hochverfügbarkeit: Failover"::: 

1. Der primäre Datenbankserver ist ausgefallen, die Clients verlieren Konnektivität mit der Datenbank. 
2. Der Standbyserver wird als neuer primärer Server aktiviert. Der Client stellt mithilfe derselben Verbindungszeichenfolge eine Verbindung mit dem neuen primären Server her. Wenn sich die Clientanwendung in derselben Zone wie der primäre Datenbankserver befindet, werden Wartezeiten verkürzt und die Leistung verbessert.
3. Der Standbyserver wird in der gleichen Zone wie der alte primäre Server eingerichtet, und die Streamingreplikation wird gestartet. 
4. Sobald die Replikation in stabilem Zustand eingerichtet ist, werden die Commit- und Schreibvorgänge der Clientanwendung bestätigt, nachdem die Daten an beiden Standorten persistent gespeichert wurden.

## <a name="on-demand-failover"></a>Bedarfsgesteuertes Failover

Der flexible Server bietet zwei Methoden, um ein bedarfsgesteuertes Failover auf den Standbyserver durchzuführen. Diese sind nützlich, wenn Sie die Failoverzeit und die Auswirkungen der Ausfallzeit für Ihre Anwendungen testen möchten und wenn Sie ein Failover in die bevorzugte Verfügbarkeitszone durchführen möchten. 

### <a name="forced-failover"></a>erzwungenes Failover

Mit diesem Feature können Sie das Szenario eines ungeplanten Ausfalls simulieren, während Ihr Workload in der Produktion läuft, und die Ausfallzeiten Ihrer Anwendung beobachten. Für den seltenen Fall, dass Ihr primärer Server aus irgendeinem Grund nicht mehr reagiert, können Sie dieses Feature auch verwenden. 

Mit diesem Feature wird der primäre Server heruntergefahren und der Failoverworkflow eingeleitet, in dem das Höherstufen des Standbyservers erfolgt. Sobald der Standbyserver den Wiederherstellungsvorgang bis zu den letzten Commitdaten abgeschlossen hat, wird er zum primären Server höher gestuft. DNS-Einträge werden aktualisiert, und Ihre Anwendung kann eine Verbindung mit dem höher gestuften primären Server herstellen. Ihre Anwendung kann weiterhin Daten auf den primären Server schreiben, während im Hintergrund ein neuer Standbyserver eingerichtet wird. Dies wirkt sich nicht auf die Betriebszeit aus. 

Während des erzwungenen Failovers werden die folgenden Schritte ausgeführt:

  | **Schritt** | **Beschreibung** | **Sind Ausfallzeiten für Apps zu erwarten?** |
  | ------- | ------ | ----- |
  | 1 | Der primäre Server wird kurz nach Empfang der Failoveranforderung beendet. | Ja |
  | 2 | Bei der Anwendung kommt es zu Ausfallzeiten, da der primäre Server ausgefallen ist. | Ja |
  | 3 | Das interne Überwachungssystem erkennt den Ausfall und leitet ein Failover auf den Standbyserver ein. | Ja |
  | 4 | Der Standbyserver wird in den Wiederherstellungsmodus versetzt, bevor er vollständig als unabhängiger Server höher gestuft wird. | Ja |
  | 5 | Der Failoverprozess wartet auf den Abschluss der Standbywiederherstellung. | Ja |
  | 6 | Sobald der Server wieder in Betrieb ist, wird der DNS-Eintrag mit demselben Hostnamen, aber unter Verwendung der IP-Adresse des Standbyservers aktualisiert. | Ja |
  | 7 | Die Anwendung kann erneut eine Verbindung mit dem neuen primären Server herstellen und den Vorgang fortsetzen. | Nein |
  | 8 | Ein Standbyserver wird in der bevorzugten Zone eingerichtet. | Nein |
  | 9 | Der Standbyserver beginnt mit der Wiederherstellung von Protokollen (aus Azure Blob Storage), die er während seiner Einrichtung verpasst hat. | Nein |
  | 10 | Zwischen dem primären und Standbyserver wird ein konstanter Zustand eingerichtet. | Nein |
  | 11 | Der erzwungene Failoverprozess ist abgeschlossen. | Nein |

Es wird erwartet, dass die Ausfallzeit der Anwendung nach Schritt 1 beginnt und bis zum Abschluss von Schritt 6 anhält. Die übrigen Schritte erfolgen im Hintergrund, ohne die Schreib- und Commitvorgänge der Anwendung zu beeinträchtigen.

>[!Important]
>Der End-to-End-Failoverprozess umfasst (a) das Failover auf den Standbyserver nach dem Ausfall des primären Servers und (b) das Einrichten eines neuen Standbyservers in einem stabilen Zustand. Da für Ihre Anwendung nur so lange Ausfallzeiten auftreten, bis das Failover auf den Standbyserver abgeschlossen ist, **messen Sie die Ausfallzeit aus Anwendungs-/Clientperspektive** anstelle des gesamten End-to-End-Failoverprozesses. 

### <a name="planned-failover"></a>Geplantes Failover

Mit diesem Feature können Sie ein Failover auf den Standbyserver mit verkürzter Ausfallzeit durchführen. Beispielsweise könnte sich Ihr primärer Server nach einem ungeplanten Failover in einer anderen Verfügbarkeitszone befinden als die Anwendung, und Sie möchten den primären Server in die vorherige Zone zurückholen, um ihn mit Ihrer Anwendung zusammenzustellen.

Bei der Ausführung dieses Features wird der Standbyserver zunächst vorbereitet, um sicherzustellen, dass er mit den jüngsten Transaktionen auf dem gleichen Stand ist, damit die Anwendung weiterhin Lese- und Schreibvorgänge durchführen kann. Der Standbyserver wird anschließend höher gestuft, und die Verbindungen mit dem primären Server werden getrennt. Ihre Anwendung kann weiterhin Daten auf den primären Server schreiben, während im Hintergrund ein neuer Standbyserver eingerichtet wird. Im Folgenden werden die Schritte für ein geplantes Failover beschrieben.

| **Schritt** | **Beschreibung** | **Sind Ausfallzeiten für Apps zu erwarten?** |
  | ------- | ------ | ----- |
  | 1 | Warten Sie, bis der Standbyserver mit dem Primärserver auf dem gleichen Stand ist. | Nein |
  | 2 | Das interne Überwachungssystem leitet den Failoverworkflow ein. | Nein |
  | 3 | Schreibvorgänge der Anwendung werden blockiert, wenn sich der Standbyserver nahe bei der primären Protokollfolgenummer (LSN) befindet. | Ja |
  | 4 | Der Standbyserver wird zu einem unabhängigen Server höher gestuft. | Ja |
  | 5 | Der DNS-Eintrag wird mit der IP-Adresse des neuen Standbyservers aktualisiert. | Ja |
  | 6 | Die Anwendung stellt die Verbindung wieder her und setzt ihre Lese-/Schreibvorgänge mit dem neuen primären Server fort | Nein |
  | 7 | Es wird ein neuer Standbyserver in einer anderen Zone eingerichtet. | Nein |
  | 8 | Der Standbyserver beginnt mit der Wiederherstellung von Protokollen (aus Azure Blob Storage), die er während seiner Einrichtung verpasst hat. | Nein |
  | 9 | Zwischen dem primären und Standbyserver wird ein konstanter Zustand eingerichtet. | Nein |
  | 10 |  Der geplante Failoverprozess ist abgeschlossen. | Nein |

Die Ausfallzeit der Anwendung beginnt bei Schritt 3, und der Betrieb kann nach Schritt 5 fortgesetzt werden. Die übrigen Schritte erfolgen im Hintergrund, ohne die Schreib- und Commitvorgänge der Anwendung zu beeinträchtigen. 

### <a name="considerations-while-performing-on-demand-failovers"></a>Überlegungen beim Ausführen bedarfsgesteuerter Failovers

* Die Gesamtdauer des Vorgangs kann länger sein als die tatsächliche Ausfallzeit der Anwendung. **Messen Sie die Ausfallzeit aus Sicht der Anwendung**.
* Bitte führen Sie nicht sofort ein erneutes Failover durch. Warten Sie mindestens 15 bis 20 Minuten zwischen Failovers, damit der neue Standbyserver vollständig eingerichtet werden kann.
* Für das geplante Failover mit reduzierter Ausfallzeit wird empfohlen, es in Zeiten geringer Aktivität durchzuführen.

Informationen zur Verwaltung von Hochverfügbarkeit finden Sie in [diesem Leitfaden](how-to-manage-high-availability-portal.md).


## <a name="point-in-time-restore-of-ha-servers"></a>Zeitpunktwiederherstellung von Servern mit Hochverfügbarkeit

Bei mit Hochverfügbarkeit konfigurierten flexiblen Servern werden Protokolldaten in Echtzeit auf den Standbyserver repliziert. Benutzerfehler auf dem primären Server, z. B. versehentliches Löschen einer Tabelle oder fehlerhafte Datenaktualisierungen, werden auch in das Standbyreplikat repliziert. Daher können Sie den Standbyserver nicht zur Behebung solcher logischen Fehler einsetzen. Um diese Fehler zu beheben, müssen Sie eine Zeitpunktwiederherstellung aus einer Sicherungen durchführen.  Dank der Möglichkeit der Zeitpunktwiederherstellung für die Flexible Server-Instanz können Sie eine Wiederherstellung zu einem Zeitpunkt vor dem Fehler durchführen. Bei mit Hochverfügbarkeit konfigurierten Datenbanken wird ein neuer Datenbankserver als flexibler Server mit einer einzelnen Zone und einem neuen vom Benutzer angegebenen Servernamen wiederhergestellt. Sie können den wiederhergestellten Server für einige Anwendungsfälle verwenden:

  1. Sie können den wiederhergestellten Server in der Produktion einsetzen und optional zonenredundante Hochverfügbarkeit aktivieren. 
  2. Wenn Sie nur ein Objekt wiederherstellen möchten, können Sie das Objekt vom wiederhergestellten Datenbankserver exportieren und in Ihren Produktionsdatenbankserver importieren. 
  3. Wenn Sie Ihren Datenbankserver für Test- und Entwicklungszwecke klonen oder für andere Zwecke wiederherstellen möchten, können Sie auch eine Zeitpunktwiederherstellung durchführen.

## <a name="zone-redundant-high-availability---features"></a>Zonenredundante Hochverfügbarkeit: Features

* Standbyreplikate werden mit einer dem primären Server exakt entsprechenden VM-Konfiguration bereitgestellt, einschließlich virtueller Kerne, Speicher, Netzwerkeinstellungen (VNET, Firewall) usw.

* Sie können einem vorhandenen Datenbankserver Hochverfügbarkeit hinzufügen.

* Sie können das Standbyreplikat entfernen, indem Sie Hochverfügbarkeit deaktivieren.

* Sie können Ihre Verfügbarkeitszone nur für Ihren primären Datenbankserver auswählen. Die Standbyzone wird automatisch ausgewählt.

* Vorgänge wie Beenden, Starten und Neustarten werden auf primären und Standbydatenbankservern gleichzeitig ausgeführt.

* Automatische Sicherungen werden auf dem primären Datenbankserver durchgeführt und in einem zonenredundanten Speicher gespeichert.

* Clients stellen stets eine Verbindung mit dem Hostnamen des primären Datenbankservers her.

* Alle Änderungen an den Serverparametern werden auch auf das Standbyreplikat angewendet.

* Möglichkeit, den Server neu zu starten, sodass alle Änderungen an statischen Serverparametern übernommen werden.
  
* Regelmäßige Wartungsaktivitäten wie Upgrades von Nebenversionen erfolgen zuerst auf dem Standbyserver, wobei für den Dienst ein Failover stattfindet, um Ausfallzeiten zu verkürzen.  

## <a name="zone-redundant-high-availability---limitations"></a>Zonenredundante Hochverfügbarkeit: Einschränkungen

* Hochverfügbarkeit wird im Computetarif „Burstfähig“ nicht unterstützt.
* Hochverfügbarkeit wird nur in Regionen mit mehreren Zonen unterstützt.
* Aufgrund der synchronen Replikation in eine andere Verfügbarkeitszone kann es bei Anwendungen zu mehr Latenz bei Schreib- und Commitvorgängen kommen.

* Standbyreplikate können nicht für Leseabfragen verwendet werden.

* Je nach Workload und Aktivität auf dem primären Server kann der Failoverprozess aufgrund der Wiederherstellung im Standbyreplikat länger als 120 Sekunden dauern, bevor es höher gestuft werden kann. 
* Der Standbyserver stellt WAL-Dateien in der Regel mit einer Geschwindigkeit von 40 MB/s wieder her. Wenn Ihre Workload diesen Grenzwert überschreitet, kann die Wiederherstellung entweder während des Failovers oder nach dem Einrichten eines neuen Standbyservers länger dauern. 

* Ein Neustart des primären Datenbankservers führt auch zu einem Neustart des Standbyreplikats. 

* Das Konfigurieren zusätzlicher Lesereplikate wird nicht unterstützt.

* Das Konfigurieren vom Kunden ausgelöster Verwaltungsaufgaben kann im verwalteten Wartungsfenster nicht geplant werden.

* Geplante Ereignisse wie das Skalieren von Compute- und Speicherkapazität finden zuerst auf dem Standby- und dann auf dem primären Server statt. Der Server führt derzeit für diese geplanten Vorgänge kein Failover aus. 

* Wenn die logische Decodierung oder logische Replikation mit einem flexiblen Server mit Hochverfügbarkeit konfiguriert ist, werden die logischen Replikationsslots bei einem Failover auf den Standbyserver nicht auf den Standbyserver kopiert.  

## <a name="availability-without-zone-redundant-ha"></a>Verfügbarkeit ohne zonenredundante Hochverfügbarkeit (High Availability, HA)

Für flexible Server, die **ohne** zonenredundante Hochverfügbarkeit konfiguriert sind, bietet der Dienst weiterhin integrierte Verfügbarkeit, Speicherredundanz und Resilienz, um die Wiederherstellung nach geplanten oder ungeplanten Ausfallereignissen zu unterstützen.

### <a name="planned-downtime"></a>Geplante Ausfallzeit 

Hier finden Sie einige Szenarien mit geplanter Wartung:

| **Szenario** | **Beschreibung**|
| ------------ | ----------- |
| <b>Hoch-/Herunterskalieren von Computeressourcen | Wenn ein Benutzer den Vorgang zum Hoch- bzw. Herunterskalieren von Computeressourcen ausführt, wird ein neuer Datenbankserver mit der skalierten Computekonfiguration bereitgestellt. Auf dem alten Datenbankserver können aktive Prüfpunkte abgeschlossen werden, Clientverbindungen werden entladen, alle Transaktionen ohne Commit werden abgebrochen, und anschließend wird der Server heruntergefahren. Der Speicher wird dann vom alten Datenbankserver getrennt und an den neuen Datenbankserver angefügt. Er wird wieder in Betrieb genommen und akzeptiert alle Verbindungen.|
| <b>Hochskalieren des Speichers | Das Hochskalieren des Speichers ist derzeit ein Offlinevorgang, der eine kurze Ausfallzeit mit sich bringt.|
| <b>Bereitstellung neuer Software (Azure) | Rollouts von neuen Features oder Fehlerbehebungen werden automatisch im Rahmen der geplanten Wartung des Diensts durchgeführt. Weitere Informationen finden Sie in der [Dokumentation](./concepts-maintenance.md) und im [Portal](https://aka.ms/servicehealthpm).|
| <b>Upgrades auf Nebenversionen | Azure Database for PostgreSQL patcht Datenbankserver automatisch auf die von Azure festgelegte Nebenversion. Dies erfolgt im Rahmen der geplanten Wartung des Diensts. Dabei kommt es zu einer kurzen Downtime von ein paar Sekunden. Danach wird der Datenbankserver automatisch mit der neuen Nebenversion neu gestartet. Weitere Informationen finden Sie in der [Dokumentation](./concepts-maintenance.md) und im [Portal](https://aka.ms/servicehealthpm).|


###  <a name="unplanned-downtime"></a>Ungeplante Ausfallzeiten 

Ungeplante Downtime kann aufgrund von unvorhergesehenen Fehlern auftreten, darunter Fehler in der zugrunde liegenden Hardware, Netzwerkprobleme und Softwarefehler. Wenn der Datenbankserver unerwartet ausfällt, wird automatisch innerhalb weniger Sekunden ein neuer Datenbankserver bereitgestellt. Der Remotespeicher wird automatisch an den neuen Datenbankserver angefügt. Die PostgreSQL-Engine führt den Wiederherstellungsvorgang mithilfe von WAL-Dateien und Datenbankdateien durch und öffnet den Datenbankserver, sodass Clients eine Verbindung herstellen können. Nicht committete Transaktionen gehen verloren und müssen von der Anwendung erneut ausgeführt werden. Ungeplante Downtime lässt sich zwar nicht vollständig vermeiden, wird aber vom flexiblen Server durch automatisches Ausführen von Wiederherstellungsvorgängen sowohl auf Ebene der Datenbankserver als auch des Speichers minimiert, ohne dass ein Eingreifen durch einen Benutzer erforderlich ist. 

Im Folgenden finden Sie einige Fehlerszenarien, und es wird beschrieben, wie der flexible Server automatisch wiederhergestellt wird:

| **Szenario** | **Automatische Wiederherstellung** |
| ---------- | ---------- |
| <B>Ausfall des Datenbankservers | Wenn der Datenbankserver aufgrund eines Fehlers an der zugrunde liegenden Hardware ausfällt, werden die aktiven Verbindungen getrennt, und alle Transaktionen, die gerade ausgeführt werden, werden abgebrochen. Ein neuer Datenbankserver wird automatisch bereitgestellt, und der Remotedatenspeicher wird an den neuen Datenbankserver angefügt. Sobald die Datenbank wiederhergestellt wurde, können Clients über denselben Endpunkt eine Verbindung mit dem neuen Datenbankserver herstellen. <br /> <br /> Die Wiederherstellungszeit (Recovery Time Objective, RTO) hängt von verschiedenen Faktoren ab, z. B. der Aktivität zum Zeitpunkt des Fehlers. Zu diesen Aktivitäten zählen große Transaktionen und die Anzahl der Wiederherstellungsvorgänge, die während des Starts des Datenbankservers ausgeführt wurden. <br /> <br /> Anwendungen, die die PostgreSQL-Datenbanken verwenden, müssen so konzipiert sein, dass sie getrennte Verbindungen und Transaktionsfehler erkennen und entsprechende Wiederholungsversuche durchführen.  |
| <B>Speicherfehler | Speicherbezogene Probleme wie der Ausfall eines Datenträgers oder physische Blockbeschädigungen haben keine Auswirkungen auf Anwendungen. Da drei Kopien der Daten gespeichert werden, wird eine Kopie der Daten vom verbleibenden Speicher bereitgestellt. Blockbeschädigungen werden automatisch behoben. Wenn eine Kopie der Daten verloren geht, wird automatisch eine neue erstellt. |

Im Folgenden finden Sie einige Fehlerszenarien, bei denen für die Wiederherstellung eine Benutzeraktion erforderlich ist:

| **Szenario** | **Plan für die Wiederherstellung** |
| ---------- | ---------- |
| <b> Verfügbarkeitszonenfehler | Wenn die Region mehrere Verfügbarkeitszonen unterstützt, werden die Sicherungen automatisch im zonenredundantem Sicherungsspeicher gespeichert. Im Falle eines Zonenausfalls können Sie eine Wiederherstellung aus der Sicherung in einer anderen Verfügbarkeitszone durchführen. Dies bietet Resilienz auf Zonenebene. Die Wiederherstellung dauert jedoch ihre Zeit. Es kann zu Datenverlusten führen, da möglicherweise nicht alle WAL-Datensätze in den Sicherungsspeicher kopiert wurden. <br> <br> Wenn Sie eine kurze Ausfallzeit und eine hohe Betriebszeit bevorzugen, empfehlen wir Ihnen, Ihren Server mit zonenredundanten Hochverfügbarkeit zu konfigurieren. |
| <b> Logische Fehler/Benutzerfehler | Die Wiederherstellung bei Benutzerfehlern, z. B. versehentlich gelöschten Tabellen oder falsch aktualisierten Daten, umfasst das Durchführen einer [Zeitpunktwiederherstellung](./concepts-backup-restore.md), bei der die Daten bis zu einem Zeitpunkt kurz vor dem Fehler wiederhergestellt werden.<br> <br>  Wenn Sie anstelle aller Datenbanken auf dem Datenbankserver nur eine Teilmenge von Datenbanken oder bestimmte Tabellen wiederherstellen möchten, können Sie den Datenbankserver in einer neuen Instanz wiederherstellen, die Tabelle(n) über [pg_dump](https://www.postgresql.org/docs/13/app-pgdump.html) exportieren und sie dann über [pg_restore](https://www.postgresql.org/docs/13/app-pgrestore.html) in Ihrer Datenbank wiederherstellen. |

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="ha-configuration-questions"></a>Fragen zur Konfiguration von Hochverfügbarkeit

* **Muss ich über zonenredundante Hochverfügbarkeit verfügen, um meinen Server vor ungeplanten Ausfällen zu schützen?** <br>
    Nein. Flexible Server bieten lokalen redundanten Speicher mit drei Kopien der Daten, zonenredundante Sicherungen (in Regionen, in denen diese unterstützt werden) sowie integrierte Serverresilienz, um einen abgestürzten Server automatisch neu zu starten und sogar den Server auf einen anderen physischen Knoten zu verlagern. Zonenredundante Hochverfügbarkeit sorgt für höhere Betriebszeiten, indem ein automatisches Failover auf einen anderen ausgeführten (Standby-) Server in einer anderen Zone ausgeführt wird. Dadurch wird zonenresiliente Hochverfügbarkeit ohne Datenverlust ermöglicht.

* **Ist zonenredundante Hochverfügbarkeit in allen Regionen verfügbar?** <br>
    Zonenredundante Hochverfügbarkeit ist in Regionen verfügbar, für die mehrere Verfügbarkeitszonen in der Region unterstützt werden. Informationen zu den neuesten unterstützten Regionen finden Sie in dieser [Dokumentation](overview.md#azure-regions). Wir fügen ständig weitere Regionen hinzu und erweitern die Unterstützung mehrerer Verfügbarkeitszonen. 

* **Welcher Replikationsmodus wird zwischen primären und Standbyservern verwendet?** <br>
    Zwischen dem primären Server und dem Standbyserver wird der Modus „Synchron“ eingerichtet. Schreib- und Commitvorgänge für die Anwendung werden erst bestätigt, nachdem die Daten des Write-Ahead-Protokolls (Write Ahead Log, WAL) am Standbystandort dauerhaft gespeichert wurden. So kann sichergestellt werden, dass es bei einem Failover nicht zu Datenverlust kommt.

* **Beim Modus „Synchron“ kommt es zu Latenz. Welche Art von Leistungseinbußen ergeben sich hierdurch für meine Anwendung?** <br>
    Beim Konfigurieren der Hochverfügbarkeit kommt es zu geringer Latenz bei Schreib- und Commitvorgängen. Es ergeben sich keine Auswirkungen auf Leseabfragen. Die Auswirkungen auf die Leistung variieren je nach Workload. Eine allgemeine Faustregel lautet: Für Schreib- und Lesevorgänge kann sich eine Beeinträchtigung von ca. 20 bis 30 % ergeben. 

* **Bietet zonenredundante Hochverfügbarkeit Schutz vor geplanten und ungeplanten Ausfällen?** <br>
    Ja. Das Hauptziel von Hochverfügbarkeit ist die Erhöhung der Betriebszeit und der Schutz vor Ausfällen. Bei einem ungeplanten Ausfall, z. B. einem Fehler in einer Datenbank, auf einer VM, einem physischen Knoten, in einem Rechenzentrum oder auf Verfügbarkeitszonenebene, wird vom Überwachungssystem für den Server automatisch ein Failover auf den Standbyserver ausgeführt. Ebenso gilt Folgendes: Bei geplanten Ausfällen, z. B. Updates von Nebenversionen oder Infrastrukturpatches, während des Zeitfensters für die geplante Wartung werden die Updates zuerst auf dem Standbyserver angewendet. Das Failover für den Dienst wird dann ausgeführt, während für den alten primären Server der Updateprozess durchlaufen wird. Hierdurch wird die Gesamtausfallzeit reduziert. 

* **Kann ich Hochverfügbarkeit jederzeit aktivieren bzw. deaktivieren?** <br>

    Ja. Sie können zonenredundante Hochverfügbarkeit jederzeit aktivieren und deaktivieren. Ausnahmen sind Fälle, in denen sich der Server in einem bestimmten Status befindet, z. B. „Beendet“, „Wird neu gestartet“ oder bereits im Prozess des Failovers. 

* **Kann ich die Verfügbarkeitszone für den Standbyserver auswählen?** <br>
    Nein. Derzeit ist es für Sie nicht möglich, die Verfügbarkeitszone für den Standbyserver auszuwählen. Es ist geplant, diese Funktion zu einem späteren Zeitpunkt hinzuzufügen.

* **Kann ich die Hochverfügbarkeit zwischen privatem (VNET) und öffentlichem Zugriff konfigurieren?** <br>
    Nein. Hochverfügbarkeit können Sie entweder in einem VNET (übergreifend für Verfügbarkeitszonen einer Region) oder für den öffentlichen Zugriff konfigurieren. 

* **Kann ich Hochverfügbarkeit regionsübergreifend konfigurieren?** <br>
    Nein. Hochverfügbarkeit wird innerhalb einer Region und übergreifend für Verfügbarkeitszonen konfiguriert. Für die Zukunft ist die Bereitstellung von Lesereplikaten geplant, die für Notfallwiederherstellungszwecke regionsübergreifend konfiguriert werden können. Wir stellen weitere Details bereit, nachdem das Feature aktiviert wurde. 

* **Kann ich die logische Replikation mit Servern nutzen, für die Hochverfügbarkeit konfiguriert wurde?** <br>
    Sie können die logische Replikation mit Hochverfügbarkeit konfigurieren. Nach einem Failover werden die Details zu den logischen Slots nicht auf den Standbyserver kopiert. Daher wird diese Konfiguration derzeit nur eingeschränkt unterstützt.

### <a name="replication-and-failover-related-questions"></a>Fragen zu Replikation und Failover

* **Wie sorgt ein flexibler Server für Hochverfügbarkeit bei einem Ausfall, z. B. einer Verfügbarkeitszone?** <br>
    Wenn Sie Ihren Server mit zonenredundanter Hochverfügbarkeit aktivieren, wird in einer anderen Verfügbarkeitszone als der primären Zone automatisch ein physisches Standbyreplikat mit der gleichen Compute- und Speicherkonfiguration bereitgestellt. Die PostgreSQL-Streamingreplikation wird zwischen dem primären Server und dem Standbyserver eingerichtet. 

* **Wie sieht der typische Failoverprozess bei einem Ausfall aus?** <br>
    Wenn der Fehler vom Überwachungssystem erkannt wird, wird ein Failoverworkflow initiiert. Hiermit wird sichergestellt, dass für den Standbyserver alle restlichen WAL-Dateien angewendet und alle erforderlichen Schritte ausgeführt wurden, bevor er für Lese- und Schreibvorgänge geöffnet wird. Anschließend wird das DNS mit der IP-Adresse des Standbyservers aktualisiert, bevor die Clients eine neue Verbindung mit dem Server über denselben Endpunkt (Hostname) herstellen können. Ein neuer Standbyserver wird instanziiert, damit die Konfiguration weiterhin im Hochverfügbarkeitsmodus vorhanden ist.

* **Mit welcher typischen Failoverdauer und welchem erwarteten Datenverlust muss bei einem Ausfall gerechnet werden?** <br>
    Normalerweise beträgt die Dauer des Failovers oder die Ausfallzeit aus Anwendungssicht 60 bis 120 Sekunden. Dieser Zeitraum kann auch länger sein, wenn der Ausfall bei zeitintensiven Transaktionen, während der Indexerstellung oder bei einer hohen Zahl von Schreibaktivitäten aufgetreten ist. Der Grund ist, dass es dann länger dauern kann, bis der Wiederherstellungsprozess durch den Standbyserver abgeschlossen ist.

    Da die Replikation im synchronen Modus durchgeführt wird, ist nicht mit Datenverlust zu rechnen.

* **Wird für die Failoverdauer eine SLA angeboten?** <br>
    Für die Failoverdauer stellen wir Informationen dazu bereit, wie lange der Vorgang normalerweise dauert. Die offizielle SLA bezieht sich auf die gesamte Betriebszeit und wird bereitgestellt, wenn der Dienst die Phase der allgemeinen Verfügbarkeit erreicht hat. Während der öffentlichen Vorschauphase werden keine SLAs angeboten.

* **Wird von der Anwendung nach dem Failover automatisch eine Verbindung mit dem Server hergestellt?** <br>
    Nein. Anwendungen sollten über einen Wiederholungsmechanismus für die erneute Verbindungsherstellung mit demselben Endpunkt (Hostname) verfügen.

* **Wie kann ich das Failover testen?** <br>
    Zum Testen des Failovers können Sie das Feature **Erzwungenes Failover** oder **Geplantes Failover** verwenden. Weitere Informationen finden Sie im Abschnitt **Bedarfsgesteuertes Failover** in diesem Dokument.

* **Wie kann ich den Replikationsstatus überprüfen?** <br>
    Im Portal werden auf der Übersichtsseite des Servers der Status für zonenredundante Hochverfügbarkeit und der Serverstatus angezeigt. Sie können den Status und die Verfügbarkeitszonen für den primären Server und den Standbyserver auch im Serverportal auf dem Blatt „Hochverfügbarkeit“ überprüfen. 

    In psql können Sie `select * from pg_stat_replication;` ausführen, um u. a. den Streamingstatus anzuzeigen.

* **Werden Leseabfragen auf dem Standbyreplikat unterstützt?** <br>
    Nein. Leseabfragen werden auf dem Standbyreplikat nicht unterstützt.

* **Wird für den wiederhergestellten Server automatisch die Hochverfügbarkeit konfiguriert, wenn ich die Zeitpunktwiederherstellung durchführe?** <br>
    Nein. Der Server wird bei der Zeitpunktwiederherstellung als eigenständiger Server wiederhergestellt. Falls Sie Hochverfügbarkeit aktivieren möchten, können Sie dies nach Abschluss der Wiederherstellung durchführen.


## <a name="next-steps"></a>Nächste Schritte

-   Weitere Informationen zur [Geschäftskontinuität](./concepts-business-continuity.md)
-   Weitere Informationen zum [Verwalten von Hochverfügbarkeit](./how-to-manage-high-availability-portal.md)
-   Weitere Informationen zu [Sicherung und Wiederherstellung](./concepts-backup-restore.md)
