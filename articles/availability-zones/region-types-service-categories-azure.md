---
title: Azure-Dienste
description: Erfahren Sie mehr über die Arten von Regionen und Kategorien von Diensten in Azure.
author: mamccrea
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: mamccrea
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 7a8159a10749e877c6502ef775071af6cc420f46
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059512"
---
# <a name="azure-services"></a>Azure-Dienste

Die Verfügbarkeit von Diensten in Azure-Regionen richtet sich nach der Art einer Region. Es gibt zwei Arten von Regionen in Azure: *empfohlene* und *alternative*.

- **Empfohlen**: Diese Regionen bieten die umfangreichste Palette an Dienstfunktionen und unterstützen derzeit Verfügbarkeitszonen. Sie sind im Azure-Portal als **Empfohlen** markiert.
- **Alternativ**: Diese Regionen erweitern die Azure-Kapazität innerhalb einer Datenresidenzgrenze, in der derzeit eine empfohlene Region vorhanden ist. Alternative Regionen helfen beim Optimieren von Latenzen und stellen eine zweite Region für Notfallwiederherstellungen bereit, unterstützen aber keine Verfügbarkeitszonen. Azure führt regelmäßige Bewertungen alternativer Regionen durch, um zu ermitteln, ob sie zu empfohlenen Regionen werden sollten. Diese Regionen sind im Azure-Portal als **Sonstiges** markiert.

## <a name="service-categories-across-region-types"></a>Dienstkategorien für verschiedene Arten von Regionen

Azure-Dienste sind in drei Kategorien gruppiert: *grundlegend*, *allgemein* und *strategisch*. Die allgemeine Richtlinie von Azure für die Bereitstellung von Diensten in einer beliebigen Region richtet sich hauptsächlich nach der Art der Region, den Dienstkategorien und dem Kundenbedarf.

- **Grundlegend**: Diese Dienste sind in allen empfohlenen und alternativen Regionen verfügbar, wenn die Region allgemein verfügbar ist, oder innerhalb von 90 Tagen ab dem Zeitpunkt, an dem ein neuer grundlegender Dienst allgemein verfügbar wird.
- **Allgemein**: Diese Dienste sind innerhalb von 90 Tagen ab dem Zeitpunkt der allgemeinen Verfügbarkeit der Region in allen empfohlen Regionen verfügbar. In alternativen Regionen richtet sich die Verfügbarkeit nach dem Bedarf, und viele dieser Dienste wurden bereits in einem großen Teil der alternativen Regionen bereitgestellt.
- **Strategisch** (früher als „spezialisiert“ bezeichnet): Dies sind gezielte Dienstangebote, die oft auf eine bestimmte Branche zugeschnitten sind oder von angepassten Hardwarekomponenten unterstützt werden. In alternativen Regionen richtet sich die Verfügbarkeit nach dem Bedarf, und viele dieser Dienste wurden bereits in einem großen Teil der empfohlenen Regionen bereitgestellt.

Informationen dazu, welche Dienste in einer bestimmten Region bereitgestellt wurden und wie die zukünftige Roadmap für Vorschauversionen oder die allgemeine Verfügbarkeit der Dienste in einer Region aussieht, finden Sie auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).

Wenn ein Dienstangebot in einer Region nicht verfügbar ist, wenden Sie sich an Ihr Microsoft-Vertriebsteam, um weitere Informationen zu erhalten und Optionen zu erkunden.

| Regionstyp | Nicht regional | Grundlegend | Hauptdienst | Strategisch | Verfügbarkeitszonen | Datenresidenz |
| --- | --- | --- | --- | --- | --- | --- |
| Empfohlen | **J** | **J** | **J** | Nachfragegesteuert | **J** | **J** |
| Alternativ | **J** | **J** | Nachfragegesteuert | Nachfragegesteuert | – | **J** |

## <a name="available-services-by-category"></a>Verfügbare Dienste nach Kategorie

Azure weist bei allgemeiner Verfügbarkeit die Dienstkategorien „grundlegend“, „allgemein“ und „strategisch“ zu. In der Regel sind Dienste zunächst „strategisch“ und werden mit steigender Nachfrage und Nutzung in die Kategorien „grundlegend“ und „allgemein“ hochgestuft.

In der folgenden Tabelle sind die Azure-Dienste nach Kategorie aufgeführt. Beachten Sie, dass einige Dienste nicht regionsspezifisch sind. Das bedeutet, dass sie unabhängig von der Region global verfügbar sind. Weitere Informationen und eine Liste nicht regionsspezifischer Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).

> [!div class="mx-tableFixed"]
> | ![Ein Symbol, das angibt, dass es sich um einen grundlegenden Dienst handelt.](media/icon-foundational.svg) Grundlegend                           | ![Ein Symbol, das angibt, dass es sich um einen allgemeinen Dienst handelt.](media/icon-mainstream.svg) Mainstream                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Azure-Speicherkonten                 | Azure API Management                              | 
> | Azure Application Gateway              | Azure App Configuration                           | 
> | Azure Backup                           | Azure App Service                                 | 
> | Azure Cosmos DB                        | Azure-Automatisierung                                  | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Öffentliche Azure-IP-Adresse                        | Azure Cache for Redis                             | 
> | Azure SQL-Datenbank                     | Azure Cognitive Services                          | 
> | Verwaltete Azure SQL-Instanz             | Azure Cognitive Services: Maschinelles Sehen         | 
> | Datenträgerspeicher                           | Azure Cognitive Services: Content Moderator       | 
> | Azure Event Hubs                       | Azure Cognitive Services: Gesicht                    | 
> | Azure-Schlüsseltresor                        | Azure Cognitive Services: Textanalyse          | 
> | Azure Load Balancer                    | Azure-Daten-Explorer                               | 
> | Azure-Servicebus                      | Azure Database for MySQL                          | 
> | Azure Service Fabric                   | Azure Database for PostgreSQL                     | 
> | Azure Storage: heiße/kalte Blobspeicherebenen   | Azure DDoS Protection                       | 
> | Speicher: Managed Disks                 | Azure Firewall                                    | 
> | Skalierungsgruppen für virtuelle Azure-Computer       | Azure Firewall Manager                            | 
> | Azure Virtual Machines                 | Azure-Funktionen                                   | 
> | Virtual Machines: Azure Dedicated Host | Azure IoT Hub                                     | 
> | Virtual Machines: Av2-Serie           | Azure Kubernetes Service (AKS)                    | 
> | Virtual Machines: Bs-Serie            | Azure Monitor: Application Insights               | 
> | Virtual Machines: DSv2-Serie          | Azure Monitor: Log Analytics                      | 
> | Virtual Machines: DSv3-Serie          | Azure Private Link                                | 
> | Virtual Machines: Dv2-Serie           | Azure Site Recovery                               | 
> | Virtual Machines: Dv3-Serie           | Azure Synapse Analytics                           |     
> | Virtual Machines: ESv3-Serie          | Azure Batch                                       | 
> | Virtual Machines: Ev3-Serie           | Azure-Clouddienst: M-Serie                     | 
> | Azure Virtual Network                  | Azure Container Instances                         | 
> | Azure VPN Gateway                      | Azure Container Registry                          | 
> |                                        | Azure Data Factory                                | 
> |                                        | Azure Event Grid                                  | 
> |                                        | Azure HDInsight                                   |  
> |                                        | Azure Logic Apps                                  | 
> |                                        | Azure Media Services                              | 
> |                                        | Azure Network Watcher                             | 
> |                                        | Blob Storage Premium                              | 
> |                                        | File Storage (Premium)                             | 
> |                                        | Virtual Machines: Ddsv4-Serie                    | 
> |                                        | Virtual Machines: Ddv4-Serie                     | 
> |                                        | Virtual Machines: Dsv4-Serie                     | 
> |                                        | Virtual Machines: Dv4-Serie                      | 
> |                                        | Virtual Machines: Edsv4-Serie                    | 
> |                                        | Virtual Machines: Edv4-Serie                     | 
> |                                        | Virtual Machines: Esv4-Serie                     | 
> |                                        | Virtual Machines: Ev4-Serie                      | 
> |                                        | Virtual Machines: Fsv2-Serie                     | 
> |                                        | Virtual Machines: M-Serie                        | 
> |                                        | Azure Virtual WAN                                 | 

### <a name="strategic-services"></a>Strategische Dienste
Wie bereits erwähnt, klassifiziert Azure Dienste in drei Kategorien: grundlegend, allgemein und strategisch. Dienstkategorien werden zusammen mit der allgemeinen Verfügbarkeit zugewiesen. Häufig beginnen Dienste ihren Lebenszyklus als strategischer Dienst. Wenn Nachfrage und Nutzung dann zunehmen, können sie zum allgemeinen oder grundlegenden Dienst hochgestuft werden. Die folgende Tabelle führt strategische Dienste auf. 

> [!div class="mx-tableFixed"]
> | ![Ein Symbol, das angibt, dass es sich um einen strategischen Dienst handelt.](media/icon-strategic.svg) Strategisch                                          |
> |------------------------------------------------------|
> | Azure-API für FHIR                                   |
> | Azure Analysis Services                              |
> | Azure Blockchain Service                             |
> | Azure Cognitive Services: Anomalieerkennung           |
> | Azure Cognitive Services: Custom Vision              |
> | Azure Cognitive Services: Formularerkennung            |
> | Azure Cognitive Services: Plastischer Reader           |
> | Azure Cognitive Services: Language Understanding     |
> | Azure Cognitive Services: Personalisierung               |
> | Azure Cognitive Services: QnA Maker                  |
> | Azure Cognitive Services: Spracherkennungsdienste            |
> | Azure Data Share                                     |
> | Azure Databricks                                     |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Dediziertes HSM von Azure                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Azure Red Hat OpenShift                              |
> | Azure SignalR Service                                |
> | Azure Spring Cloud                                   |
> | Azure Stream Analytics                               |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Azure Spatial Anchors                                |
> | Speicher: Archivspeicher                             |
> | Azure Disk Storage Ultra                             |
> | Video Indexer                                        |
> | Virtual Machines: DASv4-Serie                       |
> | Virtual Machines: DAv4-Serie                        |
> | VMs: DCsv2-Serie                       |
> | Virtual Machines: EASv4-Serie                       |
> | Virtual Machines: EAv4-Serie                        |
> | Virtual Machines: HBv1-Serie                        |
> | Virtual Machines: HBv2-Serie                        |
> | Virtual Machines: HCv1-Serie                        |
> | Virtual Machines: H-Serie                           |
> | Virtual Machines: LSv2-Serie                        |
> | Virtual Machines: Mv2-Serie                         |
> | Virtual Machines: NCv3-Serie                        |
> | Virtual Machines: NDv2-Serie                        |
> | Virtual Machines: NVv3-Serie                        |
> | Virtual Machines: NVv4-Serie                        | 
> | VMs: SAP HANA in Azure (große Instanzen)  |

\*VMs mit Unterstützung für Verfügbarkeitszonen: AV2-Serie, B-Serie, DSv2-Serie, DSv3-Serie, Dv2-Serie, Dv3-Serie, ESv3-Serie, Ev3-Serie, F-Serie, FS-Serie, FSv2-Serie und M-Serie.\*

Dienste oder VMs älterer Generationen sind nicht aufgeführt. Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../virtual-machines/sizes-previous-gen.md).

Weitere Informationen zu Vorschaudiensten, die noch nicht allgemein verfügbar sind, und eine Liste dieser Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Eine vollständige Liste der Dienste, die Verfügbarkeitszonen unterstützen, finden Sie unter [Azure-Dienste mit Unterstützung für Verfügbarkeitszonen](az-region.md).

## <a name="next-steps"></a>Nächste Schritte

- [Azure-Dienste mit Unterstützung für Verfügbarkeitszonen](az-region.md)
- [Regionen und Verfügbarkeitszonen in Azure](az-overview.md)
