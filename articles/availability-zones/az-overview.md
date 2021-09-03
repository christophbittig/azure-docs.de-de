---
title: Regionen und Verfügbarkeitszonen in Azure
description: In diesem Artikel erhalten Sie Informationen zu Regionen und Verfügbarkeitszonen in Azure, um technische und gesetzliche Anforderungen erfüllen zu können.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 8d2198c4c2c2dd100d7bbf88eebc4e521d6ace04
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340112"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regionen und Verfügbarkeitszonen in Azure

Microsoft Azure-Dienste sind global verfügbar, um Ihre Cloudvorgänge optimal zu unterstützen. Sie können basierend auf den technischen und gesetzlichen Aspekten die jeweils beste Region für Ihre Anforderungen auswählen: Dienstfunktionen, Data Residency, Konformitätsanforderungen und Wartezeit.

## <a name="terminology"></a>Begriff

Für ein besseres Verständnis von Regionen und Verfügbarkeitszonen in Azure ist ein Verständnis der zentralen Begriffe und Konzepte erforderlich.

| Begriff oder Konzept | BESCHREIBUNG |
| --- | --- |
| region | Mehrere Rechenzentren werden innerhalb eines durch Wartezeit definierten Umkreises bereitgestellt und sind über ein dediziertes regionales Netzwerk mit geringer Wartezeit verbunden. |
| geography | Hierbei handelt es sich um einen Bereich auf der Welt, in dem mindestens eine Azure-Region enthalten ist. Geografien definieren einen separaten Markt, der Data Residency- und Konformitätsgrenzen bewahrt. In Geografien können Kunden mit spezifischen Anforderungen an Datenresidenz und Compliance ihre Daten und Anwendungen eng zusammenhalten. Geografien sind fehlertolerant und erhalten dank ihrer Verbindung mit unserer dedizierten Netzwerkinfrastruktur mit sehr hohen Kapazitäten die Verfügbarkeit auch beim Ausfall einer ganzen Region. |
| Verfügbarkeitszone | Hierbei handelt es sich um physische Orte innerhalb einer Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. |
| Empfohlene Region | Hierbei handelt es sich um eine Region, in der die größte Bandbreite an Dienstfunktionen zur Verfügung gestellt werden kann. Sie ist so entworfen, dass sie Verfügbarkeitszonen jetzt oder in der Zukunft unterstützen kann. Diese Regionen werden im Azure-Portal als **Empfohlen** markiert. |
| Alternative (andere) Region | Hierbei handelt es sich um eine Region, die den Speicherbedarf von Azure innerhalb einer Data Residency-Grenze ausweitet, in der auch eine empfohlene Region vorhanden ist. Alternative Regionen unterstützen die Optimierung von Wartezeit und stellen eine zweite Region für den Fall einer Notfallwiederherstellung bereit. Sie sind nicht so entworfen, dass sie Verfügbarkeitszonen unterstützen, obwohl Azure regelmäßig Bewertungen dieser Regionen vornimmt, um festzustellen, ob sie als empfohlene Regionen deklariert werden können. Diese Regionen werden im Azure-Portal als **Andere** markiert. |
| Grundlegender Dienst | Hierbei handelt es sich um einen Azure-Kerndienst, der in allen Regionen verfügbar ist, wenn die Region allgemein verfügbar ist. |
| Hauptdienst | Hierbei handelt es sich um einen Azure-Dienst, der in allen empfohlenen Regionen innerhalb von 90 Tagen nach allgemeiner Verfügbarkeit einer Region verfügbar ist. In alternativen Regionen ist die Verfügbarkeit von der Nachfrage abhängig. |
| Spezialisierter Dienst | Hierbei handelt es sich um einen Dienst, dessen Verfügbarkeit von der Nachfrage in Regionen abhängt und der von angepasster/spezieller Hardware unterstützt wird. |
| Regionaler Dienst | Hierbei handelt es sich um einen Dienst, der regional bereitgestellt wird und es Kunden ermöglicht, die Region anzugeben, in der der Dienst bereitgestellt werden soll. Eine vollständige Liste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| Nicht-regionaler Dienst | Hierbei handelt es sich um einen Azure-Dienst, der keine Abhängigkeit von einer bestimmten Azure-Region aufweist. Nicht-regionale Dienste werden in zwei oder mehr Regionen bereitgestellt, und bei einem regionalen Ausfall wird der Dienst für die Kunden über eine Instanz in einer anderen Region weiterhin bereitgestellt. Eine vollständige Liste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regions

Eine Region ist eine Reihe von Rechenzentren, die innerhalb eines durch Wartezeit definierten Umkreises bereitgestellt und über ein dediziertes regionales Netzwerk mit geringer Wartezeit verbunden sind. Dank Azure haben Sie die Flexibilität, Anwendungen dort bereitzustellen, wo es für Sie erforderlich ist, auch in mehreren Regionen, um so eine regionsübergreifende Resilienz bieten zu können. Weitere Informationen finden Sie in der [Übersicht über die Säule „Resilienz“](/azure/architecture/framework/resiliency/principles).

## <a name="availability-zones"></a>Verfügbarkeitszonen

Verfügbarkeitszonen sind ein Hochverfügbarkeitsangebot, das Anwendungen und Daten vor Ausfällen von Rechenzentren schützt. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region schützt Anwendungen und Daten vor Ausfällen von Rechenzentren. Zonenredundante Dienste replizieren Ihre Anwendungen und Daten zum Schutz vor einzelnen Fehlerquellen über Verfügbarkeitszonen hinweg. Mit Verfügbarkeitszonen bietet Azure die branchenweit beste Betriebszeit-SLA von 99,99 % für VMs. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.

Eine Verfügbarkeitszone in einer Azure-Region ist eine Kombination aus einer Fehlerdomäne und einer Updatedomäne. Wenn Sie z.B. drei oder mehr virtuelle Computer über drei Zonen verteilt in einer Azure-Region erstellen, werden Ihre virtuellen Computer effektiv auf drei Fehlerdomänen und drei Updatedomänen verteilt. Die Azure-Plattform erkennt diese Updatedomänen übergreifende Verteilung, um sicherzustellen, dass die Aktualisierung von virtuellen Computern in unterschiedlichen Zonen nicht für den gleichen Zeitraum geplant wird.

Integrieren Sie hohe Verfügbarkeit in Ihre Anwendungsarchitektur, indem Sie Ihre Compute-, Speicher-, Netzwerk- und Datenressourcen in eine Zone aufnehmen und in anderen Zonen replizieren. Azure-Dienste, die Verfügbarkeitszonen unterstützen, können in zwei Kategorien unterteilt werden:

- **Zonendienste:** Sie binden die Ressource in eine bestimmte Zone ein (z. B. VMs, verwaltete Datenträger, Standard-IP-Adressen).
- **Zonenredundante Dienste:** Die Azure-Plattform repliziert automatisch zonenübergreifend (z. B. zonenredundanter Speicher, SQL-Datenbank).

> [!NOTE]
> Sowohl öffentliche IP-Adressen der Standard-SKU als auch Ressourcentypen des Präfixes für öffentliche IP-Adressen verfügen ebenfalls über die Option „no-zone“ (keine Zone).  Dies ermöglicht Kunden die Nutzung öffentlicher IP-Adressen der Standard-SKU (und deren Zuordnung zu Ressourcen, die nur die Standard-SKU zulassen), gibt aber keine Garantie für Redundanz.  (Alle öffentlichen IP-Adressen, die von der Basic-SKU auf die Standard-SKU [geupgradet](https://docs.microsoft.com/azure/virtual-network/public-ip-upgrade-portal) werden, haben den Typ „no-zone“.)

Um eine umfassende Geschäftskontinuität in Azure zu erreichen, erstellen Sie die Anwendungsarchitektur über die Kombination von Verfügbarkeitszonen mit Azure-Regionspaaren. Sie können Anwendungen und Daten mithilfe von Verfügbarkeitszonen für hohe Verfügbarkeit innerhalb einer Azure-Region synchron replizieren und für Notfallwiederherstellungen über Azure-Regionen asynchron replizieren.
 
![Konzeptionelle Ansicht einer Zone, die in einer Region ausfällt](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Die Verfügbarkeitszonenbezeichner (die Zahlen 1, 2 und 3 in der vorherigen Abbildung) werden den tatsächlichen physischen Zonen der einzelnen Abonnements unabhängig logisch zugeordnet. Dies bedeutet, dass die Verfügbarkeitszone 1 in einem bestimmten Abonnement möglicherweise auf eine andere physische Zone als die Verfügbarkeitszone 1 in einem anderen Abonnement verweist. Folglich empfiehlt es sich, in Verfügbarkeitszonen-IDs nicht über verschiedene Abonnements hinweg bei der Platzierung virtueller Computer ein Relay durchzuführen.

## <a name="region-and-service-categories"></a>Regions- und Dienstkategorien

Der Ansatz an die Verfügbarkeit von Azure für Azure-Dienste in Regionen kann am besten dadurch beschrieben werden, dass Dienste in empfohlenen und alternativen Regionen verfügbar gemacht werden.

- **Empfohlene Region:** Hierbei handelt es sich um eine Region, in der die größte Bandbreite an Dienstfunktionen zur Verfügung gestellt werden kann. Sie ist so entworfen, dass sie Verfügbarkeitszonen jetzt oder in der Zukunft unterstützen kann. Diese Regionen werden im Azure-Portal als **Empfohlen** markiert.
- **Alternative (andere) Region:** Hierbei handelt es sich um eine Region, die den Speicherbedarf von Azure innerhalb einer Data Residency-Grenze ausweitet, in der auch eine empfohlene Region vorhanden ist. Alternative Regionen unterstützen die Optimierung von Wartezeit und stellen eine zweite Region für den Fall einer Notfallwiederherstellung bereit. Sie sind nicht so entworfen, dass sie Verfügbarkeitszonen unterstützen, obwohl Azure regelmäßig Bewertungen dieser Regionen vornimmt, um festzustellen, ob sie als empfohlene Regionen deklariert werden können. Diese Regionen werden im Azure-Portal als **Andere** markiert.

### <a name="comparing-region-types"></a>Vergleich von Regionstypen

Azure-Dienste werden in drei Kategorien gruppiert: grundlegende, Haupt- und spezialisierte Dienste. Die allgemeine Richtlinie von Azure für die Bereitstellung von Diensten in eine beliebige Region wird hauptsächlich vom Typ der Region, der Kategorie eines Diensts und der Nachfrage durch Kunden gesteuert:

- **Grundlegend:** Dies bedeutet, der Dienst ist in allen empfohlenen und alternativen Regionen verfügbar, wenn die Region allgemein verfügbar ist, oder spätestens innerhalb von 90 Tagen nach dem Zeitpunkt, an dem ein neuer grundlegender Dienst allgemein verfügbar gemacht wird.
- **Hauptdienst:** Diese Dienste sind in allen empfohlenen Regionen innerhalb von 90 Tagen nach der allgemeinen Verfügbarkeit einer Region verfügbar. In alternativen Regionen ist die Verfügbarkeit von der Nachfrage abhängig. Viele Hauptdienste wurden bereits in einer großen Teilmenge alternativer Regionen bereitgestellt.
- **Spezialisiert:** Hier handelt es sich um gezielte Dienstangebote, die oft auf eine bestimmte Branche abzielen oder die von einer angepassten oder spezialisierten Hardware unterstützt werden. Die Verfügbarkeit hier wird von der Nachfrage in den einzelnen Regionen gesteuert. Viele dieser Dienste wurden bereits in einer großen Teilmenge empfohlener Regionen bereitgestellt.

Wenn Sie erfahren möchten, welche Dienste in einer bestimmten Region bereitgestellt wurden und Informationen zu zukünftigen Vorschauversionen oder der allgemeinen Verfügbarkeit von Diensten in einer Region erhalten möchten, sehen Sie sich die Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all) an.

Wenn ein Dienstangebot in einer bestimmten Region nicht verfügbar ist, können Sie Ihr Interesse an einem Dienst mitteilen, indem Sie sich an einen Vertriebsmitarbeiter von Microsoft wenden.

| Regionstyp | Nicht regional | Grundlegend | Hauptdienst | Spezialisiert | Verfügbarkeitszonen | Datenresidenz |
| --- | --- | --- | --- | --- | --- | --- |
| Empfohlen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Nachfragegesteuert | :heavy_check_mark: | :heavy_check_mark: |
| Alternativ | :heavy_check_mark: | :heavy_check_mark: | Nachfragegesteuert | Nachfragegesteuert | – | :heavy_check_mark: |

### <a name="services-by-category"></a>Dienste nach Kategorie

Wie bereits erwähnt wurde, klassifiziert Azure Dienste in drei Kategorien: grundlegende, Haupt- und spezialisierte Dienste. Dienstkategorien werden zusammen mit der allgemeinen Verfügbarkeit zugewiesen. Oft beginnen Dienste ihren Lebenszyklus als spezialisierter Dienst. Wenn die Nachfrage und die Nutzung dann zunehmen, können sie zum Haupt- oder grundlegenden Dienst erklärt werden. In der folgenden Tabelle sind die Kategorien von Diensten als grundlegender Dienst oder Hauptdienst aufgeführt. Beachten Sie die folgenden Hinweise zur Tabelle:

- Einige Dienste sind nicht-regional. Weitere Informationen und eine Liste nicht-regionaler Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).
- Die Dienste oder VMs älterer Generationen sind nicht aufgeführt. Weitere Informationen finden Sie unter [Vorherige Generationen von Größen virtueller Computer](../virtual-machines/sizes-previous-gen.md).
- Dienste werden erst bei allgemeiner Verfügbarkeit (GA) einer Kategorie zugeordnet. Weitere Informationen und eine Liste mit Vorschaudiensten finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Grundlegend                           | Hauptdienst                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Speicherkonten                       | API Management                                    | 
> | Application Gateway                    | App Configuration                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | Automation                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure: öffentliche IP-Adresse                        | Azure Cache for Redis                             | 
> | Azure SQL-Datenbank                     | Azure Cognitive Services                          | 
> | Verwaltete Azure SQL-Instanz             | Azure Cognitive Services: Maschinelles Sehen         | 
> | Disk Storage                           | Azure Cognitive Services: Content Moderator       | 
> | Event Hubs                             | Azure Cognitive Services: Gesicht                    | 
> | Key Vault                              | Azure Cognitive Services: Textanalyse          | 
> | Load Balancer                          | Azure-Daten-Explorer                               | 
> | Service Bus                            | Azure Database for MySQL                          | 
> | Service Fabric                         | Azure Database for PostgreSQL                     | 
> | Speicher: Heiße/Kalte Blobspeicherebenen   | Azure DDoS Protection                             | 
> | Speicher: Managed Disks                 | Azure Firewall                                    | 
> | Virtual Machine Scale Sets             | Azure Firewall Manager                            | 
> | Virtual Machines                       | Azure-Funktionen                                   | 
> | Virtual Machines: Azure Dedicated Host | Azure IoT Hub                                     | 
> | VMs: Av2-Serie           | Azure Kubernetes Service (AKS)                    | 
> | VMs: Bs-Serie            | Azure Monitor: Application Insights               | 
> | VMs: DSv2-Serie          | Azure Monitor: Log Analytics                      | 
> | VMs: DSv3-Serie          | Azure Private Link                                | 
> | VMs: Dv2-Serie           | Azure Site Recovery                               | 
> | VMs: Dv3-Serie           | Azure Synapse Analytics                           |     
> | VMs: ESv3-Serie          | Batch                                             | 
> | VMs: Ev3-Serie           | Cloud Services: M-Serie                          | 
> | Virtual Network                        | Container Instances                               | 
> | VPN Gateway                            | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Network Watcher                                   | 
> |                                        | Blob Storage Premium                              | 
> |                                        | File Storage (Premium)                             | 
> |                                        | VMs: Ddsv4-Serie                    | 
> |                                        | VMs: Ddv4-Serie                     | 
> |                                        | VMs: Dsv4-Serie                     | 
> |                                        | VMs: Dv4-Serie                      | 
> |                                        | VMs: Edsv4-Serie                    | 
> |                                        | VMs: Edv4-Serie                     | 
> |                                        | VMs: Esv4-Serie                     | 
> |                                        | VMs: Ev4-Serie                      | 
> |                                        | VMs: Fsv2-Serie                     | 
> |                                        | VMs: M-Serie                        | 
> |                                        | Virtuelles WAN                                       | 



### <a name="specialized-services"></a>Spezialisierte Dienste
Wie bereits erwähnt wurde, klassifiziert Azure Dienste in drei Kategorien: grundlegende, Haupt- und spezialisierte Dienste. Dienstkategorien werden zusammen mit der allgemeinen Verfügbarkeit zugewiesen. Oft beginnen Dienste ihren Lebenszyklus als spezialisierter Dienst. Wenn die Nachfrage und die Nutzung dann zunehmen, können sie zum Haupt- oder grundlegenden Dienst erklärt werden. In der folgenden Tabelle sind spezialisierte Dienste aufgeführt. 

> [!div class="mx-tableFixed"]
> | Spezialisiert                                          |
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
> | Spatial Anchors                                      |
> | Speicher: Archivspeicher                             |
> | Disk Storage Ultra                                   |
> | Video Indexer                                        |
> | VMs: DASv4-Serie                       |
> | VMs: DAv4-Serie                        |
> | VMs: DCsv2-Serie                       |
> | VMs: EASv4-Serie                       |
> | VMs: EAv4-Serie                        |
> | VMs: HBv1-Serie                        |
> | VMs: HBv2-Serie                        |
> | VMs: HCv1-Serie                        |
> | VMs: H-Serie                           |
> | VMs: LSv2-Serie                        |
> | VMs: Mv2-Serie                         |
> | VMs: NCv3-Serie                        |
> | VMs: NDv2-Serie                        |
> | VMs: NVv3-Serie                        |
> | VMs: NVv4-Serie                        | 
> | VMs: SAP HANA in Azure (große Instanzen)  |




## <a name="next-steps"></a>Nächste Schritte

- [Regionen, die Verfügbarkeitszonen in Azure unterstützen](az-region.md)
- [Schnellstartvorlagen](https://aka.ms/azqs)
