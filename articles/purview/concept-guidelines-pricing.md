---
title: Richtlinien für Purview-Preise
description: Dieser Artikel bietet einen Leitfaden zum Verstehen der verschiedenen Komponenten der Purview-Preisgestaltung.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: conceptual
ms.date: 10/03/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ee7364637e79b2603c6eca31d986dbcc3e153db3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084310"
---
# <a name="azure-purview-pricing"></a>Azure Purview Preisgestaltung   

Azure Purview ermöglicht eine einheitliche Governance-Erfahrung, indem es ein einziges Fenster für die Verwaltung von Data Governance bietet, indem es automatisches Scannen und Klassifizieren von Daten in großem Umfang ermöglicht.


## <a name="intended-audience"></a>Zielpublikum

- Data Governance und Verwaltungsteams

## <a name="why-do-you-need-to-understand-the-components-of-the-azure-purview-pricing"></a>Warum müssen Sie die Komponenten der Preisgestaltung von Azure Purview verstehen? 

- Obwohl die Preisgestaltung für Azure Purview auf einem abonnementbasierten **Nutzungsbasierte Zahlung** Modell beruht, gibt es verschiedene Dimensionen, die Sie bei der Budgetierung für Purview berücksichtigen können
- Dieser Leitfaden soll Ihnen helfen, die Budgetierung für Purview zu planen, indem er einen Überblick über die verschiedenen Kontrollfaktoren gibt, die das Budget beeinflussen


## <a name="factors-impacting-azure-pricing"></a>Faktoren, die sich auf die Azure-Preisgestaltung auswirken  
Es gibt **direkte** und **indirekte** Kosten, die bei der Planung der Budgetierung und des Kostenmanagements von Purview berücksichtigt werden müssen.

### <a name="direct-costs"></a>Direkte Kosten

Die direkten Kosten, die sich auf die Preisgestaltung von Azure Purview auswirken, basieren auf den folgenden drei Dimensionen:
- **Elastische Data Map**
- **Automatisiertes Scannen & Klassifizierung**
- **Erweiterte Ressourcen-Sets**

#### <a name="elastic-data-map"></a>Elastische Data Map

- Die **Datenkarte** ist die Grundlage der Purview-Architektur und muss daher zu jedem Zeitpunkt mit den Bestandsinformationen im Datenbestand auf dem neuesten Stand sein

- Die Datenkarte wird in Form von **Kapazitätseinheiten** (CU) berechnet. Die Datenkarte wird für eine CU bereitgestellt, wenn der Katalog bis zu 2 GB an Metadaten speichert und bis zu 25 Datenkartenoperationen pro Sekunde bedient

- Bei der Erstbereitstellung eines Kontos wird die Datenabbildung immer mit einer CU bereitgestellt

- Die Datenkarte skaliert jedoch automatisch zwischen der minimalen und der maximalen Grenze dieses Elastizitätsfensters, um Änderungen der Datenkarte in Bezug auf zwei Schlüsselfaktoren - **Durchsatz** und **Metadatenspeicher** - zu berücksichtigen

##### <a name="operation-throughput"></a>Operationeller Durchsatz

- Ein ereignisgesteuerter Faktor, der auf den auf der Datenkarte durchgeführten Operationen Erstellen, Lesen, Aktualisieren, Löschen basiert
- Einige Beispiele für Operationen in der Datenübersicht sind:       - Erstellen eines Assets in der Data Map - Hinzufügen einer Beziehung zu einem Asset wie Eigentümer, Verwalter, übergeordnetes Element, Abstammung - Bearbeiten eines Assets, um geschäftliche Metadaten wie Beschreibung, Glossarbegriff hinzuzufügen - Stichwortsuche mit Rückgabe der Ergebnisse auf der Suchergebnisseite - Importieren oder Exportieren von Informationen über API
- Wenn mehrere Abfragen auf der Data Map ausgeführt werden, erhöht sich auch die Anzahl der E/A-Operationen, was zu einer Vergrößerung der Data Map führt
- Die Anzahl der gleichzeitigen Benutzer ist ebenfalls ein Faktor, der die Kapazitätseinheit der Data Map bestimmt
- Weitere zu berücksichtigende Faktoren sind die Art der Suchabfrage, API-Interaktion, Arbeitsabläufe, Genehmigungen usw,
- Daten-Burst-Level
    - Wenn ein höherer Durchsatz an Operationen/Sekunde erforderlich ist, kann die Datenkarte innerhalb des Elastizitätsfensters automatisch skaliert werden, um der veränderten Last gerecht zu werden
    - Dies ist die **Burst-Charakteristik**, die abgeschätzt und eingeplant werden muss
    - Die Burst-Charakteristik umfasst den **Burst-Level** und die **Burst-Dauer**, für die der Burst besteht
        - Der **Burstlevel** ist ein multiplikativer Index der erwarteten konsistenten Elastizität im eingeschwungenen Zustand
        - Die **Burstdauer** ist der prozentuale Anteil des Monats, für den solche Bursts (in der Elastizität) aufgrund wachsender Metadaten oder einer höheren Anzahl von Operationen auf der Datenkarte erwartet werden


##### <a name="metadata-storage"></a>Metadatenspeicherung

- Verringert sich die Anzahl der Assets im Datenbestand und werden diese durch nachfolgende inkrementelle Scans aus der Data Map entfernt, wird die Speicherkomponente automatisch reduziert und damit die Data Map verkleinert


#### <a name="automated-scanning--classification"></a>Automatisches Scannen und Klassifizierung

- Ein **vollständiger Scan** verarbeitet alle Assets innerhalb eines ausgewählten Bereichs einer Datenquelle, während ein **inkrementeller Scan** die Assets erkennt und verarbeitet, die seit dem letzten erfolgreichen Scan erstellt, geändert oder gelöscht wurden 

- Alle Scans (vollständige oder inkrementelle Scans) erfassen **aktualisierte, geänderte oder gelöschte** Assets

- Es ist wichtig, Szenarien zu berücksichtigen und zu vermeiden, in denen mehrere Personen oder Gruppen, die zu verschiedenen Abteilungen gehören, Scans für dieselbe Datenquelle einrichten, was zu zusätzlichen Kosten für doppelte Scans führt

- Es wird empfohlen, nach dem ersten vollständigen Scan  **häufige inkrementelle Scans** zu planen, die auf die Änderungen im Datenbestand abgestimmt sind. Dadurch wird sichergestellt, dass die Data Map immer auf dem neuesten Stand ist und die inkrementellen Scans im Vergleich zu einem vollständigen Scan weniger v-core Stunden verbrauchen

- Über den Link **"Details anzeigen"** für eine Datenquelle können Benutzer einen vollständigen Scan durchführen. Es wird jedoch empfohlen, inkrementelle Scans nach einem vollständigen Scan durchzuführen, um das Scannen zu optimieren, es sei denn, es gibt eine Änderung des Scan-Regelsatzes (Klassifizierungen/Dateitypen)

- Es wird empfohlen, **die Datenquelle in einer übergeordneten Sammlung** zu registrieren und **Scans in untergeordneten Sammlungen** mit unterschiedlichen Zugriffskontrollen durchzuführen, um sicherzustellen, dass keine doppelten Scan-Kosten anfallen

- Es wird empfohlen, die Benutzer, die Datenquellen zum Scannen registrieren dürfen, durch **feinkörnige Zugriffskontrolle** und **Datenquellenadministrator**-Rolle mit [Sammlungsautorisierung](./catalog-permissions.md) einzuschränken. Dadurch wird sichergestellt, dass nur gültige Datenquellen registriert werden können und die Scanstunden des v-core kontrolliert werden, was zu geringeren Kosten für das Scannen führt

- Beachten Sie, dass der **Typ der Datenquelle** und die **Anzahl der zu überprüfenden Assets** die Dauer der Überprüfung beeinflussen

- Es wird empfohlen, **benutzerdefinierte Scan-Regelsätze** zu erstellen, die nur die Teilmenge der in Ihrem Datenbestand verfügbaren **Dateitypen** und **Klassifizierungen** umfassen, die für Ihre Geschäftsanforderungen relevant sind, um eine optimale Nutzung der Scanner zu gewährleisten

- Bei der Erstellung eines neuen Scans für eine Datenquelle empfiehlt es sich, die **Vorbereitungsreihenfolge** zu befolgen, die vor der eigentlichen Durchführung des Scans empfohlen wird. Dazu gehört das Erfassen der Anforderungen für **geschäftsspezifische Klassifikationen** und **Dateitypen** (für Speicherkonten), damit geeignete Scan-Regelsätze definiert werden können, um Mehrfachscans zu vermeiden und unnötige Kosten für Mehrfachscans durch fehlende Anforderungen zu kontrollieren

- Es wird empfohlen, die Scan-Zeitpläne an die Größe der Self-Hosted Integration Runtime (SHIR) VMs (Virtual Machines) anzupassen, um zusätzliche Kosten im Zusammenhang mit virtuellen Maschinen zu vermeiden


#### <a name="advanced-resource-sets"></a>Erweiterte Ressourcen-Sets

- Azure Purview verwendet **Ressourcensets**, um die Herausforderung zu bewältigen, eine große Anzahl von Datenbeständen einer einzigen logischen Ressource zuzuordnen, indem es die Möglichkeit bietet, alle Dateien im Data Lake zu scannen und Muster (GUID, Lokalisierungsmuster usw.) zu finden, um sie als einen einzigen Bestand in der Datenübersicht zu gruppieren

- **Erweiterte Ressourcengruppe** ist eine optionale Funktion, die es Kunden ermöglicht, erweiterte Ressourcengruppeninformationen wie Gesamtgröße, Partitionsanzahl usw. zu berechnen und die Gruppierung von Ressourcengruppen über Musterregeln anzupassen. Wenn die erweiterte Ressourcensatz-Funktion nicht aktiviert ist, enthält Ihr Datenkatalog weiterhin Ressourcensatz-Assets, jedoch ohne die aggregierten Eigenschaften. In diesem Fall wird dem Kunden kein "Resource Set"-Zähler in Rechnung gestellt.

- Es wird empfohlen, die grundlegende Ressourcensatz-Funktion zu verwenden, bevor Sie die erweiterten Ressourcensätze in Purview aktivieren, um zu überprüfen, ob die Anforderungen erfüllt sind

- Ziehen Sie die Aktivierung von Advanced Resource Sets in Betracht, wenn:
    - Ihr Data Lakes Schema sich ständig ändert und Sie einen zusätzlichen Nutzen über das Basis-Ressourcenset-Feature hinaus suchen, um Purview in die Lage zu versetzen, Parameter wie z.B. #Partitionen, Größe des Datenbestands, etc. als Service zu berechnen
    - es besteht die Notwendigkeit, die Gruppierung von Ressourcensatz-Assets anzupassen 

- Es ist wichtig zu beachten, dass die Abrechnung für erweiterte Ressourcensets auf der Rechenleistung basiert, die von der Offline-Ebene zur Aggregation von Ressourcenset-Informationen verwendet wird, und von der Größe/Anzahl der Ressourcensets in Ihrem Katalog abhängt


### <a name="indirect-costs"></a>Indirekte Kosten   

Indirekte Kosten, die sich auf die Preisgestaltung von Azure Purview auswirken, sind zu berücksichtigen:

- [Verwaltete Ressourcen](https://azure.microsoft.com/pricing/details/azure-purview/)
    - Bei der Einrichtung eines Purview-Kontos werden innerhalb des Abonnements ein Speicherkonto und eine Event-Hub-Warteschlange für das gesicherte Scannen erstellt, die separat berechnet werden können


- [Azure privater Endpunkt](./catalog-private-link.md)
    - Private Azure-Endpunkte werden für Purview-Konten verwendet, bei denen es erforderlich ist, dass Benutzer in einem virtuellen Netzwerk (VNet) über eine private Verbindung sicher auf den Katalog zugreifen können
    - Die Voraussetzungen für die Einrichtung privater Endpunkte können zu zusätzlichen Kosten führen

- [Laufzeitkosten der selbst gehosteten Integration](./manage-integration-runtimes.md) 
    - Die selbst gehostete Integrationslaufzeit erfordert eine Infrastruktur, die zusätzliche Kosten verursacht
    - Es ist erforderlich, die selbst gehostete Integrationslaufzeit (SHIR) innerhalb desselben virtuellen Netzwerks zu implementieren und zu registrieren, in dem auch die privater Endpunkte für die Azure Purview-Ingestion implementiert sind.
    - [Zusätzlicher Speicherbedarf für das Scannen](./register-scan-sapecc-source.md#create-and-run-scan)
        - Bestimmte Datenquellen wie SAP erfordern zusätzlichen Speicher auf der SHIR-Maschine zum Scannen


- [Dimensionierung virtueller Maschinen](../virtual-machines/sizes.md)
    - Planen Sie die Dimensionierung der virtuellen Maschine, um die Scan-Arbeitslast auf die VMs zu verteilen und die genutzten V-Cores bei der Durchführung von Scans zu optimieren

- [Microsoft 365-Lizenz](./create-sensitivity-label.md) 
    - Microsoft Information Protection (MIP) Sensitivitätskennzeichnungen können automatisch auf Ihre Azure-Assets in Azure Purview angewendet werden.
    - Die MIP-Vertraulichkeitsbezeichnungen werden im Microsoft 365 Security and Compliance Center erstellt und verwaltet.
    - Um Sensitivitätskennzeichnungen für die Verwendung in Azure Purview zu erstellen, müssen Sie über eine aktive Microsoft 365-Lizenz verfügen, die den Vorteil der automatischen Kennzeichnung bietet. Eine vollständige Liste der Lizenzen finden Sie in den FAQ zu Sensitivitätskennzeichnungen in Azure Purview. 

- [Azure-Warnungen](../azure-monitor/alerts/alerts-overview.md)
    - Azure Alerts kann Kunden über Probleme mit der Infrastruktur oder Anwendungen benachrichtigen, die anhand der Überwachungsdaten in Azure Monitor gefunden wurden
    - Die zusätzlichen Preise für Azure Alerts finden Sie [hier](https://azure.microsoft.com/pricing/details/monitor/)

- [Cost Management Budgets & Alerts](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md)
    - Automatisch generierte Kostenwarnungen werden in Azure verwendet, um die Azure-Nutzung und -Ausgaben zu überwachen, je nachdem, wann die Azure-Ressourcen verbraucht werden
    - Mit Azure können Sie Azure-Budgets erstellen und verwalten. Siehe [Tutorial](../cost-management-billing/costs/tutorial-acm-create-budgets.md)

- Multi-cloud Ausgehende Gebühren
    - Berücksichtigen Sie die ausgehenden Gebühren (minimale Gebühren, die als Teil des Multi-Cloud-Abonnements hinzugefügt werden), die mit dem Scannen von Multi-Cloud-Datenquellen (z. B. AWS, Google) verbunden sind, die mit Ausnahme der S3- und RDS-Quellen native Dienste ausführen


## <a name="next-steps"></a>Nächste Schritte
- [Azure Purview-Preisseite](https://azure.microsoft.com/pricing/details/azure-purview/)
