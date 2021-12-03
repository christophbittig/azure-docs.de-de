---
title: Verwalten mehrerer Datenbanken mit Pools für elastische Datenbanken
description: Verwalten und skalieren Sie Hunderte oder Tausende Datenbanken in Azure SQL-Datenbank, indem Sie Pools für elastische Datenbanken verwenden. Ein Preis für Ressourcen, die Sie nach Bedarf verteilen können.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: arvindshmicrosoft
ms.author: arvindsh
ms.reviewer: mathoma
ms.date: 06/23/2021
ms.openlocfilehash: 669b8610cba44be369ba805834eca2f700d211e0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548879"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren vieler Datenbanken in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pools für elastische Azure SQL-Datenbank-Instanzen sind eine einfache, kostengünstige Lösung zum Verwalten und Skalieren einer großen Zahl von Datenbanken mit variierenden und unvorhersehbaren Nutzungsanforderungen. Die Datenbanken in einem Pool für elastische Datenbanken befinden sich auf einem einzelnen Server und nutzen gemeinsam eine festgelegte Anzahl von Ressourcen zu einem festen Preis. Mit Pools für elastische Datenbanken in Azure SQL-Datenbank sind SaaS-Entwickler in der Lage, das Preis-Leistungs-Verhältnis einer Gruppe von Datenbanken im Rahmen eines vorgegebenen Budgets zu optimieren und gleichzeitig eine flexible Leistung für jede Datenbank sicherzustellen.

> [!div class="nextstepaction"]
> [Umfrage zur Verbesserung von Azure SQL](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="what-are-sql-elastic-pools"></a>Was sind Pools für elastische SQL-Datenbanken?

SaaS-Entwickler erstellen Anwendungen, die auf der obersten von umfangreichen Datenebenen aufsetzen, die wiederum aus zahlreichen Datenbanken bestehen können. In einem üblichen Anwendungsmuster wird jedem Kunden eine eigene Datenbank zur Verfügung gestellt. Verschiedene Kunden haben jedoch häufig unterschiedliche und unvorhersehbare Verwendungsmuster, und es ist schwierig, den Ressourcenbedarf jedes einzelnen Datenbankbenutzers vorherzusagen. Bisher hatten Sie zwei Optionen:

- Überdimensionierte Bereitstellung der Ressourcen, die sich an der Nutzung zu Spitzenzeiten orientiert (samt der damit verbundenen Kosten) oder
- Unterdimensionierte Bereitstellung, um Kosten zu Lasten von Leistung und Kundenzufriedenheit während Spitzenzeiten einzusparen.

Pools für elastische Datenbanken lösen dieses Problem, indem sie sicherstellen, dass die Datenbanken die Leistungsressourcen erhalten, die sie benötigen, und zwar genau zu dem Zeitpunkt, zu dem sie sie benötigen. Sie stellen einen einfachen Ressourcenzuordnungsmechanismus innerhalb eines vorhersagbaren Budgets bereit. Weitere Informationen zu Entwurfsmustern für SaaS-Anwendungen, für die Pools für elastische Datenbanken verwendet werden, finden Sie unter [Entwurfsmuster für SaaS-Anwendungen mit mehreren Mandanten und Azure SQL-Datenbank](saas-tenancy-app-design-patterns.md).
>
> [!IMPORTANT]
> Für Pools für elastische Datenbanken erfolgt keine Abrechnung pro Datenbank. Die Abrechnung erfolgt für jede Stunde, in der ein Pool auf der höchsten eDTU- oder V-Kern-Ebene existiert. Dies gilt unabhängig davon, ob der Pool genutzt wurde oder ob er weniger als eine Stunde aktiv war.

Mit Pools für elastische Datenbanken können Entwickler Ressourcen für einen Pool erwerben, der von mehreren Datenbanken genutzt wird, um unvorhersehbare Auslastungszeiten von einzelnen Datenbanken abzufedern. Sie können Ressourcen für den Pool entweder basierend auf dem [DTU-basierten Kaufmodell](service-tiers-dtu.md) oder dem [vCore-basierten Kaufmodell](service-tiers-vcore.md) konfigurieren. Die Ressourcenanforderungen eines Pools werden anhand der zusammengefassten Auslastung der hierin befindlichen Datenbanken ermittelt. Die Anzahl der für den Pool verfügbaren Ressourcen wird vom Budget des Entwicklers gesteuert. Der Entwickler fügt dem Pool einfach Datenbanken hinzu, legt optional die minimalen und maximalen Ressourcen für die Datenbanken fest (je nach ausgewähltem Ressourcenmodell die minimale und maximale Anzahl von DTUs oder virtuellen Kernen) und legt dann – basierend auf dem Budget – die Ressourcen des Pools fest. Mithilfe von Pools können Entwickler ihre Dienste problemlos und kontinuierlich ausbauen, von schlanken Startups bis hin zu etablierten Unternehmen.

Im Pool können einzelne Datenbanken die automatische Skalierung innerhalb der angegebenen Parameter flexibel automatisch skalieren. Bei hoher Auslastung kann eine Datenbank mehr Ressourcen nutzen, um die Anforderungen zu erfüllen. Datenbanken verbrauchen bei geringerer Auslastung weniger Ressourcen und ohne Auslastung gar keine Ressourcen. Durch die Bereitstellung von Ressourcen für den gesamten Pool und nicht nur für einzelne Datenbanken vereinfachen Sie Ihre Verwaltungsaufgaben. Außerdem verfügen Sie über ein vorhersagbares Budget für den Pool. Einem vorhandenen Pool können (mit minimalen Ausfallzeiten) zusätzliche Ressourcen hinzugefügt werden. Ebenso können zusätzliche Ressourcen, die nicht mehr benötigt werden, zu jedem beliebigen Zeitpunkt aus einem vorhandenen Pool entfernt werden. Und Sie können Datenbanken zum Pool hinzufügen oder daraus entfernen. Wenn die Ressourcen für eine Datenbank voraussichtlich nicht ausgeschöpft werden, sollten Sie sie entfernen.

> [!NOTE]
> Beim Verschieben von Datenbanken in oder aus Pools für elastische Datenbanken gibt es keine Ausfallzeiten, nur einen kurzen Zeitraum (in der Größenordnung von Sekunden) am Ende des Vorgangs, wenn Datenbankverbindungen gelöscht werden.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Wann sollten Sie einen Pool für elastische SQL-Datenbank-Instanzen erwägen?

Pools eignen sich hervorragend für eine große Anzahl an Datenbanken mit spezifischen Nutzungsmustern. Im Hinblick auf eine einzelne Datenbank wird dieses Muster durch eine geringe durchschnittliche Auslastung mit relativ wenigen Nutzungslastspitzen gekennzeichnet. Im Gegensatz dazu sollten mehrere Datenbanken mit dauerhafter mittlerer Auslastung nicht in demselben Pool für elastische Datenbanken hinzugefügt werden.

Je mehr Datenbanken Sie einem Pool hinzufügen können, desto größer werden Ihre Ersparnisse. Abhängig vom Auslastungsmuster Ihrer Anwendung können Sie bereits mit nur zwei S3-Datenbanken Ersparnisse erzielen.

Die folgenden Abschnitte helfen Ihnen einzuschätzen, ob Ihre Datenbanksammlung von einem Pool profitieren kann. In den Beispielen wird von Standardpools ausgegangen. Die Grundlagen gelten jedoch auch für Basic- und Premiumpools.

### <a name="assessing-database-utilization-patterns"></a>Bewerten der Datenbankauslastungsmuster

In der folgenden Abbildung finden Sie ein Beispiel für eine Datenbank, die sich in der Regel im Leerlauf befindet, bei der es allerdings auch regelmäßig zu Auslastungsspitzen kommt. Dieses Auslastungsmuster eignet sich für einen Pool:

   ![eine einzelne, für einen Pool geeignete Datenbank](./media/elastic-pool-overview/one-database.png)

Das Diagramm veranschaulicht die DTU-Nutzung über einen Zeitraum von einer Stunde zwischen 12:00 und 13:00 Uhr, wobei jeder Datenpunkt eine Granularität von einer Minute hat. DB1 erreicht um 12:01 Uhr den Spitzenwert von 90 DTUs, die mittlere Auslastung liegt jedoch bei unter fünf DTUs. Zum Ausführen dieser Workload in einer einzelnen Datenbank ist die Computegröße S3 erforderlich. Allerdings bewirkt dies, dass die meisten Ressourcen in Zeiten geringer Aktivität nicht verwendet werden.

Mit einem Pool können diese ungenutzten DTUs auf mehrere Datenbanken verteilt werden, wodurch sich die erforderlichen DTUs und damit auch die Gesamtkosten verringern.

Ausgehend vom vorherigen Beispiel nehmen wir einmal an, dass es weitere Datenbanken mit ähnlichen Auslastungsmustern wie DB1 gibt. In den beiden folgenden Abbildungen wird die Auslastung von vier und von weiteren 20 Datenbanken in jeweils einem Graphen dargestellt. Dies soll anhand des DTU-basierten Kaufmodells veranschaulichen, dass sich die Auslastung der Datenbanken zeitlich nicht überlappt:

   ![vier Datenbanken mit einem für einen Pool geeigneten Auslastungsmuster](./media/elastic-pool-overview/four-databases.png)

   ![20 Datenbanken mit einem für einen Pool geeigneten Auslastungsmuster](./media/elastic-pool-overview/twenty-databases.png)

Die zusammengefasste DTU-Auslastung aller 20 Datenbanken wird in der vorstehenden Abbildung durch die schwarze Linie dargestellt. Diese zeigt, dass die zusammengefasste DTU-Auslastung niemals 100 DTUs übersteigt, und gibt an, dass die 20 Datenbanken in diesem Zeitraum 100 eDTUs gemeinsam nutzen können. Dies führt zu einer 20-fachen Verringerung im Hinblick auf die DTUs und einer 13-fachen Preisreduzierung gegenüber der Positionierung der einzelnen Datenbank in S3-Computegrößen für Einzeldatenbanken.

Dieses Beispiel ist aus den folgenden Gründen ideal:

- Es gibt große Unterschiede zwischen der Spitzenauslastung und der mittleren Auslastung pro Datenbank.
- Die Spitzenauslastung jeder Datenbank ergibt sich zu jeweils unterschiedlichen Zeitpunkten.
- eDTUs werden von mehreren Datenbanken gemeinsam genutzt.

Im DTU-basierten Kaufmodell ergibt sich der Preis eines Pools aus den eDTUs des Pools. Während der Preis pro eDTU für einen Pool 1,5 Mal höher als der Preis pro DTU für eine Einzeldatenbank ist, können hierbei **Pool-eDTUs von vielen Datenbanken gemeinsam genutzt werden, daher liegt der Preis unter der Gesamtanzahl der benötigten eDTUs**. Die Unterschiede bei der Preisgestaltung und die gemeinsame Nutzung von eDTUs sind die Basis des Einsparungspotenzials, das von Pools geboten wird.

Beim vCore-basierten Kaufmodell ist der Preis für Pools für elastische Datenbanken mit dem Einheitenpreis für virtuelle Kerne für die einzelnen Datenbanken identisch.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Wie wähle ich die richtige Poolgröße?

Die optimale Größe eines Pools hängt von den zusammengefassten Ressourcen ab, die für alle Datenbanken im Pool benötigt werden. Dies setzt voraus, dass Sie die folgenden Mengen ermitteln:

- Maximale Computeressourcen, die von allen Datenbanken im Pool verwendet werden.  Computeressourcen werden je nach Kaufmodell entweder anhand der eDTUs oder der virtuellen Kerne indiziert.
- Maximale Speicherbytes, die von allen Datenbanken im Pool verwendet werden.

Weitere Informationen zu den Dienstebenen und Ressourcengrenzwerten für jedes Kaufmodell finden Sie unter [DTU-basiertes Kaufmodell](service-tiers-dtu.md) bzw. [vCore-basiertes Kaufmodell](service-tiers-vcore.md).

Die folgenden Schritte können Ihnen dabei helfen, einzuschätzen, ob ein Pool kostengünstiger als einzelne Datenbanken ist:

1. Schätzen Sie die für den Pool benötigten eDTUs oder V-Kerne nach der folgenden Methode:
   - Für das DTU-basierte Kaufmodell:
     - MAX(<*Gesamtanzahl von Datenbanken* &times; *durchschnittliche DTU-Auslastung pro Datenbank*>, *<Anzahl von parallelen Datenbanken mit Spitzenauslastung* &times; *DTU-Spitzenauslastung pro Datenbank*>)
   - Für das vCore-basierte Kaufmodell:
     - MAX(<*Gesamtanzahl von Datenbanken* &times; *durchschnittliche vCore-Auslastung pro Datenbank*>, *<Anzahl von parallelen Datenbanken mit Spitzenauslastung* &times; *vCore-Spitzenauslastung pro Datenbank*>)
2. Schätzen Sie den für den Pool benötigten Gesamtspeicherplatz, indem Sie das erforderliche Datenvolumen für alle Datenbanken im Pool addieren. Ermitteln Sie für das DTU-basierte Kaufmodell dann die eDTU-Poolgröße, die diese Menge an Speicher bietet.
3. Verwenden Sie für das DTU-basierte Kaufmodell die größere der eDTU-Schätzungen aus Schritt 1 und Schritt 2. Verwenden Sie für das vCore-basierte Kaufmodell die V-Kern-Schätzung aus Schritt 1.
4. Auf der [SQL-Datenbank Preisseite](https://azure.microsoft.com/pricing/details/sql-database/) finden Sie die kleinste Poolgröße, die größer ist als die Schätzung aus Schritt 3.
5. Vergleichen Sie den Poolpreis aus Schritt 4 mit dem Preis der geeigneten Computegrößen für Einzeldatenbanken.

> [!IMPORTANT]
> Wenn sich die Anzahl von Datenbanken in einem Pool dem unterstützten Maximalwert nähert, informieren Sie sich unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md) über das mögliche weitere Vorgehen.

### <a name="per-database-properties"></a>Datenbankspezifische Eigenschaften

Sie können optional Eigenschaften pro Datenbank festlegen, um Ressourcenverbrauchsmuster in Pools für elastische Datenbanken zu ändern. Weitere Informationen finden Sie in der Dokumentation zu Ressourcenlimits für [DTUs](resource-limits-dtu-elastic-pools.md#database-properties-for-pooled-databases) und [virtuelle Kerne](resource-limits-vcore-elastic-pools.md#database-properties-for-pooled-databases) in Pools für elastische Datenbanken.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Verwenden anderer SQL Datenbank-Funktionen mit Pools für elastische Datenbanken

### <a name="elastic-jobs-and-elastic-pools"></a>Elastische Aufträge und Pools für elastische Datenbanken

Mit einem Pool werden die Verwaltungsaufgaben vereinfacht, indem Skripts in **[elastischen Aufträgen](elastic-jobs-overview.md)** ausgeführt werden. Durch einen elastischen Auftrag werden die meisten aufwändigen Schritte beseitigt, die bei der Verwendung einer großen Anzahl von Datenbanken anfallen.

Weitere Informationen zu anderen Datenbanktools für die Verwendung mit mehreren Datenbanken finden Sie unter [Horizontales Hochskalieren mit Azure SQL-Datenbank](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Optionen für Geschäftskontinuität für Datenbanken in einem Pool für elastische Datenbanken

Pooldatenbanken unterstützen in der Regel die gleichen [Geschäftskontinuitätsfeatures](business-continuity-high-availability-disaster-recover-hadr-overview.md), die auch bei Einzeldatenbanken zur Verfügung stehen.

- **Point-in-Time-Wiederherstellung**

  Bei der Point-in-Time-Wiederherstellung werden automatische Datenbanksicherungen verwendet, um den Status einer Datenbank in einem Pool zu einem bestimmten Zeitpunkt wiederherzustellen. Siehe [Point-in-Time-Wiederherstellung](recovery-using-backups.md#point-in-time-restore)

- **Geowiederherstellung**

  Die geografische Wiederherstellung ist die Standardoption für die Wiederherstellung, wenn eine Datenbank aufgrund eines Vorfalls in der Region, in der die Datenbank gehostet wird, nicht verfügbar ist. Siehe [Wiederherstellen einer Azure SQL-Datenbank oder Failover auf eine sekundäre Datenbank](disaster-recovery-guidance.md)

- **Aktive Georeplikation**

  Für Anwendungen, für die umfangreichere Wiederherstellungsanforderungen erforderlich sind, als die Geowiederherstellung bieten kann, konfigurieren Sie [aktive Georeplikation](active-geo-replication-overview.md) oder eine [Autofailover-Gruppe](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Erstellen eines neuen Pools für elastische SQL-Datenbank-Instanzen im Azure-Portal

Zur Erstellung eines Pools für elastische Datenbanken im Azure-Portal gibt es zwei Möglichkeiten.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um einen Pool für elastische Datenbanken zu erstellen. Suchen Sie nach **Azure SQL**, und wählen Sie die entsprechende Option aus.
2. Wählen Sie **+Hinzufügen** aus, um die Seite **SQL-Bereitstellungsoption auswählen** zu öffnen. Sie können weitere Informationen zu Pools für elastische Datenbanken anzeigen, indem Sie **Details anzeigen** auf der Kachel **Datenbanken** auswählen.
3. Wählen Sie auf der Kachel **Datenbanken** in der Dropdownliste **Ressourcentyp** die Option **Pool für elastische Datenbanken** und anschließend **Erstellen** aus:

   ![Erstellen eines Pools für elastische Datenbanken](./media/elastic-pool-overview/create-elastic-pool.png)

4. Sie können einen Pool für elastische Datenbanken auch erstellen, indem Sie zu einem vorhandenen Server navigieren und auf **+ Neuer Pool** klicken, um direkt auf diesem Server einen Pool zu erstellen.

> [!NOTE]
> Auf einem Server können mehrere Pools erstellt werden, es ist jedoch nicht möglich, Datenbanken von verschiedenen Servern im gleichen Pool zusammenzufassen.

Die Dienstebene des Pools bestimmt die Features für die elastischen Datenbanken im Pool sowie die für jede Datenbank verfügbare maximale Menge der Ressourcen. Ausführliche Informationen finden Sie in den Ressourceneinschränkungen für Pools für elastische Datenbanken im [DTU-Modell](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Informationen zu V-Kern-basierte Ressourceneinschränkungen für Pools für elastische Datenbanken finden Sie unter [V-Kern-basierte Ressourceneinschränkungen – Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).

Zum Konfigurieren der Ressourcen und Preise des Pools klicken Sie auf **Pool konfigurieren**. Wählen Sie dann eine Dienstebene aus, fügen Sie den Pool Datenbanken hinzu, und konfigurieren Sie die Ressourceneinschränkungen für den Pool und seine Datenbanken.

Wenn Sie die Konfiguration des Pools abgeschlossen haben, können Sie auf „Anwenden“ klicken, dem Pool einen Namen geben und auf „OK“ klicken, um den Pool zu erstellen.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Überwachen eines Pools für elastische Datenbanken und der zugehörigen Datenbanken

Im Azure-Portal können Sie die Verwendung eines Pools für elastische Datenbanken sowie der darin enthaltenen Datenbanken überwachen. Außerdem können Sie einen Änderungssatz für Ihren Pool für elastische Datenbanken erstellen und alle Änderungen gleichzeitig übermitteln. So können Sie etwa Datenbanken hinzufügen oder entfernen, die Einstellungen des Pools für elastische Datenbanken ändern oder Ihre Datenbankeinstellungen anpassen.

Sie können die integrierte [Leistungsüberwachung](./performance-guidance.md) und die [Warnungstools](./alerts-insights-configure-portal.md) in Kombination mit den Leistungsbewertungen verwenden.  Darüber hinaus kann SQL-Datenbank zur einfacheren Überwachung [Metriken und Ressourcenprotokolle ausgeben](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal).

## <a name="customer-case-studies"></a>Fallstudien

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart verwendet Pools für elastische Datenbanken mit Azure SQL-Datenbank, um seine Geschäftsdienste schnell um 1.000 neue Azure SQL-Datenbanken pro Monat zu erweitern.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco verwendet Pools für elastische Datenbanken mit Azure SQL-Datenbank, um in der Cloud schnell Dienste für Tausende von Mandanten bereitzustellen und zu skalieren.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI verwendet Pools für elastische Datenbanken mit Azure SQL-Datenbank, um den Entwicklungszyklus zu beschleunigen und sowohl Kundendienste als auch Leistung zu verbessern.

## <a name="next-steps"></a>Nächste Schritte

- Preisinformationen finden Sie unter [Pool für elastische Datenbanken – Preise](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Informationen zum Skalieren von Pools für elastische Datenbanken finden Sie unter [Skalieren von Pools für elastische Datenbanken](elastic-pool-scale.md) und [Skalieren eines Pools für elastische Datenbanken – Beispielcode](scripts/monitor-and-scale-pool-powershell.md).
- Weitere Informationen zu Entwurfsmustern für SaaS-Anwendungen, für die Pools für elastische Datenbanken verwendet werden, finden Sie unter [Entwurfsmuster für SaaS-Anwendungen mit mehreren Mandanten und Azure SQL-Datenbank](saas-tenancy-app-design-patterns.md).
- Ein SaaS-Tutorial, in dem Pools für elastische Datenbanken verwendet werden, finden Sie in der [Einführung in die SaaS-Anwendung Wingtip](saas-dbpertenant-wingtip-app-overview.md).
- Weitere Informationen zur Ressourcenverwaltung in Pools für elastische Datenbanken, die viele Datenbanken umfassen, finden Sie unter [Ressourcenverwaltung in umfangreichen Pools für elastische Datenbanken](elastic-pool-resource-management.md).
