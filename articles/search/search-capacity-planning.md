---
title: Schätzen der Kapazität für Abfrage- und Indexworkloads
titleSuffix: Azure Cognitive Search
description: Passen Sie Partitions- und Replikatcomputerressourcen in der kognitiven Azure-Suche an, wobei jede Ressource in abrechenbaren Sucheinheiten abgerechnet wird.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: c7c5ce32801efe68d653dc3abd97c1e5ff3a7f9b
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113000408"
---
# <a name="estimate-and-manage-capacity-of-a-search-service"></a>Schätzen und Verwalten der Kapazität eines Suchdiensts

Nehmen Sie sich vor dem [Erstellen eines Suchdiensts](search-create-service-portal.md) und Festlegen eines bestimmten [Tarifs](search-sku-tier.md) einige Minuten Zeit, um zu verstehen, wie die Kapazität funktioniert und wie Sie Replikate und Partitionen an Fluktuationen der Workload anpassen können.

In Azure Cognitive Search basiert die Kapazität auf *Replikaten* und *Partitionen*. Replikate sind Kopien des Suchmoduls.
Partitionen sind Speichereinheiten. Jeder neue Suchdienst verfügt zunächst über jeweils ein Replikat und eine Partition. Sie haben aber die Möglichkeit, jede Ressource separat hochzuskalieren, um Fluktuationen bei den Workloads abzudecken. Das Hinzufügen dieser Ressourcen ist jeweils [abrechenbar](search-sku-manage-costs.md#billable-events).

Die technischen Merkmale von Replikaten und Partitionen, z. B. Verarbeitungsgeschwindigkeit und Datenträger-E/A, variieren je nach [Dienstebene](search-sku-tier.md). Wenn Sie die Bereitstellung unter „Standard“ durchgeführt haben, sind Replikate und Partitionen schneller und größer als bei „Basic“.

Wenn die Kapazität geändert wird, ist dies nicht sofort wirksam. Die Inbetrieb- bzw. Außerbetriebnahme von Partitionen kann bis zu eine Stunde dauern. Dies gilt vor allem für Dienste mit großen Datenmengen.

Beim Skalieren eines Suchdiensts können Sie zwischen den folgenden Tools und Ansätzen wählen:

+ [Azure portal](#adjust-capacity)
+ [Azure PowerShell](search-manage-powershell.md)
+ [Azure-Befehlszeilenschnittstelle](/cli/azure/search)
+ [Verwaltungs-REST-API](/rest/api/searchmanagement/2020-08-01/services)

## <a name="concepts-search-units-replicas-partitions-shards"></a>Konzepte: Sucheinheiten, Replikate, Partitionen, Shards

Die Kapazität wird in *Sucheinheiten* ausgedrückt, die in Kombinationen von *Partitionen* und *Replikaten* unter Verwendung eines zugrunde liegenden *Sharding*-Mechanismus zur Unterstützung flexibler Konfigurationen zugewiesen werden können:

| Konzept  | Definition|
|----------|-----------|
|*Sucheinheit* | Eine einzelnes Inkrement der verfügbaren Gesamtkapazität (36 Einheiten). Es ist auch die Abrechnungseinheit für einen Azure Cognitive Search-Dienst. Zum Ausführen des Diensts ist mindestens eine Einheit erforderlich.|
|*Replikat* | Replikate sind Instanzen des Suchdiensts und dienen in erster Linie zum Lastenausgleich bei Abfragevorgängen. Jedes Replikat hostet eine Kopie eines Indexes. Wenn Sie drei Replikate zuordnen, stehen Ihnen drei Kopien eines Indexes für die Bearbeitung von Abfrageanforderungen zur Verfügung.|
|*Partition* | Physischer Speicher und E/A für Lese-/Schreibvorgänge (z. B. bei der Neuerstellung oder Aktualisierung eines Index). Jede Partition hat einen Anteil am Gesamtindex. Wenn Sie drei Partitionen zuordnen, wird Ihr Index in Drittel aufgeteilt. |
|*Shard* | Ein Block eines Indexes. Azure Cognitive Search unterteilt jeden Index in Shards, um das Hinzufügen von Partitionen zu beschleunigen (durch Verschieben der Shards in neue Sucheinheiten).|

Das folgende Diagramm zeigt die Beziehung zwischen Replikaten, Partitionen, Shards und Sucheinheiten. Es zeigt ein Beispiel dafür, wie ein einzelner Index in einem Dienst mit zwei Replikaten und zwei Partitionen über vier Sucheinheiten verteilt ist. Jede der vier Sucheinheiten speichert nur die Hälfte der Shards des Indexes. Die Sucheinheiten in der linken Spalte speichern die erste Hälfte der Shards, bestehend aus der ersten Partition, während die in der rechten Spalte die zweite Hälfte der Shards, bestehend aus der zweiten Partition, speichern. Da es zwei Replikate gibt, sind zwei Kopien der einzelnen Indexshards vorhanden. Die Sucheinheiten in der oberen Zeile speichern eine aus dem ersten Replikat bestehende Kopie, während die Sucheinheiten in der unteren Zeile eine aus dem zweiten Replikat bestehende weitere Kopie speichern.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Für Suchindizes erfolgt das Sharding partitionsübergreifend.":::

Das obige Diagramm ist nur ein Beispiel. Es sind viele Kombinationen von Partitionen und Replikaten möglich, bis zu einer Gesamtzahl von maximal 36 Sucheinheiten.

In Cognitive Search ist die Shardverwaltung ein Implementierungsdetail und nicht konfigurierbar. Aber das Wissen, dass das Sharding für einen Index ausgeführt wird, hilft dabei, die gelegentlichen Anomalien bei der Erstellung der Rangfolge und beim Verhalten von AutoVervollständigen zu verstehen:

+ Anomalien beim Erstellen der Rangfolge: Suchbewertungen werden zunächst auf der Shard-Ebene berechnet und dann zu einem einzelnen Resultset aggregiert. Abhängig von den Merkmalen des Shardinhalts können Übereinstimmungen aus einem Shard höher eingestuft werden als Übereinstimmungen in einem anderen Shard. Wenn Sie in den Suchergebnissen intuitive Rangfolgen bemerken, ist dies höchstwahrscheinlich auf die Auswirkungen des Shardings zurückzuführen – insbesondere wenn die Indizes klein sind. Sie können diese Anomalien in der Rangfolge vermeiden, indem Sie die Option zum [globalen Berechnen von Bewertungen über den gesamten Index](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions) auswählen. Dies ist jedoch mit Leistungseinbußen verbunden.

+ Anomalien beim AutoVervollständigen: AutoVervollständigen-Abfragen, bei denen Vergleiche anhand der ersten paar Zeichen eines teilweise eingegebenen Begriffs vorgenommen werden, akzeptieren einen Fuzzyparameter, der kleine Abweichungen in der Rechtschreibung verzeiht. Bei AutoVervollständigen ist die Fuzzyübereinstimmung auf Begriffe innerhalb des aktuellen Shards beschränkt. Wenn z. B. ein Shard „Microsoft“ enthält und ein Teilbegriff von „micor“ eingegeben wird, findet die Suchmaschine in diesem Shard eine Übereinstimmung mit „Microsoft“, aber nicht in anderen Shards, die die restlichen Teile des Indexes enthalten.

## <a name="approaching-estimation"></a>Annäherung an die Schätzung

Zwischen der Kapazität und den Kosten für die Ausführung des Diensts besteht ein direkter Zusammenhang. Tarife sind mit Grenzwerten auf zwei Ebenen verbunden: Inhalt (z. B. eine Anzahl von Indizes für einen Dienst) und Speicher. Weil der zuerst erreichte Grenzwert gilt, müssen Sie beide Aspekte berücksichtigen.

Mengen von Indizes und anderen Objekten werden normalerweise von geschäftlichen und technischen Anforderungen vorgegeben. Beispielsweise könnten Sie mehrere Versionen desselben Indexes für aktive Entwicklung, Tests und Produktion haben.

Speicheranforderungen werden durch die Größe der Indizes bestimmt, die Sie für die Erstellung erwarten. Es gibt keine solide Heuristik oder allgemein gültige Regeln, die bei Schätzungen helfen. Die einzige Möglichkeit zur Ermittlung der Größe eines Indexes ist, [einen zu erstellen](search-what-is-an-index.md). Dessen Größe basiert auf den importierten Daten, der Textanalyse und Indexkonfiguration, also beispielsweise, ob Sie Vorschlagsfunktionen, die Filterung und Sortierung aktivieren.

Bei der Volltextsuche entspricht die primäre Datenstruktur der Struktur eines [invertierten Indexes](https://en.wikipedia.org/wiki/Inverted_index), die über andere Eigenschaften als die Quelldaten verfügt. Bei einem invertierten Index werden Größe und Komplexität vom Inhalt bestimmt, nicht notwendigerweise von der Menge der Daten, die Sie eingeben. Eine große Datenquelle mit hoher Redundanz könnte einen kleineren Index ergeben als ein kleineres Dataset mit stark variierendem Inhalt. Daher ist es kaum möglich, die Indexgröße aus der Größe des ursprünglichen Datasets abzuleiten.

Attribute im Index, z. B. das Aktivieren von Filtern und Sortierung, wirken sich auf die Speicheranforderungen aus. Die Verwendung von Vorschlagsfunktionen hat ebenfalls Auswirkungen auf den Speicher. Weitere Informationen finden Sie unter [Attribute und Indexgröße](search-what-is-an-index.md#index-size).

> [!NOTE]
> Auch wenn sich das Schätzen der künftig benötigten Indizes und des erforderlichen Speichers wie bloßes Mutmaßen anfühlt, lohnt sich der Aufwand. Stellt sich die Kapazität eines Tarifs als zu gering heraus, müssen Sie einen neuen Dienst in einem höheren Tarif bereitstellen und anschließend die [Indizes neu laden](search-howto-reindex.md). Für einen Dienst kann kein direktes Upgrade von einem Tarif auf einen anderen durchgeführt werden.
>

### <a name="estimate-with-the-free-tier"></a>Schätzung mit dem Free-Tarif

Eine Möglichkeit zum Abschätzen der Kapazität besteht darin, mit dem Tarif „Free“ zu beginnen. Bedenken Sie, dass der Dienst vom Typ „Free“ bis zu drei Indizes, 50 MB Speicherplatz und 2 Minuten Indizierungszeit bietet. Es kann schwierig sein, mit diesen Einschränkungen eine voraussichtliche Indexgröße zu schätzen. Sie können es dennoch wie folgt versuchen:

+ [Erstellen Sie einen kostenlosen Dienst](search-create-service-portal.md).
+ Bereiten Sie ein kleines repräsentatives Dataset vor.
+ Erstellen Sie einen Index, und laden Sie Ihre Daten. Wenn das Dataset in einer von Indexern unterstützten Azure-Datenquelle gehostet werden kann, können Sie den Index mithilfe des [Datenimport-Assistenten im Portal](search-get-started-portal.md) erstellen und laden. Andernfalls sollten Sie REST und [Postman](search-get-started-rest.md) oder [Visual Studio Code](search-get-started-vs-code.md) verwenden, um den Index zu erstellen und die Daten zu pushen. Beim Pushmodell müssen Daten in Form von JSON-Dokumenten vorliegen, wobei Felder im Dokument Feldern im Index entsprechen.
+ Sammeln Sie Informationen zum Index, z. B. die Größe. Funktionen und Attribute haben Auswirkungen auf den Speicher. Durch das Hinzufügen von Vorschlagsfunktionen (Search-as-you-Type-Abfragen) erhöhen sich beispielsweise die Speicheranforderungen. 

  Sie können mehrere Versionen eines Index unter Verwendung desselben Datasets erstellen und dabei verschiedene Attribute für die einzelnen Felder verwenden, um zu ermitteln, wie sich die Speicheranforderungen ändern. Weitere Informationen finden Sie [im Abschnitt „Hinweise zum Speicher“ unter „Erstellen eines einfachen Index“](search-what-is-an-index.md#index-size).

Mit einer groben Schätzung können Sie die betreffende Größe für zwei Indizes verdoppeln (Entwicklung und Produktion) und dann Ihren Tarif entsprechend auswählen.

### <a name="estimate-with-a-billable-tier"></a>Schätzung mit einem abrechenbaren Tarif

Dedizierte Ressourcen ermöglichen längere Sampling- und Verarbeitungszeiten und eignen sich besser für realistische Schätzungen bezüglich Indexmenge, Größe und Abfragevolumen während der Entwicklung. Einige Kunden beginnen direkt mit einem abrechenbaren Tarif und werten ihn im Lauf des Entwicklungsprojekts erneut aus.

1. [Überprüfen Sie die Dienstgrenzwerte in jedem Tarif](./search-limits-quotas-capacity.md#index-limits), um festzustellen, ob niedrigere Tarife die Anzahl der benötigten Indizes unterstützen können. In den Tarifen „Basic“, „S1“ und „S2“ betragen die Grenzwerte für Indizes 15, 50 bzw. 200. Der Tarif „Speicheroptimiert“ ist auf zehn Indizes beschränkt, da er für eine geringe Anzahl sehr großer Indizes ausgelegt ist.

1. [Erstellen Sie einen Dienst unter einem abzurechnenden Tarif](search-create-service-portal.md):

    + Beginnen Sie mit einem niedrigen Tarif, z. B. „Basic“ oder „S1“, wenn Sie sich über die projizierte Last nicht sicher sind.
    + Starten Sie hoch, mit „S2“ oder sogar „S3“, wenn Tests umfangreiche Indizierungs- und Abfrageworkloads enthalten.
    + Beginnen Sie mit einem Tarif vom Typ „Speicheroptimiert“ („L1“ oder „L2“), wenn Sie sehr viele Daten indizieren möchten und die Abfragelast relativ gering ist (etwa im Fall einer internen Geschäftsanwendung).

1. [Erstellen Sie einen anfänglichen Index](search-what-is-an-index.md), um zu bestimmen, wie Quelldaten in einen Index übersetzt werden. Dies ist die einzige Möglichkeit, die Größe des Indexes zu schätzen. 

1. [Überwachen Sie Speicher, Dienstgrenzwerte, Abfragevolumen und Latenz](search-monitor-usage.md) im Portal. Im Portal werden die Abfragen pro Sekunde, gedrosselte Abfragen und die Wartezeit bei Suchvorgängen angezeigt. Diese Werte können Ihnen dabei helfen, den richtigen Tarif auszuwählen.

1. Fügen Sie Replikate hinzu, wenn Sie Hochverfügbarkeit benötigen oder wenn die Abfrageleistung verlangsamt wird.

   Es gibt keine Richtlinien zur Anzahl der Replikate, die für bestimmte Abfragelasten benötigt werden. Die Abfrageleistung hängt von der Komplexität der Abfrage und den konkurrierenden Workloads ab. Obwohl das Hinzufügen von Replikaten die Leistung deutlich erhöht, ist das Endergebnis aber nicht streng linear: Das Hinzufügen von 3 Replikaten garantiert keinen dreifachen Durchsatz. Leitlinien zur Schätzung der QPS (Queries per Second, Abfragen pro Sekunde) für Ihre Lösung finden Sie unter [Skalieren zur Verbesserung der Leistung](search-performance-optimization.md) und [Überwachen von Abfragen](search-monitor-queries.md).

> [!NOTE]
> Speicheranforderungen können vergrößert werden, wenn Sie Daten einbeziehen, die nie durchsucht werden. Im Idealfall enthalten Dokumente nur die Daten, die Sie für die Suche benötigen. Binäre Daten sind nicht durchsuchbar und sollten separat gespeichert werden (beispielsweise in einer Azure-Tabelle oder in Azure Blob Storage). In diesem Fall sollte dem Index ein Feld mit einem URL-Verweis auf die externen Daten hinzugefügt werden. Die maximale Größe eines einzelnen Suchdokuments beträgt 16 MB (oder weniger, wenn Sie im Rahmen einer einzelnen Anforderung mehrere Dokumente gleichzeitig hochladen). Weitere Informationen finden Sie unter [Grenzwerte für den Dienst für die kognitive Azure-Suche](search-limits-quotas-capacity.md).
>

**Überlegungen zum Abfragevolumen**

„Abfragen pro Sekunde“ (Queries per Second, QPS) ist eine wichtige Metrik bei der Leistungsoptimierung, spielt aber im Allgemeinen bei der Tarifauswahl nur dann eine Rolle, wenn Sie zu Beginn ein hohes Abfragevolumen erwarten.

Die Tarife vom Typ „Standard“ bieten ein ausgewogenes Verhältnis von Replikaten und Partitionen. Durch Hinzufügen von Replikaten für den Lastenausgleich oder durch Hinzufügen von Partitionen für eine parallele Verarbeitung können Sie kürzere Abfrageverarbeitungszeiten erzielen. Nach der Bereitstellung des Diensts können Sie die Leistung optimieren.

Ist von Anfang an ein hohes durchgängiges Abfragevolumen zu erwarten, sollten Sie höhere Tarife vom Typ „Standard“ in Verbindung mit einer leistungsfähigeren Hardware in Erwägung ziehen. Bleiben diese Abfragevolumen aus, können Sie Partitionen und Replikate offline schalten oder auch zu einem Dienst mit niedrigerem Tarif wechseln. Weitere Informationen zum Berechnen des Abfragedurchsatzes finden Sie unter [Überlegungen zur Leistung und Optimierung der kognitiven Azure-Suche](search-performance-optimization.md).

Die Tarife vom Typ „Speicheroptimiert“ eigenen sich für Workloads mit großen Datenmengen und unterstützen insgesamt mehr verfügbaren Indexspeicher bei niedrigeren Anforderungen an die Abfragewartezeit. Dabei sollten Sie weiterhin zusätzliche Replikate für den Lastenausgleich und zusätzliche Partitionen für die parallele Verarbeitung verwenden. Nach der Bereitstellung des Diensts können Sie die Leistung optimieren.

**Vereinbarungen zum Servicelevel**

Für Features des kostenlosen Tarifs (Free) und der Previewfunktionen gelten keine [Vereinbarungen zum Servicelevel (Service Level Agreements, SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Für alle abrechenbaren Tarife gelten SLAs, wenn Sie genügend Redundanz für Ihren Dienst bereitstellen. Für Abfrage-SLAs (Lesezugriff) benötigen Sie zwei oder mehr Replikate. Für Abfrage- und Indizierungs-SLAs (Lese-/Schreibzugriff) sind drei oder mehr Replikate erforderlich. Die Anzahl der Partitionen hat keine Auswirkungen auf die SLAs.

## <a name="tips-for-capacity-planning"></a>Tipps zur Kapazitätsplanung

+ Erstellen Sie Metriken zu Abfragen, und sammeln Sie Daten zu Verwendungsmustern (Abfragen während der Geschäftszeiten, Indizierung außerhalb der Spitzenzeiten). Treffen Sie anhand dieser Daten Entscheidungen zur Dienstbereitstellung. Da ein stündliches oder tägliches Intervall ungeeignet ist, können Sie Partitionen und Ressourcen dynamisch so anpassen, dass geplante Änderungen von Abfragevolumen ermöglicht werden. Auch ungeplante, aber anhaltende Änderungen können berücksichtigt werden, wenn Ebenen beständig genug sind, um die Durchführung von Aktionen zu garantieren.

+ Beachten Sie, dass der einzige Nachteil einer unterdimensionierten Bereitstellung darin besteht, dass Sie ggf. einen Dienst entfernen müssen, wenn die tatsächlichen Anforderungen Ihre Schätzungen überschreiten. Um Dienstunterbrechungen zu vermeiden, sollten Sie einen neuen Dienst in einem höheren Tarif erstellen und parallel ausführen, bis alle Apps und Anforderungen den neuen Endpunkt zum Ziel haben.

## <a name="when-to-add-capacity"></a>Wann Sie Kapazität hinzufügen sollten

Zunächst wird einem Dienst eine Mindestmenge von Ressourcen (bestehend aus einer Partition und einem Replikat) zugeordnet. Der [ausgewählte Tarif](search-sku-tier.md) bestimmt die Größe und Geschwindigkeit der Partition, und jeder Tarif ist um eine Reihe von Eigenschaften optimiert, die auf bestimmte Szenarien ausgerichtet sind. Wenn Sie sich für einen höherwertigen Tarif entscheiden, [benötigen Sie möglicherweise weniger Partitionen](search-performance-tips.md#service-capacity) als bei S1. Eine der Fragen, die Sie durch selbstgesteuerte Tests beantworten müssen, lautet: Bringt eine größere und teurere Partition eine bessere Leistung als zwei billigere Partitionen bei einem Dienst, der in einem niedrigeren Tarif bereitgestellt wird?

Ein einzelner Dienst muss über genügend Ressourcen verfügen, um sämtliche Workloads (Indizierung und Abfragen) bewältigen zu können. Beide Workloads laufen nicht im Hintergrund. Sie können die Indizierung für Zeiten planen, in denen Abfrageanforderungen naturgemäß weniger häufig sind, aber der Dienst priorisiert ansonsten keine Aufgabe gegenüber einer anderen. Zusätzlich gleicht ein gewisses Maß an Redundanz die Abfrageleistung aus, wenn Dienste oder Knoten intern aktualisiert werden.

Richtlinien für die Entscheidung, ob Kapazität hinzugefügt werden soll, sind z. B.:

+ Erfüllen der Hochverfügbarkeitskriterien für die Vereinbarung zum Servicelevel
+ Die Häufigkeit von HTTP 503-Fehlern nimmt zu
+ Es werden große Abfragevolumina erwartet

Allgemein gilt: Suchanwendungen benötigen in der Regel mehr Replikate als Partitionen – insbesondere, wenn die Dienstvorgänge auf Abfrageworkloads ausgerichtet sind. Jedes Replikat ist eine Kopie Ihres Index und ermöglicht dem Dienst, die Anforderungslast auf verschiedene Kopien zu verteilen. Der gesamte Lastenausgleich und die Replikation eines Index werden von Azure Cognitive Search verwaltet. Sie können die Anzahl der Replikate, die Ihrem Dienst zugeordnet sind, jederzeit ändern. In einem Suchdienst mit Tarif „Standard“ können Sie bis zu 12 Replikate zuordnen, im Tarif „Basic“ bis zu drei. Die Replikatzuordnung kann entweder über die [Azure-Portal](search-create-service-portal.md) oder eine der programmgesteuerten Optionen vorgenommen werden.

Suchanwendungen, die Datenaktualisierung nahezu in Echtzeit erfordern, benötigen proportional mehr Partitionen als Replikate. Das Hinzufügen von Partitionen verteilt Lese-/ Schreibvorgänge über eine größere Anzahl von Computerressourcen. Außerdem erhalten Sie mehr Speicherplatz auf dem Datenträger zum Speichern zusätzlicher Indizes und Dokumente.

Schließlich erfordern größere Indizes eine längere Abfragezeit. Daher werden Sie feststellen, dass jede inkrementelle Zunahme an Partitionen einen kleineren, aber proportionalen Anstieg der Replikate erforderlich macht. Die Komplexität Ihrer Abfragen und das Abfragevolumen haben darauf Einfluss, wie schnell die Abfrage ausgeführt wird.

> [!NOTE]
> Wenn Sie weitere Replikate oder Partitionen hinzufügen, erhöhen sich die Kosten für die Ausführung des Diensts. Außerdem kann die Sortierung der Ergebnisse leicht variieren. Sie sollten daher den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) verwenden, um die Auswirkungen des Hinzufügens weiterer Knoten auf die Abrechnung zu verstehen. Das [Diagramm unten ](#chart) kann Ihnen helfen, die Anzahl der für eine bestimmte Konfiguration erforderlichen Sucheinheiten zu ermitteln. Weitere Informationen dazu, wie sich zusätzliche Replikate auf die Abfrageverarbeitung auswirken, finden Sie unter [Sortieren von Ergebnissen](search-pagination-page-layout.md#ordering-results).

<a name="adjust-capacity"></a>

## <a name="add-or-reduce-replicas-and-partitions"></a>Hinzufügen oder Reduzieren von Replikaten und Partitionen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie den Suchdienst aus.

1. Öffnen Sie unter **Einstellungen** die Seite **Skalierung**, um Replikate und Partitionen zu ändern. 

   Der folgende Screenshot zeigt einen Basic Standard, der mit einem Replikat und einer Partition bereitgestellt wurde. Die Formel im unteren Bereich gibt an, wie viele Sucheinheiten verwendet werden (1). Wenn der Preis pro Einheit 100 US-Dollar wäre (kein echter Preis), würden die monatlichen Kosten für die Ausführung dieses Diensts durchschnittlich 100 US-Dollar betragen.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Seite „Skalierung“ mit aktuellen Werten" border="true":::

1. Mit dem Schieberegler können Sie die Anzahl der Partitionen erhöhen oder verringern. Wählen Sie **Speichern** aus.

   In diesem Beispiel werden ein zweites Replikat und eine zweite Partition hinzugefügt. Beachten Sie die Anzahl der Sucheinheiten. Sie beträgt jetzt vier, weil die Formel für die Abrechnung lautet: Replikate, multipliziert mit Partitionen (2 x 2). Bei einer Verdopplung der Kapazität fallen mehr als doppelt so hohe Kosten für die Ausführung des Diensts an. Wenn die Kosten für die Sucheinheit 100 US-Dollar wären, würde die neue Monatsrechnung jetzt 400 US-Dollar betragen.

   Wenn Sie die aktuellen Kosten pro Einheit für die einzelnen Tarife erfahren möchten, besuchen Sie die [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Hinzufügen von Replikaten und Partitionen" border="true":::

1. Nach dem Speichern können Sie Benachrichtigungen überprüfen, um zu bestätigen, dass die Aktion erfolgreich war.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Änderungen speichern" border="true":::

   Es kann zwischen 15 Minuten und mehreren Stunden dauern, bis Änderungen bei der Kapazität abgeschlossen sind. Sie können den Prozess nach seinem Start nicht abbrechen, und es gibt keine Echtzeitüberwachung für Replikat- und Partitionsanpassungen. Allerdings bleibt die folgende Meldung sichtbar, während Änderungen vorgenommen werden.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Statusmeldung im Portal" border="true":::

> [!NOTE]
> Sobald ein Dienst bereitgestellt ist, kann kein direktes Upgrade auf einen höheren Tarif erfolgen. In diesem Fall müssen Sie einen Suchdienst unter dem neuen Tarif erstellen und Ihre Indizes neu laden. Informationen zur Dienstbereitstellung finden Sie unter [Erstellen eines Diensts für die kognitive Azure-Suche im Portal](search-create-service-portal.md).

## <a name="how-scale-requests-are-handled"></a>Verarbeiten von Skalierungsanforderungen

Nach dem Erhalt einer Skalierungsanforderung wird vom Suchdienst Folgendes durchgeführt:

1. Er überprüft, ob die Anforderung gültig ist.
1. Er beginnt mit der Sicherung von Daten und Systeminformationen.
1. Er überprüft, ob sich der Dienst bereits in einem Bereitstellungsstatus befindet (aktive Vorgänge zum Hinzufügen oder Entfernen von Replikaten oder Partitionen).
1. Er startet den Bereitstellungsvorgang.

Die Skalierung eines Diensts kann je nach seiner Größe und dem Umfang der Anforderung nur 15 Minuten oder auch deutlich länger als eine Stunde dauern. Der Sicherungsvorgang kann je nach Datenmenge und Anzahl von Partitionen und Replikaten mehrere Minuten dauern.

Die obigen Schritte folgen nicht unbedingt direkt aufeinander. Das System beginnt beispielsweise erst mit der Bereitstellung, wenn dieser Vorgang auf sichere Weise möglich ist, und dies kann auch gegen Ende des Sicherungsvorgangs der Fall sein.

## <a name="errors-during-scaling"></a>Fehler während der Skalierung

Die Fehlermeldung der Art „Vorgänge zur Dienstaktualisierung sind derzeit nicht zulässig, weil eine vorherige Anforderung verarbeitet wird“ wird verursacht, wenn eine Anforderung zum Herunter- oder Hochskalieren wiederholt wird, während vom Dienst bereits eine vorherige Anforderung verarbeitet wird.

Beheben Sie diesen Fehler, indem Sie den Dienststatus überprüfen, um den Bereitstellungsstatus zu verifizieren:

1. Verwenden Sie die [Verwaltungs-REST-API](/rest/api/searchmanagement/2020-08-01/services), [Azure PowerShell](search-manage-powershell.md) oder die [Azure CLI](/cli/azure/search), um den Dienststatus abzurufen.
1. Rufen Sie [Get Service](/rest/api/searchmanagement/2020-08-01/services/get) auf.
1. Überprüfen Sie die Antwort auf ["provisioningState": "provisioning"](/rest/api/searchmanagement/2020-08-01/services/get#provisioningstate).

Wenn der Status „Provisioning“ (Bereitstellung wird durchgeführt) lautet, warten Sie, bis die Anforderung abgeschlossen ist. Der Status sollte entweder „Succeeded“ (Erfolgreich) oder „Failed“ (Fehler) lauten, bevor versucht wird, eine weitere Anforderung zu senden. Für die Sicherung wird kein Status angegeben. Die Sicherung ist ein interner Vorgang, und es ist unwahrscheinlich, dass dieser bei einer Störung einer Skalierungsübung eine Rolle spielt.

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partitions- und Replikatskombinationen

Ein Basic-Dienst kann genau eine Partition und bis zu drei Replikate besitzen. Die Obergrenze liegt bei drei SUs. Nur die Replikate können angepasst werden. Für Hochverfügbarkeit bei Abfragen sind mindestens zwei Replikate erforderlich.

Alle Suchdienste vom Typ „Standard“ oder „Datenspeicheroptimiert“ können die folgenden Kombinationen von Replikaten und Partitionen annehmen, vorbehaltlich dem für diese Ebenen zulässigen Limit von 36 SU. 

|   | **1 Partition** | **2 Partitionen** | **3 Partitionen** | **4 Partitionen** | **6 Partitionen** | **12 Partitionen** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 Replikat:** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 Replikate** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 Replikate** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 Replikate** |4 SU |8 SU |12 SU |16 SU |24 SU |– |
| **5 Replikate** |5 SU |10 SU |15 SU |20 SU |30 SU |– |
| **6 Replikate** |6 SU |12 SU |18 SU |24 SU |36 SU |– |
| **12 Replikate** |12 SU |24 SU |36 SU |– |– |– |

SUs, Preise und Kapazität werden auf der Azure-Website ausführlich erläutert. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Die Anzahl der Replikate und Partitionen ist ein ganzzahliger Teiler von 12 (d.h. 1, 2, 3, 4, 6, 12). Bei Azure Cognitive Search wird jeder Index vorab in zwölf Shards unterteilt, damit er gleichmäßig auf alle Partitionen verteilt werden kann. Wenn Ihr Dienst z.B. drei Partitionen aufweist und Sie einen Index erstellen, enthält jede Partition 4 Shards des Indexes. Die kognitive Azure-Suche erstellt Shards eines Index in Form von Implementierungsdetails, die sich bei zukünftigen Versionen ändern können. Auch wenn die Anzahl heute 12 beträgt, sollten Sie nicht davon ausgehen, das dies auch in Zukunft immer so ist.
>

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Kosten](search-sku-manage-costs.md)