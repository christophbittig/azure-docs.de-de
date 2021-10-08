---
title: Verwalten von Anomalien bei der getakteten Abrechnung in Partner Center | Azure Marketplace
description: Hier erfahren Sie, wie die automatische Anomalieerkennung für getaktete Abrechnungen dafür sorgt, dass Ihre Kunden für die gemessene Nutzung von Angeboten über Ihren kommerziellen Marketplace eine korrekte Rechnung erhalten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: sayantanroy83
ms.author: sroy
ms.date: 9/27/2021
ms.openlocfilehash: 5d253ab72d2915145444485c98131027555efdbc
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081608"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>Verwalten von Anomalien bei der getakteten Abrechnung in Partner Center

Die Option für die benutzerdefinierte getaktete Abrechnung ist aktuell für [SaaS-Angebote](plan-saas-offer.md) (Software-as-a-Service) und [Azure-Anwendungen](plan-azure-application-offer.md#types-of-plans) mit einem Plan einer verwalteten Anwendung verfügbar.

Wenn Sie mit der Option für getaktete Abrechnung Angebote im Marketplace-Programm erstellen, mit denen Sie die Nutzung basierend auf nicht standardmäßigen Einheiten berechnen können, müssen Sie wissen, wann der Kunde den Dienst in größerem Umfang als erwartet verwendet hat.

## <a name="use-the-anomaly-detection-feature"></a>Verwenden des Anomalieerkennungsfeatures

Sie als Partner müssen die Überschreitungsnutzung ihrer Kunden bezüglich ihrer SaaS- oder Azure Managed Applications-Angebote an Microsoft melden, bevor Microsoft eine Rechnung für den Kunden erstellen kann. Wenn eine falsche Nutzung gemeldet wird, erhält der Kunde möglicherweise eine falsche Rechnung, was die Glaubwürdigkeit von Microsoft sowie die des Partners schädigen kann.

Um sicherzustellen, dass Ihre Kunden eine korrekte Abrechnung erhalten, verwenden Sie das Feature zur **Anomalieerkennung** für SaaS-Apps und für Pläne verwalteter Azure-Anwendungen. Dieses Feature überwacht die Nutzung und vergleicht sie mit der getakteten Abrechnung, um so einen zu erwartenden Nutzungswert innerhalb eines erwarteten Bereichs vorherzusagen. Wenn sich die Nutzung außerhalb des erwarteten Bereichs befindet, wird sie als unerwartete Nutzung (Anomalie) behandelt, und für Sie erhalten eine Warnmeldung auf der Seite „Übersicht“ im Programm „Kommerzieller Marketplace“ im Partner Center. Sie können die Nutzung Ihrer Kunden täglich für jede von Ihnen festgelegte benutzerdefinierte Verbrauchseinheitsdimension nachverfolgen.

## <a name="view-and-manage-metered-usage-anomalies"></a>Anzeigen und Verwalten von Anomalien bei der gemessenen Nutzung

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie auf der Homepage die Kachel **Insights** aus.

    [ ![Abbildung der Kachel „Insights“ auf der Homepage von Partner Center.](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. Wählen Sie im linken Menü **Verbrauch** aus.
1. Klicken Sie auf die Registerkarte **Metered usage anomalies** (Anomalien bei der gemessenen Nutzung).

    [![Screenshot der Registerkarte „Metered usage anomalies“ (Anomalien bei der gemessenen Nutzung) auf der Seite „Usage“ (Nutzung)](./media/anomaly-detection/metered-usage-anomalies-workspaces.png)](./media/anomaly-detection/metered-usage-anomalies-workspaces.png#lightbox)<br>
    ***Abbildung 1: Registerkarte „Metered usage anomalies“ (Anomalien bei der gemessenen Nutzung)***

1. Als Herausgeber werden Sie für jede ermittelte Nutzungsanomalie im Hinblick auf die getaktete Abrechnung aufgefordert, Untersuchungen anzustellen und zu ermitteln, ob es sich um eine Anomalie handelt oder nicht. Klicken Sie auf **Mark as anomaly** (Als Anomalie kennzeichnen), um die Diagnose zu bestätigen.

     [![Screenshot des Dialogfelds „Mark as anomaly“ (Als Anomalie kennzeichnen)](./media/anomaly-detection/mark-as-anomaly-workspaces.png)](./media/anomaly-detection/mark-as-anomaly-workspaces.png#lightbox)<br>
    ***Abbildung 2: Dialogfeld „Mark as anomaly“ (Als Anomalie kennzeichnen)***

1. Wenn Sie der Meinung sind, dass es sich bei der erkannten Überschreitungsnutzung um keine Anomalie handelt, können Sie Feedback geben, indem Sie bei der gemeldeten Anomalie in Partner Center für die konkrete Überschreitungsnutzung auf **Not an anomaly** (Keine Anomalie) klicken.

    [![Screenshot des Dialogfelds „Why is it not an anomaly?“ (Warum ist es keine Anomalie?)](./media/anomaly-detection/why-is-it-not-an-anomaly-workspaces.png)](./media/anomaly-detection/why-is-it-not-an-anomaly-workspaces.png#lightbox)
    ***Abbildung 3: Dialogfeld „Why is it not an anomaly?“ (Warum ist es keine Anomalie?)***

1. Sie können auf der Seite nach unten scrollen, damit eine Liste aller nicht bestätigter Anomalien angezeigt wird. Die Liste zeigt alle Anomalien an, die Sie nicht bestätigt haben. Für alle gemeldeten Anomalien in Partner Center können Sie entweder „Als Anomalie kennzeichnen“ oder „Keine Anomalie“ auswählen.

   [![Screenshot der Liste nicht bestätigter Anomalien auf der Seite „Usage“ (Nutzung) in Partner Center](./media/anomaly-detection/unacknowledged-anomalies-workspaces.png)](./media/anomaly-detection/unacknowledged-anomalies-workspaces.png#lightbox)<br>
    ***Abbildung 4: Liste nicht bestätigter Anomalien in Partner Center***

    Standardmäßig werden gekennzeichnete Anomalien mit geschätzten finanziellen Auswirkungen von mehr als 100 USD in Partner Center angezeigt. Sie können jedoch die Option **Alle** in der Liste **Geschätzte finanzielle Auswirkungen von Anomalien** auswählen, um alle gekennzeichneten Anomalien anzuzeigen.

    [ ![Screenshot: alle gemessenen Verbrauchsanomalien für das ausgewählte Angebot.](./media/anomaly-detection/all-anomalies.png) ](./media/anomaly-detection/all-anomalies.png#lightbox)

1. Außerdem wird ein Aktionsprotokoll für Anomalien angezeigt, in dem die Aktionen aufgeführt sind, die Sie für die Überschreitungsnutzungen durchgeführt haben. Im Aktionsprotokoll wird angezeigt, für welche Überschreitungsnutzungsereignisse Sie „Als Anomalie kennzeichnen“ oder „Keine Anomalie“ ausgewählt haben.

   [![Veranschaulicht das Aktionsprotokoll für Anomalien auf der Seite „Nutzung“.](./media/anomaly-detection/anomaly-action-log-workspaces.png)](./media/anomaly-detection/anomaly-action-log-workspaces.png#lightbox)<br>
   ***Abbildung 5: Aktionsprotokoll für Anomalien***

1. Rückwirkende Korrekturen für Überschreitungsnutzungsereignisse in exportierten Berichten werden in Partner Center-Analysen nicht unterstützt. Sie können in Partner Center die korrigierte Überschreitungsnutzung zu einer Anomalie eingeben, und die Details werden für eine weitere Untersuchung an Microsoft-Teams übermittelt. Je nach Untersuchungsergebnis überweist Microsoft ein Guthaben an den Kunden, dem ein zu hoher Betrag in Rechnung gestellt wurde. Wenn Sie eine der gekennzeichneten Anomalien auswählen, können Sie auf **Mark as anomaly** (Als Anomalie kennzeichnen) klicken, um die Überschreitungsnutzungsanomalie als tatsächliche Anomalie zu kennzeichnen.

   [![Veranschaulicht das Dialogfeld „Als Anomalie kennzeichnen.](./media/anomaly-detection/mark-as-anomaly-workspaces.png)](./media/anomaly-detection/mark-as-anomaly-workspaces.png#lightbox)<br>
   ***Abbildung 6: Dialogfeld „Als Anomalie kennzeichnen“***

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Navigationsmenü **Kommerzieller Marketplace** > **Analysieren** > **Nutzung** aus.
1. Klicken Sie auf die Registerkarte **Metered usage anomalies** (Anomalien bei der gemessenen Nutzung).

    [![Screenshot der Registerkarte „Metered usage anomalies“ (Anomalien bei der gemessenen Nutzung) auf der Seite „Usage“ (Nutzung)](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)<br>
    ***Abbildung 1: Registerkarte „Metered usage anomalies“ (Anomalien bei der gemessenen Nutzung)***

1. Als Herausgeber werden Sie für jede ermittelte Nutzungsanomalie im Hinblick auf die getaktete Abrechnung aufgefordert, Untersuchungen anzustellen und zu ermitteln, ob es sich um eine Anomalie handelt oder nicht. Klicken Sie auf **Mark as anomaly** (Als Anomalie kennzeichnen), um die Diagnose zu bestätigen.

     [![Screenshot des Dialogfelds „Mark as anomaly“ (Als Anomalie kennzeichnen)](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)<br>
    ***Abbildung 2: Dialogfeld „Mark as anomaly“ (Als Anomalie kennzeichnen)***

1. Wenn Sie der Meinung sind, dass es sich bei der erkannten Überschreitungsnutzung um keine Anomalie handelt, können Sie Feedback geben, indem Sie bei der gemeldeten Anomalie in Partner Center für die konkrete Überschreitungsnutzung auf **Not an anomaly** (Keine Anomalie) klicken.

    [![Screenshot des Dialogfelds „Why is it not an anomaly?“ (Warum ist es keine Anomalie?)](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Abbildung 3: Dialogfeld „Why is it not an anomaly?“ (Warum ist es keine Anomalie?)***

1. Sie können auf der Seite nach unten scrollen, damit eine Liste aller nicht bestätigter Anomalien angezeigt wird. Die Liste zeigt alle Anomalien an, die Sie nicht bestätigt haben. Für alle gemeldeten Anomalien in Partner Center können Sie entweder „Als Anomalie kennzeichnen“ oder „Keine Anomalie“ auswählen.

   [![Screenshot der Liste nicht bestätigter Anomalien auf der Seite „Usage“ (Nutzung) in Partner Center](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)<br>
    ***Abbildung 4: Liste nicht bestätigter Anomalien in Partner Center***

    Standardmäßig werden gekennzeichnete Anomalien mit geschätzten finanziellen Auswirkungen von mehr als 100 USD in Partner Center angezeigt. Sie können jedoch die Option **Alle** in der Liste **Geschätzte finanzielle Auswirkungen von Anomalien** auswählen, um alle gekennzeichneten Anomalien anzuzeigen.

    :::image type="content" source="./media/anomaly-detection/all-anomalies.png" alt-text="Screenshot: alle Nutzungsanomalien bei Verbrauchseinheiten für das ausgewählte Angebot":::

1. Außerdem wird ein Aktionsprotokoll für Anomalien angezeigt, in dem die Aktionen aufgeführt sind, die Sie für die Überschreitungsnutzungen durchgeführt haben. Im Aktionsprotokoll wird angezeigt, für welche Überschreitungsnutzungsereignisse Sie „Als Anomalie kennzeichnen“ oder „Keine Anomalie“ ausgewählt haben.

   [![Veranschaulicht das Aktionsprotokoll für Anomalien auf der Seite „Nutzung“.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox)<br>
   ***Abbildung 5: Aktionsprotokoll für Anomalien***

1. Rückwirkende Korrekturen für Überschreitungsnutzungsereignisse in exportierten Berichten werden in Partner Center-Analysen nicht unterstützt. Sie können in Partner Center die korrigierte Überschreitungsnutzung zu einer Anomalie eingeben, und die Details werden für eine weitere Untersuchung an Microsoft-Teams übermittelt. Je nach Untersuchungsergebnis überweist Microsoft ein Guthaben an den Kunden, dem ein zu hoher Betrag in Rechnung gestellt wurde. Wenn Sie eine der gekennzeichneten Anomalien auswählen, können Sie auf **Mark as anomaly** (Als Anomalie kennzeichnen) klicken, um die Überschreitungsnutzungsanomalie als tatsächliche Anomalie zu kennzeichnen.

   [![Veranschaulicht das Dialogfeld „Als Anomalie kennzeichnen.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox)<br>
   ***Abbildung 6: Dialogfeld „Als Anomalie kennzeichnen“***

---

Wenn eine Überschreitungsnutzung in Partner Center erstmalig gemeldet wird, erhalten Sie 30 Tage Zeit, für die Anomalie „Als Anomalie kennzeichnen“ oder „Keine Anomalie“ auszuwählen. Nach Ablauf der 30 Tage können Sie als Herausgeber für die Anomalien keine Aktionen mehr ausführen.

> [!IMPORTANT]
> Nicht gemeldete Überschreitungsnutzungseinheiten können nicht rückwirkend korrigiert und finanziell beglichen werden.

Sie können alle Anomalien (bestätigt oder nicht bestätigt) für einen ausgewählten Berechnungszeitraum anzeigen. Die verschiedenen Berechnungszeiträume sind die letzten 30 Tage, die letzten 60 Tage und die letzten 90 Tage.

> [!IMPORTANT]
> Sie sollten innerhalb von 30 Tagen nach erstmaliger Meldung einer Anomalie in Partner Center tätig werden.

Per Filter im Widget können Sie einzelne Angebote und einzelne benutzerdefinierte Verbrauchseinheiten auswählen.

Wenn Sie eine Überschreitungsnutzung als Anomalie gekennzeichnet haben oder ein Modell, das eine Anomalie gemeldet hat, bestätigt haben, können Sie die Auswahl nicht mehr zurück in „Keine Anomalie“ ändern.

> [!IMPORTANT]
> Wenn ein zu hoher Betrag berechnet wurde, können Sie Überschreitungsnutzungen erneut übermitteln.

---

## <a name="see-also"></a>Weitere Informationen
- [Volumenabrechnung für SaaS mit dem Messungsdienst für den kommerziellen Marketplace](./partner-center-portal/saas-metered-billing.md)
- [Getaktete Abrechnung für verwaltete Anwendungen](marketplace-metering-service-apis.md)
- [Anomalieerkennungsdienst für getaktete Abrechnung](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
