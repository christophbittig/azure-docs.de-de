---
title: 'Schnellstart: Erste Schritte beim Erfassen von Daten mit Pipelines (Vorschau)'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure Synapse-Pipelines Daten in Data Explorer-Pools erfassen.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 746f37fae42b117853d2f808d9fc5ff9fa9f980b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479764"
---
# <a name="quickstart-ingest-data-using-azure-synapse-pipelines-preview"></a>Schnellstart: Erfassen von Daten mithilfe von Azure Synapse-Pipelines (Vorschau)

In dieser Schnellstartanleitung erfahren Sie, wie Sie Daten aus einer Datenquelle in einen Azure Synapse Data Explorer-Pool laden.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Erstellen einer Tabelle   [!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)]

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Vergewissern Sie sich, dass die Tabelle erstellt wurde. Wählen Sie im linken Bereich **Daten**, dann das Menü mit weiteren Optionen für *contosodataexplorer* und anschließend **Aktualisieren** aus. Erweitern Sie unter *contosodataexplorer* den Knoten **Tabellen**, und stellen Sie sicher, dass die Tabelle *StormEvents* in der Liste angezeigt wird.

- Abrufen der Abfrage- und Datenerfassungsendpunkte Sie benötigen den Abfrageendpunkt zum Konfigurieren Ihres verknüpften Diensts.
    [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]

## <a name="create-a-linked-service"></a>Erstellen eines verknüpften Diensts

In Azure Synapse Analytics definieren Sie in einem verknüpften Dienst Ihre Verbindungsinformationen für andere Dienste. In diesem Abschnitt erstellen Sie einen verknüpften Dienst für Azure Data Explorer.

1. Wählen Sie in Synapse Studio im linken Bereich **Verwalten** > **Verknüpfte Dienste** aus.
1. Wählen Sie **&plus; Neu** aus.

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-linked-service.png" alt-text="Screenshot: Bildschirm „Verknüpfte Dienste“ mit der Liste der vorhandenen Dienste und der hervorgehobenen Schaltfläche „Neu hinzufügen“":::

1. Wählen Sie im Katalog den Dienst **Azure Data Explorer** und dann **Weiter** aus.

    :::image type="content" source="../media/ingest-data-pipeline/select-new-data-explorer-linked-service.png" alt-text="Screenshot: Bereich für neue verknüpfte Dienste mit der Liste verfügbarer Dienste und hervorgehobener Option zum Hinzufügen des neuen Azure Data Explorer-Diensts":::

1. Verwenden Sie auf der Seite „Neue verknüpfte Dienste“ die folgenden Informationen:

    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    |--|--|--|
    | Name | *contosodataexplorerlinkedservice* | Der Name für den neuen verknüpften Azure Data Explorer-Dienst |
    | Authentifizierungsmethode | *Verwaltete Identität* | Die Authentifizierungsmethode für den neuen Dienst |
    | Kontoauswahlmethode | *Manuell eingeben* | Die Methode zum Angeben des Abfrageendpunkts |
    | Endpunkt | *https:\/\/contosodataexplorer.contosoanalytics.dev.kusto.windows.net* | Der Abfrageendpunkt, den Sie sich [zuvor notiert haben](#prerequisites) |
    | Datenbank | *TestDatabase* | Die Datenbank, in der Sie Daten erfassen möchten |

    :::image type="content" source="../media/ingest-data-pipeline/create-new-data-explorer-linked-service.png" alt-text="Screenshot: Detailbereich für den neuen verknüpften Dienst mit den Feldern, die für den neuen Dienst ausgefüllt werden müssen":::

1. Wählen Sie **Verbindung testen** aus, um die Einstellungen zu überprüfen, und wählen Sie dann **Erstellen** aus.

## <a name="create-a-pipeline-to-ingest-data"></a>Erstellen einer Pipeline zum Erfassen von Daten

Eine Pipeline enthält den logischen Ablauf für die Ausführung einer Aktivitätenmenge. In diesem Abschnitt erstellen Sie eine Pipeline mit einer Copy-Aktivität, die Daten aus Ihrer bevorzugten Quelle in einem Data Explorer-Pool erfasst.

1. Wählen Sie in Synapse Studio im linken Bereich **Integrieren** aus.

1. Wählen Sie **&plus;**  > **Pipeline** aus. Im rechten Bereich können Sie Ihre Pipeline benennen.

    :::image type="content" source="../media/ingest-data-pipeline/add-new-data-explorer-pipeline.png" alt-text="Screenshot: Auswahl zum Erstellen einer neuen Pipeline":::

1. Ziehen Sie unter **Aktivitäten** > **Verschieben und transformieren** den Befehl **Daten kopieren** auf die Pipelinecanvas.
1. Wählen Sie die Copy-Aktivität aus, und wechseln Sie zur Registerkarte **Quelle**. Wählen Sie ein neues Quelldataset als Quelle aus, aus der Daten kopiert werden sollen, oder erstellen Sie ein neues Quelldataset.
1. Wechseln Sie zur Registerkarte **Senke**. Klicken Sie auf **Neu**, um ein neues Senkendataset zu erstellen.

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink.png" alt-text="Screenshot: Copy-Aktivität für die Pipeline mit der Auswahl zum Erstellen einer neuen Senke":::

1. Wählen Sie im Katalog das Dataset **Azure Data Explorer** und dann **Weiter** aus.
1. Verwenden Sie im Bereich **Eigenschaften festlegen** die folgenden Informationen, und wählen Sie dann **OK** aus.

    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    |--|--|--|
    | Name | *AzureDataExplorerTable* | Der Name für die neue Pipeline |
    | Verknüpfter Dienst | *contosodataexplorerlinkedservice* | Der verknüpfte Dienst, den Sie zuvor erstellt haben |
    | Tabelle | *StormEvents* | Die zuvor erstellte Tabelle |

    :::image type="content" source="../media/ingest-data-pipeline/add-data-explorer-pipeline-copy-sink-set-properties.png" alt-text="Screenshot: Copy-Aktivität für die Pipeline im Bereich „Eigenschaften festlegen“ mit den Feldern, die für die neue Senke ausgefüllt werden müssen":::

1. Wählen Sie zum Überprüfen der Pipeline auf der Symbolleiste die Option **Überprüfen** aus. Das Ergebnis der Pipelineüberprüfungsausgabe wird rechts auf der Seite angezeigt.

## <a name="debug-and-publish-the-pipeline"></a>Debuggen und Veröffentlichen der Pipeline

Nach Abschluss der Konfiguration Ihrer Pipeline können Sie einen Debuglauf durchführen, bevor Sie Ihre Artefakte zur Überprüfung veröffentlichen, ob alles einwandfrei ist.

1. Wählen Sie auf der Symbolleiste **Debuggen** aus. Der Status der Pipelineausführung wird unten im Fenster auf der Registerkarte **Ausgabe** angezeigt.

1. Wenn die Pipelineausführung erfolgreich war, wählen Sie **Alle veröffentlichen** aus. Mit dieser Aktion werden erstellte Entitäten (Datasets und Pipelines) im Synapse Analytics-Dienst veröffentlicht.
1. Warten Sie, bis die Meldung **Erfolgreich veröffentlicht** angezeigt wird. Wenn Sie Benachrichtigungsmeldungen anzeigen möchten, wählen Sie oben rechts die Schaltfläche mit der Glocke aus.

## <a name="trigger-and-monitor-the-pipeline"></a>Auslösen und Überwachen der Pipeline

In diesem Abschnitt lösen Sie die im vorherigen Schritt veröffentlichte Pipeline manuell aus.

1. Wählen Sie in der Symbolleiste die Option **Trigger hinzufügen** und dann **Jetzt auslösen**. Klicken Sie auf der Seite **Pipelineausführung** auf **OK**.

1. Wechseln Sie in der linken Randleiste zur Registerkarte **Monitor**. Sie sehen eine Pipelineausführung, die von einem manuellen Trigger ausgelöst wird.
1. Wenn die Pipelineausführung erfolgreich abgeschlossen wurde, wählen Sie den Link unter der Spalte **Pipelinename** aus, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen. Da in diesem Beispiel nur eine Aktivität vorhanden ist, wird in der Liste nur ein Eintrag angezeigt.
1. Wenn Sie Details zum Kopiervorgang anzeigen möchten, wählen Sie unter der Spalte **Activity name** (Aktivitätsname) den Link **Details** (das Brillensymbol) aus. Sie können Details wie die Menge der Daten, die aus der Quelle in die Senke kopiert wurden, den Datendurchsatz, die Ausführungsschritte mit entsprechender Dauer sowie die verwendeten Konfigurationen überwachen.
1. Wählen Sie oben den Link **Alle Pipelineausführungen** aus, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**.
1. Vergewissern Sie sich, dass Ihre Daten korrekt in den dedizierten Data Explorer-Pool geschrieben werden.

## <a name="next-steps"></a>Nächste Schritte

- [Analysieren mit Data Explorer](../../get-started-analyze-data-explorer.md)
- [Überwachen von Data Explorer-Pools](../data-explorer-monitor-pools.md)
