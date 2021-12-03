---
title: Installieren der Lösung „Azure Data Factory-Analyse“ über den Azure Marketplace
description: Erfahren Sie wie die Lösung „Azure Data Factory-Analytics“ über den Azure Marketplace installieren können.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 07e60aef3e6e3a968d22f76351d00cb722e99f78
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850411"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Installieren der Lösung „Azure Data Factory-Analyse“ über den Azure Marketplace

Diese Lösung gibt Ihnen einen Überblick über den Gesamtzustand Ihrer Datenfabrik, mit Optionen zum Aufschlüsseln von Details und zur Fehlerbehebung bei unerwarteten Verhaltensmustern. Mit umfangreichen, sofort einsatzbereiten Ansichten erhalten Sie Einblicke in wichtige Verarbeitungen, darunter:

* Zusammenfassung der Pipeline, der Aktivität und der Triggerläufe der Data Factory auf einen Blick.
* Möglichkeit der Aufschlüsselung der Datenfabrik-Aktivitätsausführungen nach Art.
* Zusammenfassung der wichtigsten Pipeline-Aktivitätsfehler der Datenfabrik.

1. Gehen Sie zu **Azure Marketplace**, wählen Sie den Filter **Analytics**, und suchen Sie nach **Azure Data Factory Analytics (Preview)** .

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Screenshot, der zeigt, wie Sie zum Azure Marketplace gehen, den Analytics-Filter auswählen und Azure Data Factory Analytics (Preview) auswählen ":::.

1. Überprüfen Sie die Details zu **Azure Data Factory Analytics (Preview)** .

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Screenshot mit Details zu Azure Data Factory Analytics (Preview).":::

1. Wählen Sie **Erstellen**, und erstellen oder wählen Sie dann **Log Analytics Workspace**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="Screenshot, der die Erstellung einer neuen Lösung zeigt.":::

## <a name="monitor-data-factory-metrics"></a>Überwachen von Data Factory-Metriken

Beim Installieren dieser Lösung wird im Arbeitsmappenabschnitt des ausgewählten Log Analytics-Arbeitsbereichs ein Standardsatz von Sichten erstellt. Infolgedessen werden die folgenden Metriken aktiviert:

* Datenläufe - 1) Pipeline-Läufe nach Data Factory
* ADF läuft - 2) Aktivitätsausführung durch Data Factory
* ADF-Läufe - 3) Trigger-Läufe nach Data Factory
* ADF-Fehler - 1) Top 10 Pipeline-Fehler nach Data Factory
* ADF-Fehler - 2) Top 10 Aktivitätsläufe nach Data Factory
* ADF-Fehler - 3) Top 10 Trigger-Fehler nach Data Factory
* ADF-Statistiken - 1) Aktivitätsausführungen nach Art
* ADF-Statistiken - 2) Auslöserläufe nach Typ
* ADF-Statistiken - 3) Maximale Dauer der Pipeline-Läufe

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="Screenshot, der ein Fenster mit Workbooks (Vorschau) und AzureDataFactoryAnalytics hervorgehoben zeigt. ":::

Sie können u.a. die voranstehenden Metriken visualisieren, die Abfragen hinter diesen Metriken betrachten, die Abfragen bearbeiten Benachrichtigungen erstellen oder andere Aktionen ausführen.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="Screenshot, der eine grafische Darstellung der Pipeline-Läufe durch Data Factory zeigt.":::

> [!NOTE]
> Azure Data Factory Analytics (Preview) sendet Diagnoseprotokolle an _Ressourcen-spezifische_ Zieltabellen. Sie können Abfragen für die folgenden Tabellen schreiben: _ADFPipelineRun_, _ADFTriggerRun_ und _ADFActivityRun_.

## <a name="next-steps"></a>Nächste Schritte

[Programmseitiges Überwachen und Verwalten von Pipelines](monitor-programmatically.md)
