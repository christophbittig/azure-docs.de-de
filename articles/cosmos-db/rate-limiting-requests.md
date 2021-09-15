---
title: Optimieren Ihrer Azure Cosmos DB-Anwendung mit Ratenbegrenzung
description: In diesem Artikel wird eine Methodik zur Ratenbegrenzung von Anforderungen an Azure Cosmos DB für Entwickler beschrieben. Die Implementierung dieses Musters kann Fehler reduzieren und die Gesamtleistung für Workloads verbessern, die den bereitgestellten Durchsatz der Zieldatenbank oder des Zielcontainers überschreiten.
author: plasne
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/26/2021
ms.author: pelasne
ms.openlocfilehash: 0677a3f4180ab429dc51a4e40c969e32e6e8f655
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098138"
---
# <a name="optimize-your-azure-cosmos-db-application-using-rate-limiting"></a>Optimieren Ihrer Azure Cosmos DB-Anwendung mit Ratenbegrenzung
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

In diesem Artikel wird eine Methodik zur Ratenbegrenzung von Anforderungen an Azure Cosmos DB für Entwickler beschrieben. Die Implementierung dieses Musters kann Fehler reduzieren und die Gesamtleistung für Workloads verbessern, die den bereitgestellten Durchsatz der Zieldatenbank oder des Zielcontainers überschreiten.

Anforderungen, die den bereitgestellten Durchsatz in Azure Cosmos DB überschreiten, können zu vorübergehenden Fehlern wie [TooManyRequests](troubleshoot-request-rate-too-large.md), [Timeout](troubleshoot-request-timeout.md) und [ServiceUnavailable](troubleshoot-service-unavailable.md) führen. In der Regel werden diese Anforderungen erfolgreich wiederholt, wenn Kapazität verfügbar ist. Diese Vorgehensweise kann jedoch zu einer großen Anzahl von Anforderungen führen, die dem Fehlerpfad im Code folgen, was in der Regel zu einem geringeren Durchsatz führt.

Eine optimale Systemleistung, gemessen an Kosten und Zeit, kann erreicht werden, indem der clientseitige Workloaddatenverkehr mit dem serverseitigen bereitgestellten Durchsatz abgeglichen wird.

Als Beispiel dient das folgende Szenario:

* Sie stellen Azure Cosmos DB mit 20.000 RU/Sekunde bereit.
* Die Anwendung verarbeitet einen Erfassungsauftrag, der 10.000 Datensätze enthält, die jeweils 10 RU kosten. Die erforderliche Gesamtkapazität für diesen Auftrag beträgt 100.000 RU.
* Wenn Sie den gesamten Auftrag an Azure Cosmos DB senden, sollten Sie eine große Anzahl vorübergehender Fehler und einen großen Puffer mit Anforderungen erwarten, die wiederholt werden müssen. Dieser Zustand ist darauf zurückzuführen, dass die Gesamtanzahl der für den Auftrag benötigten RUs (100.000) wesentlich größer als das bereitgestellte Maximum (20.000) ist. Ungefähr 2.000 Datensätze werden in der Datenbank akzeptiert, aber ca. 8.000 werden abgelehnt. Bei einer Wiederholung werden ca. 8.000 Datensätze an Azure Cosmos DB gesendet, von denen ca. 2.000 akzeptiert werden usw. Sie müssen davon ausgehen, dass bei diesem Muster ca. 30.000 Datensätze anstelle von 10.000 Datensätzen gesendet werden.
* Wenn Sie die betreffenden Anforderungen stattdessen gleichmäßig über 5 Sekunden verteilt senden, sollten Sie keine Fehler und insgesamt einen schnelleren Durchsatz erwarten, da jeder Batch bei oder unter dem bereitgestellten Wert von 20.000 liegt.

Die Verteilung der Anforderungen auf einen größeren Zeitraum kann durch die Einfügen eines Ratenbegrenzungsmechanismus im Code erreicht werden.

Die für einen Container bereitgestellten RUs werden gleichmäßig auf die Anzahl der physischen Partitionen verteilt. Wenn Azure Cosmos DB im obigen Beispiel zwei physische Partitionen bereitgestellt hätte, würde jede 10.000 RU umfassen.

Weitere Informationen zu Anforderungseinheiten finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](request-units.md).
Weitere Informationen zum Schätzen der Anzahl von RUs, die von Ihrer Workload verbraucht werden, finden Sie in den [Überlegungen zu Anforderungseinheiten](request-units.md#request-unit-considerations).
Weitere Informationen zum Partitionieren von Azure Cosmos DB finden Sie unter [Partitionierung und horizontale Skalierung in Azure Cosmos DB](partitioning-overview.md).

## <a name="methodology"></a>Methodik

Eine Methode zum Implementieren der Ratenbegrenzung könnte wie folgt aussehen:

1. Erstellen Sie ein Anwendungsprofil, damit Sie über Daten zu den verwendeten Schreib- und Leseanforderungen verfügen.
1. Definieren Sie alle Indizes.
1. Füllen Sie die Sammlung mit einer angemessenen Menge von Daten (z. B. Beispieldaten). Wenn zu erwarten ist, dass die Anwendung normalerweise Millionen von Datensätzen umfasst, müssen Sie Millionen von Datensätzen vorsehen.
1. Erstellen Sie maßgebliche Dokumente, und notieren Sie die RU-Kosten.
1. Führen Sie maßgebliche Abfragen aus, und notieren Sie die RU-Kosten.
1. Implementieren Sie basierend auf Ihren Ergebnissen eine Funktion in der Anwendung, um die Kosten einer bestimmten Anforderung zu ermitteln.
1. Implementieren Sie einen Mechanismus zur Ratenbegrenzung in Ihrem Code, um sicherzustellen, dass die Summe aller Vorgänge, die in einer Sekunde an Azure Cosmos DB gesendet werden, den bereitgestellten Durchsatz nicht überschreitet.
1. Führen Sie einen Auslastungstest für die Anwendung durch, und vergewissern Sie sich, dass der bereitgestellte Durchsatz nicht überschritten wird.
1. Testen Sie die RU-Kosten regelmäßig erneut, und aktualisieren Sie die Kostenfunktion je nach Bedarf.

## <a name="indexing"></a>Indizierung

Im Gegensatz zu anderen SQL- und NoSQL-Datenbanken, die Sie möglicherweise kennen, indiziert die Standardindizierungsrichtlinie von Azure Cosmos DB für neu erstellte Container **jede** Eigenschaft. Jede indizierte Eigenschaft erhöht die RU-Kosten für Schreibvorgänge.

Die Standardindizierungsrichtlinie kann die Latenz in Systemen mit hoher Leselast senken, in denen die Abfragefilterbedingungen gleichmäßig auf alle gespeicherten Felder verteilt sind. Davon können beispielsweise Systeme profitieren, bei denen Azure Cosmos DB die meiste Zeit für die Bereitstellung von Ad-hoc-Suchvorgängen aufwendet, die von Endbenutzern erstellt werden.

Sie sollten nach Möglichkeit Eigenschaften, die nie durchsucht werden, aus der Indizierung ausschließen. Das Entfernen von Eigenschaften aus dem Index kann die Gesamtleistung des Systems (Kosten und Zeit) für Systeme mit hohem Schreibaufwand und Datensatzabrufmuster mit stärkeren Einschränkungen verbessern.

Vor der Messung von Kosten sollten Sie extra eine geeignete Indexrichtlinie für Ihre Anwendungsfälle konfigurieren. Wenn Sie Indizes später ändern, müssen Sie auch alle Kostenberechnungen erneut ausführen. 

Testen Sie ein System in der Entwicklung nach Möglichkeit mit einer Last mit typischen Abfragen bei Normal- und Spitzenbedarf, um festzustellen, welche Indizierungsrichtlinie verwendet werden soll.

Weitere Informationen zur Indizes finden Sie in den [Indizierungsrichtlinien für Azure Cosmos DB](index-policy.md).

## <a name="measuring-cost"></a>Messen der Kosten

Beim Messen der Kosten gibt es einige wichtige Konzepte:

* Berücksichtigen Sie alle Faktoren, die sich auf die RU-Nutzung auswirken (siehe Beschreibung in den [Überlegungen zu Anforderungseinheiten](request-units.md#request-unit-considerations)).
* Alle Lese- und Schreibvorgänge in Ihrer Datenbank oder Ihrem Container teilen sich denselben bereitgestellten Durchsatz.
* Die RU-Nutzung erfolgt unabhängig von den verwendeten Azure Cosmos DB-APIs.
* Die Partitionsstrategie für eine Sammlung kann erhebliche Auswirkungen auf die Kosten eines Systems haben. Weitere Informationen finden Sie unter [Partitionieren und horizontales Skalieren in Azure Cosmos DB](partitioning-overview.md#choose-partitionkey).
* Verwenden Sie maßgebliche Dokumente und maßgebliche Abfragen.
  * Dies sind Dokumente und Abfragen, die Ihrer Meinung nach im operativen System auftreten werden.
  * Die beste Möglichkeit, diese maßgeblichen Dokumente und Abfragen zu erhalten, besteht darin, die Verwendung der Anwendung zu instrumentieren. Es ist immer besser, eine datengesteuerte Entscheidung zu treffen.
* Messen Sie die Kosten in regelmäßigen Abständen.
  * Indexänderungen, die Größe der Indizes kann sich auf die Kosten auswirken. 
  * Es ist hilfreich, einen wiederholbaren (vielleicht sogar automatisierten) Test für die maßgeblichen Dokumente und Abfragen zu erstellen.
  * Achten Sie darauf, dass die maßgeblichen Dokumente und Abfragen noch maßgeblich sind.

Die Methode zum Ermitteln der Kosten einer Anforderung ist für jede API verschieden:

* [Core-API](find-request-unit-charge.md)
* [Cassandra-API](cassandra/find-request-unit-charge-cassandra.md)
* [Gremlin-API](find-request-unit-charge-gremlin.md)
* [Mongo DB-API](mongodb/find-request-unit-charge-mongodb.md)
* [Tabellen-API](table/find-request-unit-charge.md)

## <a name="write-requests"></a>Schreibanforderungen

Die Kosten von Schreibvorgängen sind in der Regel leicht vorherzusagen. Sie fügen Datensätze ein und dokumentieren die von Azure Cosmos gemeldeten Kosten.

Wenn Sie über Dokumente unterschiedlicher Größe und/oder Dokumente verfügen, die unterschiedliche Indizes verwenden, ist es wichtig, Messungen für alle Dokumente durchzuführen.
Sie werden möglicherweise feststellen, dass die Kosten der maßgeblichen Dokumente nah beieinander liegen, sodass Sie einen einzelnen Wert für alle Schreibvorgänge zuweisen können.
Wenn Sie beispielsweise Kosten von 13,14 RU, 16,01 RU und 12,63 RU ermittelt haben, können Sie für die Kosten einen Mittelwert von 14 RU verwenden.

## <a name="read-requests"></a>Leseanforderungen

Die Vorhersage der Kosten von Abfragevorgängen kann aus den folgenden Gründen viel schwieriger sein:

* Wenn das System benutzerdefinierte Abfragen unterstützt, müssen Sie die eingehenden Abfragen den maßgeblichen Abfragen zuordnen, um die Kosten zu ermitteln. Dieser Prozess kann verschiedene Formen annehmen:
  * Unter Umständen ist es möglich, die Abfragen exakt zuzuordnen. Wenn es keine direkte Übereinstimmung gibt, müssen Sie möglicherweise die maßgebliche Abfrage finden, die am nächsten liegt.
  * Möglicherweise können Sie Kosten basierend auf den Merkmalen der Abfrage berechnen. Sie stellen z. B. möglicherweise fest, dass jede Klausel der Abfrage bestimmte Kosten verursacht oder dass eine indizierte Eigenschaft „x“ kostet, während eine nicht indizierte Eigenschaft „y“ kostet usw.
* Die Anzahl der Ergebnisse kann variieren, und solange Sie keine Statistiken haben, können Sie die RU-Auswirkungen der zurückgegebenen Nutzlast nicht vorhersagen.

Wahrscheinlich können Sie keine Einzelkosten für Abfragevorgänge verwenden, sondern eine Funktion, die die Abfrage auswertet und Kosten berechnet.
Wenn Sie die Core-API verwenden, können Sie die tatsächlichen Kosten des Vorgangs auswerten und ermitteln, wie genau Ihre Schätzung war (die Optimierung dieser Schätzung kann sogar automatisch im Code erfolgen).

## <a name="transient-fault-handling"></a>Behandeln vorübergehender Fehler

Die Anwendung benötigt aus den folgenden Gründen weiterhin eine Behandlung vorübergehender Fehler – auch wenn Sie einen Mechanismus zur Ratenbegrenzung implementieren:

* Die tatsächlichen Kosten einer Anforderung können von den projizierten Kosten abweichen.
* Vorübergehende Fehler können aus anderen Gründen als TooManyRequests auftreten.

Durch die geeignete Implementierung eines Mechanismus zur Ratenbegrenzung in der Anwendung wird die Anzahl vorübergehender Fehler jedoch erheblich reduziert.

## <a name="alternate-and-related-techniques"></a>Alternative und verwandte Techniken

In diesem Artikel werden zwar die clientseitige Koordination und Batchverarbeitung von Workloads beschrieben, es gibt jedoch auch andere Verfahren, mit denen der Gesamtdurchsatz des Systems verwaltet werden kann.

### <a name="autoscaling"></a>Automatische Skalierung

Bei per Autoskalierung bereitgestelltem Durchsatz ermöglicht Azure Cosmos DB das Skalieren des Durchsatzes (RU/s) Ihrer Datenbank oder Ihres Containers automatisch und sofort. Der Durchsatz wird basierend auf der Nutzung skaliert, ohne dass dadurch Verfügbarkeit, Latenz, Durchsatz oder Leistung der Workload beeinträchtigt werden.

Per Autoskalierung bereitgestellter Durchsatz eignet sich gut für unternehmenskritische Workloads, die über variable oder unvorhersehbare Datenverkehrsmuster verfügen und SLAs für hohe Leistung und Skalierung erfordern.

Weitere Informationen zur automatischen Skalierung finden Sie unter [Erstellen von Azure Cosmos-Containern und -Datenbanken mit automatisch skaliertem Durchsatz](provision-throughput-autoscale.md).

### <a name="queue-based-load-leveling-pattern"></a>Warteschlangenbasiertes Lastenausgleichsmuster

Sie können eine Warteschlange implementieren, die als Puffer zwischen einem Client und Azure Cosmos DB fungiert, um unregelmäßig auftretende hohe Lasten aufzufangen, die den Ausfall des Diensts oder eine Zeitüberschreitung der Aufgabe verursachen können.

Dieses Muster ist für jede Anwendung hilfreich, die Dienste verwendet, die Überlastung ausgesetzt sind. Dieses Muster ist jedoch nicht hilfreich, wenn die Anwendung eine Antwort vom Dienst mit minimaler Wartezeit erwartet.

Dieses Muster eignet sich oftmals gut für Erfassungsvorgänge.

Weitere Informationen zu diesem Muster finden Sie unter [Warteschlangenbasiertes Lastenausgleichsmuster](/azure/architecture/patterns/queue-based-load-leveling).

### <a name="cache-aside-pattern"></a>Cachefremdes Muster

Sie können erwägen, Daten bei Bedarf in einen Cache zu laden, anstatt jedes Mal Azure Cosmos DB abzufragen. Die Verwendung eines Caches kann die Leistung verbessern und trägt zudem dazu bei, die Konsistenz zwischen den Daten im Cache und den Daten im zugrunde liegenden Datenspeicher aufrechtzuerhalten.

Weitere Informationen finden Sie unter [Cachefremdes Muster](/azure/architecture/patterns/cache-aside).

### <a name="materialized-view-pattern"></a>Muster für materialisierte Sichten

Sie können Sichten nach dem Speichern der Daten in Azure Cosmos DB vorab in andere Sammlungen einpflegen, wenn die Daten für erforderliche Abfragevorgänge nicht ideal formatiert sind. Dieses Muster kann für die Unterstützung effizienter Abfragen und Datenextraktionen hilfreich sein und die Leistung verbessern.

Weitere Informationen finden Sie im Abschnitt zur [Muster für materialisierte Sichten](/azure/architecture/patterns/materialized-view).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [TooManyRequests-Fehler](troubleshoot-request-rate-too-large.md) in Azure Cosmos DB beheben.
* Erfahren Sie, wie Sie [Timeout-Fehler](troubleshoot-request-timeout.md) in Azure Cosmos DB beheben.
* Erfahren Sie, wie Sie [ServiceUnavailable-Fehler](troubleshoot-service-unavailable.md) in Azure Cosmos DB beheben.
* Erfahren Sie mehr zu [Anforderungseinheiten](request-units.md) in Azure Cosmos DB.
* Erfahren Sie mehr über [Partitionierung und horizontale Skalierung](partitioning-overview.md) in Azure Cosmos DB.
* Erfahren Sie mehr über [Indizierungsrichtlinien](index-policy.md) in Azure Cosmos DB.
* Erfahren Sie mehr zur [automatischen Skalierung](provision-throughput-autoscale.md) in Azure Cosmos DB.
* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)
