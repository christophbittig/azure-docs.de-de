---
title: Azure DevTest Labs-Nutzung für mehrere Labs und Abonnements
description: Es wird beschrieben, wie Sie die Azure DevTest Labs-Nutzung für mehrere Labs und Abonnements melden.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: d2bf6954b629c3a9fc28569a86df4aa61f5dc94f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401039"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Melden der Azure DevTest Labs-Nutzung für mehrere Labs und Abonnements

Die meisten großen Organisationen möchten die Ressourcennutzung nachverfolgen, um Trends und Ausreißer effektiver zu visualisieren. Basierend auf der Ressourcennutzung können Labbesitzer oder -manager Labs anpassen, um die [Ressourcennutzung und die Kosten](../cost-management-billing/cost-management-billing-overview.md)zu verbessern. In Azure DevTest Labs können Sie die Ressourcennutzung pro Lab herunterladen, um einen tieferen Einblick in den Verlauf und die Nutzungsmuster zu erhalten. Diese Verwendungsmuster helfen dabei, Änderungen zu ermitteln, um die Effizienz zu verbessern. Die meisten Unternehmen benötigen sowohl Informationen zur Nutzung einzelner Labs als auch übergreifend zur allgemeinen Nutzung [mehrerer Labs und Abonnements](/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

In diesem Artikel wird beschrieben, wie Sie Informationen zur Ressourcennutzung für mehrere Labs und Abonnements verarbeiten.

![Melden der Nutzung](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Nutzung eines einzelnen Labs

In diesem Abschnitt wird beschrieben, wie Sie die Ressourcennutzung für ein einzelnes Lab exportieren.

Bevor Sie die DevTest Labs-Ressourcennutzung exportieren können, müssen Sie ein Azure Storage Konto für die Dateien einrichten, die die Nutzungsdaten enthalten. Es gibt zwei gängige Möglichkeiten zum Ausführen des Datenexports:

* [DevTest Labs-REST-API](/rest/api/dtl/labs/exportresourceusage) 
* Das PowerShell-Az.Resource-Modul [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) mit der Aktion `exportResourceUsage`, der Lab-Ressourcen-ID und den erforderlichen Parametern. 

    Der Artikel [Exportieren oder Löschen personenbezogener Daten aus Azure DevTest Labs](personal-data-delete-export.md) enthält ein PowerShell-Beispielskript mit ausführlichen Informationen zu den Daten, die exportiert werden. 

    > [!NOTE]
    > Da der Datumsparameter keinen Zeitstempel hat, sind alle Daten ab Mitternacht enthalten (basierend auf der Zeitzone, in der sich das Lab befindet).

Nach Abschluss des Exports enthält der Blobspeicher mehrere CSV-Dateien mit den verschiedenen Ressourceninformationen.
  
Derzeit sind zwei CSV-Dateien vorhanden:

* *virtualmachines.csv* enthält Informationen zu den virtuellen Computern im Lab.
* *disks.csv* enthält Informationen zu den unterschiedlichen Datenträgern des Labs. 

Diese Dateien werden im Blobcontainer *labresourceusage* gespeichert. Die Dateien befinden sich unter dem Labnamen, der eindeutigen Lab-ID, dem ausgeführten Datum und `full` entweder oder dem Startdatum der Exportanforderung. Ein Beispiel für eine Blobstruktur ist:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Exportieren der Nutzung für alle Labs

Um die Nutzungsinformationen für mehrere Labs zu exportieren, sollten Sie Folgendes verwenden: 

* [Azure Functions](../azure-functions/index.yml): Für viele Sprachen verfügbar, z. B. PowerShell. 
* [Azure Automation-Runbook](../automation/index.yml): Verwenden Sie PowerShell, Python oder einen benutzerdefinierten grafischen Designer, um den Exportcode zu schreiben.

Mit diesen Technologien können Sie die einzelnen Lab-Exporte für alle Labs zu einem bestimmten Datum und einer bestimmten Uhrzeit durchführen. 

Ihre Azure-Funktion sollte die Daten per Pushvorgang in den Langzeitspeicher übertragen. Wenn Sie Daten für mehrere Labs exportieren, kann der Export einige Zeit in Anspruch nehmen. Um die Leistung zu verbessern und die Möglichkeit der Duplizierung von Informationen zu reduzieren, wird empfohlen, jedes Lab parallel auszuführen. Führen Sie Azure Functions asynchron aus, um die Parallelität zu ermöglichen. Nutzen Sie auch den Timertrigger, den Azure Functions bietet.

## <a name="using-a-long-term-storage"></a>Nutzen eines Langzeitspeichers

Bei Nutzung eines Langzeitspeichers werden die Exportinformationen aus verschiedenen Labs in einer einzelnen Datenquelle zusammengefasst. Ein weiterer Vorteil des Langzeitspeichers besteht darin, dass Sie die Dateien aus dem Speicherkonto entfernen können, um Duplizierungen und Kosten zu reduzieren. 

Der Langzeitspeicher kann verwendet werden, um Textbearbeitungen durchzuführen, z. B.: 

* Hinzufügen von Anzeigenamen
* Erstellen komplexer Gruppierungen
* Aggregieren der Daten

Einige gängige Speicherlösungen sind: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/) und [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Die Entscheidung, welche Langzeitspeicher-Lösung Sie wählen, hängt von Ihren Vorlieben ab. Je nachdem, was es für die Interaktionsverfügbarkeit bietet, können Sie beim Visualisieren der Daten das Tool auswählen.

## <a name="visualizing-data-and-gathering-insights"></a>Visualisieren von Daten und Sammeln von Erkenntnissen

Nutzen Sie ein Datenvisualisierungstool Ihrer Wahl, um eine Verbindung mit Ihrem Langzeitspeicher herzustellen und die Nutzungsdaten anzuzeigen, damit Sie Erkenntnisse zur Nutzungseffizienz sammeln können. Beispielsweise können Sie [Power BI](/power-bi/power-bi-overview) verwenden, um die Nutzungsdaten zu organisieren und anzuzeigen. 

Sie können [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) verwenden, um Ihre Ressourcen an einem zentralen Ort zu erstellen, zu verlinken und zu verwalten. Falls eine stärkere Kontrolle erforderlich ist, können Sie einzelne Ressourcen in einer einzelnen Ressourcengruppe erstellen und unabhängig vom Data Factory-Dienst verwalten.  

## <a name="next-steps"></a>Nächste Schritte

Sobald Sie das System eingerichtet haben und die Daten in den langfristigen Speicher verschoben werden, besteht der nächste Schritt darin, die Fragen zu stellen, die die Daten beantworten müssen. Beispiel: 

-   Welche VM-Größen werden genutzt?

    Wählen Benutzer VM-Größen für Hochleistung aus (höhere Kosten)?
-   Welche Marketplace-Images werden verwendet?

    Werden benutzerdefinierte Images am häufigsten als VM-Basis verwendet, und sollte ein gemeinsamer Imagespeicher erstellt werden, z. B. [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) oder [Image Factory](image-factory-create.md)?
-   Welche benutzerdefinierten Images werden verwendet bzw. nicht verwendet?