---
title: Datei einfügen
description: include file
services: synapse-analytics
author: jonburchel
ms.service: synapse-analytics
ms.topic: include
ms.date: 09/22/2021
ms.author: jburchel
ms.custom: include file
ms.openlocfilehash: d97a314a5928de17ad878aee5dfc14b5519d0f80
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129095919"
---
Für Azure Synapse Analytics gelten die folgenden Standardgrenzwerte, um sicherzustellen, dass Kundenabonnements vor anderen Workloads geschützt sind. Wenden Sie sich an den Support, um die Grenzwerte bis zum Höchstwert für Ihr Abonnement zu erhöhen.

### <a name="synapse-workspace-limits"></a>Grenzwerte für Synapse-Arbeitsbereiche

| Resource | Standardlimit | Maximales Limit | 
| -------- | ------------- | ------------- |
| Synapse-Arbeitsbereiche in einem Azure-Abonnement | 20 | 20 |

### <a name="synapse-pipeline-limits"></a>Grenzwerte für Synapse-Pipelines

| Resource | Standardlimit | Maximales Limit |
| -------- | ------------- | ------------- |
| Synapse-Pipelines in einem Synapse-Arbeitsbereich | 800 | 800 |
| Gesamtanzahl von Entitäten (z. B. Pipelines, Datasets, Trigger, verknüpfte Dienste, private Endpunkte und Integration Runtimes) in einem Arbeitsbereich | 5\.000 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gesamtanzahl CPU-Kerne für Azure-SSIS Integration Runtimes unter einem Arbeitsbereich | 256 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gleichzeitige Pipelineausführungen pro Arbeitsbereich (von allen Pipelines des Arbeitsbereichs gemeinsam genutzt) | 10.000  | 10.000 |
| Gleichzeitige externe Aktivitätsausführungen pro Arbeitsbereich und [Azure Integration Runtime-Region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Externe Aktivitäten werden in der Integration Runtime verwaltet, aber in verknüpften Diensten ausgeführt. Hierzu gehören unter anderem Databricks, gespeicherte Prozeduren, HDInsight und Web. Die Beschränkung gilt nicht für die selbstgehostete IR.</small> | 3,000 | 3,000 |
| Gleichzeitige Pipelineaktivitätsausführungen pro Arbeitsbereich und [Azure Integration Runtime-Region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>Pipelineaktivitäten werden in der Integration Runtime ausgeführt. Hierzu zählen unter anderem „Lookup“, „GetMetadata“ und „Delete“. Die Beschränkung gilt nicht für die selbstgehostete IR.</small> | 1\.000 | 1\.000                                                        |
| Gleichzeitige Erstellungsvorgänge pro Arbeitsbereich und [Azure Integration Runtime-Region](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Einschließlich Verbindung testen, Ordnerliste und Tabellenliste durchsuchen und Datenvorschau anzeigen. Die Beschränkung gilt nicht für die selbstgehostete IR.</small> | 200 | 200                                                          |
| Gleichzeitige Nutzung von Datenintegrationseinheiten<sup>1</sup> pro Arbeitsbereich und [Azure Integration Runtime-Region](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Regionsgruppe 1<sup>2</sup>: 6.000<br>Regionsgruppe 2<sup>2</sup>: 3,000<br>Regionsgruppe 3<sup>2</sup>: 1.500<br>Verwaltetes virtuelles Netzwerk<sup>2</sup>: 2.400 | Regionsgruppe 1<sup>2</sup>: 6.000<br/>Regionsgruppe 2<sup>2</sup>: 3,000<br/>Regionsgruppe 3<sup>2</sup>: 1.500<br>Verwaltetes virtuelles Netzwerk: [Support kontaktieren](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximale Aktivitäten pro Pipeline, einschließlich interner Aktivitäten für Container | 40 | 40 |
| Maximale Anzahl verknüpfter Integration Runtimes, die für eine einzelne selbstgehostete Integration Runtime erstellt werden kann | 100 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximale Parameter pro Pipeline | 50 | 50 |
| ForEach-Elemente | 100.000 | 100.000 |
| ForEach-Parallelität | 20 | 50 |
| Maximale Anzahl von Ausführungen in Warteschlange pro Pipeline | 100 | 100 |
| Zeichen pro Ausdruck | 8\.192 | 8\.192 |
| Mindestintervall für Trigger für ein rollierendes Fenster | 5 Min. | 15 Min. |
| Maximales Zeitlimit für Ausführungen von Pipelineaktivitäten | 7 Tage | 7 Tage |
| Bytes pro Objekt für Pipelineobjekte<sup>3</sup> | 200 KB | 200 KB |
| Bytes pro Objekt für Datasetobjekte und verknüpfte Dienstobjekte<sup>3</sup> | 100 KB | 2\.000 KB |
| Bytes pro Nutzdaten für jede Aktivitätsausführung<sup>4</sup> | 896 KB | 896 KB |
| Datenintegrationseinheiten<sup>1</sup> pro Kopieraktivitätsausführung | 256 | 256 |
| Schreiben von API-Aufrufen | 1\.200/h | 1\.200/h<br/><br/> Dieser Grenzwert wird von Azure Resource Manager, nicht von Azure Synapse Analytics vorgegeben. |
| Lesen von API-Aufrufen | 12.500/h | 12.500/h<br/><br/> Dieser Grenzwert wird von Azure Resource Manager, nicht von Azure Synapse Analytics vorgegeben. |
| Überwachungsabfragen pro Minute | 1\.000 | 1\.000 |
| Maximale Zeit für Datenfluss-Debugsitzung | 8 Std. | 8 Std. |
| Gleichzeitige Anzahl von Datenflüssen pro Integration Runtime | 50 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gleichzeitige Anzahl von Datenflüssen pro Integration Runtime in einem verwalteten VNET| 20 | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gleichzeitige Anzahl von Datenfluss-Debugsitzungen pro Benutzer und Arbeitsbereich | 3 | 3 |
| Azure IR TTL-Limit für Datenfluss | 4 Std. |  4 Std. |
| Größenbeschränkung für Metadatenentitäten in einem Arbeitsbereich | 2 GB | [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Die Datenintegrationseinheit (Data Integration Unit, DIU) wird bei einem Cloud-zu-Cloud-Kopiervorgang verwendet. Weitere Informationen finden Sie unter [Datenintegrationseinheiten](../articles/data-factory/copy-activity-performance.md#data-integration-units). Informationen zur Abrechnung finden Sie unter [Azure Synapse Analytics – Preise](https://azure.microsoft.com/pricing/details/synapse-analytics/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) ist [global verfügbar](https://azure.microsoft.com/global-infrastructure/services/), um Datencompliance, Effizienz und geringere Kosten für ausgehenden Netzwerkdatenverkehr zu gewährleisten. 

| Regionsgruppe | Regions |
| -------- | ------ |
| Regionsgruppe 1 | „USA, Mitte“, „USA, Osten“, „USA, Osten 2“, „Europa, Norden“, „Europa, Westen“, „USA, Westen“, „USA, Westen 2“ |
| Regionsgruppe 2 | „Australien, Osten“, „Australien, Südosten“, „Brasilien, Süden“, „Indien, Mitte“, „Japan, Osten“, „USA, Norden-Mitte“, „USA, Süden-Mitte“, „Asien, Südosten“, „USA, Westen-Mitte“ |
| Regionsgruppe 3 | Andere Regionen |
Wenn das verwaltete virtuelle Netzwerk aktiviert ist, liegt die Datenintegrationseinheit in allen Regionsgruppen bei 2.400.

<sup>3</sup> Objekte für Pipelines, Datasets und verknüpfte Dienste stellen eine logische Gruppierung Ihrer Workload dar. Die Grenzwerte für diese Objekte beziehen sich nicht auf die Datenmenge, die Sie mit Azure Synapse Analytics verschieben und verarbeiten können. Synapse Analytics kann so skaliert werden, dass Petabytes an Daten verarbeitet werden können.

<sup>4</sup> Die Nutzdaten für jede Aktivitätsausführung enthalten die Aktivitätskonfiguration, die Konfigurationen der zugeordneten Datasets und verknüpften Dienste (sofern vorhanden) sowie einen kleinen Teil der Systemeigenschaften, die pro Aktivitätstyp generiert werden. Die Grenzwerte für die Größe der Nutzdaten beziehen sich nicht auf die Datenmenge, die Sie mit Azure Synapse Analytics verschieben und verarbeiten können. [Hier](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large) erfahren Sie mehr über die Symptome und Empfehlungen bei Erreichen dieser Grenzwerte.

### <a name="dedicated-sql-pool-limits"></a>Grenzwerte für dedizierte SQL-Pools
Ausführliche Informationen zu Kapazitätsgrenzen für dedizierte SQL-Pools in Azure Synapse Analytics finden Sie unter [Kapazitätsgrenzen für dedizierten SQL-Pool in Azure Synapse Analytics](../articles/synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

### <a name="web-service-call-limits"></a>Limits für Webdienstaufrufe
Azure Resource Manager weist Grenzwerte für API-Aufrufe auf. Sie können API-Aufrufe mit einer Rate innerhalb der [API-Limits von Azure Resource Manager](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)ausführen.
