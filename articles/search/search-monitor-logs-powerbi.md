---
title: Visualisieren von Protokollen und Metriken mit Power BI
description: Visualisieren von Azure Cognitive Search-Protokollen und -Metriken mit Power BI
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 57dd31643560b54f6f54b8352966a4c05cb6a35b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892891"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualisieren von Azure Cognitive Search-Protokollen und -Metriken mit Power BI

[Azure Cognitive Search](./search-what-is-azure-search.md) kann Vorgangsprotokolle und Dienstmetriken an ein Azure Storage-Konto senden, die Sie dann in Power BI visualisieren können. In diesem Artikel werden die Schritte und die Verwendung einer Power BI-Vorlagen-App zum Visualisieren der Daten erläutert. Die Vorlage kann Ihnen helfen, detaillierte Einblicke in Ihren Suchdienst zu gewinnen, einschließlich Informationen über Abfragen, Indizierung, Vorgänge und Dienstmetriken.

Sie finden die Power BI-Vorlagen-App **Azure Cognitive Search: Analyze Logs and Metrics** im [Power BI-Apps-Marketplace](https://appsource.microsoft.com/marketplace/apps).

## <a name="set-up-the-app"></a>Einrichten der App

1. Aktivieren Sie Metriken und Ressourcenprotokollierung für Ihren Suchdienst:

    1. Erstellen oder identifizieren Sie ein vorhandenes [Azure Storage-Konto](../storage/common/storage-account-create.md), in dem Sie die Protokolle archivieren können.
    1. Navigieren Sie im Azure-Portal zu Ihrem Azure Cognitive Search-Dienst.
    1. Wählen Sie in der linken Spalte im Abschnitt „Überwachung“ die Option **Diagnoseeinstellungen** aus.

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="Screenshot, der zeigt, wie die Diagnoseeinstellungen im Abschnitt „Überwachung“ des Azure Cognitive Search-Diensts ausgewählt werden." border="false":::

    1. Wählen Sie **+ Diagnoseeinstellung hinzufügen**  aus.
    1. Aktivieren Sie **In einem Speicherkonto archivieren**, geben Sie Ihre Speicherkontoinformationen an, und aktivieren Sie **OperationLogs** und **AllMetrics**.

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="Screenshot zur Auswahl von Metriken und Ressourcenprotokollierung auf der Seite mit den Diagnoseeinstellungen.":::
    1. Wählen Sie **Speichern** aus.

1. Verwenden Sie nach dem Aktivieren der Protokollierung ihren Suchdienst, um mit dem Generieren von Protokollen und Metriken zu beginnen. Es kann bis zu einer Stunde dauern, bis die Container mit diesen Protokollen im Blobspeicher angezeigt werden. Es wird ein Container **insights-logs-operationlogs** für Suchdatenverkehrsprotokolle und ein Container **insights-metrics-pt1m** angezeigt.

1. Suchen Sie die Power BI-App für Azure Cognitive Search im [Marketplace für Power BI-Apps](https://appsource.microsoft.com/marketplace/apps), und installieren Sie sie in einem neuen oder vorhandenen Arbeitsbereich. Die App wird **Azure Cognitive Search: Analyze Logs and Metrics** genannt.

1. Nachdem Sie die App installiert haben, wählen Sie die App in der Liste der Apps in Power BI aus.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="Screenshot mit der Azure Cognitive Search-App zum Auswählen aus der Liste der Apps.":::

1. Wählen Sie **Verbinden** aus, um die Verbindung mit Ihren Daten herzustellen.

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="Screenshot, der zeigt, wie Sie eine Verbindung mit Ihren Daten in der Azure Cognitive Search-App herstellen.":::

1. Geben Sie den Namen des Speicherkontos ein, das die Datei enthält. Standardmäßig betrachtet die App die Daten der letzten 10 Tage, aber dieser Wert kann mit dem Parameter **Tage** geändert werden.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="Screenshot, der zeigt, wie der Speicherkontoname und die Anzahl der Abfragetage auf der Seite „Herstellen einer Verbindung mit Azure Cognitive Search“ eingegeben wird.":::

1. Wählen Sie **Schlüssel** als Authentifizierungsmethode aus, und geben Sie Ihren Speicherkontoschlüssel an. Wählen Sie **Privat** als Datenschutzebene aus. Klicken Sie auf „Anmelden“, und beginnen Sie den Ladevorgang.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="Screenshot, der zeigt, wie die Authentifizierungsmethode, der Kontoschlüssel und die Datenschutzebene auf der Seite „Herstellen einer Verbindung mit Azure Cognitive Search“ eingegeben werden.":::

1. Warten Sie, bis die Daten aktualisiert sind. Dies kann eine Weile dauern, je nachdem, wie viele Daten Sie nutzen. Der folgende Indikator zeigt Ihnen, ob die Daten noch aktualisiert werden.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="Screenshot, der zeigt, wie die Informationen auf der Seite „Datenaktualisierung“ gelesen werden.":::

1. Nachdem die Datenaktualisierung abgeschlossen ist, wählen Sie **Azure Cognitive Search-Bericht** aus, um den Bericht anzuzeigen.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="Screenshot, der zeigt, wie der Azure Cognitive Search-Bericht auf der Seite „Datenaktualisierung“ ausgewählt wird.":::

1. Aktualisieren Sie die Seite nach dem Öffnen des Berichts unbedingt, damit sie mit Ihren Daten geöffnet wird.

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Screenshot des Azure Cognitive Search Power BI-Berichts.":::

## <a name="modify-app-parameters"></a>Ändern von App-Parametern

Wenn Sie Daten aus einem anderen Speicherkonto visualisieren oder die Anzahl der Tage, deren Daten abgefragt werden sollen, ändern möchten, führen Sie die folgenden Schritte aus, um die Parameter **Tage** und **StorageAccount** zu ändern.

1. Navigieren Sie zu ihren Power BI-Apps, suchen Sie Ihre Azure Cognitive Search-App, und wählen Sie die Schaltfläche **App bearbeiten** aus, um den Arbeitsbereich anzuzeigen.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="Screenshot, der zeigt, wie die Schaltfläche „App bearbeiten“ für die Azure Cognitive Search-App ausgewählt wird.":::

1. Wählen Sie aus den Datasetoptionen **Einstellungen** aus.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="Screenshot, der zeigt, wie Sie „Einstellungen“ aus den Azure Cognitive Search-Datasetoptionen auswählen":::

1. Ändern Sie auf der Registerkarte „Datasets“ die Parameterwerte, und wählen Sie **Anwenden** aus. Wenn ein Problem mit der Verbindung vorliegt, aktualisieren Sie die Anmeldeinformationen für die Datenquelle auf derselben Seite.

1. Navigieren Sie zurück zum Arbeitsbereich, und wählen Sie **Jetzt aktualisieren** aus den Datasetoptionen aus.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="Screenshot, der zeigt, wie Sie „Jetzt aktualisieren“ aus den Azure Cognitive Search-Datasetoptionen auswählen":::

1. Öffnen Sie den Bericht, um die aktualisierten Daten anzuzeigen. Möglicherweise müssen Sie den Bericht auch aktualisieren, um die neuesten Daten anzuzeigen.

## <a name="troubleshooting-report-issues"></a>Problembehandlung bei Berichtsproblemen

Wenn Sie feststellen, dass Ihre Daten nicht angezeigt werden, befolgen Sie diese Schritte zur Problembehandlung:

1. Öffnen Sie den Bericht, und aktualisieren Sie die Seite, um sicherzustellen, dass Sie die neuesten Daten anzeigen. Der Bericht enthält eine Option zum Aktualisieren der Daten. Wählen Sie diese Option aus, um die neuesten Daten zu erhalten.

1. Stellen Sie sicher, dass der angegebene Speicherkontoname und der angegebene Zugriffsschlüssel korrekt sind. Der Speicherkontoname sollte dem Konto entsprechen, das mit ihren Suchdienstprotokollen konfiguriert wurde.

1. Vergewissern Sie sich, dass Ihr Speicherkonto die Container **insights-logs-operationlogs** und **insights-metrics-pt1m** und jeder Container Daten enthält. Die Protokolle und Metriken befinden sich in einigen Ordnerebenen.

1. Überprüfen Sie, ob das Dataset weiterhin aktualisiert wird. Der Aktualisierungsstatusindikator wird oben in Schritt 8 angezeigt. Wenn die Aktualisierung noch immer ausgeführt wird, warten Sie, bis die Aktualisierung beendet ist, um den Bericht zu öffnen und zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

+ [Überwachen von Suchvorgängen und -aktivitäten](search-monitor-usage.md)
+ [Was ist Power BI?](/power-bi/fundamentals/power-bi-overview)
+ [Grundlegende Konzepte für Designer im Power BI-Dienst](/power-bi/service-basic-concepts)