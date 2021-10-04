---
title: Verarbeiten von Textdateien mit fester Länge mithilfe von Mapping Data Flows in Azure Data Factory
description: Erfahren Sie, wie Sie Textdateien mit fester Länge in Azure Data Factory mithilfe von Mapping Data Flows verarbeiten.
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 58bd77eb815a510328c49ca7c614b0bc680a4b94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571426"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Verarbeiten von Textdateien mit fester Länge mithilfe von Data Factory Mapping Data Flows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mithilfe von Mapping Data Flows in Microsoft Azure Data Factory können Sie Daten aus Textdateien mit fester Breite transformieren. In der folgenden Aufgabe definieren Sie ein Dataset für eine Textdatei ohne Trennzeichen und richten dann Unterteilungen in Teilzeichenfolgen basierend auf der Ordinalposition ein.

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

1. Wählen Sie **+Neue Pipeline** aus, um eine neue Pipeline zu erstellen.

2. Fügen Sie eine Datenflussaktivität für die Verarbeitung von Dateien mit fester Breite hinzu:

    :::image type="content" source="media/data-flow/fwpipe.png" alt-text="Pipeline mit fester Breite":::

3. Wählen Sie in der Datenflussaktivität die Option für eine **neue Mapping Data Flow-Instanz** aus.

4. Fügen Sie eine Transformation für Quelle, abgeleitete Spalte, Auswahl und Senke hinzu:

    :::image type="content" source="media/data-flow/fw2.png" alt-text="Datenfluss mit fester Breite":::

5. Konfigurieren Sie die Quellentransformation so, dass ein neues Dataset vom Typ „Durch Trennzeichen getrennter Text“ verwendet wird.

6. Legen Sie keine Spaltentrennzeichen oder -überschriften fest.

   Als Nächstes werden die Ausgangspunkte und Längen für die Inhalte dieser Datei festgelegt:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Auf der Registerkarte **Projektion** der Quellentransformation wird eine Zeichenfolgenspalte namens *Column_1* angezeigt.

8. Erstellen Sie in der abgeleiteten Spalte eine neue Spalte.

9. Geben Sie den Spalten einfache Namen wie etwa *col1*.

10. Geben Sie im Ausdrucks-Generator Folgendes ein:

    `substring(Column_1,1,4)`

    :::image type="content" source="media/data-flow/fwderivedcol1.png" alt-text="Abgeleitete Spalte":::

11. Wiederholen Sie Schritt 10 für alle zu analysierenden Spalten.

12. Wählen Sie die Registerkarte **Untersuchen** aus, um die neuen Spalten anzuzeigen, die generiert werden:

    :::image type="content" source="media/data-flow/fwinspect.png" alt-text="Überprüfen":::

13. Verwenden Sie die Auswahltransformation, um alle Spalten zu entfernen, die Sie nicht für die Transformation benötigen:

    :::image type="content" source="media/data-flow/fwselect.png" alt-text="Auswahltransformation":::

14. Verwenden Sie „Senke“, um die Daten in einen Ordner auszugeben:

    :::image type="content" source="media/data-flow/fwsink.png" alt-text="Senke mit fester Breite":::

    Die Ausgabe sieht wie folgt aus:

    :::image type="content" source="media/data-flow/fxdoutput.png" alt-text="Ausgabe mit fester Breite":::

  Die Daten mit fester Breite sind nun in jeweils vier Zeichen unterteilt und „Col1“, „Col2“, „Col3“, „Col4“ usw. zugeordnet. Basierend auf dem vorherigen Beispiel werden die Daten auf vier Spalten aufgeteilt.

## <a name="next-steps"></a>Nächste Schritte

* Erstellen Sie die restliche Datenflusslogik mithilfe von Mapping Data Flow-[Transformationen](concepts-data-flow-overview.md).
