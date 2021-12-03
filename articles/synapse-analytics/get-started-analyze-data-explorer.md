---
title: 'Schnellstart: Erste Schritte bei der Analyse mit Data Explorer-Pools (Vorschau)'
description: In diesem Schnellstart erfahren Sie, wie Sie Daten mit Data Explorer analysieren.
ms.topic: quickstart
ms.date: 09/30/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 1473b0db71d0ceaa4e7f78d20b10319047427311
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479802"
---
# <a name="quickstart-analyze-with-data-explorer-preview"></a>Schnellstart: Analyse mit Data Explorer (Vorschau)

In diesem Artikel werden die grundlegenden Schritte zum Laden und Analysieren von Daten mit Data Explorer für Azure Synapse beschrieben.

## <a name="create-a-data-explorer-pool"></a>Erstellen eines Data Explorer-Pools

1. Wählen Sie in Synapse Studio im linken Bereich die Option **Verwalten** > **Data Explorer-Pools** aus.
1. Wählen Sie **Neu**, und geben Sie auf der Registerkarte **Grundlagen** die folgenden Informationen ein:

    | Einstellung | Vorgeschlagener Wert | Beschreibung |
    |--|--|--|
    | Name des Data Explorer-Pools | contosodataexplorer | Dies ist der Name des Data Explorer-Pools. |
    | Workload | Computeoptimiert | Diese Workload bietet ein höheres CPU-zu-SSD-Speicherverhältnis. |
    | Knotengröße | Klein (4 Kerne) | Legen Sie diese Einstellung auf die kleinste Größe fest, um die Kosten für diesen Schnellstart zu senken. |

    > [!IMPORTANT]
    > Beachten Sie, dass für die Namen, die für Data Explorer-Pools verwendet werden können, bestimmte Einschränkungen gelten. Namen dürfen nur aus Kleinbuchstaben und Zahlen bestehen, müssen 4 bis 15 Zeichen lang sein und mit einem Buchstaben beginnen.

1. Wählen Sie **Bewerten + erstellen** > **Erstellen** aus. Ihr Data Explorer-Pool startet den Bereitstellungsprozess.

## <a name="create-a-data-explorer-database"></a>Erstellen einer Data Explorer-Datenbank

1. Wählen Sie in Synapse Studio im linken Bereich **Daten** aus.
1. Wählen Sie **&plus;** (Neue Ressource hinzufügen) > **Data Explorer-Pool** aus, und geben Sie die folgenden Informationen ein:

    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    |--|--|--|
    | Poolname | *contosodataexplorer* | Name des zu verwendende Data Explorer-Pools |
    | Name | *TestDatabase* | Der Datenbankname muss innerhalb des Clusters eindeutig sein. |
    | Standardaufbewahrungszeitraum | *365* | Die Zeitspanne (in Tagen), für die garantiert wird, dass die Daten für Abfragen verfügbar bleiben. Die Zeitspanne wird ab dem Zeitpunkt gemessen, zu dem die Daten erfasst werden. |
    | Standardcachezeitraum | *31* | Die Zeitspanne (in Tagen), wie lange häufig abgefragte Daten im SSD-Speicher oder RAM (und nicht im längerfristigen Speicher) verfügbar bleiben. |

1. Wählen Sie **Erstellen**, um die Datenbank zu erstellen. Die Erstellung dauert in der Regel weniger als eine Minute.

## <a name="ingest-sample-data-and-analyze-with-a-simple-query"></a>Erfassen von Beispieldaten und Analyse mit einer einfachen Abfrage

1. Wählen Sie in Synapse Studio im linken Bereich **Entwickeln** aus.
1. Wählen Sie unter **KQL-Skripts** die Option **&plus;** (Neue Ressource hinzufügen) > **KQL-Skript** aus. Im rechten Bereich können Sie Ihr Skript benennen.
1. Wählen Sie im Menü **Verbinden mit** den Eintrag *contosodataexplorer* aus.
1. Wählen Sie im Menü **Datenbank verwenden** die Option *TestDatabase* aus.
1. Fügen Sie den folgenden Befehl ein, und wählen Sie **Ausführen** aus, um eine StormEvents-Tabelle zu erstellen.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```

    > [!TIP]
    > Vergewissern Sie sich, dass die Tabelle erstellt wurde. Wählen Sie im linken Fensterbereich **Daten** aus, klicken Sie auf das Menü mit weiteren Optionen für *contosodataexplorer*, und wählen Sie dann **Aktualisieren** aus. Erweitern Sie unter *contosodataexplorer* den Knoten **Tabellen**, und stellen Sie sicher, dass die Tabelle *StormEvents* in der Liste angezeigt wird.

1. Fügen Sie den folgenden Befehl ein, und wählen Sie **Ausführen** aus, um Daten in einer StormEvents-Tabelle zu erfassen.

    ```Kusto
    .ingest into table StormEvents 'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?sv=2019-12-12&ss=b&srt=o&sp=r&se=2022-09-05T02:23:52Z&st=2020-09-04T18:23:52Z&spr=https&sig=VrOfQMT1gUrHltJ8uhjYcCequEcfhjyyMX%2FSc3xsCy4%3D' with (ignoreFirstRecord=true)
    ```

1. Fügen Sie nach Abschluss der Erfassung die folgende Abfrage ein, wählen Sie im Fenster die Abfrage aus, und wählen Sie dann **Ausführen** aus.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

    Die Abfrage gibt die folgenden Ergebnisse aus den erfassten Beispieldaten zurück.

    :::image type="content" source="data-explorer/media/get-started-analyze-data-explorer/sample-query-results.png" alt-text="Ergebnisse der Abfrageausführung für die Beispieldaten":::

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Verwenden von KQL-Abfragen](/azure/data-explorer/kusto/query/tutorial?context=/azure/synapse-analytics/context/context&pivots=synapse)
- [Überwachen von Data Explorer-Pools](data-explorer/data-explorer-monitor-pools.md)
