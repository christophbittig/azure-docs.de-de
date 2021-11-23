---
title: 'Tutorial: Sammeln von Ressourcenprotokollen von einer Azure-Ressource'
description: Tutorial zum Konfigurieren von Diagnoseeinstellungen zum Senden von Ressourcenprotokollen von einer Azure-Ressource in einem Log Analytics-Arbeitsbereich, wo diese per Protokollabfrage analysiert werden können
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 11/08/2021
ms.openlocfilehash: 90a1f193bf1f136e4d3501b68bfab8346604fbbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297975"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Tutorial: Sammeln und Analysieren von Ressourcenprotokollen von einer Azure-Ressource
Ressourcenprotokolle bieten detaillierte Einblicke in die Vorgänge einer Azure-Ressource und sind hilfreich bei der Überwachung der Integrität und Verfügbarkeit der Ressource. Ressourcenprotokolle werden von Azure-Ressourcen automatisch generiert. Sie müssen jedoch eine Diagnoseeinstellung erstellen, um sie zu erfassen. In diesem Tutorial erfahren Sie, wie Sie eine Diagnoseeinstellung erstellen, um Ressourcenprotokolle an einen Log Analytics-Arbeitsbereich zu senden, in dem Sie sie mit Protokollabfragen analysieren können.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Log Analytics-Arbeitsbereich in Azure Monitor
> * Erstellen einer Diagnoseeinstellung zum Sammeln von Ressourcenprotokollen 
> * Erstellen einer einfachen Protokollabfrage zum Analysieren von Protokollen


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes: 

- Eine zu überwachende Azure-Ressource. Sie können jede Ressource in Ihrem Azure-Abonnement verwenden, die Diagnoseeinstellungen unterstützt. Um zu überprüfen, ob eine Ressource Diagnoseeinstellungen unterstützt, navigieren Sie im Azure-Portal zum Menü der Ressource, und vergewissern Sie sich, dass im Abschnitt **Überwachung** des Menüs eine Option **Diagnoseeinstellungen** vorhanden ist.


> [!NOTE]
> Dieses Verfahren gilt nicht für virtuelle Azure-Computer, da ihr Menü **Diagnoseeinstellungen** zum Konfigurieren der Diagnoseerweiterung verwendet wird.

## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]

## <a name="create-a-diagnostic-setting"></a>Erstellen einer Diagnoseeinstellung
[Diagnoseeinstellungen](../essentials/diagnostic-settings.md) definieren, wohin Ressourcenprotokolle für eine bestimmte Ressource gesendet werden sollen. Eine einzelne Diagnoseeinstellung kann mehrere [Ziele](../essentials/diagnostic-settings.md#destinations) enthalten. In diesem Tutorial verwenden wir nur einen Log Analytics-Arbeitsbereich.

Wählen Sie im Menü Ihrer Ressource im Abschnitt **Überwachung** die Option **Diagnoseeinstellungen** aus, und klicken Sie auf **Diagnoseeinstellung hinzufügen**.

> [!NOTE]
> Für einige Ressourcen muss unter Umständen eine zusätzliche Auswahl vorgenommen werden. Beispielsweise müssen Sie für ein Speicherkonto eine Ressource auswählen, bevor die Option **Diagnoseeinstellung hinzufügen** angezeigt wird. Unter Umständen fällt Ihnen auch die Bezeichnung **Vorschau** für einige Ressourcen auf, wenn ihre Diagnoseeinstellungen derzeit als Public Preview verfügbar sind.

:::image type="content" source="media/tutorial-resource-logs/diagnostic-settings.png" lightbox="media/tutorial-resource-logs/diagnostic-settings.png"alt-text="Diagnoseeinstellungen":::


Jede Diagnoseeinstellung besteht aus drei Teilen:
 
   - **Name**: Dieser Teil hat keine Auswirkungen und dient nur zur Beschreibung.
   - **Kategorien**: Kategorien der Protokolle, die an die einzelnen Ziele gesendet werden können. Die Kategorien unterscheiden sich je nach Azure-Dienst.
   - **Ziele:** Ein oder mehrere Ziele zum Senden der Protokolle. Für alle Azure-Dienste gelten dieselben möglichen Ziele. Jede Diagnoseeinstellung kann ein oder mehrere Ziele definieren, aber nicht mehr als ein Ziel eines bestimmten Typs. 

Geben Sie einen Namen für die Diagnoseeinstellung ein, und wählen Sie die Kategorien aus, die Sie erfassen möchten. Eine Definition der verfügbaren Kategorien finden Sie in der Dokumentation jedes Diensts. **AllMetrics** sendet die gleichen Plattformmetriken, die auch in Azure Monitor-Metriken für die Ressource verfügbar sind, an den Arbeitsbereich. Dadurch können Sie diese Daten zusammen mit anderen Überwachungsdaten mit Protokollabfragen analysieren. Klicken Sie auf **An Log Analytics-Arbeitsbereich senden**, und wählen Sie dann den erstellten Arbeitsbereich aus. 

:::image type="content" source="media/tutorial-resource-logs/diagnostic-setting-details.png" lightbox="media/tutorial-resource-logs/diagnostic-setting-details.png"alt-text="Details der Diagnoseeinstellung":::

Klicken Sie auf **Speichern**, um die Diagnoseeinstellungen zu speichern.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Verwenden einer Protokollabfrage zum Abrufen von Protokollen
Die Daten werden mithilfe einer in Kusto Query Language (KQL) geschriebenen Protokollabfrage aus einem Log Analytics-Arbeitsbereich abgerufen. Für viele Azure-Dienste steht eine Reihe von vorab erstellen Abfragen zur Verfügung, sodass Sie für den Einstieg keine KQL-Kenntnisse benötigen.

Wählen Sie im Menü Ihrer Ressource die Option **Protokolle** aus. Log Analytics wird mit dem Fenster **Abfragen** geöffnet, das vordefinierte Abfragen für Ihren **Ressourcentyp** enthält. 

> [!NOTE]
> Sollte das Fenster **Abfragen** nicht geöffnet werden, klicken Sie rechts oben auf **Abfragen**. 

:::image type="content" source="media/tutorial-resource-logs/queries.png" lightbox="media/tutorial-resource-logs/queries.png"alt-text="Screenshot: Beispielabfragen unter Verwendung von Ressourcenprotokollen":::


Durchsuchen Sie die verfügbaren Abfragen. Identifizieren Sie eine auszuführende Abfrage, und klicken Sie auf **Ausführen**. Die Abfrage wird dem Abfragefenster hinzugefügt, und die Ergebnisse werden zurückgegeben.

:::image type="content" source="media/tutorial-resource-logs/query-results.png" lightbox="media/tutorial-resource-logs/query-results.png"alt-text="Screenshot: Ergebnisse einer Bespielprotokollabfrage":::

## <a name="next-steps"></a>Nächste Schritte
Sie haben Ressourcenprotokolle gesammelt. Erstellen Sie nun eine Protokollabfragewarnung, um proaktiv benachrichtigt zu werden, wenn interessante Daten in Ihren Protokolldaten identifiziert werden.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer Protokollabfragewarnung für eine Azure-Ressource](../alerts/tutorial-log-alert.md)
