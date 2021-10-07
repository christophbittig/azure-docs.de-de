---
title: Installieren der Lösung „Azure Data Factory-Analyse“ über den Azure Marketplace
description: Erfahren Sie wie die Lösung „Azure Data Factory-Analytics“ über den Azure Marketplace installieren können.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4ae7015c575dc10c9f8c2ec2b896e75e1a2a2a91
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838153"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Installieren der Lösung „Azure Data Factory-Analyse“ über den Azure Marketplace

Diese Lösung liefert Ihnen eine Zusammenfassung der Gesamtintegrität Ihrer Data Factory-Instanz mit Optionen zum Durchführen eines Drilldowns zu den Details und zum Beheben von Problemen mit unerwarteten Verhaltensmustern. Anhand von umfassenden Ansichten, die standardmäßig vorhanden sind, können Sie Erkenntnisse zu wichtigen Verarbeitungsschritten gewinnen, z. B.:

* Zentrale Übersicht über Pipeline-, Aktivitäts- und Triggerausführungen einer Data Factory-Instanz
* Drilldown in Data Factory-Aktivitätsausführungen nach Typ
* Zusammenfassung mit Informationen zur wichtigsten Data Factory-Pipeline und zu Aktivitätsfehlern

1. Navigieren Sie zu **Azure Marketplace**, wählen Sie den Filter **Analysen** aus, und suchen Sie nach **Azure Data Factory-Analyse (Vorschau)** .

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Navigieren Sie zu &quot;Azure Marketplace&quot;, geben Sie &quot;Analytics-Filter&quot; ein, und wählen Sie &quot;Azure Data Factory-Analytics (Vorschau)&quot;"::: aus.

1. Details zu **Azure Data Factory-Analyse (Vorschau)**

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Details zu &quot;Azure Data Factory-Analytics (Vorschau)&quot;":::

1. Wählen Sie **Erstellen** aus, und erstellen Sie dann den **Log Analytics-Arbeitsbereich**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="Erstellen einer neuen Lösung":::

## <a name="monitor-data-factory-metrics"></a>Überwachen von Data Factory-Metriken

Beim Installieren dieser Lösung wird im Arbeitsmappenabschnitt des ausgewählten Log Analytics-Arbeitsbereichs ein Standardsatz von Sichten erstellt. Dadurch werden die folgenden Metriken aktiviert:

* ADF-Ausführungen – 1) Pipelineausführungen nach Data Factory
* ADF-Ausführungen – 2) Aktivitätsausführungen nach Data Factory
* ADF-Ausführungen – 3) Triggerausführungen nach Data Factory
* ADF-Fehler – 1) Top 10-Pipelinefehler nach Data Factory
* ADF-Fehler – 2) Top 10-Aktivitätsausführungen nach Data Factory
* ADF-Fehler – 3) Top 10-Triggerfehler nach Data Factory
* ADF-Statistik – 1) Aktivitätsausführungen nach Typ
* ADF-Statistik – 2) Triggerausführungen nach Typ
* ADF-Statistik – 3) Höchstdauer der Pipelineausführungen

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="Fenster mit den hervorgehobenen Optionen &quot;Arbeitsmappen (Vorschau)&quot; und &quot;AzureDataFactoryAnalytics&quot;":::

Sie können u.a. die voranstehenden Metriken visualisieren, die Abfragen hinter diesen Metriken betrachten, die Abfragen bearbeiten Benachrichtigungen erstellen oder andere Aktionen ausführen.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="Grafische Darstellung von Pipelineausführungen durch Data Factory":::

> [!NOTE]
> Azure Data Factory Analytics (Vorschau) sendet Diagnoseprotokolle an _ressourcenspezifische_ Zieltabellen. Sie können Abfragen für die folgenden Tabellen schreiben: _ADFPipelineRun_, _ADFTriggerRun_ und _ADFActivityRun_.

## <a name="next-steps"></a>Nächste Schritte

[Programmseitiges Überwachen und Verwalten von Pipelines](monitor-programmatically.md)
