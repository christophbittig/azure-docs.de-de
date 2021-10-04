---
title: Überprüfungserkenntnisse über Ihre Daten in Azure Purview
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Überprüfungsberichterstellung mit Purview-Erkenntnissen für Ihre Daten anzeigen und verwenden können.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 17935a3dc4aef3c5cd9d6b1e162a2002b3c06e2c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211373"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Überprüfungserkenntnisse über Ihre Daten in Azure Purview

In dieser Schrittanleitung wird beschrieben, wie Sie auf Azure Purview-Erkenntnisberichte zu Überprüfungen Ihrer Daten zugreifen und diese anzeigen und filtern können.

> [!IMPORTANT]
> Azure Purview-Erkenntnisse befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In dieser Schrittanleitung lernen Sie Folgendes:

> [!div class="checklist"]
> * Anzeigen von Erkenntnissen aus Ihrem Purview-Konto
> * Erhalten einer Übersicht über Ihre Überprüfungen

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Beginn mit Purview-Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

* Einrichten Ihrer Azure-Ressourcen und Auffüllen des Kontos mit Daten
* Einrichten und Ausführen einer Überprüfung der Datenquelle

Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Verwenden der Überprüfungserkenntnisse von Purview

Sie können in Azure Purview Quelltypen registrieren und überprüfen. Der Überprüfungsstatus kann in den Überprüfungserkenntnissen über einen Zeitraum angezeigt werden. Die Erkenntnisse geben Aufschluss darüber, wie viele Überprüfungen innerhalb eines bestimmten Zeitraums fehlerhaft oder erfolgreich waren und wie viele abgebrochen wurden.

### <a name="view-scan-insights"></a>Scanerkenntnisse anzeigen

1. Wechseln Sie zum **Azure Purview**-Instanzbildschirm im Azure-Portal, und wählen Sie Ihr Purview-Konto aus.

1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** die Kachel zum **Öffnen von Purview Studio** aus.

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Starten von Purview im Azure-Portal":::

1. Klicken Sie auf der Purview-Seite **Start** im linken Menü auf **Erkenntnisse**.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Anzeigen Ihrer Erkenntnisse im Azure-Portal":::

1. Wählen Sie im Bereich **Erkenntnisse** die Option **Überprüfungen** aus, um den Purview-Bericht mit **Überprüfungserkenntnissen** anzuzeigen.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Anzeigen von KPIs auf hoher Ebene, um die Anzahl der Überprüfungen nach Status anzuzeigen und ausführliche Informationen zu den einzelnen Überprüfungen abzurufen
 
1. Die allgemeinen KPIs zeigen an, wie viele Überprüfungen insgesamt innerhalb eines Zeitraums ausgeführt wurden. Standardmäßig umfasst dieser Zeitraum die letzten 30 Tage. Sie können jedoch auch die letzten sieben Tage auswählen. Basierend auf dem Zeitfilter geben die KPI-Werte die entsprechende Anzahl der Überprüfungen wieder.


1. Sie können je nach ausgewähltem Zeitfilterwert die Verteilung erfolgreicher, fehlerhafter und abgebrochener Überprüfungen pro Woche oder pro Tag im Diagramm anzeigen.

1. Am unteren Rand des Diagramms finden Sie einen Link **Mehr anzeigen**, über den Sie weitere Informationen erhalten. Der Link öffnet die Seite **Überprüfungsstatus** in der Scan Insights-Benutzeroberfläche. Dort sehen Sie einen Überprüfungsnamen und die Häufigkeit, mit der die Überprüfung innerhalb der letzten 30 Tage erfolgreich war, fehlgeschlagen ist oder abgebrochen wurde.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Anzeigen des Überprüfungsstatus im Zeitverlauf":::

4. Sie können eine bestimmte Überprüfung weiter untersuchen, indem Sie den **Überprüfungsnamen** auswählen, wodurch Sie mit dem Überprüfungsverlauf in der Benutzeroberfläche **Data Map** von Azure Purview verbunden werden. Auf der Seite „Ausführungsverlauf“ können Sie die Ausführungs-ID abrufen, die Sie bei der weiteren Untersuchung von Fehlern unterstützt.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Überprüfungsdetails anzeigen":::

5. Schließlich können Sie zur Seite **Überprüfungsstatus** von Scan Insights zurückkehren, indem Sie den Breadcrumbs in der oberen linken Ecke der Seite „Ausführungsverlauf“ folgen.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Anzeigen des Überprüfungsverlaufs"::: 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure Purview **Insights** mit [Data Insights](./concept-insights.md)

* Weitere Informationen zur Benutzeroberfläche **Data Map** von Azure Purview mit [Verwalten von Datenquellen](./manage-data-sources.md)
