---
title: Azure Synapse Link für Azure Cosmos DB, Vorteile, Anwendungsmöglichkeiten
description: Erfahren Sie mehr über Azure Synapse Link für Azure Cosmos DB. Synapse Link ermöglicht Ihnen das Ausführen von Analysen in Quasi-Echtzeit (HTAP) mithilfe von Azure Synapse Analytics über operative Daten in Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/12/2021
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 459aedbda8ea42fb0ee0990fb1074373efc38acc
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133938"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Was ist Azure Synapse Link für Azure Cosmos DB?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link für Azure Cosmos DB ist eine cloudnative hybride Verarbeitungsfunktion für Transaktionen und Analysen (Hybrid Transactional and Analytical Processing, HTAP), mit der Sie in Azure Cosmos DB in Quasi-Echtzeit Analysen für operative Daten ausführen können. Azure Synapse Link sorgt für eine nahtlose Integration zwischen Azure Cosmos DB und Azure Synapse Analytics.

Mithilfe des [Azure Cosmos DB-Analysespeichers](analytical-store-introduction.md), einem vollständig isolierten Columnstore, ermöglicht Azure Synapse Link keine maßstabgerechten ETL-Analysen (Extract-Transform-Load) in [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) für Ihre operativen Daten. Business Analysts, Data Engineers und Data Scientists können Synapse Spark oder Synapse SQL jetzt austauschbar verwenden, um Business Intelligence-, Analyse- und Machine Learning-Pipelines in Quasi-Echtzeit auszuführen. Dies können Sie erreichen, ohne dass es sich auf die Leistung der Transaktionsworkloads in Azure Cosmos DB auswirkt.

Die folgende Abbildung zeigt die Integration von Azure Synapse Link in Azure Cosmos DB und Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Architekturdiagramm für die Integration von Azure Synapse Analytics in Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Vorteile

Um umfangreiche operative Datasets zu analysieren und gleichzeitig die Auswirkungen auf die Leistung unternehmenskritischer Transaktionsworkloads zu minimieren, werden die operativen Daten in Azure Cosmos DB von ETL-Pipelines (Extrahieren, Transformieren, Laden) extrahiert und verarbeitet. ETL-Pipelines erfordern viele Datenverschiebungsebenen, die zu einer starken betrieblichen Komplexität und zu Leistungseinbußen bei ihren Transaktionsworkloads führt. Außerdem nimmt die Wartezeit von der Ursprungszeit zum Analysieren der operativen Daten zu.

Im Vergleich zu herkömmlichen ETL-basierten Lösungen bietet Azure Synapse Link für Azure Cosmos DB mehrere Vorteile, wie z. B.:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Verringerte Komplexität ohne zu verwaltende ETL-Aufträge

Azure Synapse Link ermöglicht Ihnen den direkten Zugriff auf den Azure Cosmos DB-Analysespeicher mithilfe von Azure Synapse Analytics ohne komplexe Datenverschiebungen. Alle Aktualisierungen der operativen Daten werden im Analysespeicher in Quasi-Echtzeit ohne ETL- oder Änderungsfeedaufträge angezeigt. Sie können umfangreiche Analysen für den Analysespeicher von Azure Synapse Analytics ohne eine zusätzliche Datentransformation ausführen.

### <a name="near-real-time-insights-into-your-operational-data"></a>Einblicke in Ihre operativen Daten in Quasi-Echtzeit

Sie können mit Azure Synapse Link jetzt umfassende Einblicke in Ihre operativen Daten in Quasi-Echtzeit erhalten. ETL-basierte Systeme verfügen tendenziell über längere Wartezeiten bei der Analyse der operativen Daten, da zum Extrahieren, Transformieren und Laden dieser Daten viele Ebenen erforderlich sind. Mit nativer Integration des Azure Cosmos DB-Analysespeichers in Azure Synapse Analytics können Sie operative Daten in Quasi-Echtzeit analysieren, um neue Geschäftsszenarien zu ermöglichen. 

### <a name="no-impact-on-operational-workloads"></a>Keine Auswirkungen auf operative Workloads

Mit Azure Synapse Link können Sie analytische Abfragen für einen Azure Cosmos DB-Analysespeicher (einen separaten Columnstore) ausführen, während die Transaktionsvorgänge mithilfe des bereitgestellten Durchsatzes für die Transaktionsworkload (ein zeilenbasierter Transaktionsspeicher) verarbeitet werden.  Die Analyseworkload wird unabhängig vom Transaktionsworkload-Datenverkehr bereitgestellt, ohne dass der für Ihre operativen Daten bereitgestellte Durchsatz beansprucht wird.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Optimiert für umfangreiche Analyseworkloads

Der Azure Cosmos DB-Analysespeicher ist optimiert, um Skalierbarkeit, Elastizität und Leistung für Analyseworkloads ohne jegliche Abhängigkeit von den Computelaufzeiten bereitzustellen. Die Speichertechnologie ist für die Optimierung Ihrer Analyseworkloads selbstverwaltet. Dank in Azure Synapse Analytics integrierter Unterstützung bietet der Zugriff auf diese Speicherebene Einfachheit und hohe Leistung.

### <a name="cost-effective"></a>Kosteneffizienz

Mit Azure Synapse Link erhalten Sie eine kostenoptimierte, vollständig verwaltete Lösung für die operative Analyse. Die zusätzlichen Speicherebenen und die Computekapazität entfallen, die bei herkömmlichen ETL-Pipelines zum Analysieren operativer Daten erforderlich sind. 

Der Azure Cosmos DB-Analysespeicher verfügt über ein nutzungsbasiertes Preismodell basierend auf Datenspeicher, analytischen Lese-/Schreibvorgängen und ausgeführten Abfragen. Es ist nicht erforderlich, dass Sie einen Durchsatz bereitstellen, wie es heute bei Transaktionsworkloads erforderlich ist. Durch den Zugriff auf Ihre Daten mit äußerst elastischen Computemodulen aus Azure Synapse Analytics werden die Gesamtkosten für die Ausführung von Speicher und Compute sehr günstig.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Analysen für lokal verfügbare, global verteilte Schreibvorgänge in mehreren Regionen

Sie können analytische Abfragen effektiv für die nächstgelegene regionale Kopie der Daten in Azure Cosmos DB ausführen. Azure Cosmos DB bietet eine fortschrittliche Aktiv-/Aktiv-Funktion zum Ausführen der global verteilten Analyseworkloads zusammen mit Transaktionsworkloads.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>Ermöglichen von HTAP-Szenarien für Ihre operativen Daten

Synapse Link verbindet den Azure Cosmos DB-Analysespeicher mit Laufzeitunterstützung für die Azure Synapse-Analyse. Diese Integration ermöglicht Ihnen das Erstellen cloudnativer HTAP-Lösungen (Hybrid Transactional/Analytical Processing, hybride transaktionale/analytische Verarbeitung), die Erkenntnisse basierend auf Echtzeitaktualisierungen Ihrer operativen Daten über große Datasets generieren. Dies ermöglicht neue Geschäftsszenarien, um Warnungen auf Grundlage von Livetrends auszugeben, Dashboards in Quasi-Echtzeit zu erstellen und geschäftliche Erfahrungen ausgehend vom Benutzerverhalten zu erhalten.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB-Analysespeicher

Der Azure Cosmos DB-Analysespeicher ist eine spaltenorientierte Darstellung Ihrer operativen Daten in Azure Cosmos DB. Dieser Analysespeicher eignet sich für schnelle, kostengünstige Abfragen für große operative Datasets, ohne dass dabei Daten kopiert werden und ohne Auswirkungen auf die Leistung der Transaktionsworkloads.

Der Analysespeicher ruft automatisch Einfüge-, Aktualisierungs- und Löschvorgänge in Ihren Transaktionsworkloads in Quasi-Echtzeit als vollständig verwaltete Funktion (automatische Synchronisierung) von Azure Cosmos DB auf. Es ist kein Änderungsfeed oder ETL erforderlich. 

Wenn Sie über ein global verteiltes Azure Cosmos DB-Konto verfügen, ist es nach dem Aktivieren des Analysespeichers für einen Container in allen Regionen für dieses Konto verfügbar. Weitere Informationen zum Analysespeicher finden Sie in der [Übersicht über den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md).

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integration in Azure Synapse Analytics

Mit Synapse Link können Sie jetzt ausgehend von Azure Synapse Analytics direkt eine Verbindung mit Ihren Azure Cosmos DB-Containern herstellen und ohne separate Connectors auf den Analysespeicher zugreifen. Azure Synapse Analytics unterstützt derzeit Synapse Link mit [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) und [serverlosen SQL-Pools](../synapse-analytics/sql/on-demand-workspace-overview.md).

Sie können die Daten aus dem Azure Cosmos DB-Analysespeicher gleichzeitig abfragen, wobei Interoperabilität für verschiedene von Azure Synapse Analytics unterstützte Analyselaufzeiten unterstützt wird. Zum Analysieren der operativen Daten sind keine weiteren Datentransformationen erforderlich. Sie können die Analysespeicherdaten mithilfe von Folgendem abfragen und analysieren:

* Synapse Apache Spark mit vollständiger Unterstützung für Scala, Python, SparkSQL und C#. Synapse Spark ist von zentraler Bedeutung für Datentechnik- und Data Science-Szenarien.

* Serverlose SQL-Pools mit T-SQL und Unterstützung für bekannte BI-Tools (z. B. Power BI Premium usw.)

> [!NOTE]
> Aus Azure Synapse Analytics können Sie auf Analyse- und Transaktionsspeicher in Ihrem Azure Cosmos DB-Container zugreifen. Wenn Sie jedoch umfangreiche Analysen oder Scans für Ihre operativen Daten ausführen möchten, empfiehlt es sich, den Analysespeicher zu verwenden, um Leistungseinbußen bei Transaktionsworkloads zu vermeiden.

> [!NOTE]
> Sie können Analysen mit geringer Latenz in einer Azure-Region ausführen, indem Sie Ihren Azure Cosmos DB-Container mit der Synapse-Laufzeit in dieser Region verbinden.

Diese Integration ermöglicht die folgenden HTAP-Szenarien für verschiedene Benutzer:

* Ein BI-Entwickler, der einen Power BI-Bericht modellieren und veröffentlichen möchte, um direkt über Synapse SQL auf die operativen Daten in Azure Cosmos DB zuzugreifen.

* Ein Datenanalyst, der Erkenntnisse aus den operativen Daten in einem Azure Cosmos DB-Container ableiten möchte, indem er ihn mit Synapse SQL abfragt, die Daten maßstabgerecht liest und diese Ergebnisse mit anderen Datenquellen kombiniert.

* Ein Datenanalyst, der Synapse Spark verwenden möchte, um eine Funktion zu finden, die das Modell verbessert und es ohne eine komplexe Datentechnik trainiert. Außerdem können Sie die Ergebnisse des Modells im Anschluss in Azure Cosmos DB schreiben, um eine Echtzeitbewertung der Daten über Spark Synapse durchzuführen.

* Ein Data Engineer, der Daten für Consumer zugänglich machen möchte, indem er SQL- oder Spark-Tabellen über Azure Cosmos DB-Container ohne manuelle ETL-Prozesse erstellt.

Weitere Informationen zur Azure Synapse Analytics-Laufzeitunterstützung für Azure Cosmos DB finden Sie unter [Unterstützung für Azure Synapse Analytics für Cosmos DB](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Anwendungsmöglichkeiten für Azure Synapse Link für Azure Cosmos DB

Synapse Link wird in den folgenden Fällen empfohlen:

* Wenn Sie Azure Cosmos DB-Kunde sind und Analysen, BI und maschinelles Lernen für Ihre operativen Daten ausführen möchten. In solchen Fällen bietet Synapse Link eine stärker integrierte Analyse, ohne dass sich dies auf den bereitgestellten Durchsatz Ihres Transaktionsspeichers auswirkt. Beispiel:

  * Wenn Sie Analysen oder BI auf Ihren operativen Azure Cosmos DB-Daten direkt mithilfe von separaten Connectors ausführen, oder

  * Wenn Sie ETL-Prozesse ausführen, um operative Daten in ein separates Analysesystem zu extrahieren.
 
In solchen Fällen bietet Synapse Link eine stärker integrierte Analyse, ohne dass sich dies auf den bereitgestellten Durchsatz Ihres Transaktionsspeichers auswirkt.

Synapse Link wird nicht empfohlen, wenn Sie herkömmliche Data Warehouse-Anforderungen wie hohe Parallelität, Workloadverwaltung und Persistenz von Aggregaten über mehrere Datenquellen hinweg haben. Weitere Informationen finden Sie unter den [gängigen Szenarien, die mit Azure Synapse Link für Azure Cosmos DB](synapse-link-use-cases.md).

## <a name="limitations"></a>Einschränkungen

* Azure Synapse Link für Azure Cosmos DB wird für die SQL-API und die Azure Cosmos DB-API für MongoDB unterstützt. Für die Gremlin-API, die Cassandra-API und die Tabellen-API gibt es keine Unterstützung.

* Synapse Link kann für neue Container sowohl für SQL-API- als auch für MongoDB-API-Konten aktiviert werden, vorhandene Container werden jedoch nur für die SQL API unterstützt.

* Die Sicherung und Wiederherstellung Ihrer Daten im Analysespeicher wird derzeit nicht unterstützt. Diese Einschränkung gilt sowohl für periodische als auch für fortlaufende Sicherungsmodi und wirkt sich nicht auf ihre Cosmos DB-Transaktionsspeicherdaten aus.

* Synapse Link und der regelmäßige Sicherungsmodus können in demselben Datenbankkonto gleichzeitig vorhanden sein. Obwohl die Sicherung und Wiederherstellung Ihrer Daten im Analysespeicher nicht unterstützt wird.

* Synapse Link und der fortlaufende Sicherungsmodus können in demselben Datenbankkonto gleichzeitig vorhanden sein.

* Der Zugriff auf den Azure Cosmos DB-Analysespeicher mit dem Azure Synapse Dedicated SQL-Pool wird derzeit nicht unterstützt.

* Azure Synapse Link und der regelmäßige Sicherungsmodus können in demselben Datenbankkonto gleichzeitig vorhanden sein. Analysespeicherdaten sind jedoch nicht in den Sicherungen und Wiederherstellungen enthalten. Wenn Synapse Link aktiviert ist, erstellt Azure Cosmos DB weiterhin automatisch im geplanten Sicherungsintervall Sicherungen Ihrer Daten im Transaktionsspeicher.


## <a name="security"></a>Sicherheit

Synapse Link ermöglicht das Ausführen von Analysen in Quasi-Echtzeit über unternehmenskritische Daten in Azure Cosmos DB. Es ist von entscheidender Bedeutung sicherzustellen, dass kritische Geschäftsdaten in den Transaktions- und Analysespeichern sicher gespeichert werden. Azure Synapse Link für Azure Cosmos DB trägt mithilfe der folgenden Features dazu bei, diese Sicherheitsanforderungen zu erfüllen:

* **Netzwerkisolation mithilfe privater Endpunkte:** Sie können den Netzwerkzugriff auf die Daten in den Transaktions- und Analysespeichern unabhängig voneinander steuern. Die Netzwerkisolation erfolgt über separate verwaltete private Endpunkte für jeden Speicher in verwalteten virtuellen Netzwerken in Azure Synapse-Arbeitsbereichen. Weitere Informationen finden Sie im Artikel [Konfigurieren privater Endpunkte für den Analysespeicher](analytical-store-private-endpoints.md).

* **Datenverschlüsselung mit kundenseitig verwalteten Schlüsseln:** Sie können Daten nahtlos im Transaktions- und Analysespeicher verschlüsseln und dabei die gleichen kundenseitig verwalteten Schlüssel automatisiert und transparent verwenden. Azure Synapse Link unterstützt nur das Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der verwalteten Identität Ihres Azure Cosmos DB-Kontos. Sie müssen die verwaltete Identität Ihres Kontos in Ihrer Azure Key Vault-Zugriffsrichtlinie konfigurieren, bevor Sie Azure Synapse Link (configure-synapse-link.md#enable-synapse-link) für Ihr Konto aktivieren. Weitere Informationen finden Sie in dem Artikel [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe verwalteter Identitäten eines Azure Cosmos DB-Kontos](how-to-setup-cmk.md#using-managed-identity).

* **Sichere Schlüsselverwaltung:** Der Zugriff auf die Daten im Analysespeicher von Synapse Spark und serverlosen Synapse-SQL-Pools aus erfordert die Verwaltung von Azure Cosmos DB-Schlüsseln in Synapse Analytics-Arbeitsbereichen. Anstatt die Azure Cosmos DB-Kontoschlüssel in Spark-Aufträgen oder SQL-Skripts zu verwenden, bietet Azure Synapse Link deutlich sicherere Funktionen.

  * Wenn Sie serverlose Synapse-SQL-Pools verwenden, können Sie den Azure Cosmos DB-Analysespeicher abfragen, indem Sie vorab SQL-Anmeldeinformationen erstellen und die Kontoschlüssel speichern, auf die Sie dann in der `OPENROWSET`-Funktion verweisen. Weitere Informationen finden Sie im Artikel [Abfragen mit einem serverlosen SQL-Pool in Azure Synapse Link](../synapse-analytics/sql/query-cosmos-db-analytical-store.md).

  * Wenn Sie Synapse Spark verwenden, können Sie die Kontoschlüssel in Objekten im verknüpften Dienst speichern, die auf eine Azure Cosmos DB-Datenbank verweisen, und zur Laufzeit in der Spark-Konfiguration darauf verweisen. Weitere Informationen finden Sie im Artikel [Kopieren von Daten in einen dedizierten SQL-Pool mithilfe von Apache Spark](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md).


## <a name="pricing"></a>Preise

Das Abrechnungsmodell für Azure Synapse Link umfasst die Kosten für die Nutzung des Azure Cosmos DB-Analysespeichers und der Synapse-Runtime. Weitere Informationen finden Sie unter den [Preisen für den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md#analytical-store-pricing) und den [Preisen für Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Übersicht über den Azure Cosmos DB-Analysespeicher](analytical-store-introduction.md)

* Sehen Sie sich das Lernmodul zum [Entwerfen der Hybridanalysen und Transaktionsverarbeitung mit Azure Synapse Analytics](/learn/modules/design-hybrid-transactional-analytical-processing-using-azure-synapse-analytics/) an

* [Erste Schritte mit Azure Synapse Link für Azure Cosmos DB](configure-synapse-link.md)
 
* [In der Azure Synapse Analytics-Laufzeit unterstützte Features](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Häufig gestellte Fragen zu Azure Synapse Link für Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)

* [Anwendungsfälle für Azure Synapse Link für Azure Cosmos DB](synapse-link-use-cases.md)
