---
title: Azure DevTest Labs-Nutzung für mehrere Labs und Abonnements
description: Es wird beschrieben, wie Sie die Azure DevTest Labs-Nutzung für mehrere Labs und Abonnements melden.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 671941e259bd1329dab3e30c1c95eb77ed3091f9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642831"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Melden der Azure DevTest Labs-Nutzung für mehrere Labs und Abonnements

Die meisten größeren Organisationen möchten die Ressourcennutzung nachverfolgen, um die Effektivität zu erhöhen, indem Trends und Ausreißer in Bezug auf die Nutzung visualisiert werden. Basierend auf der Ressourcennutzung können die Lab-Besitzer oder -Manager die Labs so anpassen, dass eine [Verbesserung der Ressourcennutzung und der Kosten](../cost-management-billing/cost-management-billing-overview.md) erzielt wird. In Azure DevTest Labs können Sie die Ressourcennutzung pro Lab herunterladen, um einen tieferen Einblick in den Verlauf und die Nutzungsmuster zu erhalten. Diese Nutzungsmuster können bei der Ermittlung von Änderungen hilfreich sein, um die Effizienz zu erhöhen. Die meisten Unternehmen benötigen sowohl Informationen zur Nutzung einzelner Labs als auch übergreifend zur allgemeinen Nutzung [mehrerer Labs und Abonnements](/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

In diesem Artikel wird beschrieben, wie Sie Informationen zur Ressourcennutzung für mehrere Labs und Abonnements verarbeiten.

![Melden der Nutzung](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Nutzung eines einzelnen Labs

In diesem Abschnitt wird beschrieben, wie Sie die Ressourcennutzung für ein einzelnes Lab exportieren.

Bevor Sie die Ressourcennutzung von DevTest Labs exportieren können, müssen Sie ein Azure Storage-Konto einrichten, damit die unterschiedlichen Dateien mit den Nutzungsdaten gespeichert werden können. Es gibt zwei gängige Möglichkeiten, den Export von Daten durchzuführen:

* [DevTest Labs-REST-API](/rest/api/dtl/labs/exportresourceusage) 
* Das PowerShell-Az.Resource-Modul [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) mit der Aktion `exportResourceUsage`, der Lab-Ressourcen-ID und den erforderlichen Parametern. 

    Der Artikel [Exportieren oder Löschen personenbezogener Daten aus Azure DevTest Labs](personal-data-delete-export.md) enthält ein PowerShell-Beispielskript mit ausführlichen Informationen zu den Daten, die exportiert werden. 

    > [!NOTE]
    > Da der Datumsparameter keinen Zeitstempel hat, sind alle Daten ab Mitternacht enthalten (basierend auf der Zeitzone, in der sich das Lab befindet).

Nach Abschluss des Exports enthält der Blobspeicher mehrere CSV-Dateien mit den verschiedenen Ressourceninformationen.
  
Derzeit sind zwei CSV-Dateien vorhanden:

* *virtualmachines.csv* enthält Informationen zu den virtuellen Computern im Lab.
* *disks.csv* enthält Informationen zu den unterschiedlichen Datenträgern des Labs. 

Diese Dateien werden im Blobcontainer *labresourceusage* unter dem Namen des Labs gespeichert. Außerdem sind die eindeutige ID des Labs, das Ausführungsdatum und das vollständige Datum bzw. Startdatum für die Exportanforderung angegeben. Beispiel für eine Blobstruktur:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Exportieren der Nutzung für alle Labs

Sie können zum Exportieren der Nutzungsinformationen für mehrere Labs beispielsweise Folgendes verwenden: 

* [Azure Functions](../azure-functions/index.yml): Für viele Sprachen verfügbar, z. B. PowerShell. 
* [Azure Automation-Runbook](../automation/index.yml): Verwenden Sie PowerShell, Python oder einen benutzerdefinierten grafischen Designer, um den Exportcode zu schreiben.

Mit diesen Technologien können Sie die einzelnen Lab-Exporte für alle Labs zu einem bestimmten Datum und einer bestimmten Uhrzeit durchführen. 

Ihre Azure-Funktion sollte die Daten per Pushvorgang in den Langzeitspeicher übertragen. Beim Exportieren von Daten für mehrere Labs kann der Export etwas länger dauern. Wir empfehlen Ihnen für die Labs eine parallele Durchführung, um die Leistung zu verbessern und die Wahrscheinlichkeit einer Duplizierung von Informationen zu verringern. Führen Sie Azure Functions asynchron aus, um die Parallelität zu ermöglichen. Nutzen Sie auch den Zeitgebertrigger von Azure Functions.

## <a name="using-a-long-term-storage"></a>Nutzen eines Langzeitspeichers

Bei Nutzung eines Langzeitspeichers werden die Exportinformationen aus verschiedenen Labs in einer einzelnen Datenquelle zusammengefasst. Ein weiterer Vorteil des Langzeitspeichers besteht darin, dass Sie die Dateien aus dem Speicherkonto entfernen können, um Duplizierungen und Kosten zu reduzieren. 

Der Langzeitspeicher kann verwendet werden, um Textbearbeitungen durchzuführen, z. B.: 

* Hinzufügen von Anzeigenamen
* Erstellen komplexer Gruppierungen
* Aggregieren der Daten

Einige gängige Speicherlösungen sind: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/) und [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Die Entscheidung, welche Langzeitspeicher-Lösung Sie wählen, hängt von Ihren Vorlieben ab. Sie können bei der Auswahl des Tools beispielsweise darauf achten, welche Optionen in Bezug auf die Interaktionsverfügbarkeit beim Visualisieren der Daten vorhanden sind.

## <a name="visualizing-data-and-gathering-insights"></a>Visualisieren von Daten und Sammeln von Erkenntnissen

Nutzen Sie ein Datenvisualisierungstool Ihrer Wahl, um eine Verbindung mit Ihrem Langzeitspeicher herzustellen und die Nutzungsdaten anzuzeigen, damit Sie Erkenntnisse zur Nutzungseffizienz sammeln können. Beispielsweise können Sie [Power BI](/power-bi/power-bi-overview) verwenden, um die Nutzungsdaten zu organisieren und anzuzeigen. 

Sie können [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) verwenden, um Ihre Ressourcen an einem zentralen Ort zu erstellen, zu verlinken und zu verwalten. Falls eine stärkere Kontrolle erforderlich ist, können Sie einzelne Ressourcen in einer einzelnen Ressourcengruppe erstellen und unabhängig vom Data Factory-Dienst verwalten.  

## <a name="next-steps"></a>Nächste Schritte

Nachdem das System eingerichtet wurde und die Verschiebung der Daten in den Langzeitspeicher begonnen hat, ist der nächste Schritt das Ermitteln der Fragen, die anhand der Daten beantwortet werden müssen. Beispiel: 

-   Welche VM-Größen werden genutzt?

    Wählen Benutzer VM-Größen für Hochleistung aus (höhere Kosten)?
-   Welche Marketplace-Images werden verwendet?

    Werden benutzerdefinierte Images am häufigsten als VM-Basis verwendet, und sollte ein gemeinsamer Imagespeicher erstellt werden, z. B. [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) oder [Image Factory](image-factory-create.md)?
-   Welche benutzerdefinierten Images werden verwendet bzw. nicht verwendet?