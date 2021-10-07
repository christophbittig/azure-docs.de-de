---
title: Tool zum Kopieren von Daten
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie mehr über das Tool „Daten kopieren“ auf der Azure Data Factory- und Synapse Analytics-Benutzeroberfläche.
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: yexu
ms.openlocfilehash: d4ae44d3c3121ff5cc0240ea9dac0ed11e1580ce
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760298"
---
# <a name="copy-data-tool-in-azure-data-factory-and-synapse-analytics"></a>Tool „Daten kopieren“ in Azure Data Factory und Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Das Tool „Daten kopieren“ vereinfacht und optimiert den Vorgang der Erfassung von Daten in einem Data Lake. Dies ist normalerweise ein erster Schritt in einem umfassenden Szenario für die Datenintegration.  Sie sparen Zeit, insbesondere bei Verwendung des Diensts für die erstmalige Erfassung von Daten aus einer Datenquelle. Die Verwendung dieses Tools ist z.B. mit den folgenden Vorteilen verbunden:

- Wenn Sie das Tool „Daten kopieren“ verwenden, müssen Sie die Dienstdefinitionen für verknüpfte Dienste, Datasets, Pipelines, Aktivitäten und Trigger nicht verstehen. 
- Der Fluss des Tools zum Kopieren von Daten ist für das Laden von Daten in einen Data Lake intuitiv. Das Tool erstellt automatisch alle erforderlichen Ressourcen zum Kopieren von Daten aus dem ausgewählten Quelldatenspeicher in den ausgewählten Ziel-/Senkendatenspeicher. 
- Das Tool zum Kopieren von Daten hilft Ihnen dabei, die Daten zu überprüfen, die zum Zeitpunkt der Erstellung des Dokuments erfasst werden, wodurch Sie mögliche Fehler bereits am Anfang vermeiden können.
- Wenn Sie komplexe Geschäftslogik implementieren müssen, um Daten in einen Data Lake zu laden, können Sie die vom Tool „Daten kopieren“ erstellten Ressourcen mithilfe der Dokumenterstellung pro Aktivität auf der Benutzeroberfläche weiterhin bearbeiten. 

In der folgenden Tabelle finden Sie Hinweise dazu, unter welchen Umständen Sie das Tool „Daten kopieren“ bzw. die Dokumenterstellung pro Aktivität auf der Benutzeroberfläche verwenden sollten: 

| Tool zum Kopieren von Daten | Dokumenterstellung pro Aktivität (Kopieraktivität) |
| -------------- | -------------------------------------- |
| Sie möchten einfach einen Datenladetask erstellen, ohne sich mit den Entitäten vertraut zu machen (verknüpfte Dienste, Datasets, Pipelines usw.). | Sie möchten komplexe und flexible Logik für das Laden von Daten in den Data Lake implementieren. |
| Sie möchten schnell eine große Anzahl von Datenartefakten in einen Data Lake laden. | Sie möchten die Kopieraktivität mit nachfolgenden Aktivitäten zur Bereinigung oder Verarbeitung von Daten verketten. |

Klicken Sie zum Starten des Tools „Daten kopieren“ auf der Startseite Ihrer Data Factory oder auf der Synapse Studio-Benutzeroberfläche auf die Kachel **Ingest** (Erfassen).

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
:::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Screenshot, der die Startseite zeigt – Link zum Tool zum Kopieren von Daten.":::
# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="./media/doc-common-process/get-started-page-synapse.png" alt-text="Screenshot, der die Startseite zeigt – Link zum Tool zum Kopieren von Daten.":::

---
Nach dem Starten des Tools zum Kopieren von Daten sehen Sie zwei Arten von Aufgaben: eine ist die **Aufgabe integrierte Kopie** und die andere ist die **Aufgabe metadatenbasierte Kopie**. Die integrierte Kopieraufgabe führt Sie dazu, innerhalb von fünf Minuten eine Pipeline zum Replizieren von Daten zu erstellen, ohne mehr über Entitäten zu erfahren.  Die Aufgabe „metadatenbasierte Kopie“ erleichtert Ihnen das Erstellen parametrisierter Pipelines und externer Steuertabellen, um große Mengen von Objekten (z. B. Tausende von Tabellen) im großen Stil zu kopieren. Weitere Einzelheiten erhalten Sie unter [metadatenbasiertes Kopieren von Daten](copy-data-tool-metadata-driven.md).

## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitiver Fluss zum Laden von Daten in einen Data Lake
Mit diesem Tool können Sie mit einem intuitiven Fluss problemlos Daten in wenigen Minuten aus einer Vielzahl von Quellen an verschiedene Ziele verschieben:  

1. Konfigurieren Sie Einstellungen für die **Quelle**.
2. Konfigurieren Sie Einstellungen für das **Ziel**. 
3. Konfigurieren Sie **erweiterte Einstellungen** für den Kopiervorgang, z.B. Spaltenzuordnung, Leistungseinstellungen und Fehlertoleranzeinstellungen. 
4. Geben Sie einen **Zeitplan** für den Datenladetask an. 
5. Überprüfen Sie die **Zusammenfassung** der zu erstellenden Entitäten. 
6. **Bearbeiten** Sie die Pipeline, um die Einstellungen für die Kopieraktivität nach Bedarf zu aktualisieren. 

   Das Tool wurde von Beginn an für Big Data konzipiert und bietet Unterstützung für verschiedene Daten- und Objekttypen. Sie können es zum Verschieben von Hunderten von Ordnern, Dateien oder Tabellen verwenden. Das Tool unterstützt die automatische Datenvorschau, Schemaerfassung und automatische Zuordnung sowie das Filtern von Daten.

:::image type="content" source="./media/copy-data-tool/copy-data-tool.png" alt-text="Tool zum Kopieren von Daten":::

## <a name="automatic-data-preview"></a>Automatische Datenvorschau
Sie können einen Teil der Daten aus dem ausgewählten Quelldatenspeicher als Vorschau anzeigen. Auf diese Weise können Sie die Daten überprüfen, die kopiert werden. Wenn die Datenquelle eine Textdatei ist, analysiert das Tool zum Kopieren von Daten darüber hinaus diese Datei und erkennt Trennzeichen für Zeilen und Spalten sowie das Schema automatisch.

:::image type="content" source="./media/copy-data-tool/file-format-settings.png" alt-text="Dateieinstellungen":::

Wählen Sie nach der Erkennung **Datenvorschau** aus:

:::image type="content" source="./media/copy-data-tool/after-detection.png" alt-text="Erkannte Dateieinstellungen und Vorschau":::

## <a name="schema-capture-and-automatic-mapping"></a>Schemaerfassung und automatische Zuordnung
Das Schema der Datenquelle ist in vielen Fällen ggf. nicht mit dem Schema des Datenziels identisch. In diesem Szenario müssen die Spalten des Quellschemas den Spalten des Zielschemas zugeordnet werden.

Das Tool zum Kopieren von Daten überwacht und erlernt Ihr Verhalten beim Zuordnen von Spalten zwischen Quell- und Zielspeichern. Nachdem Sie mindestens eine Spalte aus dem Quelldatenspeicher ausgewählt und dem Zielschema zugeordnet haben, beginnt das Tool zum Kopieren von Daten das Muster für Spaltenpaare zu analysieren, die Sie auf beiden Seiten ausgewählt haben. Dann wendet es das gleiche Muster auf den Rest der Spalten an. Daher erkennen Sie bereits nach wenigen Klicks, dass alle Spalten auf die gewünschte Art und Weise dem Ziel zugeordnet wurden.  Wenn Sie mit der Auswahl der Spaltenzuordnung durch das Tool zum Kopieren von Daten nicht zufrieden sind, können Sie diese ignorieren und mit der manuellen Zuordnung der Spalten fortfahren. In der Zwischenzeit erlernt und aktualisiert das Tool zum Kopieren von Daten ständig das Muster und erreicht schließlich das richtige Muster für die gewünschte Spaltenzuordnung. 

> [!NOTE]
> Beim Kopieren von Daten aus SQL Server oder der Azure SQL-Datenbank in Azure Synapse Analytics unterstützt das Tool zum Kopieren von Daten die automatische Erstellung der Tabelle unter Verwendung des Quellschemas, wenn die Tabelle im Zielspeicher nicht vorhanden ist. 

## <a name="filter-data"></a>Filtern von Daten
Sie können Quelldaten filtern, wenn nur bestimmte Daten in den Senkendatenspeicher kopiert werden sollen. Durch Filtern wird das Datenvolumen der in den Senkendatenspeicher kopierten Daten verringert. Der Durchsatz des Kopiervorgangs steigt. Das Tool zum Kopieren von Daten bietet flexible Möglichkeiten, die Daten einer relationalen Datenbank mittels der SQL-Abfragesprache oder Dateien in einem Azure-Blobordner zu filtern. 

### <a name="filter-data-in-a-database"></a>Filtern von Daten in einer Datenbank
Der folgende Screenshot zeigt eine SQL-Abfrage zum Filtern der Daten.

:::image type="content" source="./media/copy-data-tool/filter-data-in-database.png" alt-text="Filtern von Daten in einer Datenbank":::

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtern von Daten in einem Azure-Blobordner
Sie können Variablen im Ordnerpfad verwenden, um Daten aus einem Ordner zu kopieren. Folgende Variablen werden unterstützt: **{year}** , **{month}** , **{day}** , **{hour}** und **{minute}** . Beispiel: Eingabeordner/{year}/{month}/{day}. 

Angenommen, Sie haben Eingabeordner im folgenden Format: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicken Sie auf die Schaltfläche **Durchsuchen** für **Datei oder Ordner**, navigieren Sie zu einem der Ordner (z.B. 2016->03->01->02), und klicken Sie auf **Auswählen**. Das Textfeld sollte jetzt „2016/03/01/02“ enthalten. 

Ersetzen Sie nun **2016** durch **{year}** , **03** durch **{month}** , **01** durch **{day}** , **02** durch **{hour}** , und drücken Sie dann die **TAB**-TASTE. Wenn Sie **Inkrementelles Laden: nach Zeit eingeteilte Ordner-/Dateinamen** im Abschnitt **Dateiladeverhalten** auswählen und **Zeitplan** oder **Rollierendes Fenster** auf der Seite **Eigenschaften** auswählen, sollten Dropdownlisten angezeigt werden, um das Format für diese vier Variablen auszuwählen:

:::image type="content" source="./media/copy-data-tool/filter-file-or-folder.png" alt-text="Filtern von Dateien oder Ordnern":::

Die Tool zum Kopieren von Daten generiert Parameter mit Ausdrücken, Funktionen und Systemvariablen, die verwendet werden können, um {year}, {month}, {day}, {hour} und {minute} beim Erstellen der Pipeline darzustellen.

## <a name="scheduling-options"></a>Planungsoptionen
Sie können den Kopiervorgang einmal oder nach einem Zeitplan (stündlich, täglich usw.) ausführen. Diese Optionen können für die Connectors in verschiedenen Umgebungen verwendet werden, z.B. in lokalen Umgebungen, in der Cloud und auf dem lokalen Desktop. 

Ein einmaliger Kopiervorgang ermöglicht nur einmal das Verschieben von Daten aus einer Quelle in ein Ziel. Er gilt für Daten jeder Größe in jedem unterstützten Format. Das Kopieren nach einem Zeitplan ermöglicht Ihnen das Kopieren von Daten in einer angegebenen Serie. Sie können umfangreiche Einstellungen (wie Wiederholen, Timeout, Warnungen usw.) nutzen, um das Kopieren nach einem Zeitplan zu konfigurieren.

:::image type="content" source="./media/copy-data-tool/scheduling-options.png" alt-text="Planungsoptionen":::


## <a name="next-steps"></a>Nächste Schritte
Machen Sie sich mit diesen Tutorials vertraut, die das Tool zum Kopieren von Daten verwenden:

- [Schnellstart: Erstellen einer Data Factory mithilfe des Tools zum Kopieren von Daten](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: Kopieren von Daten in Azure mit dem Tool zum Kopieren von Daten](tutorial-copy-data-tool.md) 
- [Tutorial: Kopieren lokaler Daten nach Azure mit dem Tool zum Kopieren von Daten](tutorial-hybrid-copy-data-tool.md)
