---
title: Kopieren von Dateien aus mehreren Containern
description: Erfahren Sie, wie Sie mit Azure Data Factory eine Lösungsvorlage verwenden, um Dateien aus mehreren Containern zu kopieren.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: d1d1ffaecdf57817e5ef7be48791d0cb50ca9b74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743538"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Kopieren mehrerer Ordner mit Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird eine Lösungsvorlage beschrieben, mit der Sie mehrere Kopieraktivitäten verwenden können, um Container oder Ordner zwischen dateibasierten Speichern zu kopieren. Jede Kopieraktivität dient dabei zum Kopieren eines einzelnen Containers oder Ordners. 

> [!NOTE]
> Wenn Sie Dateien aus einem einzelnen Container kopieren möchten, ist es effizienter, mit dem [Tool „Daten kopieren“](copy-data-tool.md) eine Pipeline mit einer einzelnen Kopieraktivität zu erstellen. Die Vorlage in diesem Artikel ist für dieses einfache Szenario umfangreicher als notwendig.

## <a name="about-this-solution-template"></a>Informationen zu dieser Lösungsvorlage

Diese Vorlage listet die Ordner aus einem angegebenen übergeordneten Ordner in Ihrem Quellspeicher auf. Anschließend kopiert sie jeden dieser Ordner in den Zielspeicher.

Die Vorlage enthält drei Aktivitäten:
- **GetMetadata** überprüft den Quellspeicher und ruft die Unterordnerliste aus einem angegebenen übergeordneten Ordner ab.
- **ForEach** ruft die Liste der Unterordner aus der Aktivität **GetMetadata** ab, durchläuft dann die Liste und übergibt jeden einzelnen Ordner an die Kopieraktivität.
- **Copy** kopiert jeden Ordner aus dem Quell- in den Zielspeicher.

Die Vorlage definiert die folgenden Parameter:
- *SourceFileFolder* gehört zum Pfad des übergeordneten Ordners im Quelldatenspeicher: *SourceFileFolder/SourceFileDirectory*: Hier wird die Liste der Unterordner abgerufen. 
- *SourceFileDirectory* gehört zum Pfad des übergeordneten Ordners im Quelldatenspeicher: *SourceFileFolder/SourceFileDirectory*: Hier wird die Liste der Unterordner abgerufen. 
- *DestinationFileFolder* gehört zum Pfad des übergeordneten Ordners: *DestinationFileFolder/DestinationFileDirectory*: In diesen Ordner bzw. dieses Verzeichnis im Zielspeicher werden die Dateien kopiert. 
- *DestinationFileDirectory* gehört zum Pfad des übergeordneten Ordners: *DestinationFileFolder/DestinationFileDirectory*: In diesen Ordner bzw. dieses Verzeichnis im Zielspeicher werden die Dateien kopiert. 

Wenn Sie mehrere Container in Stammordnern zwischen Speichern kopieren möchten, können Sie alle vier Parameter als */* angeben. Dadurch replizieren Sie sämtliche Daten zwischen den Speichern.

## <a name="how-to-use-this-solution-template"></a>So verwenden Sie diese Lösungsvorlage

1. Wechseln Sie zur Vorlage **Kopieren von Containern mit mehreren Dateien zwischen Dateispeichern**. Erstellen Sie eine **neue Verbindung** mit Ihrem Quellspeicher. In den Quellspeicher möchten Sie Dateien aus mehreren Containern kopieren.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png" alt-text="Erstellen einer neuen Verbindung mit der Quelle":::

2. Erstellen Sie eine **neue Verbindung** mit Ihrem Zielspeicher.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png" alt-text="Erstellen einer neuen Verbindung mit dem Ziel":::

3. Klicken Sie auf **Diese Vorlage verwenden**.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png" alt-text="„Diese Vorlage verwenden“":::
    
4. Daraufhin wird die Pipeline wie im folgenden Beispiel angezeigt:

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png" alt-text="Pipeline anzeigen":::

5. Klicken Sie auf **Debuggen**, geben Sie die **Parameter** ein, und klicken Sie dann auf **Fertig stellen**.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png" alt-text="Führen Sie die Pipeline aus.":::

6. Überprüfen Sie das Ergebnis.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png" alt-text="Überprüfen des Ergebnisses":::

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen von Massenkopiervorgängen mit Steuertabelle für eine Datenbank](solution-template-bulk-copy-with-control-table.md)

- [Kopieren von Dateien aus mehreren Containern mit Azure Data Factory](solution-template-copy-files-multiple-containers.md)
