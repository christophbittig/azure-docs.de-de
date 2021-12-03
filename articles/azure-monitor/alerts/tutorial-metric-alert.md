---
title: 'Tutorial: Erstellen einer Metrikwarnung für eine Azure-Ressource'
description: Hier erfahren Sie, wie Sie mit dem Azure-Metrik-Explorer ein Metrikdiagramm erstellen.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 431ef4418741a0406eeb4d350879dbc1be7ae737
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350850"
---
# <a name="tutorial-create-a-metric-alert-for-an-azure-resource"></a>Tutorial: Erstellen einer Metrikwarnung für eine Azure-Ressource
Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Metrikwarnungsregeln erstellen eine Warnung, wenn ein Metrikwert aus einer Azure-Ressource einen Schwellenwert überschreitet.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Metrikwarnungsregel über den Metrik-Explorer
> * Konfigurieren des Warnungsschwellenwerts
> * Erstellen einer Aktionsgruppe zum Definieren von Benachrichtigungsdetails

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes: 

- Eine zu überwachende Azure-Ressource. Sie können eine beliebige Ressource in Ihrem Azure-Abonnement verwenden, die Metriken unterstützt. Ob eine Ressource Metriken unterstützt, kann wie folgt überprüft werden: Navigieren Sie im Azure-Portal zum Menü der Ressource, und vergewissern Sie sich, dass im Abschnitt **Überwachung** des Menüs eine Option vom Typ **Metrik** vorhanden ist.
- Diagramm im Metrik-Explorer mit mindestens einer Metrik, für die eine Warnung ausgegeben werden soll. Führen Sie die Schritte unter [Tutorial: Analysieren von Metriken für eine Azure-Ressource](../essentials/tutorial-metrics.md) aus.

## <a name="create-new-alert-rule"></a>Erstellen einer neuen Warnungsregel
Klicken Sie im Metrik-Explorer auf **Neue Warnungsregel**. Die Regel wird mit dem Zielobjekt und der Metrik vorkonfiguriert, die Sie im Metrik-Explorer ausgewählt haben.

:::image type="content" source="media/tutorial-metric-alert/new-alert-rule.png" alt-text="Neue Warnungsregel" lightbox="media/tutorial-metric-alert/new-alert-rule.png":::

## <a name="configure-alert-logic"></a>Konfigurieren der Warnungslogik
Die Ressource ist bereits ausgewählt. Sie müssen die Signallogik ändern, um den Schwellenwert und alle anderen Details für die Warnungsregel anzugeben. 

Klicken Sie auf den **Bedingungsnamen**, um diese Einstellungen anzuzeigen. 

:::image type="content" source="./media/tutorial-metric-alert/configuration.png" lightbox="./media/tutorial-metric-alert/configuration.png" alt-text="Konfiguration von Warnungsregeln":::

Das Diagramm zeigt den Wert des ausgewählten Signals im Laufe der Zeit, damit Sie erkennen können, wann die Warnung ausgelöst wurde. Dieses Diagramm wird aktualisiert, wenn Sie die Signallogik angeben.

:::image type="content" source="./media/tutorial-metric-alert/signal-logic.png" lightbox="./media/tutorial-metric-alert/signal-logic.png" alt-text="Signallogik für Warnungsregel":::

Die **Warnungslogik** wird durch die Bedingung und die Auswertungszeit definiert. Die Warnung wird ausgelöst, wenn diese Bedingung erfüllt ist. Geben Sie unter **Schwellenwert** einen Wert für Ihre Warnungsregel an, und ändern Sie **Operator** und **Aggregationstyp**, um die erforderliche Logik zu definieren.

:::image type="content" source="./media/tutorial-metric-alert/alert-logic.png" lightbox="./media/tutorial-metric-alert/alert-logic.png" alt-text="Warnungslogik für Warnungsregel":::

Sie können die Standardzeitgranularität übernehmen oder sie an Ihre Anforderungen anpassen. **Häufigkeit der Auswertung** definiert, wie oft die Warnungslogik ausgewertet wird. **Aggregation granularity** (Aggregationsgranularität) definiert das Zeitintervall, für das die gesammelten Werte aggregiert werden.

Klicken Sie auf **Fertig**, wenn Sie mit dem Konfigurieren der Signallogik fertig sind.

## <a name="configure-actions"></a>Konfigurieren von Aktionen
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>Konfigurieren der Details
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="./media/tutorial-metric-alert/alert-details.png" lightbox="./media/tutorial-metric-alert/alert-details.png" alt-text="Warnungsregeldetails":::


Klicken Sie auf **Warnungsregel erstellen**, um die Warnungsregel zu erstellen.


## <a name="view-the-alert"></a>Anzeigen der Warnung
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>Nächste Schritte
Sie haben erfahren, wie Sie eine Metrikwarnung für eine Azure-Ressource erstellen. Nutzen Sie nun eins der folgenden Tutorials, um Protokolldaten zu sammeln:

> [!div class="nextstepaction"]
> [Tutorial: Sammeln und Analysieren von Ressourcenprotokollen von einer Azure-Ressource](../essentials/tutorial-resource-logs.md)
> [!div class="nextstepaction"]
> [Tutorial: Sammeln von Gastprotokollen und Metriken von einem virtuellen Azure-Computer](../vm/tutorial-monitor-vm-guest.md)