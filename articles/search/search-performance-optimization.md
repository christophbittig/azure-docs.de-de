---
title: Verfügbarkeit und Kontinuität
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie einen Suchdienst hochverfügbar und resilient gegenüber vorübergehenden Unterbrechungen oder sogar schwerwiegenden Fehlern machen.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.custom: references_regions
ms.openlocfilehash: 22f037751c913e05dde84395ed18252b16d01a6e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554372"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Verfügbarkeit und Geschäftskontinuität in Azure Cognitive Search

Verfügbarkeit wird bei Cognitive Search durch mehrere Replikate erreicht. Geschäftskontinuität und Notfallwiederherstellung werden dagegen durch mehrere Suchdienste erreicht. Dieser Artikel enthält Informationen, die Sie bei der Entwicklung einer Strategie unterstützen, die Ihre Geschäftsanforderungen hinsichtlich Verfügbarkeit und unterbrechungsfreiem Betrieb erfüllt.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Hochverfügbarkeit

In Cognitive Search handelt es sich bei Replikaten um Kopien Ihres Index. Wenn Sie also mehrere Replikate einrichten, können in Azure Cognitive Search Computerneustarts und Wartungsaktivitäten für ein Replikat durchgeführt werden, während gleichzeitig in den anderen Replikaten weiterhin Abfragen ausgeführt werden. Weitere Informationen zum Hinzufügen von Replikaten finden Sie unter [Hinzufügen oder Reduzieren von Replikaten und Partitionen](search-capacity-planning.md#adjust-capacity).

Für jeden einzelnen Suchdienst garantiert Microsoft eine Verfügbarkeit von mindestens 99,9 Prozent, sofern die Konfigurationen die folgenden Kriterien erfüllen: 

+ Zwei Replikate für Hochverfügbarkeit von schreibgeschützten Workloads (Abfragen)

+ Drei oder mehr Replikate für Hochverfügbarkeit von Lese-/Schreibworkloads (Abfragen und Indizierung) 

Für den Free-Tarif wird keine SLA bereitgestellt. Weitere Informationen finden Sie unter [SLA für Azure Cognitive Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="data-residency"></a>Datenresidenz

Azure Cognitive Search speichert Kundendaten ohne Ihre Autorisierung nicht außerhalb der vom Kunden angegebenen Region.

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Verfügbarkeitszonen

[Verfügbarkeitszonen](../availability-zones/az-overview.md) sind eine Funktion der Azure-Plattform und dienen dazu, die Rechenzentren einer Region in verschiedene physische Standortgruppen zu unterteilen, um Hochverfügbarkeit innerhalb der Region zu gewährleisten. Wenn Sie Verfügbarkeitszonen für Cognitive Search verwenden, sind einzelne Replikate die Einheiten für die Zonenzuweisung. Der Suchdienst wird innerhalb einer Region ausgeführt, die Replikate werden in verschiedenen Zonen ausgeführt.

Sie können Verfügbarkeitszonen mit Azure Cognitive Search nutzen, indem Sie Ihrem Suchdienst zwei oder mehr Replikate hinzufügen. Jedes Replikat wird in einer anderen Verfügbarkeitszone innerhalb der Region platziert. Wenn Sie mehr Replikate als Verfügbarkeitszonen haben, werden die Replikate so gleichmäßig wie möglich auf die Verfügbarkeitszonen verteilt. Es gibt keine spezifische Aktion Ihrerseits, abgesehen vom [Erstellen eines Suchdiensts](search-create-service-portal.md) in einer Region mit Verfügbarkeitszonen und dem anschließenden Konfigurieren des Diensts für die [Verwendung mehrerer Replikate](search-capacity-planning.md#adjust-capacity).

Azure Cognitive Search unterstützt derzeit Verfügbarkeitszonen für Suchdienste ab dem Standard-Tarif, die in einer der folgenden Regionen erstellt wurden:

+ Australien, Osten (Datum der Erstellung: ab dem 30. Januar 2021)
+ Brasilien, Süden (erstellt am 2. Mai 2021 oder später)
+ Kanada, Mitte (Datum der Erstellung: ab dem 30. Januar 2021)
+ USA, Mitte (Datum der Erstellung: ab dem 4. Dezember 2020)
+ USA, Osten (Datum der Erstellung: ab dem 27. Januar 2021)
+ USA, Osten 2 (Datum der Erstellung: ab dem 30. Januar 2021)
+ Frankreich, Mitte (Datum der Erstellung: ab dem 23. Oktober 2020)
+ Deutschland, Westen-Mitte (erstellt am 3. Mai 2021 oder später)
+ Japan, Osten (Datum der Erstellung: ab dem 30. Januar 2021)
+ Europa, Norden (Datum der Erstellung: ab dem 28. Januar 2021)
+ USA, Süden-Mitte (erstellt am 30. April 2021 oder später)
+ Asien, Südosten (Datum der Erstellung: ab dem 31. Januar 2021)
+ Vereinigtes Königreich, Süden (Datum der Erstellung: ab dem 30. Januar 2021)
+ US Gov Virginia (erstellt am 30. April 2021 oder später)
+ Europa, Westen (Datum der Erstellung: ab dem 29. Januar 2021)
+ USA, Westen 2 (Datum der Erstellung: ab dem 30. Januar 2021)

Verfügbarkeitszonen wirken sich nicht auf die [Vereinbarung zum Servicelevel (SLA) für Azure Cognitive Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/) aus. Sie benötigen für Hochverfügbarkeit immer noch mindestens drei Replikate.

## <a name="multiple-services-in-separate-geographic-regions"></a>Mehrere Dienste in separaten geografischen Regionen

Obwohl die meisten Kunden nur einen Dienst nutzen, kann die Dienstredundanz womöglich nötig sein, wenn die operativen Anforderungen Folgendes enthalten:

+ [Business Continuity & Disaster Recovery (BCDR)](../best-practices-availability-paired-regions.md): (Cognitive Search bietet kein sofortiges Failover bei einem Ausfall.)
+ Global bereitgestellte Anwendungen: Bei Abfrage- und Indizierungsanforderungen aus aller Welt ist die Leistung für Benutzer, die dem Hostrechenzentrum am nächsten sind, höher. Durch Erstellen zusätzlicher Dienste in Regionen, die sich in der Nähe dieser Benutzer befinden, kann dafür gesorgt werden, dass die Leistung für alle Benutzer gleich ist.
+ [Mehrinstanzenfähige Architekturen](search-modeling-multitenant-saas-applications.md) rufen manchmal zwei oder mehr Dienste auf.

Wenn Sie zwei weitere Suchdienste benötigen, können Sie sie in verschiedenen Regionen erstellen, um die Anwendungsanforderungen hinsichtlich Kontinuität und Wiederherstellung zu erfüllen und schnellere Antwortzeiten für eine globale Benutzerbasis zu erzielen.

Azure Cognitive Search bietet derzeit keine automatisierte Methode zur regionsübergreifenden Georeplikation von Suchindizes. Es gibt allerdings einige Verfahren, mit denen sich ein solcher Prozess einfach implementieren und verwalten lässt. Diese werden in den nächsten Abschnitten beschrieben.

Ziel der Einrichtung geografisch verteilter Suchdienste ist es, über mindestens zwei Indizes in mindestens zwei Regionen zu verfügen, sodass Benutzer zum Azure Cognitive Search-Dienst mit der geringsten Wartezeit weitergeleitet werden:

   ![Tabelle der Dienste nach Region][1]

Sie können diese Architektur implementieren, indem Sie mehrere Dienste erstellen und eine Strategie für die Datensynchronisierung entwerfen. Optional können Sie eine Ressource wie Azure Traffic Manager für Routinganforderungen einschließen. Weitere Informationen finden Sie unter [Erstellen eines Azure Cognitive Search-Diensts im Portal](search-create-service-portal.md).

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Gewährleisten der Synchronisierung von Daten über mehrere Dienste hinweg

Für die Synchronisierung mehrerer verteilter Suchdienste stehen zwei Optionen zur Verfügung: der [Azure Cognitive Search-Indexer](search-indexer-overview.md) und die Push-API (auch als [Azure Cognitive Search-REST-API](/rest/api/searchservice/) bezeichnet). 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Option 1: Aktualisieren von Inhalten in mehreren Diensten mithilfe von Indexern

Wenn Sie bereits einen Indexer für einen Dienst verwenden, können Sie einen zweiten Indexer für einen zweiten Dienst so konfigurieren, dass er dasselbe Datenquellenobjekt verwendet und somit Daten vom selben Standort abruft. Jeder Dienst in jeder Region hat einen eigenen Indexer und einen Zielindex (der Suchindex wird nicht gemeinsam verwendet, sodass Daten dupliziert werden), aber jeder Indexer verweist auf die gleiche Datenquelle.

Diese Architektur würde ganz allgemein in etwa wie folgt aussehen.

   ![Einzelne Datenquelle mit verteilten Indexer- und Dienstkombinationen][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Option 2: Pushen von Inhaltsaktualisierungen in mehreren Diensten mithilfe von REST-APIs

Wenn Sie die Azure Cognitive Search-REST-API zum [Pushen von Inhalten an Ihren Suchindex](tutorial-optimize-indexing-push-api.md) verwenden, können Sie die verschiedenen Suchdienste synchronisieren, indem Sie Änderungen bei jeder Aktualisierung an alle Suchdienste pushen. Stellen Sie sicher, dass Ihr Code auch mit Fällen zurecht kommt, in denen die Aktualisierung eines einzelnen Suchdiensts nicht erfolgreich ist, andere Suchdienste aber erfolgreich aktualisiert werden.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Koordinieren von Anforderungen mithilfe von Azure Traffic Manager

In [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) können Sie Anforderungen an mehrere Websites an verschiedenen geografischen Standorten weiterleiten, die dann von mehreren Diensten unterstützt werden. Traffic Manager kann testen, ob die kognitive Azure-Suche verfügbar ist, und Benutzer während eines Ausfalls an alternative Suchdienste weiterleiten – dies ist ein großer Vorteil. Wenn Sie Suchanforderungen über Azure-Websites weiterleiten, ermöglicht Azure Traffic Manager zudem den Lastenausgleich in Fällen, in denen die Website erreichbar ist, die kognitive Azure-Suche aber nicht. Hier finden Sie ein Beispiel für eine Architektur mit Traffic Manager.

   ![Tabelle der Dienste nach Region mit Traffic Manager im Zentrum][3]

## <a name="disaster-recovery-and-service-outages"></a>Notfallwiederherstellung und Dienstausfälle

Wie in der [Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) aufgeführt, garantieren wir eine hohe Verfügbarkeit für Indexabfrageanforderungen, wenn eine Azure Cognitive Search-Dienstinstanz mit zwei oder mehr Replikaten konfiguriert ist, und für Indexaktualisierungsanforderungen, wenn eine Azure Cognitive Search-Dienstinstanz mit drei oder mehr Replikaten konfiguriert ist. Es steht jedoch kein integrierter Mechanismus für die Notfallwiederherstellung bereit. Sollte ein unterbrechungsfreier Dienst im Falle schwerwiegender Fehler, die nicht der Kontrolle von Microsoft unterliegen, benötigt werden, empfehlen wir, dass Sie einen zweiten Dienst in einer anderen Region bereitstellen und eine Georeplikationsstrategie implementieren, um sicherzustellen, dass Indizes für alle Dienste vollständig redundant sind.

Kunden, die [Indexer](search-indexer-overview.md) zum Auffüllen und Aktualisieren von Indizes verwenden, können die Notfallwiederherstellung über geospezifische Indizes ausführen, welche die gleiche Datenquelle verwenden. Zwei Dienste in unterschiedlichen Regionen, die jeweils einen Indexer ausführen, könnten die gleiche Datenquelle indizieren, um Georedundanz zu erzielen. Wenn Sie Daten aus Datenquellen indizieren, die auch georedundant sind, sollten Sie darauf achten, dass Azure Cognitive Search-Indexer eine inkrementelle Indizierung (Zusammenführen von Updates neuer, geänderter oder gelöschter Dokumente) nur aus primären Replikaten ausführen können. Achten Sie im Falle eines Failoverereignisses darauf, den Indexer erneut auf das neue primäre Replikat auszurichten. 

Wenn Sie keine Indexer verwenden, können Sie Ihren Anwendungscode verwenden, um Objekte und Daten gleichzeitig per Pushvorgang an verschiedene Dienste zu übermitteln. Weitere Informationen finden Sie unter [Gewährleisten der Synchronisierung von Daten über mehrere Dienste hinweg](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Alternativen für Sicherung und Wiederherstellung

Da Azure Cognitive Search keine primäre Datenspeicherlösung ist, wird von Microsoft kein formales Verfahren für Self-Service-Sicherung und -Wiederherstellung bereitgestellt. Sie können jedoch den Beispielcode **index-backup-restore** in [diesem Azure Cognitive Search .NET-Beispielrepository](https://github.com/Azure-Samples/azure-search-dotnet-samples) verwenden, um Ihre Indexdefinition und die Momentaufnahme in einer Reihe von JSON-Dateien zu sichern, und diese Dateien dann bei Bedarf verwenden, um den Index wiederherzustellen. Mit diesem Tool können Sie auch Indizes zwischen Dienstebenen verschieben.

Andernfalls ist Ihr Anwendungscode, der zum Erstellen und Auffüllung eines Index verwendet wird, de facto die Wiederherstellungsoption, wenn Sie aus Versehen einen Index löschen. Um einen Index neu zu erstellen, würden Sie ihn (sofern vorhanden) löschen, den Index im Dienst wiederherstellen und ihn erneut laden, indem Sie Daten aus Ihrem primären Datenspeicher abrufen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Tarifen und den Grenzwerte für jeden Tarif finden Sie unter [Grenzwerte des Diensts](search-limits-quotas-capacity.md). Unter [Planen der Kapazität](search-capacity-planning.md) erfahren Sie mehr über Partitions- und Replikatkombinationen, und die [Fallstudie: Verwenden von Cognitive Search zur Unterstützung komplexer KI-Szenarien](https://techcommunity.microsoft.com/t5/azure-ai/case-study-effectively-using-cognitive-search-to-support-complex/ba-p/2804078) enthält Tipps aus der Praxis.

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png