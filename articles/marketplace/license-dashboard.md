---
title: Lizenzdashboard in Analysen für den kommerziellen Microsoft-Marketplace im Partner Center – Azure Marketplace
description: Erfahren Sie, wie Sie auf Informationen zu Ihren Lizenzen zugreifen, indem Sie das Lizenzdashboard in Analysen im kommerziellen Marketplace verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 06/10/2021
ms.openlocfilehash: 6998005024bdbf61fb941ae3ff778be55dc5bb26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346159"
---
# <a name="license-dashboard-in-commercial-marketplace-analytics"></a>Lizenzdashboard für Analysen auf dem kommerziellen Marketplace

Dieser Artikel bietet Informationen zum Lizenzdashboard im kommerziellen Marketplace-Programm im Partner Center. Im Lizenzdashboard werden die folgenden Informationen angezeigt:

- Anzahl der Kunden, die Lizenzen gekauft haben
- Gesamtanzahl erworbener Lizenzen
- Gesamtanzahl bereitgestellter Lizenzen
- Anzahl der vom Kunden gekauften und bereitgestellten Lizenzen
- Verteilung von Lizenzen über Länder und Regionen hinweg

## <a name="check-license-usage"></a>Überprüfen der Lizenznutzung

Gehen Sie wie folgt vor, um die Lizenznutzung von ISV-Apps im Partner Center zu überprüfen:
1. Melden Sie sich bei [Partner Center](https://go.microsoft.com/fwlink/?linkid=2165507) an.
1. Klicken Sie im linken Navigationsmenü auf **Kommerzieller Marketplace** > **Analysieren** > **Lizenz**.

:::image type="content" source="./media/license-dashboard/license-dashboard.png" alt-text="Screenshot des Lizenzdashboards im Partner Center.":::

## <a name="elements-of-the-license-dashboard"></a>Elemente des Lizenzdashboards

In den folgenden Abschnitten wird beschrieben, wie das Lizenzdashboard verwendet wird und wie die Daten gelesen werden.

## <a name="month-range"></a>Monatsbereich

Oben rechts auf der Seite finden Sie eine Monatsbereichsauswahl. Passen Sie die Ausgabe der Widgets auf der Seite an, indem Sie einen Monatsbereich basierend auf den letzten 6 oder 12 Monaten oder einen benutzerdefinierten Monatsbereich mit einer maximalen Dauer von 12 Monaten auswählen. Der standardmäßige Monatsbereich (Berechnungszeitraum) beträgt sechs Monate.

:::image type="content" source="./media/license-dashboard/month-range.png" alt-text="Screenshot der Monatsbereichsauswahl im Lizenzdashboard im Partner Center.":::

## <a name="customers-widget"></a>Widget „Kunden“

Das _Widget „Kunden“_ zeigt die aktuelle Anzahl von Kunden an. Das Trenddiagramm zeigt die Anzahl der Kunden im Monatsverlauf an.

:::image type="content" source="./media/license-dashboard/customers-widget.png" alt-text="Screenshot des Widgets „Kunden“ im Lizenzdashboard im Partner Center.":::

## <a name="license-widget"></a>Widget „Lizenz“

Das _Widget „Lizenz“_ zeigt die aktuelle Anzahl bereitgestellter und zugewiesener Lizenzen an. Das Trenddiagramm zeigt die Anzahl bereitgestellter und zugewiesener Lizenzen im Monatsverlauf an. Sie können die Daten der letzten 6 Monate oder 12 Monate anzeigen. Sie können auch einen benutzerdefinierten Datumsbereich auswählen, indem Sie oben rechts auf der Seite den Link **Benutzerdefiniert** auswählen.

:::image type="content" source="./media/license-dashboard/license-widget.png" alt-text="Screenshot des Widgets „Lizenz“ im Lizenzdashboard im Partner Center.":::

## <a name="analysis-widget"></a>Widget „Analyse“

Das _Widget „Analyse“_ zeigt die Anzahl und den Prozentsatz der bereitgestellten und zugewiesenen Lizenzen pro Angebot und Plan an. Das Trenddiagramm zeigt die Anzahl bereitgestellter und zugewiesener Lizenzen im Monatsverlauf an. Mit diesem Widget können Sie die Daten nach Kunden oder Produkten filtern.

:::image type="content" source="./media/license-dashboard/analysis-widget.png" alt-text="Screenshot des Widgets „Analyse“ im Lizenzdashboard im Partner Center.":::

## <a name="license-distribution-widget"></a>Widget „Lizenzverteilung“

Das _Widget „Lizenzverteilung“_ zeigt die Verteilung von Lizenzen in verschiedenen Ländern und Regionen an. Die farbigen Bereiche zeigen an, wo die Benutzerlizenzen verteilt sind. Um zur Standardansicht zurückzukehren, wählen Sie im Widget die Schaltfläche **Zoom zurücksetzen** (Startsymbol) aus.

:::image type="content" source="./media/license-dashboard/license-distribution.png" alt-text="Screenshot des Widgets „Lizenzverteilung“ im Lizenzdashboard im Partner Center.":::

## <a name="data-terms-in-license-report-downloads"></a>Datenbegriffe in Downloads von Lizenzberichten

Sie können das Downloadsymbol in der oberen rechten Ecke eines Widgets verwenden, um die Daten herunterzuladen.

| Attributname | Definition |
| ------------ | ------------- |
| Land des Kunden | Abrechnungsland des Kunden |
| Ländercode des Kunden | Code des Abrechnungslands des Kunden |
| Customer Name | Kundenname |
| Aktivierungsdatum | Datum, an dem Lizenzen aktiviert wurden |
| Produktanzeigename | Angebotstitel wie in AppSource gezeigt |
| Product ID | Product ID |
| Bereitgestellte Lizenzen | Anzahl der im Konto des Kunden aktivierten Lizenzen |
| Zugewiesene Lizenzen | Anzahl der Lizenzen, die der Kunde seinen Benutzern zugewiesen hat |
| SKU-Name | Name des Plans im Angebot |
| Mandanten-ID | Eindeutige ID des Mandanten |
| Lizenzzustand | Lizenzzustand |
| Service-ID | Eindeutiger Bezeichner, der im Paket verwendet wird, um den Plan den Lizenzprüfungen zuzuordnen |
|||

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die im kommerziellen Marketplace verfügbaren Analyseberichte finden Sie unter [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](analytics.md).
