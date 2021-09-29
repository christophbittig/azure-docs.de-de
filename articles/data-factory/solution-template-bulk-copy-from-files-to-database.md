---
title: Massenkopieren von Dateien in eine Datenbank
description: Erfahren Sie, wie Sie eine Lösungsvorlage zum Massenkopieren von Daten aus Azure Data Lake Storage Gen2 nach Azure Synapse Analytics/Azure SQL-Datenbank verwenden.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 59189f0c197294ca74e01d331663c51fa0e2bd7d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757893"
---
# <a name="bulk-copy-from-files-to-database"></a>Massenkopieren von Dateien in eine Datenbank

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird eine Lösungsvorlage beschrieben, die Sie zum Massenkopieren von Daten aus Azure Data Lake Storage Gen2 nach Azure Synapse Analytics/Azure SQL-Datenbank verwenden können.

## <a name="about-this-solution-template"></a>Informationen zu dieser Lösungsvorlage

Mit dieser Vorlage werden Dateien aus der Azure Data Lake Storage Gen2-Quelle abgerufen. Anschließend wird jede Datei in der Quelle durchlaufen, und die Daten werden in den Zieldatenspeicher kopiert. 

Diese Vorlage unterstützt derzeit nur das Kopieren von Daten im **DelimitedText**-Format. Dateien mit anderen Datenformaten können ebenfalls aus dem Quelldatenspeicher abgerufen werden, sie können jedoch nicht in den Zieldatenspeicher kopiert werden.  

Die Vorlage enthält drei Aktivitäten:
- Die Aktivität **Metadaten abrufen** ruft Dateien aus Azure Data Lake Storage Gen2 ab und übergibt sie an die nachfolgende *ForEach*-Aktivität.
- Die **ForEach**-Aktivität ruft Dateien aus der Aktivität *Metadaten abrufen* ab und durchläuft jede Datei mit der Aktivität *Kopieren*.
- Die Aktivität **Kopieren** befindet sich innerhalb der *ForEach*-Aktivität, damit jede Datei aus dem Quelldatenspeicher in den Zieldatenspeicher kopiert werden kann.

Die Vorlage definiert die folgenden beiden Parameter:
- *SourceContainer* ist der Stammcontainerpfad, in den die Daten in Ihrer Azure Data Lake Storage Gen2-Instanz kopiert werden. 
- *SourceDirectory* ist der Verzeichnispfad im Stammcontainer, in den die Daten aus Ihrer Azure Data Lake Storage Gen2-Instanz kopiert werden.

## <a name="how-to-use-this-solution-template"></a>So verwenden Sie diese Lösungsvorlage

1. Navigieren Sie zur Vorlage **Bulk Copy from Files to Database** (Massenkopieren aus Dateien in eine Datenbank). Stellen Sie eine **neue Verbindung** mit dem Gen2-Quellspeicher her. Beachten Sie, dass „GetMetadataDataset“ und „SourceDataset“ Verweise auf dieselbe Verbindung Ihres Quelldateispeichers sind.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/source-connection.png" alt-text="Herstellen einer neuen Verbindung mit dem Quelldatenspeicher":::

2. Stellen Sie eine **neue Verbindung** mit dem Senkendatenspeicher her, in den Sie Daten kopieren.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/destination-connection.png" alt-text="Herstellen einer neuen Verbindung mit dem Senkendatenspeicher":::
    
3. Klicken Sie auf **Diese Vorlage verwenden**.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/use-template.png" alt-text="„Diese Vorlage verwenden“":::
    
4. Sie sehen, dass eine Pipeline erstellt wurde, wie im nachstehenden Beispiel gezeigt wird:

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png" alt-text="Überprüfen der Pipeline":::

    > [!NOTE]
    > Wenn Sie in **Schritt 2** oben als Datenziel **Azure Synapse Analytics**  ausgewählt haben, müssen Sie eine Verbindung mit Azure Blob Storage für den Stagingprozess eingeben, da dies für Azure Synapse Analytics PolyBase erforderlich ist. Wie im folgenden Screenshot gezeigt, generiert die Vorlage automatisch einen *Speicherpfad* für Ihren Blobspeicher. Überprüfen Sie nach der Pipelineausführung, ob der Container erstellt wurde.
        
    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/staging-account.png" alt-text="PolyBase-Einstellung":::

5. Klicken Sie auf **Debuggen**, geben Sie die **Parameter** ein, und klicken Sie dann auf **Fertig stellen**.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/debug-run.png" alt-text="Auf **Debuggen** klicken":::

6. Wenn die Pipelineausführung erfolgreich abgeschlossen wurde, werden die Ergebnisse ähnlich wie im nachstehenden Beispiel angezeigt:

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png" alt-text="Überprüfen des Ergebnisses":::

       
## <a name="next-steps"></a>Nächste Schritte

- [Einführung in den Azure Data Factory-Dienst](introduction.md)