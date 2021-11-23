---
title: Neues in Azure Data Factory
description: Auf dieser Seite werden neue Features und die neuesten Verbesserungen für Azure Data Factory beschrieben. Azure Data Factory ist ein verwalteter Clouddienst, der für komplexe hybride ETL-Projekte (Extrahieren, Transformieren und Laden), ELT-Projekte (Extrahieren, Laden und Transformieren) und Datenintegrationsprojekte ausgelegt ist.
author: pennyzhou-msft
ms.author: xupzhou
ms.service: data-factory
ms.subservice: concepts
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: 52c59662e1dfa97e779cce0b812bcaf25a8494a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301625"
---
# <a name="whats-new-in-azure-data-factory"></a>Neues in Azure Data Factory

Der Azure Data Factory-Dienst (ADF) wird laufend verbessert. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neueste Versionen
- Bekannte Probleme
- Behebung von Programmfehlern
- Veraltete Funktionen
- Pläne für Änderungen

Besuchen Sie regelmäßig diese Seite. Diese wird monatlich aktualisiert. 

## <a name="october-2021"></a>Oktober 2021
<br>
<table>
<tr><td><b>Dienstkategorie</b></td><td><b>Verbesserungen beim Dienst</b></td><td><b>Details</b></td></tr>
  
<tr><td rowspan=3><b>Datenfluss</b></td><td>Azure Data Explorer-Connector und Amazon Web Services S3-Connector</td><td>Das Microsoft-Datenintegrationsteam hat gerade zwei neue Connectors für Zuordnungsdatenflüsse veröffentlicht. Wenn Sie Azure Synapse verwenden, können Sie jetzt eine direkte Verbindung mit Ihren AWS S3-Buckets für Datentransformationen herstellen. Sowohl in Azure Data Factory als auch in Azure Synapse können Sie nun nativ eine Verbindung mit Ihren Azure Data Explorer-Clustern in Zuordnungsdatenflüssen herstellen.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/mapping-data-flow-gets-new-native-connectors/ba-p/2866754">Weitere Informationen</a></td></tr>
<tr><td>Power Query-Aktivität jetzt allgemein verfügbar</td><td>Microsoft hat die Power Query-Pipelineaktivität von Azure Data Factory als allgemein verfügbar freigegeben. Dieses neue Feature bietet eine aufskalierte Datenvorbereitung und Data Wrangling für Citizen Integrators innerhalb der ADF-Browserbenutzeroberfläche, um eine integrierte Benutzeroberfläche für technische Fachkräfte für Daten zu erhalten. Das Data Wrangling-Feature von Power Query in ADF bietet eine leistungsstarke und benutzerfreundliche Pipelinefunktion, um Ihre komplexesten Datenintegrations- und ETL-Muster in einem einzigen Dienst zu lösen.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/data-wrangling-at-scale-with-adf-s-power-query-activity-now/ba-p/2824207">Weitere Informationen</a></td></tr>
<tr><td>Neue stringifizierte Datentransformation in Zuordnungsdatenflüssen</td><td>Zuordnungsdatenflüsse fügen eine neue Datentransformation mit dem Namen „Stringify“ hinzu, um komplexe Datentypen wie Strukturen und Arrays ganz einfach in Zeichenfolgenform zu konvertieren, die an strukturierte Ausgabeziele gesendet werden kann.<br><a href="data-flow-stringify.md">Weitere Informationen</a></td></tr>
  
<tr>
  <td rowspan=2><b>Integration Runtime</b></td>
  <td>Von Azure Data Factory verwaltetes vNet in allgemeine Verfügbarkeit überführt</td>
  <td>Sie können die Azure Integration Runtime jetzt als Teil eines verwalteten virtuellen Netzwerks bereitstellen und private Endpunkte nutzen, um eine sichere Verbindung mit unterstützten Datenspeichern herzustellen. Der Datenverkehr durchläuft private Azure-Links, die für eine sichere Konnektivität mit der Datenquelle sorgen. Darüber hinaus wird die Exfiltration von Daten in das öffentliche Internet verhindert.<br><a href="managed-virtual-network-private-endpoint.md">Weitere Informationen</a></td>
 </tr>
 <tr>
   <td>VNet-Injektionsmethode „Express“ für die SSIS Integration Runtime (Public Preview)</td>
   <td>Die SSIS Integration Runtime unterstützt jetzt die VNet-Injektionsmethode „Express“.<br>
Weitere Informationen:<br>
     <a href="join-azure-ssis-integration-runtime-virtual-network.md">Verknüpfung der Azure-SSIS-Integrationslaufzeit mit einem virtuellen Netzwerk</a><br>
     <a href="azure-ssis-integration-runtime-virtual-network-configuration.md">Konfigurieren eines virtuellen Netzwerks für die Injektion von Azure-SSIS Integration Runtime</a><br>
     <a href="azure-ssis-integration-runtime-express-virtual-network-injection.md">Expresseinschleusungsmethode für virtuelle Netzwerke (Vorschau)</a>
   </td>
 </tr>

<tr><td rowspan=2><b>Security</b></td><td>Azure Key Vault-Integrationsverbesserung</td><td>Wir haben die Azure Key Vault-Integration verbessert, indem vom Benutzer auswählbare Dropdownlisten hinzugefügt werden, um die geheimen Werte im verknüpften Dienst auszuwählen, die Produktivität zu steigern und Benutzer nicht zur Eingabe der Geheimnisse zu zwingen, was zu menschlichen Fehlern führen kann.</td></tr>
<tr><td>Unterstützung benutzerseitig zugewiesener verwalteter Identitäten in Azure Data Factory</td><td>Die Sicherheit von Anmeldeinformationen ist für jedes Unternehmen von entscheidender Bedeutung. Daher ist das ADF-Team (Azure Data Factory) bestrebt, den Datentechnikprozess für technische Fachkräfte für Daten sowohl sicher als auch einfach zu gestalten. Wir freuen uns, die Unterstützung benutzerseitig zugewiesener verwalteter Identitäten (Vorschauversion) in allen Connectors/verknüpften Diensten ankündigen zu können, die die Azure AD-Authentifizierung (Azure Active Directory) unterstützen.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-for-user-assigned-managed-identity-in-azure-data-factory/ba-p/2841013">Weitere Informationen</a></td></tr>
</table>

## <a name="september-2021"></a>September 2021
<br>
<table>
<tr><td><b>Dienstkategorie</b></td><td><b>Verbesserungen beim Dienst</b></td><td><b>Details</b></td></tr>
  <tr><td><b>Continuous Integration and Continuous Delivery (CI/CD)</b></td><td>Erweiterte CI/CD-Funktionen</td><td>Sie können jetzt auf beliebigen anderen Branches in Azure Data Factory basierende neue Git-Branches erstellen.<br><a href="source-control.md#version-control">Weitere Informationen</a></td></tr>
<tr><td rowspan=3><b>Datenverschiebung</b></td><td>Amazon Relational Database Service (RDS) für Oracle-Quellen</td><td>Der Connector für Amazon RDS für Oracle-Quellen ist jetzt sowohl in Azure Data Factory als auch in Azure Synapse verfügbar.<br><a href="connector-amazon-rds-for-oracle.md">Weitere Informationen</a></td></tr>
<tr><td>Amazon RDS für SQL Server-Quellen</td><td>Der Connector für Amazon RDS für SQL Server-Quellen ist jetzt sowohl in Azure Data Factory als auch in Azure Synapse verfügbar.<br><a href="connector-amazon-rds-for-sql-server.md">Weitere Informationen</a></td></tr>
<tr><td>Unterstützung für parallele Kopiervorgänge aus Azure Database for PostgreSQL</td><td>Der Connector für Azure Database for PostgreSQL unterstützt jetzt parallele Kopiervorgänge.<br><a href="connector-azure-database-for-postgresql.md">Weitere Informationen</a></td></tr>
<tr><td rowspan=3><b>Datenfluss</b></td><td>Verwenden von Azure Data Lake Storage (ADLS) Gen2 zum Ausführen von Vor- und Nachverarbeitungsbefehlen</td><td>Vor- und Nachverarbeitungsbefehle von Hadoop Distributed File System (HDFS) können jetzt mithilfe von ADLS Gen2-Senken in Datenflüssen ausgeführt werden.<br><a href="connector-azure-data-lake-storage.md#pre-processing-and-post-processing-commands">Weitere Informationen</a></td></tr>
<tr><td>Bearbeiten von Datenflusseigenschaften für vorhandene Instanzen der Azure Integration Runtime (IR)</td><td>Die Azure Integration Runtime (IR) wurde aktualisiert und ermöglicht jetzt die Bearbeitung von Datenflusseigenschaften für vorhandene IRs. Sie können jetzt Computeeigenschaften von Datenflüssen ändern, ohne eine neue Azure IR zu erstellen.<br><a href="concepts-integration-runtime.md">Weitere Informationen</a></td></tr>
<tr><td>TTL-Einstellung für Azure Synapse zum Verbessern der Startzeit für die Ausführung von Pipelineaktivitäten</td><td>In Azure Synapse Analytics wurde eine TTL-Einstellung zur Azure Integration Runtime hinzugefügt, damit die Pipelineaktivitäten Ihrer Datenflüsse innerhalb weniger Sekunden gestartet werden können, wodurch die Laufzeit Ihrer Datenflusspipelines erheblich verkürzt wird.<br><a href="control-flow-execute-data-flow-activity.md#data-flow-integration-runtime">Weitere Informationen</a></td></tr>
<tr><td><b>Integration Runtime</b></td><td>Von Azure Data Factory verwaltetes vNet in allgemeine Verfügbarkeit überführt</td><td>Sie können die Azure Integration Runtime jetzt als Teil eines verwalteten virtuellen Netzwerks bereitstellen und private Endpunkte nutzen, um eine sichere Verbindung mit unterstützten Datenspeichern herzustellen. Der Datenverkehr durchläuft private Azure-Links, die für eine sichere Konnektivität mit der Datenquelle sorgen. Darüber hinaus wird die Exfiltration von Daten in das öffentliche Internet verhindert.<br><a href="managed-virtual-network-private-endpoint.md">Weitere Informationen</a></td></tr>
<tr><td rowspan=2><b>Orchestrierung</b></td><td>Operationalisieren und Bereitstellen einer SLA für Datenpipelines</td><td>Die neue Metrik für die verstrichene Zeit der Pipelineausführung in Kombination mit Data Factory-Warnungen ermöglicht Entwicklern von Datenpipelines die Bereitstellung besserer SLAs für ihre Kunden: Sie teilen uns mit, wie lange eine Pipeline ausgeführt werden soll, und wir benachrichtigen Sie proaktiv, wenn die Pipelineausführung länger dauert als erwartet.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/operationalize-and-provide-sla-for-data-pipelines/ba-p/2767768">Weitere Informationen</a></td></tr>
<tr><td>Fail-Aktivität (Public Preview)</td><td>Mit der neuen Fail-Aktivität können Sie aus beliebigen Gründen absichtlich einen Fehler in einer Pipeline auslösen. Beispielsweise können Sie die Fail-Aktivität verwenden, wenn eine Lookup-Aktivität keine übereinstimmenden Daten zurückgibt oder eine benutzerdefinierte Aktivität mit einem internen Fehler beendet wird.<br><a href="control-flow-fail-activity.md">Weitere Informationen</a></td></tr>
</table>

## <a name="august-2021"></a>August 2021
<br>
<table>
<tr><td><b>Dienstkategorie</b></td><td><b>Verbesserungen beim Dienst</b></td><td><b>Details</b></td></tr>
  <tr><td><b>Continuous Integration and Continuous Delivery (CI/CD)</b></td><td>CI/CD-Verbesserungen mit GitHub-Unterstützung in Azure Government und Azure China</td><td>In Azure für US Government und Azure China wurde Unterstützung für GitHub hinzugefügt.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/cicd-improvements-with-github-support-in-azure-government-and/ba-p/2686918">Weitere Informationen</a></td></tr>
<tr><td rowspan=2><b>Datenverschiebung</b></td><td>Azure Cosmos DB-API für MongoDB-Connector unterstützt Version 3.6 und 4.0 in Azure Data Factory</td><td>Die Cosmos DB-API für den MongoDB-Connector von Azure Data Factory unterstützt jetzt Serverversion 3.6 und 4.0.<br><a href="connector-azure-cosmos-db-mongodb-api.md">Weitere Informationen</a></td></tr>
<tr><td>Erweiterte Verwendung der COPY-Anweisung zum Laden von Daten in Azure Synapse Analytics</td><td>Der Azure Synapse Analytics-Connector von Azure Data Factory unterstützt jetzt das gestaffelte Kopieren und Kopierquellen mit *.* als Platzhalter für Dateinamen (wildcardFilename) für die COPY-Anweisung.<br><a href="connector-azure-sql-data-warehouse.md#use-copy-statement">Weitere Informationen</a></td></tr>
<tr><td><b>Datenfluss</b></td><td>REST-Endpunkte als Quelle und Senke in Datenflüssen verfügbar</td><td>Datenflüsse in Azure Data Factory und Azure Synapse Analytics unterstützen REST-Endpunkte jetzt sowohl als Quelle als auch als Senke mit vollständiger Unterstützung für JSON- und XML-Nutzdaten.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/rest-source-and-sink-now-available-for-data-flows/ba-p/2596484">Weitere Informationen</a></td></tr>
<tr><td><b>Integration Runtime</b></td><td>Diagnosetool für selbstgehostete Integration Runtime verfügbar</td><td>Ein Diagnosetool für die selbstgehostete Integration Runtime (IR) verbessert die Benutzerfreundlichkeit und unterstützt Benutzer beim Ermitteln potenzieller Probleme. Das Tool führt eine Reihe von Testszenarien auf dem Computer mit der selbstgehosteten IR aus, und jedes Szenario verfügt über typische Integritätsprüfungsfälle für häufige Probleme.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/diagnostic-tool-for-self-hosted-integration-runtime/ba-p/2634905">Weitere Informationen</a></td></tr>
<tr><td><b>Orchestrierung</b></td><td>Benutzerdefinierter Ereignisauslöser mit erweiterter Filteroption allgemein verfügbar</td><td>Sie können jetzt einen Auslöser erstellen, der auf ein in Event Grid veröffentlichtes benutzerdefiniertes Thema reagiert. Darüber hinaus können Sie mithilfe der erweiterten Filterung präzise steuern, auf welche Ereignisse reagiert werden soll.<br><a href="how-to-create-custom-event-trigger.md">Weitere Informationen</a></td></tr>
</table>

## <a name="july-2021"></a>Juli 2021
<br>
<table>
<tr><td><b>Dienstkategorie</b></td><td><b>Verbesserungen beim Dienst</b></td><td><b>Details</b></td></tr>
<tr><td><b>Datenverschiebung</b></td><td>Erstellen von metadatengesteuerten Datenerfassungspipelines im ADF-Tool zum Kopieren von Daten in nur 10 Minuten (Public Preview)</td><td>Mit dem metadatengesteuerten Ansatz des Tools zum Kopieren von Daten (Public Preview) können Sie innerhalb von 10 Minuten umfangreiche Datenkopierpipelines erstellen.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/get-metadata-driven-data-ingestion-pipelines-on-adf-within-10/ba-p/2528219">Weitere Informationen</a></td></tr>
<tr><td><b>Datenfluss</b></td><td>Neue Zuordnungsfunktionen in Transformationsfunktionen für Datenflüsse</td><td>Es wurde eine neue Gruppe von Transformationsfunktionen für Datenflüsse hinzugefügt, die technischen Fachkräften für Daten das einfache Generieren, Lesen und Aktualisieren von Zuordnungsdatentypen und komplexen Zuordnungsstrukturen ermöglichen.<br><a href="data-flow-expression-functions.md#map-functions">Weitere Informationen</a></td></tr>
<tr><td><b>Integration Runtime</b></td><td>Fünf neue Regionen im verwalteten virtuellen Netzwerk (VNET) in Azure Data Factory (Public Preview) verfügbar</td><td>Die folgenden fünf neuen Regionen sind im verwalteten virtuellen Netzwerk in Azure Data Factory (Public Preview) verfügbar: „China, Osten 2“, „China, Norden 2“, „US Gov Arizona“, „US Gov Texas“ und „US Gov Virginia“.<br><a href="managed-virtual-network-private-endpoint.md#azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions">Weitere Informationen</a></td></tr>
<tr><td rowspan=2><b>Entwicklerproduktivität</b></td><td>Verbesserungen an der ADF-Startseite</td><td>Die Data Factory-Startseite wurde mit besseren Kontrast- und Umbruchfunktionen umgestaltet. Darüber hinaus wurden einige Abschnitte auf der Startseite eingeführt, mit denen Sie die Produktivität bei Ihrer Datenintegrations-Journey verbessern können.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">Weitere Informationen</a></td></tr>
<tr><td>Neue Landing Page für Azure Data Factory Studio</td><td>Die Landing Page für das Blatt „Data Factory“ im Azure-Portal wurde überarbeitet.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">Weitere Informationen</a></td></tr>
</table>

## <a name="june-2021"></a>Juni 2021
<br>
<table>
<tr><td><b>Dienstkategorie</b></td><td><b>Verbesserungen beim Dienst</b></td><td><b>Details</b></td></tr>
<tr><td rowspan=4 valign="middle"><b>Datenverschiebung</b></td><td>Neue Benutzeroberfläche mit Azure Data Factory-Tool „Daten kopieren“</td><td>Das neu gestaltete Tool „Daten kopieren“ bietet jetzt eine bessere Datenerfassung.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">Weitere Informationen</a></td></tr>
<tr><td>MongoDB und MongoDB Atlas werden sowohl als Quelle als auch als Senke unterstützt.</td><td>Durch diese Verbesserung wird das Kopieren von Daten zwischen einem unterstützten Datenspeicher und einer MongoDB- oder MongoDB Atlas-Datenbank unterstützt.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">Weitere Informationen</a></td></tr>
<tr><td>Always Encrypted wird für Azure SQL-Datenbank-, Azure SQL Managed Instance- und SQL Server-Connectors als Quelle und als Senke unterstützt.</td><td>Always Encrypted ist in Azure Data Factory für Azure SQL-Datenbank-, Azure SQL Managed Instance- und SQL Server-Connectors für die Copy-Aktivität verfügbar.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">Weitere Informationen</a></td></tr>
<tr><td>Das Festlegen benutzerdefinierter Metadaten wird in der Copy-Aktivität unterstützt, wenn als Senke ADLS Gen2 oder Azure Blob verwendet werden.</td><td>Beim Schreiben in ADLS Gen2 oder Azure-Blobs unterstützt die Copy-Aktivität das Festlegen benutzerdefinierter Metadaten oder die Speicherung der Informationen zur letzten Änderung der Quelldatei als Metadaten.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">Weitere Informationen</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>Datenfluss</b></td><td>Unterstützung für SQL Server als Quelle und Senke in Datenflüssen</td><td>SQL Server wird jetzt als Quelle und Senke in Datenflüssen unterstützt. Unter dem Link finden Sie Anweisungen zum Konfigurieren Ihres Netzwerks mithilfe der Azure Integration Runtime-Funktion für ein verwaltetes VNET, die die Kommunikation mit Ihren lokalen SQL Server- und Cloud-VM-Instanzen ermöglicht.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">Weitere Informationen</a></td></tr>
<tr><td>Die schnelle Wiederverwendung von Dataflowclustern ist jetzt standardmäßig für alle neuen Azure Integration Runtime-Instanzen aktiviert.</td><td>ADF freut sich, die allgemeine Verfügbarkeit des beliebten Features für die schnelle Wiederverwendung von Dataflowstarts bekannt zu geben. Die schnelle Wiederverwendung ist ab sofort für alle neuen Azure Integration Runtime-Instanzen standardmäßig aktiviert.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">Weitere Informationen</a></td></tr>
<tr><td>Power Query-Aktivität (Public Preview)</td><td>Sie können nun mithilfe von Data Wrangling in Azure Data Factory komplexe Feldzuordnungen zu Ihrer Power Query-Senke erstellen. Die Senke wird jetzt in der Pipeline in der Power Query-Aktivität (Public Preview) konfiguriert, um dieses Update umzusetzen.<br><a href="wrangling-tutorial.md">Weitere Informationen</a></td></tr>
<tr><td>Aktualisierte Benutzeroberfläche für die Überwachung von Datenflüssen in Azure Data Factory</td><td>Ein neues Update für Azure Data Factory betrifft die Benutzeroberfläche für die Überwachung. Es vereinfacht die Anzeige der ETL-Auftragsausführungen in Ihrem Datenfluss und ermöglicht die schnelle Ermittlung von Bereichen für die Leistungsoptimierung.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">Weitere Informationen</a></td></tr>
<tr><td><b>SQL Server Integration Services (SSIS)</b></td><td>Ausführen von SQL an beliebigen Orten in drei einfachen Schritten mit SSIS in Azure Data Factory</td><td>Dieser Beitrag enthält drei einfache Schritte zum Ausführen beliebiger SQL-Anweisungen/-Skripts an einer beliebigen Stelle mit SSIS in Azure Data Factory.<ol><li>Bereiten Sie Ihre selbstgehostete Integration Runtime/SSIS Integration Runtime vor.</li><li>Bereiten Sie eine Aktivität „SSIS-Paket ausführen“ in der Azure Data Factory-Pipeline vor.</li><li>Führen Sie die Aktivität „SSIS-Paket ausführen“ in Ihrer selbstgehosteten Integration Runtime/SSIS Integration Runtime aus.</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">Weitere Informationen</a></td></tr>
</table>

## <a name="more-information"></a>Weitere Informationen

- [Blog: Azure Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
- [Stack Overflow-Forum](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [Videos](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)
