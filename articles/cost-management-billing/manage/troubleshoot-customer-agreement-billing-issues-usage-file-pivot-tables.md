---
title: Problembehandlung von Azure MCA-Abrechnungsproblemen mit Nutzungsdatei-PivotTables
description: Dieser Artikel unterstützt Sie bei der Problembehandlung von Abrechnungsproblemen der Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) mithilfe von PivotTables, die aus Ihren CSV-Nutzungsdateien erstellt wurden.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 1d4e6a407e75a70618089f20a7d19343785380ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616477"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>Problembehandlung von MCA-Abrechnungsproblemen mit Nutzungsdatei-PivotTables

Dieser Artikel unterstützt Sie bei der Problembehandlung von Abrechnungsproblemen der Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) mithilfe von PivotTables in Ihren Nutzungsdateien. Azure-Nutzungsdateien enthalten sämtliche Nutzungs- und Verbrauchsinformationen zu Azure. Die Informationen in der Datei helfen Ihnen bei Folgendem:

- Verstehen, wie Azure-Reservierungen genutzt und angewendet werden
- Abstimmen von Informationen in Azure Cost Management mit Ihrer Rechnung
- Problembehandlung bei Kostenanstiegen
- Berechnen einer Erstattungssumme für eine Vereinbarung zum Servicelevel

Mithilfe der Informationen aus Ihren Nutzungsdateien können Sie Nutzungsprobleme besser verstehen und diagnostizieren. Nutzungsdateien werden im durch Trennzeichen getrennten Format (CSV) generiert. Da es sich bei den Nutzungsdateien unter Umständen um große CSV-Dateien handelt, ist es einfacher, sie als PivotTables zu bearbeiten und in einer Tabellenkalkulationsanwendung wie Excel anzuzeigen. In den Beispielen in diesem Artikel wird Excel verwendet, Sie können jedoch jede beliebige Tabellenkalkulationsanwendung verwenden.

Nur Besitzer von Abrechnungsprofilen, Mitwirkende, Leser oder Rechnungs-Manager haben Zugriff zum Herunterladen von Nutzungsdateien. Weitere Informationen finden Sie unter [Herunterladen der Nutzungsdaten für Ihre Microsoft-Kundenvereinbarung](../understand/download-azure-daily-usage.md). 

## <a name="get-the-data-and-format-it"></a>Abrufen und Formatieren der Daten

Da Azure-Nutzungsdateien im CSV-Format vorliegen, müssen Sie die Daten für die Verwendung in Excel vorbereiten. Führen Sie die folgenden Schritte aus, um die Daten als Tabelle zu formatieren.

1. Laden Sie die Nutzungsdatei herunter, indem Sie die Anweisungen unter [Herunterladen von Nutzungsdaten im Azure-Portal](../understand/download-azure-daily-usage.md) ausführen.
1. Öffnen Sie die Datei in Excel.
1. Die unformatierten Daten ähneln dem folgenden Beispiel.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="Beispiel für unformatierte Daten" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. Wählen Sie das erste Feld in der Tabelle, **invoiceID**, aus.
1. Drücken Sie STRG + UMSCHALT + NACH-UNTEN-TASTE und anschließend STRG + UMSCHALT + NACH-RECHTS-TASTE, um alle Informationen in der Tabelle auszuwählen.
1. Wählen Sie in der oberen Menüleiste die Option **Einfügen** > **Tabelle** aus. Wählen Sie im Feld „Tabelle erstellen“ die Option **Meine Tabelle hat Überschriften** und anschließend **OK** aus.  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Beispiel für das Dialogfeld „Tabelle erstellen“" :::
1. Wählen Sie in der oberen Menüleiste **Einfügen** > **PivotTable** und anschließend **OK** aus. Mit der Aktion wird ein neues Blatt in der Datei erstellt, und Sie gelangen zum PivotTable-Bereich auf der rechten Seite des Blatts.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Beispiel für den Bereich „PivotTable-Felder“" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Der Bereich „PivotTable-Felder“ ist ein Drag & Drop-Bereich. Fahren Sie mit dem nächsten Abschnitt fort, um die PivotTable zu erstellen.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Erstellen einer PivotTable zum Anzeigen der Azure-Kosten nach Ressourcen

In diesem Abschnitt erstellen Sie eine PivotTable, mit der Sie eine Problembehandlung der allgemeinen Azure-Nutzung durchführen können. Die Beispieltabelle kann Ihnen dabei helfen, zu untersuchen, welcher Dienst die meisten Ressourcen verbraucht. Oder Sie können die Ressourcen anzeigen, die die meisten Kosten verursachen, und Sie sehen, wie ein Dienst abgerechnet wird.

1. Ziehen Sie im Bereich „PivotTables-Felder“ **Kategorie für Messung** und **Produkt** in den Abschnitt **Zeilen**. Platzieren Sie **Produkt** unter **Kategorie für Messung**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Beispiel für „Kategorie für Messung“ und „Produkt“ in „Zeilen“" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Fügen Sie als Nächstes die Spalte **costInBillingCurrenty** zum Abschnitt **Werte** hinzu. Sie können auch die Spalte **Menge** verwenden, um Informationen zu Verbrauchseinheiten und Transaktionen zu erhalten. Beispiele: GB oder Stunden. Oder verwenden Sie Transaktionen anstelle von Kosten in verschiedenen Währungen wie USD, EUR und INR.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Beispiel für Felder, die zur PivotTable hinzugefügt wurden" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Nun verfügen Sie über ein Dashboard für die allgemeine Untersuchung der Nutzung. Sie können einen bestimmten Dienst mithilfe der Filteroptionen in der PivotTable filtern.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Beispiel für die PivotTable-Filteroption für die Zeilenbeschriftung" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Wenn Sie eine zweite Ebene in einer PivotTable filtern möchten, z. B. eine Ressource, wählen Sie ein Element der zweiten Ebene in der Tabelle aus.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Beispiel für Filteroptionen für „Feld auswählen“" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Ziehen Sie die Spalte **ResourceID** in den Bereich **Zeilen** unter **Produkt**, um die Kosten der einzelnen Dienste nach Ressource anzuzeigen.
1. Fügen Sie die Spalte **Datum** zum Bereich **Spalten** hinzu, um den täglichen Verbrauch für das Produkt anzuzeigen.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Beispiel für das Platzieren des Datums in den Bereich „Spalten“" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Erweitern und reduzieren Sie Monate mit den Symbolen **+** für die Spalte der einzelnen Monate.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Beispiel für das Symbol +" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

Das Hinzufügen der Spalten **Kosten** und **Menge** im Bereich **Werte** ist optional. Auf diese Weise werden zwei Spalten für jeden Datumsabschnitt unter jedem Monat und Tag erstellt, wenn sich die Spalte „Datum“ im Abschnitt „Spalten“ der PivotTable befindet.

Wenn Sie zusätzliche Filter benötigen, können Sie „InvoiceSection“, „costCenter“, „SubscriptionID“, „ResourceGroupName“ oder „Tags“ zum Filterabschnitt hinzufügen und den gewünschten Bereich auswählen.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Erstellen einer PivotTable zum Anzeigen der Kosten für eine bestimmte Ressource

Eine einzelne Ressource kann für verschiedene Dienste mehrere Gebühren verursachen. Ein virtueller Computer kann z. B. Computegebühren sowie Gebühren für Betriebssystemlizenzen, Bandbreite (Datenübertragungen), RI-Nutzung und Speicher für Momentaufnahmen verursachen. Wenn Sie die Gesamtnutzung für bestimmte Ressourcen überprüfen möchten, erstellen Sie mithilfe der folgenden Schritte ein Dashboard, um die allgemeine Nutzung mit Ihren Nutzungsdateien anzuzeigen.

1. Ziehen Sie in der rechten Menüleiste **ResourceID** in den Abschnitt **Filter** im PivotTable-Menü.
1. Wählen Sie die Ressource aus, für die Sie die Kosten anzeigen möchten. Machen Sie eine Eingabe im Feld **Suchen**, um einen Ressourcennamen zu suchen.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="Beispiel für die Suche nach resourceID" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. Fügen Sie **meterCategory** und **Produkt** zum Abschnitt **Zeilen** hinzu. Platzieren Sie **Produkt** unter **meterCategory**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Beispiel für das Platzieren von „meterCategory“ in den Bereich „PivotTable-Felder“" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Fügen Sie als Nächstes die Spalte **Erweiterte Kosten** zum Abschnitt **Werte** hinzu. Sie können auch die Spalte „Verbrauchte Menge“ verwenden, um Informationen zu Verbrauchseinheiten und Transaktionen zu erhalten. Beispiel: GB oder Stunden. Oder verwenden Sie Transaktionen anstelle von Kosten in verschiedenen Währungen wie USD, EUR und INR. Nun verfügen Sie über ein Dashboard, das alle Dienste anzeigt, die von der Ressource verwendet werden.
1. Fügen Sie die Spalte **Datum** zum Abschnitt **Spalten** hinzu. Sie zeigt den täglichen Verbrauch an.
1. Ein Erweitern und Reduzieren ist mithilfe der Symbole **+** in der Spalte der einzelnen Monate möglich.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Beispiel für das Symbol +" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

[!INCLUDE [Troubleshoot usage spikes](../../../includes/cost-management-billing-troubleshoot-usage-spikes.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../costs/quick-acm-cost-analysis.md)