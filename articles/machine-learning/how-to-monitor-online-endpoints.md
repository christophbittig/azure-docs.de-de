---
title: Überwachen verwalteter Onlineendpunkte (Vorschau)
titleSuffix: Azure Machine Learning
description: Mit Application Insights können Sie verwaltete Onlineendpunkte überwachen und Warnungen erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 05/03/2021
ms.topic: conceptual
ms.custom: how-to, devplatv2
ms.openlocfilehash: c6b370d568b7448ae6417f050cbc902378571151
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548163"
---
# <a name="monitor-managed-online-endpoints-preview"></a>Überwachen verwalteter Onlineendpunkte (Vorschau)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

In diesem Artikel erfahren Sie, wie Sie [verwaltete Azure Machine Learning-Onlineendpunkte (Vorschau)](concept-endpoints.md) überwachen. Verwenden Sie Application Insights, um Metriken anzuzeigen und Warnungen zu erstellen, damit Sie in Bezug auf Ihre verwalteten Onlineendpunkte auf dem neuesten Stand bleiben.

In diesem Artikel wird Folgendes behandelt:

> [!div class="checklist"]
> * Anzeigen von Metriken für Ihren verwalteten Onlineendpunkt
> * Erstellen eines Dashboards für Ihre Metriken
> * Erstellen einer Metrikwarnung

## <a name="prerequisites"></a>Voraussetzungen

- Bereitstellen eines verwalteten Azure Machine Learning-Onlineendpunkts (Vorschau).
- Sie benötigen mindestens [Lesezugriff](../role-based-access-control/role-assignments-portal.md) für den Endpunkt.

## <a name="view-metrics"></a>Anzeigen von Metriken

Führen Sie die folgenden Schritte aus, um Metriken für einen verwalteten Endpunkt oder eine verwaltete Bereitstellung anzuzeigen:
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zur Ressource für den verwalteten Onlineendpunkt oder die verwaltete Onlinebereitstellung.

    Bei verwalteten Onlineendpunkten und -bereitstellungen handelt es sich um ARM-Ressourcen (Azure Resource Manager), die sich in der besitzenden Ressourcengruppe befinden. Suchen Sie nach den Ressourcentypen **Onlineendpunkt für maschinelles Lernen** und **Onlinebereitstellung für maschinelles Lernen**.

1. Wählen Sie in der linken Spalte **Metriken** aus.

## <a name="available-metrics"></a>Verfügbare Metriken

Abhängig von der ausgewählten Ressource unterscheiden sich die angezeigten Metriken. Der Gültigkeitsbereich von Metriken für verwaltete Onlineendpunkte und verwaltete Onlinebereitstellungen (Vorschau) ist unterschiedlich.

### <a name="metrics-at-endpoint-scope"></a>Metriken im Endpunktbereich

- Anforderungswartezeit
- Anforderungswartezeit P50 (Anforderungswartezeit im 50. Perzentil)
- Anforderungswartezeit P90 (Anforderungswartezeit im 90. Perzentil)
- Anforderungswartezeit P95 (Anforderungswartezeit im 95. Perzentil)
- Anforderungen pro Minute

Nehmen Sie die Aufteilung nach den folgenden Dimensionen vor:

- Bereitstellung
- Statuscode
- Statuscodeklasse

### <a name="metrics-at-deployment-scope"></a>Metriken im Bereitstellungsbereich

- CPU-Auslastung
- Bereitstellungskapazität (die Anzahl der Instanzen des angeforderten Instanztyps)
- Datenträgerverwendung
- GPU-Arbeitsspeicherauslastung (gilt nur für GPU-Instanzen)
- GPU-Auslastung (gilt nur für GPU-Instanzen)
- Speicherauslastung

Nehmen Sie die Aufteilung nach den folgenden Dimensionen vor:

- InstanceId

## <a name="create-a-dashboard"></a>Erstellen eines Dashboards

Sie können benutzerdefinierte Dashboards erstellen, um Daten aus mehreren Quellen im Azure-Portal zu visualisieren, einschließlich der Metriken für Ihren verwalteten Onlineendpunkt. Weitere Informationen finden Sie unter [Erstellen von benutzerdefinierten KPI-Dashboards mithilfe von Application Insights](../azure-monitor/app/tutorial-app-dashboards.md#add-custom-metric-chart).
    
## <a name="create-an-alert"></a>Erstellen einer Warnung

Sie können auch benutzerdefinierte Warnungen erstellen, damit Sie über wichtige Statusaktualisierungen Ihres verwalteten Onlineendpunkts informiert werden:

1. Wählen Sie dazu oben rechts auf der Seite „Metriken“ die Option **Neue Warnungsregel** aus.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-new-alert-rule.png" alt-text="Überwachen von Onlineendpunkten: Screenshot mit der rot umrandeten Schaltfläche „Neue Warnungsregel“":::

1. Wählen Sie einen Bedingungsnamen aus, um anzugeben, wann die Warnung ausgelöst werden soll.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-configure-signal-logic.png" alt-text="Überwachen von Onlineendpunkten: Screenshot mit der rot umrandeten Schaltfläche „Signallogik konfigurieren“":::

1. Wählen Sie **Aktionsgruppen hinzufügen** > **Create action groups** (Aktionsgruppen erstellen) aus, um anzugeben, was bei der Auslösung Ihrer Warnung geschieht.

1. Wählen Sie **Warnungsregel erstellen** aus, um die Erstellung Ihrer Warnung abzuschließen.


## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Anzeigen der Kosten für Ihren bereitgestellten Endpunkt](./how-to-view-online-endpoints-costs.md).
* Erfahren Sie mehr über den [Metrik-Explorer](../azure-monitor/essentials/metrics-charts.md).