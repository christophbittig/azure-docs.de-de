---
title: 'Schnellstart: Erste Schritte mit der 1-Klick-Erfassung von Daten (Vorschau)'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der 1-Klick-Erfassung Daten in Data Explorer-Pools aufnehmen.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: d5fef80a72e210e71eea0331b0d6098d5414d78a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479767"
---
# <a name="quickstart-ingest-data-using-one-click-preview"></a>Schnellstart: 1-Klick-Erfassung von Daten (Vorschau)

Die 1-Klick-Erfassung ermöglicht eine einfache, schnelle und intuitive Datenerfassung. Dank der 1-Klick-Erfassung können Sie schnell mit dem Erfassen von Daten, dem Erstellen von Datenbanktabellen und dem Zuordnen von Strukturen beginnen. Wählen Sie Daten aus unterschiedlichen Arten von Quellen in verschiedenen Datenformaten aus – entweder als einmaligen oder als kontinuierlichen Erfassungsprozess.

Die 1-Klick-Erfassung ist aufgrund der folgenden Funktionen überaus nützlich:

* Intuitiver Ablauf im Rahmen des Erfassungs-Assistenten
* Erfassung von Daten innerhalb weniger Minuten
* Erfassung von Daten aus verschiedenen Arten von Quellen: lokale Datei, Blobs und Container (bis zu 10.000 Blobs)
* Erfassung von Daten in verschiedenen [Formaten](#file-formats)
* Erfassung von Daten in neuen oder vorhandenen Tabellen
* Empfehlung und einfache Änderung von Tabellenzuordnung und -schema
<!-- * Continue ingestion easily and quickly from a container with [Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion) -->

Die 1-Klick-Erfassung ist besonders nützlich, wenn Daten erstmalig erfasst werden oder Sie nicht mit dem Schema Ihrer Daten vertraut sind.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- Erstellen Sie eine Tabelle: [!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-create-table-studio.md)]

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Vergewissern Sie sich, dass die Tabelle erstellt wurde. Wählen Sie im linken Fensterbereich **Daten** aus, klicken Sie auf das Menü mit weiteren Optionen für *contosodataexplorer*, und wählen Sie dann **Aktualisieren** aus. Erweitern Sie unter *contosodataexplorer* den Knoten **Tabellen**, und stellen Sie sicher, dass die Tabelle *StormEvents* in der Liste angezeigt wird.

## <a name="access-the-one-click-wizard"></a>Zugreifen auf den 1-Klick-Assistenten

Der Assistent für die 1-Klick-Erfassung leitet Sie durch die 1-Klick-Erfassung.

* So greifen Sie aus Azure Synapse auf den Assistenten zu

    1. Wählen Sie in Synapse Studio im linken Bereich **Daten** aus.
    1. Klicken Sie unter **Azure Data Explorer-Datenbanken** mit der rechten Maustaste auf die relevante Datenbank, und wählen Sie dann **In Azure Data Explorer öffnen** aus.

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-synapse.png" alt-text="Screenshot von Azure Synapse Studio: Öffnen von Azure Data Explorer im Kontext eines spezifischen Pools":::

    1. Klicken Sie mit der rechten Maustaste auf den relevanten Pool, und wählen Sie **Neue Daten erfassen** aus.

* So greifen Sie über das Azure-Portal auf den Assistenten zu

    1. Suchen Sie im Azure-Portal nach dem relevanten Synapse-Arbeitsbereich, und wählen Sie ihn aus.
    1. Wählen Sie unter **Data Explorer-Pools** den relevanten Pool aus.
    1. Wählen Sie auf der Startseite **Willkommen** für den Data Explorer-Pool die Option **Neue Daten erfassen** aus.

        :::image type="content" source="../media/ingest-data-one-click/open-azure-data-explorer-portal.png" alt-text="Screenshot des Azure-Portals: Öffnen von Azure Data Explorer im Kontext eines spezifischen Pools":::


* So greifen Sie über die Azure Data Explorer-Webbenutzeroberfläche auf den Assistenten zu

    1. Bevor Sie beginnen, rufen Sie mithilfe der folgenden Schritte die Endpunkte für Abfrage und Datenerfassung ab.
        [!INCLUDE [data-explorer-get-endpoint](../includes/data-explorer-get-endpoint.md)]
    1. Stellen Sie in der Azure Data Explorer-Webbenutzeroberfläche eine Verbindung mit dem *Abfrageendpunkt* her.
    1. Wählen Sie im linken Menü **Abfrage** aus, klicken Sie mit der rechten Maustaste auf die **Datenbank** oder **Tabelle**, und wählen Sie **Neue Daten erfassen** aus.

## <a name="one-click-ingestion-wizard"></a>Assistent für die 1-Klick-Erfassung

> [!NOTE]
> In diesem Abschnitt wird der Assistent beschrieben, der Event Hub als Datenquelle verwendet. Sie können diese Schritte auch verwenden, um Daten aus einem Blob, einer Datei, einem Blobcontainer und einem ADLS Gen2-Container zu erfassen.
>

1. Wählen Sie auf der Registerkarte **Ziel** Datenbank und Tabelle für die erfassten Daten aus.

    :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-destination-table.png" alt-text="Screenshot des Azure Data Explorer-Assistenten für die 1-Klick-Erfassung: Auswahl einer Datenbank und Tabelle":::

1. Auf der Registerkarte **Quelle**:
    1. Wählen Sie als **Quelltyp** für die Erfassung *Event Hub* aus.

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-source-type.png" alt-text="Screenshot des Azure Data Explorer-Assistenten für die 1-Klick-Erfassung: Auswahl des Quelltyps":::

    1. Füllen Sie die Event Hub-Datenverbindungsdetails mithilfe der folgenden Informationen aus:

        | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
        |--|--|--|
        | Name der Datenverbindung | *ContosoDataConnection* | Der Name der Event Hub-Datenverbindung |
        | Subscription | *Contoso_Synapse* | Das Abonnement, in dem sich der Event Hub befindet |
        | Even Hub-Namespace | *contosoeventhubnamespace* | Der Namespace für den Event Hub |
        | Consumergruppe | *contosoconsumergroup* | Der Name der Event Hub-Consumergruppe |

        :::image type="content" source="../media/ingest-data-one-click/select-azure-data-explorer-ingest-event-hub-details.png" alt-text="Screenshot des Azure Data Explorer-Assistenten für die 1-Klick-Erfassung: Details der Event Hub-Verbindung":::

    1. Wählen Sie **Weiter** aus.

### <a name="schema-mapping"></a>Schemazuordnung

Der Dienst generiert automatisch Schema- und Erfassungseigenschaften, die Sie ändern können. Abhängig davon, ob die Erfassung in einer neuen oder einer vorhandenen Tabelle erfolgt, können Sie eine vorhandene Zuordnungsstruktur verwenden oder eine neue erstellen.

Führen Sie auf der Registerkarte **Schema** die folgenden Aktionen aus:
   1. Bestätigen Sie den automatisch generierten Komprimierungstyp.
   1. Wählen Sie das [Format Ihrer Daten](#file-formats) aus. Unterschiedliche Formate ermöglichen weitere Änderungen.
   1. Ändern Sie die Zuordnung im [Editor-Fenster](#editor-window).

#### <a name="file-formats"></a>Dateiformate

Die 1-Klick-Erfassung unterstützt das Erfassen von Quelldaten in sämtlichen [von Data Explorer für die Erfassung unterstützten Datenformaten](data-explorer-ingest-data-supported-formats.md).

### <a name="editor-window"></a>Editor-Fenster

Im Fenster **Editor** der Registerkarte **Schema** können Sie Datentabellenspalten nach Bedarf anpassen.

Die Änderungen, die Sie an einer Tabelle vornehmen können, hängen von den folgenden Parametern ab:

* Die **Tabelle** ist neu oder vorhanden.
* Die **Zuordnung** ist neu oder vorhanden.

Tabellentyp | Zuordnungstyp | Verfügbare Anpassungen|
|---|---|---|
|Neue Tabelle   | Neue Zuordnung |Datentyp ändern, Spalte umbenennen, Neue Spalte, Spalte löschen, Spalte aktualisieren, Aufsteigend sortieren, Absteigend sortieren  |
|Vorhandene Tabelle  | Neue Zuordnung | Neue Spalte (für die Sie dann den Datentyp ändern und die Sie umbenennen und aktualisieren können)<br> Spalte aktualisieren, Aufsteigend sortieren, Absteigend sortieren  |
| | Vorhandene Zuordnung | Aufsteigend sortieren, Absteigend sortieren

> [!NOTE]
> Wenn Sie eine neue Spalte hinzufügen oder eine Spalte aktualisieren, können Sie Zuordnungstransformationen ändern. Weitere Informationen finden Sie unter [Zuordnungstransformationen](#mapping-transformations).

<!-- >[!NOTE]
> At any time, you can open the [command editor](one-click-ingestion-new-table.md#command-editor) above the **Editor** pane. In the command editor, you can view and copy the automatic commands generated from your inputs. -->

#### <a name="mapping-transformations"></a>Zuordnungstransformationen

Einige der Datenformatzuordnungen (Parquet, JSON und Avro) unterstützen einfache Transformationen während der Erfassung. Erstellen oder aktualisieren Sie zum Anwenden von Zuordnungstransformationen eine Spalte im [Editorfenster](#editor-window).

Zuordnungstransformationen können für eine Spalte ausgeführt werden, für die als **Typ** der Wert „string“ oder „datetime“ und für **Quelle** der Datentyp „int“ oder „long“ angegeben ist. Die folgenden Zuordnungstransformationen werden unterstützt:

* DateTimeFromUnixSeconds
* DateTimeFromUnixMilliseconds
* DateTimeFromUnixMicroseconds
* DateTimeFromUnixNanoseconds

### <a name="data-ingestion"></a>Datenerfassung

Wenn die Schemazuordnung und die Spaltenbearbeitung abgeschlossen sind, startet der Erfassungs-Assistent die Datenerfassung.

* Beim Erfassen von Daten aus Quellen, die **keine Container** sind, wird die Erfassung sofort ausgeführt.

* Wenn die Datenquelle ein **Container** ist:
    * Die [Richtlinie für die Batchverarbeitung](/azure/data-explorer/kusto/management/batchingpolicy?context=/azure/synapse-analytics/context/context) von Data Explorer aggregiert Ihre Daten.
    * Nach der Erfassung können Sie den Erfassungsbericht herunterladen und die Leistung jedes behandelten Blobs überprüfen.
    <!-- * You can select **Create continuous ingestion** and set up [continuous ingestion using Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion). -->

### <a name="initial-data-exploration"></a>Anfängliche Datenuntersuchung

Nach der Erfassung bietet der Assistent Optionen zur Verwendung von **Schnellbefehlen** für die erste Untersuchung Ihrer Daten.

## <a name="next-steps"></a>Nächste Schritte

- [Analyse mit Data Explorer](../../get-started-analyze-data-explorer.md)
- [Überwachen von Data Explorer-Pools](../data-explorer-monitor-pools.md)
