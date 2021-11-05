---
title: Quality of Service-Dashboard in Partner Center
description: Zeigt verschiedene Berichte an, die für die Servicequalität (Quality of Service) in Partner Center (Azure Marketplace) verfügbar sind.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: emuench
ms.author: smannepalle
ms.date: 09/27/2021
ms.openlocfilehash: c7df2a30d3de65966d289c0caad7bc6afaca6afd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088003"
---
# <a name="quality-of-service-qos-dashboard"></a>Dashboard „Quality of Service (QoS)“

Dieses Dashboard zeigt die Qualität der Bereitstellungen für alle Ihre Angebote an. Ein größerer Erfolg bei der Angebotsbereitstellung bedeutet eine höhere Servicequalität, die Sie Ihren Kunden bieten.

Zeigen Sie grafische Darstellungen folgender Elemente an:

- [Qualität nach Angeboten](#quality-by-offers)
- [Qualität nach Bereitstellungsdauer](#quality-by-deployment-duration)
- [Bereitstellungsanzahl](#deployment-count)
- [Bereitstellungsanzahl nach Status](#deployment-count-by-status)
- [Codes und Ressourcen für Bereitstellungsfehler](#deployment-errors-codes-and-resources)
- [Bereitstellungsfehler nach Angebotsplan](#deployment-errors-by-offer-plan)
- [Zuverlässigkeit der Bereitstellung nach Standort](#deployment-reliability-by-location)

Zeigen Sie außerdem [Details zur Angebotsbereitstellung](#offer-deployment-details) in Tabellenform an.

> [!IMPORTANT]
> Dieses Dashboard ist derzeit nur für **Azure-Anwendungsangebote** verfügbar, die für alle verfügbar sind (keine privaten Angebote).

Dieses Feature gilt derzeit für alle Partner, die Azure-Anwendungsangebote mithilfe von ARM-Vorlagen (Azure Resource Manager) bereitstellen (jedoch nicht für private Angebote). In diesem Bericht werden keine Daten für andere Marketplace-Angebote angezeigt.

## <a name="access-the-quality-of-service-dashboard"></a>Zugreifen auf das Dashboard „Quality of Service“

[!INCLUDE [preview interface note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Arbeitsbereichsansicht](#tab/workspaces-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

1. Wählen Sie auf der Homepage die Kachel **Insights** aus.

    [ ![Veranschaulicht die Insights-Kachel in Partner Center](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. Wählen Sie im linken Menü **Quality of Service** aus.

    [ ![Veranschaulicht den linken Navigationsbereich auf dem Insights-Dashboard](./media/quality-of-service/insights-overview-workspaces.png) ](./media/quality-of-service/insights-overview-workspaces.png#lightbox)

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

1. Wählen Sie im linken Navigationsbereich **Kommerzieller Marketplace** > [**Analysieren**](https://partner.microsoft.com/dashboard/commercial-marketplace/quality-of-service/summary) > **Quality of Service** aus.

---

## <a name="elements-of-the-quality-of-service-dashboard"></a>Elemente des Dashboards „Quality of Service“

In den folgenden Abschnitten wird beschrieben, wie das Dashboard „Quality of Service (QoS)“ verwendet und wie die Daten gelesen werden.

### <a name="month-range"></a>Monatsbereich

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

Oben rechts auf jeder Seite befindet sich eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Quality of Service** an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Auswahlzeitraum) beträgt sechs Monate.

[ ![Veranschaulicht die Filter auf dem Dashboard „Quality of Service“](./media/quality-of-service/qos-filters-workspaces.png) ](./media/quality-of-service/qos-filters-workspaces.png#lightbox)

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

Oben rechts auf jeder Seite befindet sich eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Diagramme auf der Seite **Quality of Service** an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Auswahlzeitraum) beträgt sechs Monate.

---

### <a name="quality-by-offers"></a>Qualität nach Angeboten

Dieses Diagramm zeigt die Servicequalität nach Angeboten und deren entsprechenden SKUs. Es stellt monatlich Metriken und Trends für Bereitstellungen von Angeboten vom Typ **Gesamt**, **Erfolgreich** und **Fehlerhaft** bereit. Das Balkendiagramm stellt die Anzahl der Bereitstellungen dar.

Das Liniendiagramm stellt die prozentuale Änderung für Folgendes dar:

- Gesamte Bereitstellungen (Registerkarte **Alle**)
- Erfolgreiche Bereitstellungen (Registerkarte **Erfolgreich**)
- Fehlerhafte Bereitstellungen (Registerkarte **Fehlerhaft**)

Das Diagramm zeigt die Metriken und Trends aller Angebote. Die Topangebote werden im Diagramm angezeigt, während die übrigen unter der Kategorie für sonstige Angebote gruppiert sind.

:::image type="content" source="media/quality-of-service/quality-by-offers-1.png" alt-text="Zeigt einen Graphen zur Qualität nach Angebot, Version 1.":::

Informationen zu diesem Graphen:

- Wählen Sie bestimmte Angebote in der Legende aus, um nur diese Angebote und die zugeordneten SKUs im Graphen anzuzeigen.
- Bewegen Sie den Cursor über ein Segment im Graphen, um die Anzahl der Bereitstellungen und den Prozentsatz dieses Angebots im Vergleich zur Gesamtzahl der Bereitstellungen für alle Ihre Angebote anzuzeigen.
- Der Trend „Qualität nach Angeboten“ zeigt den monatlichen Auf- oder Abwärtstrend an.

:::image type="content" source="media/quality-of-service/quality-by-offers-2.png" alt-text="Zeigt einen Graphen zur Qualität nach Angebot, Version 2.":::

Informationen zu diesem Graphen:

- Wählen Sie bestimmte Angebote und zugeordnete SKUs in der Legende aus, die angezeigt werden sollen.
- Der Trend „Qualität nach Angeboten“ zeigt monatliche Metriken an.
- Wählen Sie beim Anzeigen eines Monatstrend für ein Angebot maximal drei SKUs dieses Angebots aus.
- Das Liniendiagramm stellt die gleichen prozentualen Änderungen wie für den vorherigen Graphen dar.

### <a name="quality-by-deployment-duration"></a>Qualität nach Bereitstellungsdauer

Dieses Diagramm zeigt die Metrik und den Trend für die durchschnittliche Dauer einer erfolgreichen und fehlgeschlagenen Bereitstellung. Zeigen Sie die Metriken an, indem Sie im Dropdownmenü ein Angebot auswählen. Wählen Sie eine SKU in der Tabellenansicht aus, oder geben Sie sie in der Suchleiste ein. Nachfolgend sind verschiedene mittlere Bereitstellungsdauern (in Minuten) aufgeführt:

- **Dauer für Erfolg** – Mittlere Dauer der Bereitstellung mit dem Status „Erfolg“ für die Angebotsbereitstellung. Diese aggregierte Metrik wird anhand der Zeitdauer zwischen Start- und Endzeitstempeln von Bereitstellungen berechnet, die mit dem Status „Erfolgreich“ gekennzeichnet sind.
- **Dauer für Fehler** – Mittlere Dauer der Bereitstellung mit dem Status „Fehler“ für die Angebotsbereitstellung. Diese aggregierte Metrik wird anhand der Zeitdauer zwischen Start- und Endzeitstempeln von Bereitstellungen berechnet, die mit dem Status „Fehler“ gekennzeichnet sind.
- **Dauer für erste erfolgreiche Bereitstellung** – Mittlere Dauer der Bereitstellung mit dem Status „Erfolg“ für die Angebotsbereitstellung. Diese aggregierte Metrik wird anhand der Zeitdauer zwischen dem Startzeitstempel der ersten Bereitstellung und dem Endzeitstempel der endgültigen Bereitstellung mit dem Status „Erfolgreich“ berechnet. Sie wird für jede Bereitstellung berechnet, die für eine bestimmte Angebots-SKU und einen bestimmten Kunden gekennzeichnet ist.

:::image type="content" source="media/quality-of-service/deployment-duration-quality.png" alt-text="Zeigt einen Qualitätsgraphen für die Bereitstellungsdauer an.":::

Informationen zu diesem Graphen:

- Wählen Sie in der anzuzeigenden Legende „Fehler“, „Erfolg“ oder „Erste erfolgreiche Bereitstellung“ aus.
- Das Liniendiagramm zeigt die mittlere Dauer von Bereitstellungen an, die als erfolgreiche, fehlerhafte und erfolgreiche Bereitstellungen mit fehlerhaften vorherigen Versuchen gekennzeichnet sind.
- Die mittlere Zeit für die erste Bereitstellung berücksichtigt die Zeit, die für Fehlversuche aufgewendet wird, bevor die Bereitstellung als erfolgreich markiert wird.

### <a name="deployment-count"></a>Bereitstellungsanzahl

Dieser Graph zeigt die Gesamtbereitstellung von Angeboten. Metriken und Wachstumstrends werden durch ein Liniendiagramm dargestellt. Zeigen Sie den Wert für jeden Monat an, indem Sie den Mauszeiger über das Liniendiagramm bewegen.

Der Prozentwert unterhalb der Metriken für die Bereitstellungen stellt das Wachstum oder den Rückgang während des ausgewählten Monatsbereichs dar.

:::image type="content" source="media/quality-of-service/deployment-count.png" alt-text="Zeigt einen Graphen zur Anzahl der Bereitstellungen an.":::

Informationen zu diesem Graphen:

- Gesamtzahl der Angebotsbereitstellungen für den ausgewählten Datumsbereich.
- Änderung des Prozentsatzes der Angebotsbereitstellungen während des ausgewählten Datumsbereichs.
- Monatlicher Trend der Gesamtzahl für Angebotsbereitstellungen.

### <a name="deployment-count-by-status"></a>Bereitstellungsanzahl nach Status

Dieses Diagramm zeigt die Metrik und den Trend erfolgreicher und fehlerhafter Angebotsbereitstellungen durch Kunden für den ausgewählten Monatsbereich. Angebotsbereitstellungen können zwei Status aufweisen: **Erfolgreich** oder **Fehlerhaft**.

:::image type="content" source="media/quality-of-service/deployment-count-by-status.png" alt-text="Zeigt den Graphen zur Bereitstellungsanzahl nach Status an.":::

Informationen zu diesem Graphen:

- Gesamtzahl der erfolgreichen und fehlerhaften Bereitstellungen von Angeboten für den ausgewählten Datumsbereich.
- Änderung des Prozentsatzes erfolgreicher und fehlerhafter Angebotsbereitstellungen für den ausgewählten Datumsbereich.
- Monatlicher Trend zur Anzahl erfolgreicher und fehlerhafter Angebotsbereitstellungen

### <a name="deployment-errors-codes-and-resources"></a>Codes und Ressourcen für Bereitstellungsfehler

Dieser Graph zeigt Metriken und Trends der Basisfehlercodes und Ressourcen der Angebotsbereitstellungen. Der Tabellenabschnitt kann hinsichtlich Fehlercodes und Ressourcen pivotiert werden. Die erste Unterregisterkarte enthält eine Analyse der Basisfehlercodes, eine Beschreibung und die Fehleranzahl. Die zweite bietet eine Analysebasis für die Bereitstellungsressourcen. Das Liniendiagramm enthält die Gesamtfehlerzahl auf der Basis von Fehlercodes und Ressourcen.

Weitere Informationen zu Fehlercodes finden Sie unter [Beheben allgemeiner Azure-Bereitstellungsfehler mit Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md) und [Ressourcenanbieter für Azure-Dienste](../azure-resource-manager/management/azure-services-resource-providers.md).

:::image type="content" source="media/quality-of-service/deployment-error-codes-1.png" alt-text="Zeigt einen Graphen mit Bereitstellungsfehlercodes, Beispiel 1.":::
:::image type="content" source="media/quality-of-service/deployment-error-codes-2.png" alt-text="Zeigt einen Graphen mit Bereitstellungsfehlercodes, Beispiel 2.":::

Informationen zu diesen Graphen:

- Wählen Sie bestimmte Fehler oder Ressourcen in der Legende aus, die angezeigt werden sollen.
- Das Trend-Widget zeigt die monatliche Fehleranzahl an.
- Wenn Sie einen monatlichen Trend nach Fehlercodes oder Ressourcen anzeigen, wählen Sie maximal drei Elemente in der Tabelle aus.
- Sortieren Sie Fehlercodes und Ressourcen für Bereitstellungsfehler nach Basisfehleranzahl in der Tabelle.

### <a name="deployment-errors-by-offer-plan"></a>Bereitstellungsfehler nach Angebotsplan

In diesem Graphen stellt die Y-Achse die Anzahl von Bereitstellungsfehlern und die X-Achse das Perzentil der obersten Angebotspläne (nach Fehleranzahl) dar.

:::image type="content" source="media/quality-of-service/deployment-error-by-offer-plan.png" alt-text="Zeigt einen Graphen zu Bereitstellungsfehlern nach Angebotsplan an.":::

Informationen zu diesem Graphen:

- Die Balkendiagramme stellen die Anzahl der Bereitstellungsfehler für den ausgewählten Monatsbereich dar.
- Die Werte im Liniendiagramm stellen die kumulativen Fehlerprozentsätze nach Angebotsplan dar.

### <a name="deployment-reliability-by-location"></a>Zuverlässigkeit der Bereitstellung nach Standort

Dieser Graph zeigt das Wärmebild für die Anzahl der erfolgreichen und fehlerhaften Bereitstellungen für den ausgewählten Monatsbereich. Außerdem wird der Fehlerprozentsatz für jede Region angezeigt. Die Grün-zu-Rot-Farbskala stellt einen niedrigen bis hohen Wert von Fehlerraten dar. Wählen Sie einen Datensatz in der Tabelle aus, um eine Bereitstellungsregion zu vergrößern.

:::image type="content" source="media/quality-of-service/deployment-reliability.png" alt-text="Zeigt einen Graphen zur Zuverlässigkeit der Bereitstellung nach Standorten an.":::

Informationen zu diesem Graphen:

- Verschieben Sie die Karte, um den genauen Standort anzuzeigen.
- Zoomen Sie an einen bestimmten Ort.
- Das Wärmebild verfügt über ein ergänzendes Raster, in dem die Bereitstellungsdetails zur Anzahl von „Erfolgreich“, zur Anzahl von „Fehlerhaft“ und zum Fehlerprozentsatz an einem bestimmten Standort angezeigt werden.
- Rote Regionen weisen auf höhere und grün auf niedrigere Fehlerraten hin.
- Suchen Sie ein Land oder eine Region im Raster und wählen Sie es/sie aus, um an den Ort auf der Karte zu zoomen. Kehren Sie über das Symbol für die **Startseite** zur ursprünglichen Ansicht zurück.

## <a name="offer-deployment-details"></a>Details zur Angebotsbereitstellung

In dieser Tabelle sind alle verfügbaren Details zur Angebotsbereitstellung aufgeführt. Laden Sie den Bericht herunter, um die Rohdaten zu Angebotsbereitstellungen anzuzeigen.

:::image type="content" source="media/quality-of-service/deployment-details.png" alt-text="Zeigt eine Tabelle mit Bereitstellungsdetails an.":::

Informationen zu dieser Tabelle:

- Zeigt eine nummerierte Liste der 1.000 wichtigsten Bereitstellungen an, sortiert nach Bereitstellungsdatum.
- Jede Spalte im Raster ist sortierbar.
- Erweitern Sie das Steuerelement, und exportieren Sie die Tabelle.
- Die Detailansicht ist paginiert. Wählen Sie unten andere Seiten aus.

### <a name="dictionary-of-data-terms"></a>Wörterbuch mit Datenbegriffen

| Spaltenname | Attributname | Definition |
| --- | --- | --- |
| Angebots-ID | Angebots-ID | Der Name des bereitgestellten Angebots. |
| Sku | Sku | Der Name des bereitgestellten Angebotplans (oder der SKU). |
| Bereitstellungsstatus | Bereitstellungsstatus | Der Bereitstellungsstatus des Angebots, der entweder als **Erfolgreich** oder **Fehlerhaft** gekennzeichnet ist. |
| Abonnement-ID | Abonnement-ID | Die Abonnement-ID des Kunden. |
| Mandanten-ID des Kunden | Mandanten-ID des Kunden | Die Mandanten-ID des Kunden. |
| Customer Name | Customer Name | Der Name des Kunden. |
| Vorlagentyp | Vorlagentyp | Der Typ der bereitgestellten Azure-App. Es kann sich dabei entweder um eine verwaltete App oder um Lösungsvorlagen handeln, die nicht privat sein dürfen. |
| Startzeit der Bereitstellung | Startzeit der Bereitstellung | Die Startzeit der Bereitstellung. |
| Endzeit der Bereitstellung | Endzeit der Bereitstellung | Die Endzeit der Bereitstellung. |
| Bereitstellungsdauer: | Bereitstellungsdauer: | Die Gesamtdauer der Angebotsbereitstellung in Millisekunden. Sie wird im Graphen in Minuten angezeigt. |
| Bereitstellungsregion | Bereitstellungsregion | Der Standort der Bereitstellung der Azure-App. |
| Ressourcenanbieter | Ressourcenanbieter | Der Ressourcenanbieter für die jeweilige bereitgestellte Ressource. |
| Ressourcen-URI | Ressourcen-URI | Der URI der bereitgestellten Ressource. |
| Ressourcengruppe | Ressourcengruppe | Die Ressourcengruppe, in der die Ressource bereitgestellt wird. |
| Ressourcentyp | Ressourcentyp | Der Typ der bereitgestellten Ressource. |
| Ressourcenname | Ressourcenname | Der Name der bereitgestellten Ressource. |
| Fehlercode | Fehlercode | Der Fehlercode für den Bereitstellungsfehler. |
| Fehlerbezeichnung | Fehlerbezeichnung | Der Fehlername für den Bereitstellungsfehler. |
| Fehlermeldung | Fehlermeldung | Die Fehlermeldung für den Bereitstellungsfehler. |
| Umfassender Fehlercode | Umfassender Fehlercode | Enthält ggf. weitere Informationen zum Fehlercode. |
| Umfassender Nachrichtencode | Umfassender Nachrichtencode | Enthält ggf. weitere Informationen zur Fehlermeldung. |
| Korrelations-ID | Korrelations-ID | Der zur Unterscheidung verschiedener Bereitstellungen verwendete Bezeichner. Ein identischer Wert bedeutet, dass alle Ressourcen, die bereitgestellt werden, für eine Bereitstellung bestimmt sind. |
|

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Bereitstellungsfehlern finden Sie unter [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).
- Informationen zu Ressourcenanbietern finden Sie unter [Ressourcenanbieter für Azure-Dienste](../azure-resource-manager/management/azure-services-resource-providers.md).
- Informationen zu Diagrammen, Trends und Werten von aggregierten Daten, mit deren Hilfe Marketplace-Aktivitäten für Ihr Angebot zusammengefasst werden, finden Sie unter [Dashboard „Zusammenfassung“ in Analysen für den kommerziellen Marketplace](./summary-dashboard.md).
- Informationen zu Ihren Aufträgen in einem grafischen und herunterladbaren Format finden Sie unter [Dashboard „Aufträge“ in Analysen für den kommerziellen Marketplace](./orders-dashboard.md).
- Informationen zu Metriken zur Nutzung und zur getakteten Abrechnung für VM-Angebote finden Sie unter [Dashboard „Nutzung“ in Analysen für den kommerziellen Marketplace](./usage-dashboard.md).
- Ausführliche Informationen zu Ihren Kunden, einschließlich Wachstumstrends, finden Sie unter [Dashboard „Kunde“ in Analysen für den kommerziellen Marketplace](./customer-dashboard.md).
- Informationen zu Ihren Lizenzen finden Sie unter [Dashboard „Lizenz“ in Analysen für den kommerziellen Marketplace](./license-dashboard.md).
- Eine Liste Ihrer Downloadanforderungen der letzten 30 Tagen finden Sie unter [Dashboard „Downloads“ in Analysen für den kommerziellen Marketplace](./downloads-dashboard.md).
- Eine konsolidierte Ansicht des Kundenfeedbacks für Angebote in Microsoft AppSource und Azure Marketplace finden Sie unter [Dashboard „Bewertungen und Prüfungen“ in Analysen für den kommerziellen Marketplace](./ratings-reviews.md).
- Häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace und ein umfassendes Wörterbuch mit Datenbegriffen finden Sie unter [Häufig gestellte Fragen zu Analysen für den kommerziellen Marketplace](./analytics-faq.yml).