---
title: Einbetten eines Player-Widgets in Power BI – Azure Video Analyzer
description: Sie können Azure Video Analyzer für fortlaufende oder ereignisbasierte Videoaufzeichnungen verwenden. In diesem Artikel wird beschrieben, wie Sie Videos in Microsoft Power BI einbetten, um eine anpassbare Benutzeroberfläche für Ihre Benutzer bereitzustellen.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bb367735097200da273ab908d4a3205d62798d5b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560205"
---
# <a name="embed-player-widget-in-power-bi"></a>Einbetten des Player-Widgets in Power BI


Mit Azure Video Analyzer können Sie Videos und die zugehörigen Metadaten für den Rückschluss in Ihrer Video Analyzer Cloudressource [aufzeichnen](detect-motion-record-video-clips-cloud.md). Video Analyzer verfügt über ein [Player-Widget](player-widget.md), ein einfach einzubettendes Widget, mit dem Client-Apps Videos und Metadaten für den Rückschluss wiedergeben können.

Dashboards stellen eine aufschlussreiche Möglichkeit dar, Ihr Geschäft zu überwachen und Ihre wichtigsten Metriken in einer Übersicht anzuzeigen. Ein Power BI-Dashboard ist ein leistungsfähiges Tool zur Kombination von Videos mit mehreren Datenquellen, einschließlich Telemetriedaten von IoT Hub. In diesem Tutorial erfahren Sie, wie Sie mithilfe des [Microsoft Power BI](https://powerbi.microsoft.com/)-Webdiensts ein oder mehrere Player-Widgets zu einem Dashboard hinzufügen können.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/embed-block-diagram.svg" alt-text="Blockdiagramm, um einen Widget für einen Azure Video Analyzer Player in Microsoft Power BI einzubetten.":::

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung

- [Player-Widget](player-widget.md) von Azure Video Analyzer
- Einführung in [Power BI-Dashboards](/power-bi/create-reports/service-dashboards)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.
- Schließen Sie entweder [Erkennen von Bewegung und Aufzeichnen von Videos](detect-motion-record-video-clips-cloud.md) oder [Fortlaufende Videoaufzeichnung](continuous-video-recording.md) ab – eine Pipeline mit Videosenke ist erforderlich.

  > [!NOTE]
  > In Ihrem Video Analyzer-Konto sollte mindestens ein Video aufgezeichnet sein, damit Sie fortfahren können. Überprüfen Sie die Liste der Videos, indem Sie sich bei Ihrem Azure Video Analyzer-Konto anmelden (Abschnitt „Videos > Video Analyzer“).

- Ein [Power BI](https://powerbi.microsoft.com/)-Konto.

## <a name="create-a-token"></a>Erstellen Sie ein Token

1. Führen Sie die Schritte zum [Erstellen eines Tokens](access-policies.md#creating-a-token) aus.
2. Stellen Sie sicher, dass Sie die generierten Werte für _Aussteller, Zielgruppe, Schlüsseltyp, Algorithmus, Schlüssel-ID, RSA-Schlüsselmodulus, RSA-Schlüsselexponent und Token_ speichern. Sie benötigen diese Werte, wenn Sie unten eine Zugriffsrichtlinie erstellen.

## <a name="get-embed-code-for-player-widget"></a>Abrufen des Einbettungscodes für das Player-Widget

1. Melden Sie sich mit Ihren Anmeldeinformationen beim [Azure-Portal](https://portal.azure.com/) an. Suchen Sie Ihr Video Analyzer-Konto, das Sie für die Erfüllung der Voraussetzungen verwendet haben, und öffnen Sie den Video Analyzer-Kontobereich.
2. Führen Sie die Schritte zum [Erstellen einer Zugriffsrichtlinie](access-policies.md#creating-an-access-policy) aus.
3. Wählen Sie im Abschnitt **Video Analyzer** die Option **Videos** aus.
4. Wählen Sie ein beliebiges Video aus der Liste aus.
5. Klicken Sie auf **Widget-Setup**. Auf der rechten Seite öffnet sich der Bereich **Use widget in your application** (Widget in Ihrer Anwendung verwenden). Scrollen Sie nach unten zu **Option 2 – using HTML** (HTML verwenden), und kopieren Sie den Code. Anschließend fügen Sie ihn in einen Text-Editor ein. Klicken Sie auf die Schaltfläche **Schließen**.

   :::image type="content" source="./media/power-bi/widget-code.png" alt-text="Screenshot: Kopieren von Widget-HTML-Code aus dem AVA-Portal und Speichern zur späteren Nutzung":::

6. Bearbeiten Sie den in Schritt 5 kopierten HTML-Code, um die Werte für das
   - Token **AVA-API-JWT-TOKEN** zu ersetzen. Ersetzen Sie ihn durch den Wert des Tokens, den Sie im Schritt „Erstellen eines Tokens“ gespeichert haben. Achten Sie darauf, die spitzen Klammern zu entfernen.
   - Optional: Sie können in diesem Code auch andere Änderungen der Benutzeroberfläche vornehmen, z. B. die Kopfzeile von „Example Player Widget“ (Beispiel-Player-Widget) in „Continuous Video Recording“ (Fortlaufende Videoaufzeichnung) ändern.

## <a name="add-widget-in-power-bi-dashboard"></a>Hinzufügen des Widgets zum Power BI-Dashboard

1. Öffnen Sie den [Power BI-Dienst](http://app.powerbi.com/) in Ihrem Browser. Wählen Sie im Navigationsbereich **Mein Arbeitsbereich** aus.

   :::image type="content" source="./media/power-bi/powerbi-ws.png" alt-text="Screenshot: Power BI-Homepage des Arbeitsbereichs":::

2. Erstellen Sie ein neues Dashboard, indem Sie auf **New** > **Dashboard** (Neu > Dashboard) klicken oder ein vorhandenes Dashboard öffnen. Wählen Sie den Dropdownpfeil **Bearbeiten** und dann **Add a tile** (Kachel hinzufügen) aus. Wählen Sie **Web content** > **Next** (Webinhalt > Weiter) aus.
3. Geben Sie in **Add web content tile** (Kachel mit Webinhalt hinzufügen) Ihren **Einbettungscode** aus dem vorherigen Abschnitt ein. Klicken Sie auf **Übernehmen**.

   :::image type="content" source="./media/power-bi/embed-code.png" alt-text="Screenshot: Einbetten des HTML-Codes in Power BI-Dashboardkachel":::

4. Sie sehen ein Player-Widget mit einem Video, das an das Dashboard angeheftet ist.

   :::image type="content" source="./media/power-bi/one-player.png" alt-text="Screenshot: Ein Videoplayer-Widget wurde hinzugefügt":::

5. Um weitere Videos aus dem Abschnitt „Azure Video Analyzer Videos“ hinzuzufügen, befolgen Sie dieselben Schritten in diesem Abschnitt.

> [!NOTE]
> Für das Hinzufügen mehrerer Videos aus demselben Video Analyzer-Konto ist ein einzelner Satz von Zugriffsrichtlinien und Token ausreichend.

Hier folgt ein Beispiel für mehrere Videos, die an ein einzelnes Power BI-Dashboard angeheftet sind.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/two-players.png" alt-text="Screenshot: Zwei Videoplayer-Widgets, die als Beispiel angeheftet sind":::

## <a name="next-steps"></a>Nächste Schritte

- [Echtzeitvisualisierung von KI-Rückschlussereignissen in Power BI](visualize-ai-events-power-bi.md)
- Weitere Informationen zum [API-Widget](https://github.com/Azure/video-analyzer/tree/main/widgets)
