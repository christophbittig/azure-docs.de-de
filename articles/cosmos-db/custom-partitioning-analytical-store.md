---
title: Benutzerdefinierte Partitionierung in Azure Synapse Link für Azure Cosmos DB (Vorschauversion)
description: Mit der benutzerdefinierten Partitionierung können Sie die Daten des Analysespeichers in Feldern partitionieren, die häufig als Filter in Analyseabfragen verwendet werden, um die Abfrageleistung zu verbessern.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: ignite-fall-2021
ms.openlocfilehash: 72bd4c8a2001cc8e6f314f12862d7ed563ac770b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095330"
---
# <a name="custom-partitioning-in-azure-synapse-link-for-azure-cosmos-db-preview"></a>Benutzerdefinierte Partitionierung in Azure Synapse Link für Azure Cosmos DB (Vorschauversion)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Mit der benutzerdefinierten Partitionierung können Sie die Daten des Analysespeichers in Feldern partitionieren, die häufig als Filter in Analyseabfragen verwendet werden, um die Abfrageleistung zu verbessern.

In diesem Artikel wird beschrieben, wie Sie Ihre Daten im Azure Cosmos DB-Analysespeicher mit Schlüsseln partitionieren, die für Ihre Analyseworkloads von entscheidender Bedeutung sind. Darüber hinaus wird beschrieben, wie Sie die verbesserte Abfrageleistung mit Partitionsbereinigung nutzen. Sie erfahren auch, wie der partitionierte Speicher zur Verbesserung der Abfrageleistung beiträgt, wenn für Ihre Workloads eine erhebliche Anzahl von Aktualisierungs- oder Löschvorgängen durchgeführt werden muss.

> [!IMPORTANT]
> Das Feature für die benutzerdefinierte Partitionierung befindet sich derzeit in der Public Preview. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Azure Synapse Link sollte für Azure Cosmos DB-Konten aktiviert sein, um die Vorteile der benutzerdefinierten Partitionierung nutzen zu können. Die benutzerdefinierte Partitionierung wird derzeit nur für Azure Synapse Spark 2.0 unterstützt.

## <a name="how-does-it-work"></a>Wie funktioniert dies?

Bei der benutzerdefinierten Partitionierung können Sie ein einzelnes Feld oder eine Felderkombination aus Ihrem Dataset als Partitionsschlüssel für den Analysespeicher auswählen.

Die Partitionierung des Analysespeichers ist unabhängig von der Partitionierung im Transaktionsspeicher. Der Analysespeicher ist standardmäßig nicht partitioniert. Wenn Sie den Analysespeicher häufig basierend auf Feldern wie Datum, Uhrzeit, Kategorie usw. abfragen möchten, empfehlen wir Ihnen, basierend auf diesen Schlüsseln einen partitionierten Speicher zu erstellen.

Um die Partitionierung auszulösen, können Sie in regelmäßigen Abständen einen Partitionierungsauftrag aus einem Azure Synapse Spark-Notebook ausführen, indem Sie Azure Synapse Link verwenden. Sie können die Ausführung so planen, dass sie zu einem geeigneten Zeitpunkt als Hintergrundauftrag erfolgt.

> [!NOTE]
> Der partitionierte Speicher verweist auf das primäre ADLS Gen2-Speicherkonto, das mit dem Azure Synapse-Arbeitsbereich verknüpft ist.

:::image type="content" source="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" alt-text="Architektur des partitionierten Speichers in Azure Synapse Link für Azure Cosmos DB" lightbox="./media/custom-partitioning-analytical-store/partitioned-store-architecture.png" border="false":::

Der partitionierte Speicher enthält die Azure Cosmos DB-Analysedaten bis zum letzten Zeitstempel der Ausführung Ihres Partitionierungsauftrags. Wenn Sie Ihre analytischen Daten mit den Partitionsschlüsselfiltern in Synapse Spark abfragen, führt Synapse Link die neuesten Daten aus dem Analysespeicher automatisch mit den Daten im partitionierten Speicher zusammen. So erhalten Sie die neuesten Ergebnisse. Die Daten werden vor dem Abfragen zwar zusammengeführt, aber die Deltadaten werden nicht in den partitionierten Speicher zurückgeschrieben. Wenn die Deltaabweichung zwischen Daten im Analysespeicher und im partitionierten Speicher größer wird, können die Abfragedauern für partitionierte Daten ggf. variieren. Diese Deltaabweichung wird verringert, wenn der Partitionierungsauftrag häufiger ausgelöst wird. Bei jeder Ausführung des Partitionierungsauftrags wird nicht das gesamte Dataset verarbeitet, sondern nur inkrementelle Änderungen im Analysespeicher.

## <a name="when-to-use"></a>Einsatzgebiete

Die Verwendung des partitionierten Speichers beim Abfragen von analytischen Daten in Azure Cosmos DB ist optional. Sie können dieselben Daten direkt abfragen, indem Sie Synapse Link mit dem vorhandenen Analysespeicher verwenden. Die Aktivierung des partitionierten Speichers kann ratsam sein, wenn bei Ihnen die folgenden Anforderungen gelten:

* Sie möchten häufig analytische Daten abfragen, für die eine Filterung nach einigen Feldern genutzt wird.

* Sie verfügen über eine große Anzahl von Aktualisierungs- bzw. Löschvorgängen, oder Daten werden nur langsam erfasst. In diesen Fällen kann mit dem partitionierten Speicher eine bessere Abfrageleistung erzielt werden. Dies gilt unabhängig davon, ob Sie für die Abfragen Partitionsschlüssel nutzen.

Mit Ausnahme der obigen Workloads gilt Folgendes: Wenn Sie Livedaten mit Abfragefiltern abfragen, die sich von den Partitionsschlüsseln unterscheiden, empfehlen wir Ihnen, diese Abfrage direkt aus dem Analysespeicher durchzuführen – vor allem, wenn die Partitionierungsaufträge nicht häufig ausgeführt werden.

## <a name="benefits"></a>Vorteile

### <a name="reduced-data-scanning-from-partition-pruning"></a>Reduzierung der überprüften Datenmenge durch Partitionsbereinigung

Da die Daten zu den einzelnen eindeutigen Partitionsschlüsseln gemeinsam im partitionierten Speicher angeordnet sind, können die zugrunde liegenden Daten bei Verwendung des Partitionsschlüssels als Abfragefilter bei den Abfrageausführungen bereinigt werden, damit nur die erforderlichen Daten überprüft werden. Dank der Partitionsbereinigung wird die Leistung von Analyseabfragen verbessert, weil nur ein Teil der Daten überprüft werden muss.

### <a name="flexibility-to-partition-your-analytical-data"></a>Flexible Partitionierung Ihrer analytischen Daten

Sie können mehrere Partitionierungsstrategien für einen bestimmten Analysespeichercontainer nutzen, und die Daten des Analysespeichers können mit separaten Partitionsschlüsseln partitioniert werden. Beispielsweise kann der Container „store_sales“ partitioniert werden, indem „sold_date“ als Schlüssel genutzt wird, aber es kann auch „item“ als Schlüssel für die Partitionierung verwendet werden. In diesem Fall müssen Sie über zwei separate Partitionierungsaufträge verfügen, mit denen die Daten im Prinzip auf zwei separate partitionierte Speicher aufgeteilt werden. Diese Partitionierungsstrategie ist vorteilhaft, wenn für einige Abfragen „sold_date“ und für andere Abfragen „item“ als Abfragefilter verwendet wird.

Die zu unterschiedlichen Partitionsschlüsseln gehörenden Daten sind dann Teil desselben partitionierten Speichers, und Sie können Abfragen basierend auf dem jeweiligen Partitionsschlüssel durchführen, um die entsprechenden Daten auszuwählen.

### <a name="query-performance-improvements"></a>Verbesserungen bei der Abfrageleistung

Zusätzlich zu den Abfrageverbesserungen durch die Partitionsbereinigung führt die benutzerdefinierte Partitionierung auch zu einer verbesserten Abfrageleistung für die folgenden Workloads:

* **Aktualisieren/Löschen von Workloads mit vielen Vorgängen:** Anstatt mehrere Versionen von Datensätzen im Analysespeicher nachzuverfolgen und bei jeder Ausführung der Abfrage zu laden, enthält der partitionierte Speicher nur die aktuelle Version der Daten. Hierdurch wird die Abfrageleistung erheblich verbessert, wenn Sie über Workloads mit vielen Aktualisierungs- bzw. Löschvorgängen verfügen.

* **Workloads mit langsamer Datenerfassung:** Bei der Partitionierung werden analytische Daten komprimiert. Wenn für Ihre Workload die Datenerfassung nur langsam erfolgt, kann diese Komprimierung also zu einer besseren Abfrageleistung führen.

### <a name="transactional-guarantee"></a>Transaktionsgarantie

Beachten Sie den wichtigen Hinweis, dass bei der benutzerdefinierten Partitionierung eine vollständige Transaktionsgarantie gilt. Der Abfragepfad wird nicht blockiert, während die Partitionierung ausgeführt wird. Bei jeder Ausführung der Abfrage werden die partitionierten Daten der letzten erfolgreichen Partitionierung gelesen. Hierbei werden die neuesten Daten aus dem Analysespeicher gelesen. So wird sichergestellt, dass bei Abfragen immer die neuesten verfügbaren Daten zurückgegeben werden, wenn der partitionierte Speicher verwendet wird.

## <a name="security"></a>Sicherheit

Wenn Sie [verwaltete private Endpunkte](analytical-store-private-endpoints.md) für Ihren Analysespeicher konfiguriert haben, empfehlen wir Ihnen Folgendes, um die Netzwerkisolation für den partitionierten Speicher sicherzustellen: Fügen Sie für den partitionierten Speicher zusätzlich verwaltete private Endpunkte hinzu. Der partitionierte Speicher ist das primäre Speicherkonto, das Ihrem Synapse-Arbeitsbereich zugeordnet ist.

Ebenso gilt Folgendes: Wenn Sie [kundenseitig verwaltete Schlüssel für den Analysespeicher](how-to-setup-cmk.md#is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store) konfiguriert haben, müssen Sie dies auch direkt im primären Speicherkonto (partitionierter Speicher) des Synapse-Arbeitsbereichs aktivieren.

## <a name="limitations"></a>Einschränkungen

* Die benutzerdefinierte Partitionierung ist nur für Azure Synapse Spark verfügbar. Für serverlose SQL-Pools wird die benutzerdefinierte Partitionierung derzeit nicht unterstützt.

* Für den partitionierten Speicher kann derzeit nur auf das primäre Speicherkonto verwiesen werden, das dem Synapse-Arbeitsbereich zugeordnet ist. Die Auswahl von benutzerdefinierten Speicherkonten wird noch nicht unterstützt.

* Die API für MongoDB unterstützt zwar Analysespeicher und Synapse Link, aber noch nicht die benutzerdefinierte Partitionierung.

## <a name="pricing"></a>Preise

Zusätzlich zu den Kosten, die gemäß den [Preisen für Azure Synapse Link](synapse-link.md#pricing) anfallen, werden Ihnen bei der Verwendung der benutzerdefinierten Partitionierung die folgenden Gebühren berechnet:

* Ihnen wird die Nutzung von Synapse Apache Spark-Pools [berechnet](https://azure.microsoft.com/pricing/details/synapse-analytics/#pricing), wenn Sie Partitionierungsaufträge im Analysespeicher ausführen.

* Die partitionierten Daten werden im primären Azure Data Lake Storage Gen2-Konto gespeichert, das Ihrem Azure Synapse Analytics-Arbeitsbereich zugeordnet ist. Ihnen werden die Kosten berechnet, die für die Nutzung des ADLS Gen2-Speichers und die Transaktionen anfallen. Diese Kosten richten sich nach dem Speicher, der für partitionierte analytische Daten benötigt wird, bzw. den verarbeiteten Daten für Analyseabfragen in Synapse. Weitere Informationen zu den Preisen finden Sie unter [Azure Data Lake Storage – Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-often-should-i-run-the-custom-partitioning-job"></a>Wie oft sollte ich den benutzerdefinierten Partitionierungsauftrag ausführen?

Es gibt mehrere Faktoren, z. B. die Menge inkrementeller Daten, die Anforderungen in Bezug auf die Abfragelatenz usw., die bestimmen, wie oft Sie den benutzerdefinierten Partitionierungsauftrag ausführen können. Es ist beispielsweise möglich, dass der Auftrag einmal täglich oder nach einigen Stunden erneut ausgeführt werden kann. Es kann ratsam sein, die Ausführung des Partitionierungsauftrags häufiger einzuplanen, wenn die Menge der eingehenden Daten hoch und die erwartete Abfragelatenz gering ist. Darüber hinaus müssen Sie zunächst inkrementelle Daten im Analysespeicher sammeln, damit sich die Partitionsbereinigung lohnt.

### <a name="do-the-query-results-include-latest-data-while-the-partitioning-job-execution-is-in-progress"></a>Enthalten die Abfrageergebnisse die neuesten Daten, während der Partitionierungsauftrag ausgeführt wird?

Ja. Für die benutzerdefinierte Partitionierung gilt eine vollständige Transaktionsgarantie. In den Abfrageergebnissen werden also jeweils die vorhandenen partitionierten Daten mit den Tail-Daten kombiniert, um das aktuelle Analysespeicher-Dataset zurückzugeben.

### <a name="can-the-custom-partitioning-make-use-of-linked-service-authentication-on-azure-synapse-analytics"></a>Kann für die benutzerdefinierte Partitionierung die Authentifizierung per verknüpftem Dienst in Azure Synapse Analytics genutzt werden?

Ja. Die Authentifizierung per verknüpftem Dienst kann für die Analysespeicherpartitionierung verwendet werden.

### <a name="can-i-change-the-partition-key-for-a-given-container-at-a-later-point-in-time"></a>Kann ich den Partitionsschlüssel für einen bestimmten Container zu einem späteren Zeitpunkt ändern?

Ja. Der Partitionsschlüssel für den jeweiligen Container kann geändert werden. Anhand der neuen Partitionsschlüsseldefinition wird dann ein neuer partitionierter Speicher erstellt.

> [!NOTE]
> Die Partitionsschlüsseldefinition ist Teil des Pfads für den partitionierten Speicher.

### <a name="can-different-partition-keys-point-to-the-same-basepath"></a>Können unterschiedliche Partitionsschlüssel auf denselben BasePath verweisen?

Ja. Da die Partitionsschlüsseldefinition Teil des Pfads des partitionierten Speichers ist, verfügen unterschiedliche Partitionsschlüssel über unterschiedliche Pfade, die von demselben BasePath abgeleitet sind.

Das BasePath-Format kann wie folgt angegeben werden: /mnt/partitionedstorename/\<Cosmos_DB_account_name\>/\<Cosmos_DB_database_rid\>/\<Cosmos_DB_container_rid\>/partition=partitionkey/

Beispiel: /mnt/CosmosDBPartitionedStore/store_sales/…/partition=sold_date/... /mnt/CosmosDBPartitionedStore/store_sales/…/partition=Date/...

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Konfigurieren der benutzerdefinierten Partitionierung](configure-custom-partitioning.md) zum Partitionieren der Analysespeicherdaten
* [Azure Synapse Link für Azure Cosmos DB](synapse-link.md)
* [Übersicht über den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md)
* [Erste Schritte mit Azure Synapse Link für Azure Cosmos DB](configure-synapse-link.md)
* [Häufig gestellte Fragen zu Azure Synapse Link für Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)
* [Anwendungsfälle für Azure Synapse Link für Azure Cosmos DB](synapse-link-use-cases.md)
