---
title: Problembehandlung von Azure CSP-Abrechnungsproblemen mit Nutzungsdatei-PivotTables
description: Dieser Artikel unterstützt Sie bei der Problembehandlung von Abrechnungsproblemen von Azure Cloud Solution Provider (CSP) mithilfe von PivotTables, die aus Ihren CSV-Nutzungsdateien erstellt wurden.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: de526c46a9aace4a6740b8b9afaab5cc23f3f87d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711104"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Problembehandlung von CSP-Abrechnungsproblemen mit Nutzungsdatei-PivotTables

Dieser Artikel unterstützt Sie bei der Problembehandlung von Abrechnungsproblemen von Cloud Solution Provider (CSP) mithilfe von PivotTables, die in Ihren Partner Center-Abstimmungsdateien (Nutzungsdateien) erstellt wurden. Azure-Nutzungsdateien enthalten sämtliche Nutzungs- und Verbrauchsinformationen zu Azure. Die Informationen in der Datei helfen Ihnen bei Folgendem:

- Verstehen, wie Azure-Reservierungen genutzt und angewendet werden
- Abstimmen von Informationen in Cost Management mit Ihrer Rechnung
- Problembehandlung bei Kostenanstiegen
- Berechnen einer Erstattungssumme für eine Vereinbarung zum Servicelevel

Mithilfe der Informationen aus Ihren Nutzungsdateien können Sie Nutzungsprobleme besser verstehen und diagnostizieren. Nutzungsdateien werden im durch Trennzeichen getrennten Format (CSV) generiert. Da es sich bei den Nutzungsdateien unter Umständen um große CSV-Dateien handelt, ist es einfacher, sie als PivotTables zu bearbeiten und in einer Tabellenkalkulationsanwendung wie Excel anzuzeigen. In den Beispielen in diesem Artikel wird Excel verwendet, Sie können jedoch jede beliebige Tabellenkalkulationsanwendung verwenden.

Nur Abrechnungsadministratoren und globale Administratoren haben Zugriff zum Herunterladen von Abstimmungsdateien. Weitere Informationen finden Sie unter [Lesen der Zeilenelemente in Ihren Partner Center-Abstimmungsdateien](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Abrufen und Formatieren der Daten

Da Azure-Nutzungsdateien im CSV-Format vorliegen, müssen Sie die Daten für die Verwendung in Excel vorbereiten. Führen Sie die folgenden Schritte aus, um die Daten als Tabelle zu formatieren.

1. Laden Sie die Nutzungsdatei herunter, indem Sie die Anweisungen unter [Auffinden Ihrer Rechnung](/partner-center/read-your-bill#find-your-bill) ausführen.
1. Öffnen Sie die Datei in Excel.
1. Die unformatierten Daten ähneln dem folgenden Beispiel.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Beispiel für unformatierte Daten in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Wählen Sie das erste Feld in der Tabelle, **PartnerID**, aus.
1. Drücken Sie STRG + UMSCHALT + NACH-UNTEN-TASTE und anschließend STRG + UMSCHALT + NACH-RECHTS-TASTE, um alle Informationen in der Tabelle auszuwählen.
1. Wählen Sie in der oberen Menüleiste die Option **Einfügen** > **Tabelle** aus. Wählen Sie im Feld „Tabelle erstellen“ die Option **Meine Tabelle hat Überschriften** und anschließend **OK** aus.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Beispiel für das Dialogfeld „Tabelle erstellen“" :::
1. Wählen Sie in der oberen Menüleiste **Einfügen** > **PivotTable** und anschließend **OK** aus. Mit der Aktion wird ein neues Blatt in der Datei erstellt, und Sie gelangen zum PivotTable-Bereich auf der rechten Seite des Blatts.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Beispiel für den Bereich „PivotTable-Felder“" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Der Bereich „PivotTable-Felder“ ist ein Drag & Drop-Bereich. Fahren Sie mit dem nächsten Abschnitt fort, um die PivotTable zu erstellen.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Erstellen einer PivotTable zum Anzeigen der Azure-Kosten nach Ressourcen

In diesem Abschnitt erstellen Sie eine PivotTable, mit der Sie eine Problembehandlung der allgemeinen Azure-Nutzung durchführen können. Die Beispieltabelle kann Ihnen dabei helfen, zu untersuchen, welcher Dienst die meisten Ressourcen verbraucht. Oder Sie können die Ressourcen anzeigen, die die meisten Kosten verursachen, und Sie sehen, wie ein Dienst abgerechnet wird.

1. Ziehen Sie im Bereich „PivotTables-Felder“ **Dienstname** und **Ressource** in den Bereich **Zeilen**. Platzieren Sie **Ressource** unter **Dienstname**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Beispiel für „Dienstname“ und „Ressource“ in „Zeilen“" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Platzieren Sie im nächsten Schritt **Post-Tax Total** (Summe inkl. MwSt.) in den Bereich **Werte**. Sie können auch die Spalte „Verbrauchte Menge“ verwenden, um Informationen zu Verbrauchseinheiten und Transaktionen zu erhalten. Beispiel: GB oder Stunden. Oder verwenden Sie Transaktionen anstelle von Kosten in verschiedenen Währungen wie USD, EUR und INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Beispiel für Spalten, die zu PivotTable-Feldern hinzugefügt wurden" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Nun verfügen Sie über ein Dashboard für die allgemeine Untersuchung der Nutzung. Sie können einen bestimmten Dienst mithilfe der Filteroptionen in der PivotTable filtern.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Beispiel für die PivotTable-Filteroption für die Zeilenbeschriftung" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Wenn Sie eine zweite Ebene in einer PivotTable filtern möchten, z. B. eine Ressource, wählen Sie ein Element der zweiten Ebene in der Tabelle aus.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Beispiel für Filteroptionen für „Feld auswählen“" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Für zusätzliche Filter können Sie **SubscriptionID** und **Unternehmensname des Kunden** zum Bereich **Filter** hinzufügen und den gewünschten Bereich auswählen.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Erstellen einer PivotTable zum Anzeigen der Azure-Nutzung nach Datum

In diesem Abschnitt erstellen Sie eine PivotTable, mit der Sie eine Problembehandlung der allgemeinen Azure-Nutzung nach verbrauchter Menge und Datum durchführen können. Es ist hilfreich, Abrechnungsspitzen nach Datum und Dienst zu identifizieren. Oder Sie können die Ressourcen anzeigen, die die meisten Kosten verursachen, und Sie sehen, wie ein Dienst abgerechnet wird.

Die Abstimmungsdatei verfügt über zwei Tabellen. Eine befindet sich oben (die Haupttabelle), und am Ende des Dokuments finden Sie eine weitere Tabelle. Diese zweite Tabelle enthält zu einem großen Teil die gleichen Informationen, sie enthält jedoch keine Preis- oder Kostendetails. Sie umfasst das Nutzungsdatum und die verbrauchte Menge.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Beispiel für die Abstimmungsdateien mit zwei Tabellen mit Daten" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Führen Sie die gleichen Schritte wie im Abschnitt [Abrufen und Formatieren der Daten](#get-the-data-and-format-it) durch, um eine Excel-Tabelle mit den Informationen am Ende der Abstimmungsdatei zu erstellen.
1. Wenn die Tabelle fertig ist und Sie über ein PivotTable-Blatt verfügen, führen Sie die gleichen Schritte wie im Abschnitt „Erstellen einer PivotTable zum Anzeigen der Azure-Kosten nach Ressourcen“ durch, um das Dashboard vorzubereiten. Platzieren Sie **Verbrauchte Menge** im Bereich **Werte**, anstatt „Post-Tax Total“ (Summe inkl. MwSt.) zu verwenden.
1. Fügen Sie **Nutzungsdaten** zum Abschnitt „Spalten“ hinzu. Die PivotTable sollte wie im folgenden Beispiel aussehen.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Beispiel für die finalen PivotTable-Felder" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Sie verfügen jetzt über ein Dashboard, das die Nutzung pro Datum anzeigt. Sie können jeden Monat erweitern, indem Sie das Symbol **+** auswählen.

Das Dashboard zeigt die verbrauchte Menge in Einheiten wie etwa GB, Stunden und Übertragungen an.

Damit Sie den Preis pro Tag anzeigen können, fügen Sie **Ressourcen-GUID** zum Bereich **Zeilen** hinzu. Fügen Sie in der oberen Tabelle den Preis pro Einheit (**ListPrice**) für die Ressource hinzu. Multiplizieren Sie **ListPrice** mit **Verbrauchte Menge**, um die Gebühren vor Steuern zu berechnen. Die Beträge müssen übereinstimmen.

Einige Ressourcen (Dienste) verfügen über skalierte Preise nach verbrauchter Menge. Einige Ressourcen haben z. B. einen höheren Preis für die ersten verbrauchten 100 GB und einen niedrigeren Preis für die im Anschluss verbrauchten GB. Behalten Sie die skalierten Preise im Hinterkopf, wenn Sie die Kosten manuell berechnen.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Erstellen einer PivotTable zum Anzeigen der Kosten für eine bestimmte Ressource

Eine einzelne Ressource kann für verschiedene Dienste mehrere Gebühren verursachen. Ein virtueller Computer kann z. B. Computegebühren sowie Gebühren für Betriebssystemlizenzen, Bandbreite (Datenübertragungen), RI-Nutzung und Speicher für Momentaufnahmen verursachen. Wenn Sie die Gesamtnutzung für bestimmte Ressourcen überprüfen möchten, erstellen Sie mithilfe der folgenden Schritte ein Dashboard, um die allgemeine Nutzung mit Ihren Nutzungsdateien anzuzeigen.

Abstimmungsdateien enthalten keine ressourcenspezifischen Details. Verwenden Sie die aggregierte Nutzungsdatei. Wenden Sie sich an den [Azure-Abrechnungssupport](https://go.microsoft.com/fwlink/?linkid=2083458), damit Sie die aggregierte Nutzungsdatei für Ihr Abonnement erhalten. Aggregierte Dateien werden auf Abonnementebene generiert. Die unformatierten Daten ähneln dem folgenden Beispiel.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Beispiel für eine unformatierte aggregierte Nutzungsdatei" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

Die Datei enthält die folgenden Spalten.

- **UsageStart** und **UsageEnd** – Datum für jedes Zeilenelement (jede Einheit der Verwendung). Beispielsweise jeden Tag.
- **MeteredResourceID** – entspricht in Azure der ID der Verbrauchseinheit.
- **Eigenschaften** – enthält die Instanz-ID (Ressourcenname) mit anderen Details, wie etwa dem Speicherort.
- **Menge** – verbrauchte Menge in der Abstimmungsdatei

1. Wählen Sie das erste Feld in der Tabelle, **PartnerID**, aus.  
1. Drücken Sie STRG + UMSCHALT + NACH-UNTEN-TASTE und anschließend STRG + UMSCHALT + NACH-RECHTS-TASTE, um alle Informationen in der Tabelle auszuwählen.
1. Wählen Sie in der oberen Menüleiste die Option **Einfügen** > **Tabelle** aus. Wählen Sie im Feld „Tabelle erstellen“ die Option **Meine Tabelle hat Überschriften** und anschließend **OK** aus.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Beispiel für das Dialogfeld „Tabelle erstellen“" :::
1. Wählen Sie in der oberen Menüleiste **Einfügen** > **PivotTable** und anschließend **OK** aus. Mit der Aktion wird ein neues Blatt in der Datei erstellt, und Sie gelangen zum PivotTable-Bereich auf der rechten Seite des Blatts.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Beispiel für den Bereich „PivotTable-Felder“ für die Abstimmungsdatei" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Fügen Sie als Nächstes **MeteredResourceID** zum Bereich **Zeilen** und **Menge** zu **Werte** hinzu. In den Ergebnissen werden die Informationen zur Gesamtnutzung angezeigt. Um weitere Details zu erhalten, platzieren Sie **UsageEndDateTime** in den Bereich **Spalten**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Beispiel für allgemeine Nutzungsinformationen" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Um einen Gesamtbericht anzuzeigen, fügen Sie **Eigenschaften** zu **Zeilen** unter **MeteredResourceID** hinzu. Er zeigt ein umfassendes Dashboard für Ihre Nutzung an.
1. Wenn Sie nach einer bestimmten Ressource filtern möchten, fügen Sie **Eigenschaften** zum Bereich **Filter** hinzu, und wählen Sie die gewünschte Nutzung aus. Verwenden Sie die Suche, um einen Ressourcennamen zu finden.
    Um die Kosten für die Ressource anzuzeigen, ermitteln Sie die insgesamt verbrauchte Menge, und multiplizieren Sie den Wert mit dem Listenpreis. Der Listenpreis ist spezifisch für jede Ressourcen-GUID (MeteredResourceID). Wenn eine Ressource mehrere MeteredResourceIDs beansprucht, berücksichtigen Sie den Gesamtwert für jede ID.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit Cost Management für Partner](../costs/get-started-partners.md)