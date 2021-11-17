---
title: Bewährte Methoden für Datenherkunft in Azure Purview
description: Dieser Artikel enthält bewährte Methoden für die Datenherkunft verschiedener Datenquellen in Azure Purview.
author: amberz
ms.author: amberz
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: 0036004e8222ce79fc43e5d6603abecc2e375b1a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478764"
---
# <a name="azure-purview-data-lineage-best-practices"></a>Bewährte Methoden für Datenherkunft in Azure Purview

Unter Datenherkunft wird im Allgemeinen der Lebenszyklus verstanden, der den Ursprung der Daten und ihre Bewegung in der Datenumgebung im Laufe der Zeit umfasst. Purview kann die Herkunft für Daten in verschiedenen Teilen des Datenbestands Ihrer Organisation und auf unterschiedlichen Vorbereitungsstufen erfassen, einschließlich: 
* Von verschiedenen Plattformen bereitgestellte Rohdaten 
* Transformierte und vorbereitete Daten 
* Von Visualisierungsplattformen verwendete Daten

 
## <a name="intended-audience"></a>Zielpublikum

* Datentechniker 
* Data Scientist 
* Datenbesitzer 

## <a name="why-do-you-need-adopt-lineage"></a>Gründe für die Einführung der Datenherkunft  

Bei der Datenherkunft wird beschrieben, welche Daten vorhanden sind, wo sie gespeichert sind und wie sie zwischen Systemen fließen. Es gibt viele Gründe, warum die Datenherkunft wichtig ist, doch lassen sich diese allgemein in drei Kategorien zusammenfassen, die hier untersucht werden: 
* Nachverfolgen von Daten in Berichten 
* Auswirkungsanalyse 
* Erfassen der Änderungen und der Speicherorte der Daten während ihres Lebenszyklus 

## <a name="azure-data-factory-lineage-best-practice-and-considerations"></a>Bewährte Methode und Überlegungen zur Azure Data Factory-Datenherkunft 

### <a name="azure-data-factory-instance"></a>Azure Data Factory-Instanz 

* Die Datenherkunft wird erst dann automatisch an den Katalog gemeldet, wenn sich der Data Factory-Verbindungsstatus in „Verbunden“ ändert. Bei den übrigen Statusarten („Getrennt“ und „Kein Zugriff“) kann die Herkunft nicht erfasst werden. 

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Screenshot einer Data Factory-Verbindungsliste" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

* Jede Data Factory-Instanz kann nur eine Verbindung mit einem einzigen Purview-Konto herstellen. Sie können eine neue Verbindung in einem anderen Purview-Konto herstellen, doch wird dadurch die vorhandene Verbindung in getrennt.  

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Screenshot: Warnung vor dem Trennen einer Azure Data Factory-Verbindung":::

* Die verwaltete Identität von Data Factory wird verwendet, um die Herkunft im Purview-Konto zu authentifizieren. Die Rolle „Datenkurator“ der verwalteten Identität von Data Factory für die Purview-Stammsammlung ist erforderlich. 
* Unterstützen Sie nicht mehr als zehn Data Factorys gleichzeitig. Wenn Sie mehr als zehn Data Factorys gleichzeitig hinzufügen möchten, erstellen Sie ein Supportticket. 

### <a name="azure-data-factory-activities"></a>Azure Data Factory-Aktivitäten  

* Azure Purview erfasst die Runtimeherkunft aus den folgenden Azure Data Factory-Aktivitäten: 
    * [Kopieraktivität](../data-factory/copy-activity-overview.md)
    * [Datenflussaktivität](../data-factory/concepts-data-flow-overview.md)
    * [Ausführen einer SSIS-Paketaktivität](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

* Azure Purview verwirft die Herkunft, wenn die Quelle oder das Ziel ein nicht unterstütztes Datenspeichersystem verwendet.  
    * Unterstützte Datenquellen in der Kopieraktivität sind unter **Unterstützung der Copy-Aktivität** im Artikel zum [Herstellen einer Verbindung mit Azure Data Factory](how-to-link-azure-data-factory.md) aufgeführt.
    * Unterstützte Datenquellen in der Datenflussaktivität sind unter **Datenflussunterstützung** im Artikel zum [Herstellen einer Verbindung mit Azure Data Factory](how-to-link-azure-data-factory.md) aufgeführt.
    * Unterstützte Datenquellen in SSIS sind unter **Unterstützung der Aktivität „SSIS-Paket ausführen“** im Artikel zur [Datenherkunft aus SQL Server Integration Services](how-to-lineage-sql-server-integration-services.md) aufgeführt.

* Purview kann die Herkunft nicht erfassen, wenn die Azure Data Factory-Kopieraktivität Funktionen verwenden, die unter **Einschränkungen der Herkunftserfassung für die Kopieraktivität** im Artikel zum [Herstellen einer Verbindung mit Azure Data Factory](how-to-link-azure-data-factory.md) aufgeführt sind.  

* Bei der Herkunftserfassung für die Datenflussaktivität unterstützt Purview nur Quelle und Senke. Herkunftsdaten für die Datenflusstransformation werden noch nicht unterstützt. 

* Die Herkunftserfassung für den Datenfluss kann nicht in den Purview-Ressourcensatz integriert werden. 

    **Ressourcensatz – Beispiel 1**    

    Qualifizierter Name: https://myblob.blob.core.windows.net/sample-data/data{N}.csv 

    Anzeigename: „data“ (Daten) 

* Bei den Herkunftsdaten der Aktivität „SSIS-Paket ausführen“ werden nur Quelle und Ziel unterstützt. Herkunftsdaten der Transformation werden noch nicht unterstützt. 

    :::image type="content" source="./media/concept-best-practices-lineage/ssis-lineage.png" alt-text="Screenshot: Datenherkunft für die Aktivität „SSIS-Paket ausführen“ in Purview" lightbox="./media/concept-best-practices-lineage/ssis-lineage.png":::

* Lesen Sie die folgende schrittweise Anleitung, um die [Azure Data Factory-Herkunftsdaten an Purview zu pushen](../data-factory/tutorial-push-lineage-to-purview.md).  

## <a name="next-steps"></a>Nächste Schritte
-  [Datenquellen verwalten](./manage-data-sources.md)
