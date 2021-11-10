---
title: Konfigurieren von Diagnoseeinstellungen und Arbeitsbereich
description: Erfahren Sie, wie Sie Diagnoseeinstellungen und einen Log Analytics-Arbeitsbereich zum Überwachen von Azure Data Factory konfigurieren.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 6637cd45f6c7dd9260eb3bb5af1601457213158f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845283"
---
# <a name="configure-diagnostic-settings-and-a-workspace"></a>Konfigurieren von Diagnoseeinstellungen und Arbeitsbereich

Erstellen Sie Diagnoseeinstellungen für Ihre Data Factory, oder fügen Sie diese hinzu.

1. Wechseln Sie im Azure-Portal zu **Monitor**. Wählen Sie **Einstellungen** > **Diagnoseeinstellungen** aus.

1. Wählen Sie die Data Factory aus, für die Sie eine Diagnoseeinstellung festlegen möchten.

1. Sollten für die ausgewählte Data Factory keine Einstellungen vorhanden sein, werden Sie aufgefordert, eine Einstellung zu erstellen. Wählen Sie **Diagnose aktivieren**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image1.png" alt-text="Screenshot: Erstellen einer Diagnoseeinstellung, wenn keine Einstellungen vorhanden sind":::

   Sind Einstellungen für die Data Factory vorhanden, wird eine Liste mit den Einstellungen angezeigt, die bereits für die Data Factory konfiguriert sind. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

   :::image type="content" source="media/data-factory-monitor-oms/add-diagnostic-setting.png" alt-text="Screenshot: Hinzufügen einer Diagnoseeinstellung, wenn Einstellungen vorhanden sind":::

1. Legen Sie einen Namen für Ihre Einstellung fest, wählen Sie **An Log Analytics senden** aus, und wählen Sie dann unter **Log Analytics-Arbeitsbereich** einen Arbeitsbereich aus.

    * Im Modus _Azure-Diagnose_ werden Diagnoseprotokolle in die Tabelle _AzureDiagnostics_ übernommen.
    * Im _ressourcenspezifischen_ Modus werden Diagnoseprotokolle von Azure Data Factory in die folgenden Tabellen geleitet:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Sie können verschiedene Protokolle, die für Ihre Workloads relevant sind, für das Senden an Log Analytics-Tabellen auswählen. Beispiel: 
        - Falls Sie SQL Server Integration Services (SSIS) überhaupt nicht nutzen, müssen Sie keine SSIS-Protokolle auswählen. 
        - Wenn Sie das Starten, Beenden oder Warten von Azure-SSIS Integration Runtime-Vorgängen (IR) protokollieren möchten, wählen Sie SSIS IR-Protokolle aus. 
        - Wenn Sie SSIS-Paketausführungen über T-SQL in SQL Server Management Studio (SSMS), im SQL Server-Agent oder in anderen dafür vorgesehenen Tools aufrufen, wählen Sie SSIS-Paketprotokolle aus. 
        - Wenn Sie Ausführungen von SSIS-Paketen über die entsprechenden Aktivitäten vom Typ „SSIS-Paket ausführen“ in Data Factory-Pipelines aufrufen, wählen Sie alle Protokolle aus.

    * Bei Auswahl von _AllMetrics_ werden verschiedene Data Factory-Metriken zur Verfügung gestellt, die Sie überwachen oder für die Sie Warnungen auslösen können. Zu diesen Metriken gehören die Metriken für die Ausführung von Data Factory-Aktivitäten, Pipelines und Triggern sowie für SSIS IR-Vorgänge und SSIS-Paketausführungen.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Screenshot: Benennen Ihrer Einstellungen und Auswählen eines Log Analytics-Arbeitsbereichs":::

    > [!NOTE]
    > Da eine Azure-Protokolltabelle nicht mehr als 500 Spalten enthalten darf, *empfehlen wir Ihnen dringend*, den _Modus „Ressourcenspezifisch“_ auszuwählen. Weitere Informationen finden Sie in der [Referenz zu Azure-Diagnoseprotokollen](/azure/azure-monitor/reference/tables/azurediagnostics).

1. Wählen Sie **Speichern** aus.

Nach einigen Augenblicken wird die neue Einstellung in der Liste der Einstellungen für diese Data Factory angezeigt. Diagnoseprotokolle werden in diesen Arbeitsbereich gestreamt, sobald neue Ereignisdaten generiert werden. Zwischen der Ausgabe eines Ereignisses und dessen Anzeige in Log Analytics können bis zu 15 Minuten vergehen.

## <a name="next-steps"></a>Nächste Schritte

[Einrichten von Diagnoseprotokollen über die Azure Monitor-REST-API](monitor-logs-rest.md)
