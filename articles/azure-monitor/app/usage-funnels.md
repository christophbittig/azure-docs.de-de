---
title: Application Insights-Trichter
description: In diesem Artikel wird erläutert, wie Sie Trichter dazu verwenden können, sich über die Interaktionen der Kunden mit Ihrer Anwendung zu informieren.
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 9733cc11c346263ad0801f97bd876e5b8c7d4fd9
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131674"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Informieren Sie sich mit Application Insights-Trichtern darüber, wie die Kunden Ihre Anwendung verwenden

Es ist für Ihr Unternehmen von essenzieller Bedeutung, ein Gefühl für die Kundenerfahrung zu entwickeln. Wenn in Ihrer Anwendung mehrere Schritte durchlaufen werden, müssen Sie wissen, ob die meisten Kunden den gesamten Prozess durchlaufen oder diesen an einem bestimmten Punkt beenden. Das Durchlaufen einer Reihe von Schritten in einer Webanwendung wird als *Trichter* (auch: Verkaufstrichter) bezeichnet. Sie können Application Insights-Trichter dazu verwenden, Erkenntnisse über Ihre Benutzer zu erhalten und die schrittweisen Konvertierungsraten zu überwachen. 

## <a name="create-your-funnel"></a>Erstellen Ihres Trichters
Bevor Sie Ihren Trichter erstellen, sollten Sie eine Entscheidung darüber treffen, welche Frage damit beantwortet werden soll. Beispiel: Sie möchten wissen, wie viele Benutzer die Homepage oder ein Kundenprofil anzeigen und ein Ticket erstellen. 

So erstellen Sie einen Trichter

1. Wählen Sie auf der Registerkarte **Trichter** die Option **Bearbeiten** aus.
1. Wählen Sie Ihren *Obersten Schritt* aus.

     :::image type="content" source="./media/usage-funnels/funnel.png" alt-text="Screenshot der Registerkarte „Trichter“ und Auswählen von Schritten auf der Registerkarte „Bearbeiten“" lightbox="./media/usage-funnels/funnel.png":::.

1. Um Filter auf den Schritt anzuwenden, wählen Sie **Filter hinzufügen** aus, die angezeigt werden, nachdem Sie ein Element für den obersten Schritt ausgewählt haben.
1. Wählen Sie dann Ihren *Zweiten Schritt* aus usw.
1. Wählen Sie die Registerkarte **Ansicht** aus, um Ihre Trichterergebnisse anzuzeigen.

      :::image type="content" source="./media/usage-funnels/funnel-2.png" alt-text="Screenshot der Registerkarte „Trichter“ auf der Registerkarte „Ansicht“ mit Ergebnissen aus dem obersten und zweiten Schritt" lightbox="./media/usage-funnels/funnel-2.png":::.

1. Um Ihren Trichter für eine spätere Anzeige zu speichern, wählen Sie oben **Speichern** aus. Mithilfe von **Öffnen** können Sie Ihre gespeicherten Trichter öffnen.

### <a name="funnels-features"></a>Trichter-Features

- Wenn für Ihre App Stichproben erstellt werden, wird ein Stichprobenentnahme-Banner angezeigt. Wenn Sie das Banner auswählen, wird ein Kontextbereich mit Informationen zum Deaktivieren der Stichprobenfunktion geöffnet. 
- Wählen Sie einen Schritt aus, um rechts weitere Details anzuzeigen. 
- Im Graphen zum Verlauf der Abschlüsse sind die Abschlussraten für die letzten 90 Tage angegeben. 
- Entwickeln Sie ein besseres Verständnis Ihrer Benutzer, indem Sie auf das Benutzertool zugreifen. Sie können in jedem Schritt Filter verwenden. 

## <a name="next-steps"></a>Nächste Schritte
  * [Nutzungsübersicht](usage-overview.md)
  * [Benutzer-, Sitzungs- und Ereignisanalyse in Azure Application Insights](usage-segmentation.md)
  * [Vermerkdauer](usage-retention.md)
  * [Arbeitsmappen](../visualize/workbooks-overview.md)
  * [Hinzufügen von Benutzerkontext](./usage-overview.md)
  * [Exportieren nach Power BI](./export-power-bi.md)