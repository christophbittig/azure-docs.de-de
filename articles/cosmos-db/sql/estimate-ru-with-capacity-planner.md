---
title: Geschätzte Kosten bei der Verwendung des Azure Cosmos DB-Capacity Planners - SQL API
description: Mit dem Azure Cosmos DB Capacity Planner können Sie den erforderlichen Durchsatz (RU/s) und die Kosten für Ihre Workload schätzen. Dieser Artikel beschreibt, wie Sie den Capacity Planner verwenden, um den erforderlichen Durchsatz und die Kosten bei der Verwendung der SQL-API zu schätzen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: dech
ms.openlocfilehash: 41059de2efd316c492933209f412e9b4512a0de9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115364"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner---sql-api"></a>Schätzen Sie die RU/s anhand des Azure Cosmos DB-Capacity Planners - SQL API
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!NOTE]
> Wenn Sie eine Datenmigration zu Azure Cosmos DB planen und sie nur die Anzahl von virtuellen Kernen und Servern in Ihrem vorhandenen Shard-Datenbankcluster und replizierten Datenbankcluster kennen, informieren Sie sich auch über das [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
>

Die Konfiguration Ihrer Azure Cosmos-Datenbanken und -Container mit der richtigen Menge an bereitgestelltem Durchsatz bzw. [Anforderungseinheiten (RU/s)](../request-units.md) für Ihre Workload ist sehr wichtig, um die Kosten und die Leistung zu optimieren. In diesem Artikel wird beschrieben, wie Sie den Azure Cosmos DB-[Capacity Planner](https://cosmos.azure.com/capacitycalculator/) verwenden, um eine Schätzung der erforderlichen RU/s und der Kosten für Ihre Workload bei Verwendung der SQL-API zu erhalten. Wenn Sie die API für MongoDB verwenden, erfahren Sie in dem [MongoDB-Artikel, wie Sie den Kapazitätsrechner](../mongodb/estimate-ru-capacity-planner.md) verwenden.

[!INCLUDE [capacity planner modes](../includes/capacity-planner-modes.md)]

## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a><a id="basic-mode"></a>Schätzen des bereitgestellten Durchsatzes und der Kosten im Modus „Basic“
Navigieren Sie zum [Capacity Planner](https://cosmos.azure.com/capacitycalculator/), um eine schnelle Schätzung für Ihre Workload im Modus „Basic“ zu erhalten. Geben Sie die folgenden Parameter basierend auf Ihrer Workload ein:

|**Input** (Eingabe)  |**Beschreibung**  |
|---------|---------|
| API |Wählen Sie die SQL-API (Core-API) aus. |
|Number of regions (Anzahl von Regionen)|Azure Cosmos DB ist in allen Azure-Regionen verfügbar. Wählen Sie die Anzahl von Regionen aus, die für Ihre Workload erforderlich ist. Sie können Ihrem Cosmos-Konto eine beliebige Anzahl von Regionen zuordnen. Ausführlichere Informationen finden Sie in Azure Cosmos DB unter [Globale Verteilung](../distribute-data-globally.md).|
|Schreibvorgänge in mehreren Regionen|Wenn Sie [Multi-region writes](../distribute-data-globally.md#key-benefits-of-global-distribution) (Schreibvorgänge in mehreren Regionen) aktivieren, kann Ihre Anwendung Lese- und Schreibvorgänge für alle Azure-Regionen durchführen. Bei einer Deaktivierung der Schreibvorgänge in mehreren Regionen kann Ihre Anwendung Daten in einer Region schreiben. <br/><br/> Aktivieren Sie Schreibvorgänge in mehreren Regionen, wenn Sie mit einer Aktiv-Aktiv-Workload rechnen, für die Schreibvorgänge mit geringer Latenz in mehreren Regionen erforderlich sind. Ein Beispiel hierfür ist eine IoT-Workload, bei der große Datenmengen in unterschiedlichen Regionen in die Datenbank geschrieben werden. <br/><br/> Für Schreibvorgänge in mehreren Regionen ist eine Lese- und Schreibverfügbarkeit von 99,999 % garantiert. Für Schreibvorgänge in mehreren Regionen ist im Vergleich mit dem Schreiben in einzelnen Regionen ein höherer Durchsatz erforderlich. Weitere Informationen finden Sie in dem Artikel, in dem beschrieben wird, [wie sich Anforderungseinheiten (RUs) für Schreibvorgänge in einzelne und mehrere Regionen unterscheiden](../optimize-cost-regions.md).|
|Gesamtanzahl der im Transaktionsspeicher gespeicherten Daten |Gesamtzahl der geschätzten gespeicherten Daten (GB) im Transaktionsspeicher in einer einzelnen Region.|
|Verwenden Sie den Analysespeicher| Wählen Sie **Ein**, wenn Sie den Analysespeicher verwenden möchten. Geben Sie die **Gesamtzahl der im analytischen Speicher gespeicherten Daten** ein, d. h. die geschätzten Daten (GB), die im analytischen Speicher in einer einzelnen Region gespeichert sind.  |
|Elementgröße|Die geschätzte Größe des Datenelements (z. B. des Dokuments), die von 1 KB bis 2 MB reicht. |
|Abfragen/s |Anzahl der erwarteten Abfragen pro Sekunde pro Region. Die durchschnittliche RU-Last zum Ausführen einer Abfrage wird auf 10 RUs geschätzt. |
|Punktlesungs-Vorgänge/Sek |Anzahl der erwarteten Punktlesungen pro Sekunde. Punktlesungen sind die Schlüssel/Wert-Suche auf einer einzelnen Element-ID und einem Partitionsschlüssel. Weitere Informationen zu Punktlese-Funktionen finden Sie im Artikel [Optionen zum Lesen von Daten.](../optimize-cost-reads-writes.md#reading-data-point-reads-and-queries) |
|Erstellungen/Sekunde |Anzahl der erwarteten Erstellungs-Vorgänge pro Sekunde. |
|Aktualisierungen/Sek |Anzahl der erwarteten Aktualisierungen pro Sekunde. Wenn Sie die automatische Indizierung auswählen, werden die geschätzten RU/s für den Aktualisierungsvorgang als eine Eigenschaft berechnet, die pro Aktualisierung geändert wird. |
|Löschvorgänge/Sek |Anzahl der erwarteten Löschvorgänge pro Sekunde, pro Region. |

Wählen Sie **Berechnen**, nachdem Sie die erforderlichen Angaben eingefügt haben. Auf der Registerkarte **Kostenschätzung** werden die Gesamtkosten für den Speicher und den bereitgestellten Durchsatz angezeigt. Sie können die Anzeige über den Link **Details anzeigen** auf dieser Registerkarte erweitern, um eine Aufstellung des Durchsatzes zu erhalten, der für verschiedene CRUD- und Abfrageanforderungen benötigt wird. Wählen Sie bei jeder Änderung des Werts eines Felds die Option **Berechnen**, um die geschätzten Kosten neu zu berechnen.

:::image type="content" source="../media/estimate-ru-with-capacity-planner/basic-mode-sql-api.png" alt-text="Capacity Planner: Modus „Basic“" border="true":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a><a id="advanced-mode"></a>Schätzen des bereitgestellten Durchsatzes und der Kosten im Modus „Advanced“

Im Modus „Advanced“ können Sie weitere Einstellungen nutzen, die sich auf die Schätzung des Durchsatzes (RU/s) auswirken. Navigieren Sie zum Verwenden dieser Option zum [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) und melden Sie sich im Tool mit einem Konto an, das Sie für Azure verwenden. Die Anmeldeoption befindet sich oben rechts.

Nachdem Sie sich angemeldet haben, sehen Sie mehr Felder im Vergleich zu den Feldern im Basismodus. Geben Sie die zusätzlichen Parameter basierend auf Ihrer Workload ein.

|**Input** (Eingabe)  |**Beschreibung**  |
|---------|---------|
|API|Azure Cosmos DB ist ein Dienst mit mehreren Modellen und APIs. Wählen Sie die SQL(Kern)-API. |
|Number of regions (Anzahl von Regionen)|Azure Cosmos DB ist in allen Azure-Regionen verfügbar. Wählen Sie die Anzahl von Regionen aus, die für Ihre Workload erforderlich ist. Sie können Ihrem Cosmos-Konto eine beliebige Anzahl von Regionen zuordnen. Ausführlichere Informationen finden Sie in Azure Cosmos DB unter [Globale Verteilung](../distribute-data-globally.md).|
|Schreibvorgänge in mehreren Regionen|Wenn Sie [Multi-region writes](../distribute-data-globally.md#key-benefits-of-global-distribution) (Schreibvorgänge in mehreren Regionen) aktivieren, kann Ihre Anwendung Lese- und Schreibvorgänge für alle Azure-Regionen durchführen. Bei einer Deaktivierung der Schreibvorgänge in mehreren Regionen kann Ihre Anwendung Daten in einer Region schreiben. <br/><br/> Aktivieren Sie Schreibvorgänge in mehreren Regionen, wenn Sie mit einer Aktiv-Aktiv-Workload rechnen, für die Schreibvorgänge mit geringer Latenz in mehreren Regionen erforderlich sind. Ein Beispiel hierfür ist eine IoT-Workload, bei der große Datenmengen in unterschiedlichen Regionen in die Datenbank geschrieben werden. <br/><br/> Für Schreibvorgänge in mehreren Regionen ist eine Lese- und Schreibverfügbarkeit von 99,999 % garantiert. Für Schreibvorgänge in mehreren Regionen ist im Vergleich mit dem Schreiben in einzelnen Regionen ein höherer Durchsatz erforderlich. Weitere Informationen finden Sie in dem Artikel, in dem beschrieben wird, [wie sich Anforderungseinheiten (RUs) für Schreibvorgänge in einzelne und mehrere Regionen unterscheiden](../optimize-cost-regions.md).|
|Standardkonsistenz|Azure Cosmos DB unterstützt fünf Konsistenzebenen, damit Entwickler eine gute Balance zwischen Konsistenz, Verfügbarkeit und Latenz erzielen können. Weitere Informationen finden Sie im Artikel zu [Konsistenzebenen](../consistency-levels.md). <br/><br/> Standardmäßig nutzt Azure Cosmos DB die Sitzungskonsistenz, um sicherzustellen, dass Sie in einer Sitzung Ihre eigenen Schreibvorgänge lesen können. <br/><br/> Bei der Auswahl der starken oder begrenzten Veraltung wird für Lesevorgänge der doppelte Durchsatz (RU/s) im Vergleich zu „Sitzungskonsistenz“, „Präfixkonsistenz“ und „Letztliche Konsistenz“ benötigt. Die starke Konsistenz mit Schreibvorgängen in mehreren Regionen wird nicht unterstützt, und es wird automatisch die Nutzung von Schreibvorgängen in einer einzelnen Region mit starker Konsistenz festgelegt. |
|Indizierungsrichtlinie|Standardmäßig wird von Azure Cosmos DB eine [Indizierung aller Eigenschaften](../index-policy.md) in allen Elementen durchgeführt, um für flexible und effiziente Abfragen zu sorgen (der Indizierungsrichtlinie **Automatisch** zugeordnet). <br/><br/> Wenn Sie **Aus** wählen, wird keine Eigenschaft indiziert. Dies führt zu den niedrigsten RU-Gebühren für Schreibvorgänge. Wählen Sie für die Richtlinie die Option **Aus**, falls Sie nur mit [Lesevorgängen für Datenpunkte](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) (Schlüsselwertsuchen) bzw. Schreibvorgängen und nicht mit Abfragen rechnen. <br/><br/> Wenn Sie den Modus **Automatisch** wählen, indiziert Azure CosmosDB automatisch alle Elemente, während sie geschrieben werden. <br/><br/> Mit der **benutzerdefinierten** Indizierungsrichtlinie können Sie bestimmte Eigenschaften in den Index aufnehmen oder davon ausschließen, um den Schreibdurchsatz und den Speicherbedarf zu verringern. Weitere Informationen finden Sie in den Artikeln zur [Indizierungsrichtlinie](../index-overview.md) und zu den [Beispielen für Indizierungsrichtlinien](how-to-manage-indexing-policy.md#indexing-policy-examples).|
|Gesamtanzahl der im Transaktionsspeicher gespeicherten Daten |Gesamtzahl der geschätzten gespeicherten Daten (GB) im Transaktionsspeicher in einer einzelnen Region.|
|Verwenden Sie den Analysespeicher| Wählen Sie **Ein**, wenn Sie den Analysespeicher verwenden möchten. Geben Sie die **Gesamtzahl der im analytischen Speicher** gespeicherten Daten ein, d. h. die geschätzten Daten (GB), die im analytischen Speicher in einer einzelnen Region gespeichert sind.  |
|Workloadmodus|Wählen Sie die **Konstant**-Option, wenn Ihr Workload-Volumen konstant ist. <br/><br/> Wählen Sie die **Variable**-Option, wenn sich Ihr Workload-Volumen im Laufe der Zeit ändert.  Beispielsweise an einem bestimmten Tag oder in einem Monat. Die folgende Einstellung ist verfügbar, wenn Sie die Variable-Workload-Option wählen:<ul><li>Percentage of time at peak (Spitzenzeiten in Prozent): Der Prozentsatz der Zeit eines Monats, in der für Ihre Workload der höchste Durchsatz (Spitzendurchsatz) erforderlich ist. </li></ul> <br/><br/> Wenn Sie beispielsweise über eine Workload verfügen, für die während der Geschäftszeiten an Werktagen zwischen 9 Uhr und 18 Uhr eine hohe Aktivität besteht, beträgt der Prozentsatz der Spitzenzeit: 45 Spitzenstunden/730 Stunden/Monat = ~6 %.<br/><br/>Mit Intervallen für Spitzenzeiten und andere Zeiten können Sie Ihre Kosten optimieren, indem Sie [Ihren bereitgestellten Durchsatz entsprechend programmgesteuert zentral hoch- und herunterskalieren](../set-throughput.md#update-throughput-on-a-database-or-a-container).|
|Elementgröße|Die Größe des Datenelements (z. B. Dokument), die von 1 KB bis 2 MB reicht. Sie können Schätzungen für mehrere Musterelemente hinzufügen. <br/><br/>Sie können auch die Option zum **Hochladen eines Beispieldokuments (JSON)** verwenden, um eine genauere Schätzung zu erhalten.<br/><br/>Wenn Ihre Workload über mehrere Arten von Elementen (mit unterschiedlichem JSON-Inhalt) in demselben Container verfügt, können Sie mehrere JSON-Dokumente hochladen und eine entsprechende Schätzung erhalten. Verwenden Sie die Schaltfläche **Add new item** (Neues Element hinzufügen), um mehrere JSON-Beispieldokumente hinzuzufügen.|
| Anzahl von Eigenschaften | Die durchschnittliche Anzahl von Eigenschaften pro Element. |
|Punktlesungs-Vorgänge/Sek |Anzahl der erwarteten Punktlesungen pro Sekunde. Punktlesungen sind die Schlüssel/Wert-Suche auf einer einzelnen Element-ID und einem Partitionsschlüssel. Punktlesevorgänge unterscheiden sich von Abfragelesevorgängen. Weitere Informationen zu Punktlese-Funktionen finden Sie im Artikel [Optionen zum Lesen von Daten.](../optimize-cost-reads-writes.md#reading-data-point-reads-and-queries) Wenn Ihr Workload-Modus **Variabel** ist, können Sie die erwartete Anzahl von Punktlesevorgängen zu Spitzen- und Nebenzeiten bereitstellen. |
|Erstellungen/Sekunde |Anzahl der erwarteten Erstellungs-Vorgänge pro Sekunde. |
|Aktualisierungen/Sek |Anzahl der erwarteten Aktualisierungen pro Sekunde. |
|Löschvorgänge/Sek |Anzahl der erwarteten Löschvorgänge pro Sekunde, pro Region. |
|Abfragen/s |Anzahl der erwarteten Abfragen pro Sekunde pro Region. Für eine genaue Schätzung verwenden Sie entweder die durchschnittlichen Kosten der Abfragen oder geben Sie die RU/s ein, die Ihre Abfragen aus den Abfragestatistiken im Azure-Portal verwenden. |
| Durchschnittliche RU/s-Gebühr pro Abfrage | Standardmäßig werden die durchschnittlichen Kosten für Abfragen/Sek. pro Region auf 10 RU/s geschätzt. Sie können sie auf der Grundlage der Kosten für RU/s gemäß Ihrer geschätzten Abfragegebühr erhöhen oder verringern.|

Sie können auch die Schaltfläche **Save Estimate** (Schätzung speichern) verwenden, um eine CSV-Datei mit der aktuellen Schätzung herunterzuladen.

:::image type="content" source="../media/estimate-ru-with-capacity-planner/advanced-mode-sql-api.png" alt-text="Capacity Planner: Modus „Advanced“" border="true":::

Die im Azure Cosmos DB Capacity Planner angezeigten Preise sind Schätzungen, die auf den öffentlichen Preisen für Durchsatz und Speicher basieren. Alle Preise sind in US-Dollar angegeben. Auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/) sind alle Preise nach Region aufgeführt.  

## <a name="next-steps"></a>Nächste Schritte

* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Shard-Datenbankcluster und replizierten Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
* Informieren Sie sich weiter über das [Preismodell von Azure Cosmos DB](../how-pricing-works.md).
* Erstellen Sie ein neues [Cosmos-Konto, eine Datenbank und einen Container](create-cosmosdb-resources-portal.md).
* Erfahren Sie, wie Sie die [Kosten für den bereitgestellten Durchsatz optimieren](../optimize-cost-throughput.md) können.
* Informieren Sie sich über das [Optimieren der Kosten mit Reservekapazität](../cosmos-db-reserved-capacity.md).

