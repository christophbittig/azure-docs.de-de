---
title: Dashboard „Marketplace-Erkenntnisse“ in Analysen für den kommerziellen Marketplace
description: Nutzen Sie eine Zusammenfassung der Marketplace-Webanalysen im Partner Center, mit denen Sie die Kundeninteraktion in Microsoft AppSource- und Azure Marketplace messen können.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 09/27/2021
ms.openlocfilehash: 30b26ea098c66bc807250c0100db93d7564222b6
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069195"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Dashboard „Marketplace-Erkenntnisse“ in Analysen für den kommerziellen Marketplace

Dieser Artikel bietet Informationen zum Dashboard „Marketplace-Erkenntnisse“ im Partner Center. In diesem Dashboard wird eine Zusammenfassung der Webanalysen im kommerziellen Marketplace angezeigt, mit deren Hilfe Herausgeber die Kundeninteraktion mit ihren jeweiligen Produktdetailseiten messen können, die in den Onlineshops des kommerziellen Marketplace aufgelistet sind: Microsoft AppSource und Azure Marketplace.

Ausführliche Definitionen der Analyseterminologie finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen und Terminologie](./analytics-faq.yml).

## <a name="marketplace-insights-dashboard"></a>Dashboard „Marketplace-Erkenntnisse“

Das [Dashboard „Marketplace Insights“](https://go.microsoft.com/fwlink/?linkid=2165936) bietet eine Übersicht über die Unternehmensleistung der Angebote von Azure Marketplace und AppSource. Dieses Dashboard bietet eine umfassende Übersicht über Folgendes:

- Seitenbesuchstrend
- Handlungsaufforderungstrend
- Seitenbesuche und Handlungsaufforderungen nach Angeboten, Empfehlungsdomänen und Kampagnen-IDs
- Marketplace-Erkenntnisse nach Geografie
- Marketplace-Erkenntnisse – Tabelle „Details“

Das Marketplace Insights-Dashboard bietet Clickstreamdaten, die nicht mit Leads korreliert werden dürfen, die auf dem Leadzielendpunkt generiert werden.

> [!NOTE]
> Die maximale Latenz zwischen dem Zeitpunkt, zu dem sich Benutzer Angebote im Azure Marketplace oder in AppSource ansehen, und der Berichterstattung im Partner Center beträgt 48 Stunden.

## <a name="access-the-marketplace-insights-dashboard"></a>Zugriff auf das Dashboard „Marketplace-Erkenntnisse“

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie auf der Homepage die Kachel **Insights** aus.

    [ ![Abbildung der Kachel „Insights“ auf der Homepage von Partner Center.](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. Wählen Sie im linken Menü **Marketplace-Erkenntnisse** aus.

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.
1. Wählen Sie im linken Navigationsbereich **Gewerblicher Marktplatz** > **Analysieren** > **Marketplace-Erkenntnisse**.

---

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Elemente des Dashboards „Marketplace-Erkenntnisse“

Das Dashboard „Marketplace-Erkenntnisse“ zeigt Webtelemetriedetails für Azure Marketplace und AppSource auf zwei separaten Registerkarten an. In den folgenden Abschnitten wird beschrieben, wie das Dashboard „Marketplace-Erkenntnisse“ verwendet und wie die Daten gelesen werden.

### <a name="month-range"></a>Monatsbereich

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

Oben rechts auf jeder Seite finden Sie eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Marketplace-Erkenntnisse** an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Berechnungszeitraum) beträgt sechs Monate.

[ ![Veranschaulicht die Monatsfilter im Dashboard „Marketplace-Erkenntnisse“.](./media/insights-dashboard/marketplace-insights-filters.png) ](./media/insights-dashboard/marketplace-insights-filters.png#lightbox)

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

Oben rechts auf jeder Seite finden Sie eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Marketplace-Erkenntnisse** an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Berechnungszeitraum) beträgt sechs Monate.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Veranschaulicht die Monatsfilter im Dashboard „Marketplace-Erkenntnisse“.":::

---

> [!NOTE]
> Alle Metriken in den Visualisierungswidgets und Exportberichten berücksichtigen den vom Benutzer ausgewählten Berechnungszeitraum.

### <a name="page-visits-trends"></a>Seitenbesuchstrends

Im Diagramm **Besucher** von „Marketplace-Erkenntnisse“ wird die Anzahl der _Seitenbesuche_ und _Unterschiedliche Besucher_ für den ausgewählten Berechnungszeitraum angezeigt.

**Seitenbesuche**: Dieser Wert stellt die Anzahl unterschiedlicher Benutzersitzungen auf der Angebotsauflistungsseite (Produktdetailseite) für einen ausgewählten Berechnungszeitraum dar. Die roten und grünen Prozentangaben stellen das prozentuale Wachstum der Seitenbesuche dar. Im Trenddiagramm wird die monatliche Anzahl von Seitenbesuchen dargestellt.

**Unterschiedliche Besucher**: Diese Zahl stellt die Anzahl unterschiedlicher Besucher während des ausgewählten Berechnungszeitraums für die Angebote in Azure Marketplace und AppSource dar. Ein Besucher, der mindestens eine Produktdetailseite besucht hat, wird als ein individueller Benutzer gezählt.

[![Veranschaulicht das Diagramm „Besucher“ im Dashboard „Marketplace-Erkenntnisse“.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Handlungsaufforderungstrend

Dieser Wert steht für die Anzahl von Klicks auf die Schaltfläche **Handlungsaufforderung**, die auf der Angebotsauflistungsseite (Produktdetailseite) ausgeführt wurden. _Handlungsaufforderungen_ werden gezählt, wenn Benutzer die Schaltfläche **Jetzt abrufen**, **Kostenlose Testversion**, **Kontakt mit mir aufnehmen** oder **Testversion** auswählen. *Die erteilte Zustimmung* gibt die Gesamtzahl der Klicks, mit denen Kunden Microsoft oder dem Partner ihre Zustimmung geben, und entspricht der Anzahl der Kunden, die für Ihre Angebote erworben wurden. Die folgenden zwei Beispiele zeigen, wo die *Erteilte Zustimmung*-Klicks angezeigt werden:

:::image type="content" source="./media/insights-dashboard/consent-screen.png" alt-text="Veranschaulicht einen Ort, an dem eine Zustimmungsschaltfläche ausgewählt ist.":::

Das folgende Diagramm zeigt die Metrik *Handlungsaufforderungen* im Vergleich zu *Erteilte Zustimmung*:

:::image type="content" source="./media/insights-dashboard/consent-given-graph.png" alt-text="Zeigt ein Beispieldiagramm von Handlungsaufforderungen im Vergleich zu „Erteilte Zustimmung“.":::

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Seitenbesuche und Handlungsaufforderungen nach Angeboten, Empfehlungsdomänen und Kampagnen-IDs

#### <a name="offers"></a>Angebote

Wählen Sie ein bestimmtes Angebot aus, um den monatlichen Trend von Seitenbesuchen, Handlungsaufforderungen und „Erteilte Zustimmung“-Klicks im Diagramm anzuzeigen.

:::image type="content" source="./media/insights-dashboard/offers-funnel-graph.png" alt-text="Zeigt ein Beispieldiagramm eines Verkaufstrichters mit Angeboten.":::

#### <a name="referral-domains"></a>Empfehlungsdomänen

Wenn Sie eine bestimmte Empfehlungsdomäne auswählen, wird der monatliche Trend von Seitenbesuchen, Handlungsaufforderungen und Zustimmungsklicks im Diagramm auf der rechten Seite angezeigt. Zusätzlich gibt es eine Spalte für die Plattform – Website und Client, die nur für AppSource-Angebote angezeigt wird. In der Verkaufstrichter-Ansicht werden die Konvertierungsraten unter Seitenaufrufen, Handlungsaufforderungen und „Erteilte Zustimmung“-Klicks im Diagramm dargestellt.

:::image type="content" source="./media/insights-dashboard/referral-domains-funnel-graph.png" alt-text="Zeigt ein Beispiel des Verkaufstrichter-Diagramms der Empfehlungsdomänen.":::

#### <a name="campaign-ids"></a>Kampagnen-IDs

Wenn Sie eine bestimmte Kampagnen-ID auswählen, sollten Sie den Erfolg der Kampagne nachvollziehen können. Für jede Kampagne sollte der monatliche Trend von Seitenbesuchen, Handlungsaufforderungen und „Erteilte Zustimmung“-Klicks im Diagramm angezeigt werden.

:::image type="content" source="./media/insights-dashboard/campaign-id-funnel-graph.png" alt-text="Veranschaulicht das Kampagnendiagramm im Dashboard „Marketplace-Erkenntnisse“.":::

### <a name="marketplace-insights-by-geography"></a>Marketplace-Erkenntnisse nach Geografie

Für den ausgewählten Berechnungszeitraum zeigt das Wärmebild die Anzahl von Seitenbesuchen, unterschiedlichen Besuchern und Handlungsaufforderungen (Calls To Action, CTA) an. Die Helligkeit der Farbe auf der Karte entspricht einer niedrigen (hell) bis hohen (dunkel) Anzahl unterschiedlicher Besucher. Wählen Sie einen Datensatz in der Tabelle aus, um ein Land/eine Region zu vergrößern.

[ ![Veranschaulicht das Diagramm der geografischen Verteilung auf dem Marketplace Insights-Dashboard.](./media/insights-dashboard/geographical-spread.png) ](./media/insights-dashboard/geographical-spread.png#lightbox)

Beachten Sie Folgendes:

- Sie können die Karte verschieben, um den genauen Standort anzuzeigen.
- Sie können an einen bestimmten Ort zoomen.
- Das Wärmebild verfügt über ein zusätzliches Raster, um Details zur Anzahl der Kunden oder Aufträge und zu den Stunden für die normalisierte Nutzung an einem bestimmten Ort anzuzeigen.
- Sie können ein Land oder eine Region im Raster suchen und auswählen, um an den Ort in der Karte zu zoomen. Um wieder zur ursprünglichen Ansicht zurückzukehren, wählen Sie in der Karte die Schaltfläche **Startseite** aus.

### <a name="marketplace-insights-details-table"></a>Marketplace-Erkenntnisse – Tabelle „Details“

Diese Tabelle enthält eine Listenansicht der Seitenbesuche und Handlungsaufforderungen der Seiten Ihrer ausgewählten Angebote nach Datum sortiert.

- Die Daten können in eine TSV- oder CSV-Datei extrahiert werden, wenn die Anzahl der Einträge unter 1.000 liegt.
- Liegt die Anzahl der Einträge über 1.000, werden die exportierten Daten für die nächsten 30 Tage asynchron auf einer Downloadseite abgelegt.
- Filtern Sie Daten nach Angebotsnamen und Kampagnennamen, um die für Sie interessanten Daten anzuzeigen.

> [!TIP]
> Sie können das Downloadsymbol in der oberen rechten Ecke eines Widgets verwenden, um die Daten herunterzuladen. Sie können Feedback zu jedem der Widgets geben, indem Sie auf das Symbol „Daumen nach oben“ oder „Daumen nach unten“ klicken.

| Spaltenname auf<br>Benutzeroberfläche | Attributname | Definition | Spaltenname in Berichten zum<br>programmgesteuerten Zugriff |
| ------------ | ------------- | ------------- | ------------- |
| Date | Date of Visit | Das Datum des Seitenbesuchs und/oder der Generierung eines CTA-Klickereignisses auf der Angebotsseite im Azure Marketplace und/oder in AppSource | Date |
| Angebotsname | Angebotsname | Der Name des Angebots im kommerziellen Marketplace. | OfferName |
| Referral Domain | Referral Domain | Hier wird der Name der Empfehlungsdomäne angegeben, aus der der Seitenbesuch erfolgt ist. Wenn keine Empfehlungsdomänen für den Seitenbesuch erfasst wurden, lautet der entsprechende Eintrag „Referral domain not present“ (Empfehlungsdomäne nicht vorhanden). |  ReferralDomain |
| Name des Lands | Name des Lands | Der Name des Landes, in dem der Seitenbesuch erfolgt ist | CountryName |
| Page Visits | Page Visits | Die Anzahl der dem Angebotsnamen für ein bestimmtes Datum zugeordneten Seitenbesuche | PageVisits |
| Jetzt abrufen | Jetzt abrufen | Die Anzahl der Klicks auf die Handlungsaufforderung „Jetzt herunterladen“ auf der Angebotsseite für ein bestimmtes Datum | GetItNow |
| Kontaktformular | Kontaktformular | Die Anzahl der Klicks auf die Handlungsaufforderung „Kontakt mit mir aufnehmen“ auf der Angebotsseite für ein bestimmtes Datum | ContactMe |
| Testversion | Testversion | Die Anzahl der Klicks auf die Handlungsaufforderung „Testlaufwerk“ auf der Angebotsseite für ein bestimmtes Datum | TestDrive |
| Kostenlose Testversion | Kostenlose Testversion | Die Anzahl der Klicks auf die Handlungsaufforderung „Kostenlose Testversion“ auf der Angebotsseite für ein bestimmtes Datum | FreeTrial |
| Kampagne | Name der Kampagne | Das Verständnis von Webtelemetrie (Seitenbesuch und Klicks auf Handlungsaufforderungen) anhand des Kampagnennamens. | Kampagne |
| Zustimmung erteilt | Zustimmung erteilt | Gesamtanzahl der Klicks, mit denen Kunden Microsoft oder dem Partner ihre Zustimmung geben | consentGivenCount |
| Plattform | Plattform | Gibt die Website oder den Client (In-Product-Store) als Quelle von Seitenaufruf, Handlungsaufforderung oder Zustimmungsklicks an | Plattformen |
| – | Website | Der Name der Storefront, von der aus der Seitenbesuch oder Klick auf die Handlungsaufforderung erfolgte. Mögliche Werte sind:<br><ul><li>AZUREMARKETPLACE<li>APPSOURCE</ul> | Website |
|

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die im kommerziellen Marketplace verfügbaren Analyseberichte finden Sie unter [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](analytics.md).
- Informationen zu Ihren Aufträgen in einem grafischen und herunterladbaren Format finden Sie unter [Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace](orders-dashboard.md).
- Informationen zu Metriken zur Nutzung und zur getakteten Abrechnung für VM-Angebote finden Sie unter [Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace](usage-dashboard.md).
- Ausführliche Informationen zu Ihren Kunden, einschließlich Wachstumstrends, finden Sie unter [Dashboard „Kunde“ in Analysen für den kommerziellen Marketplace](customer-dashboard.md).
- Eine Liste Ihrer Downloadanforderungen der letzten 30 Tagen finden Sie unter [Dashboard „Downloads“ in Analysen für den kommerziellen Marketplace](downloads-dashboard.md).
- Eine konsolidierte Ansicht des Kundenfeedbacks für Angebote im Azure Marketplace und in AppSource finden Sie unter [Analyse-Dashboard „Bewertungen und Rezensionen“ in Partner Center](ratings-reviews.md).
- Häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace und ein umfassendes Wörterbuch mit Datenbegriffen finden Sie unter [Analysen für den kommerziellen Marketplace: Häufig gestellte Fragen](analytics-faq.yml).
