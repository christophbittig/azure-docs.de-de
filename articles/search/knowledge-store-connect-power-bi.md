---
title: Herstellen einer Verbindung mit einem Wissensspeicher mit Power BI
titleSuffix: Azure Cognitive Search
description: Stellen Sie mit Power BI eine Verbindung mit einem Azure Cognitive Search-Wissensspeicher her, um diesen zu analysieren und zu untersuchen.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 14a6cd5ec07d9c4c5ee584312e9ba20b2251d0ef
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178301"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Herstellen einer Verbindung mit einem Wissensspeicher mit Power BI

In diesem Artikel erfahren Sie, wie Sie mithilfe von Power Query in der Power BI Desktop-App eine Verbindung mit einem Wissensspeicher herstellen und anschließend abfragen. Sie können mit Vorlagen schneller beginnen oder ein benutzerdefiniertes Dashboard von Grund auf neu erstellen.

Ein Wissensspeicher, der aus Tabellen in Azure Storage besteht, funktioniert am besten in Power BI. Wenn die Tabellen Projektionen aus demselben Skillset und derselben Projektionsgruppe enthalten, können Sie sie einfach „verknüpfen“, um Tabellenvisualisierungen mit Feldern aus verknüpften Tabellen zu erstellen.

Folgen Sie den Schritten in diesem Artikel unter Verwendung von Beispieldaten und eines Wissensspeichers, wie [in diesem Portal-Schnellstart](knowledge-store-create-portal.md) oder über [Postman und REST-APIs](knowledge-store-create-rest.md) erstellt. 

## <a name="connect-to-azure-storage"></a>Verbinden mit Azure Storage

1. Starten Sie [Power BI Desktop](https://powerbi.microsoft.com/downloads/), und wählen Sie **Daten abrufen** aus. 

1. Wählen Sie im Fenster **Daten abrufen** die Option **Azure** und anschließend **Azure-Tabellenspeicher** aus.

1. Wählen Sie **Verbinden**.

1. Geben Sie unter **Kontoname oder URL** den Namen Ihres Azure Storage-Kontos ein (die vollständige URL wird für Sie erstellt).

1. Wenn Sie dazu aufgefordert werden, geben Sie den Schlüssel des Speicherkontos ein.

## <a name="set-up-tables"></a>Einrichten von Tabellen

1. Aktivieren Sie das Kontrollkästchen neben allen Tabellen, die aus demselben Skillset erstellt wurden, und wählen Sie dann **Laden** aus.

   ![Laden von Tabellen](media/knowledge-store-connect-power-bi/power-bi-load-tables.png "Laden von Tabellen")

1. Klicken Sie im oberen Menüband auf **Daten transformieren**, um den **Power Query-Editor** zu öffnen.

   ![Power Query öffnen](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query öffnen")

1. Öffnen Sie *hotelReviewsDocument*, und entfernen Sie die Spalten *PartitionKey*, *RowKey* und *Timestamp*. Diese Spalten werden für Tabellenbeziehungen in Azure Table Storage verwendet. In Power BI werden sie nicht benötigt. Es sollte eine Spalte mit dem Namen „Inhalt“ übrig bleiben, in der jeweils ein *Datensatz* angezeigt wird. 

   ![Tabellen bearbeiten](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Tabellen bearbeiten")

1. Klicken Sie rechts oben in der Tabelle auf das Symbol mit den entgegengesetzten Pfeilen, um *Inhalt* zu erweitern. Wenn die Liste der Spalten angezeigt wird, wählen Sie alle Spalten aus. Löschen Sie Spalten, die mit „metadata“ beginnen. Klicken Sie auf **OK**, um die ausgewählten Spalten einzubeziehen.

   ![Inhalt erweitern](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Inhalt erweitern")

1. Ändern Sie den Datentyp für die folgenden Spalten, indem Sie oben links in der Spalte auf das Symbol „ABC-123“ klicken.

   + Wählen Sie für *content.latitude* und *Content.longitude* die Option **Dezimalzahl** aus.
   + Wählen Sie für *Content.reviews_date* und *Content.reviews_dateAdded* die Option **Datum/Uhrzeit** aus.

   ![Datentypen ändern](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Datentypen ändern")

1. Öffnen Sie *hotelReviewsSsPages*, und wiederholen Sie die Schritte zum Löschen von Spalten, indem Sie *Inhalt* erweitern, um Spalten aus den Datensätzen auszuwählen. Für diese Tabelle sind keine Datentypänderungen erforderlich.

1. Öffnen Sie *hotelReviewsSsKeyPhrases*, und wiederholen Sie die Schritte zum Löschen von Spalten, indem Sie *Inhalt* erweitern, um Spalten aus den Datensätzen auszuwählen. Für diese Tabelle sind keine Datentypänderungen erforderlich.

1. Klicken Sie in der Befehlsleiste auf **Close and Apply** (Schließen und Anwenden).

## <a name="check-table-relationships"></a>Überprüfen von Tabellenbeziehungen

1. Klicken Sie im linken Navigationsbereich auf die Modellkachel, und überprüfen Sie, ob Power BI Beziehungen zwischen allen drei Tabellen anzeigt.

   ![Beziehungen überprüfen](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Beziehungen überprüfen")

1. Doppelklicken Sie auf die einzelnen Beziehungen, und stellen Sie sicher, dass die **Kreuzfilterrichtung** auf **Beide** festgelegt ist.  Dadurch können visuelle Elemente aktualisiert werden, wenn ein Filter angewendet wird.

## <a name="build-a-report"></a>Erstellen eines Berichts

1. Klicken Sie im linken Navigationsbereich auf die Kachel „Bericht“, um Daten anhand von Visualisierungen zu untersuchen. Für Textfelder sind Tabellen und Karten hilfreiche Visualisierungen.

1. Sie können Felder aus jeder der drei Tabellen auswählen, um die Tabelle oder Karte zu füllen.

   ![Erstellen eines Tabellenberichts](media/knowledge-store-connect-power-bi/power-bi-table-report.png "Erstellen eines Tabellenberichts")

## <a name="sample-power-bi-template---azure-portal-only"></a>Power BI-Beispielvorlage (nur Azure-Portal)

Beim [Erstellen eines Wissensspeichers über das Azure-Portal](knowledge-store-create-portal.md) können Sie auf der zweiten Seite des Assistenten **Daten importieren** eine [Power BI-Vorlage](https://github.com/Azure-Samples/cognitive-search-templates) herunterladen. Die Vorlage bietet mehrere Visualisierungen für textbasierte Inhalte (beispielweise WordCloud und Network Navigator). 

Klicken Sie auf der Seite **Kognitive Qualifikationen hinzufügen (Optional)** auf **Power BI-Vorlage abrufen**, um die Vorlage aus dem öffentlichen GitHub-Speicherort abzurufen und herunterzuladen. Der Assistent passt die Vorlage an die Form Ihrer Daten an (gemäß der Erfassung in den im Assistenten angegebenen Wissensspeicherprojektionen). Aus diesem Grund variiert die heruntergeladene Vorlage bei jeder Ausführung des Assistenten, wenn sich die Dateneingaben und die ausgewählten Qualifikationen unterscheiden.

![Beispiel: Power BI-Vorlage für Azure Cognitive Search](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Beispiel für Power BI-Vorlage")

> [!NOTE]
> Die Vorlage wird heruntergeladen, während der Assistent ausgeführt wird. Sie müssen warten, bis der Wissensspeicher in Azure Table Storage tatsächlich erstellt wurde, bevor Sie ihn verwenden können.

## <a name="video-introduction"></a>Einführungsvideo

Eine Demonstration der Verwendung von Power BI mit einem Wissensspeicher erhalten Sie im folgenden Video.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tabellen in Power BI-Berichten und -Dashboards](/power-bi/visuals/power-bi-visualization-tables)