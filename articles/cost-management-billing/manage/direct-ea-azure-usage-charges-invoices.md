---
title: Anzeigen der Details zur Azure-Nutzungszusammenfassung und Herunterladen von Berichten für direkte EA-Registrierungen
description: In diesem Artikel wird erläutert, wie Unternehmensadministratoren von direkten EA-Registrierungen (Enterprise Agreement) eine Zusammenfassung ihrer Nutzungsdaten, die in Anspruch genommene Azure-Vorauszahlung und die Gebühren für andere Nutzung im Azure-Portal anzeigen.
author: bandersmsft
ms.author: banders
ms.date: 10/12/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: 005f2c16e463bd01c7e453548ddb29c50e2f937b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007331"
---
# <a name="view-your-usage-summary-details-and-download-reports-for-direct-ea-enrollments"></a>Anzeigen der Details zur Nutzungszusammenfassung und Herunterladen von Berichten für direkte EA-Registrierungen

In diesem Artikel wird erläutert, wie Unternehmensadministratoren von direkten EA-Registrierungen (Enterprise Agreement) eine Zusammenfassung ihrer Nutzungsdaten, die in Anspruch genommene Azure-Vorauszahlung und die Gebühren für andere Nutzung im Azure-Portal anzeigen. Die Gebühren werden auf der Übersichtsebene für alle Konten und Abonnements der Registrierung angezeigt.

## <a name="prerequisites"></a>Voraussetzungen

Um die Gebühren in Ihrer Rechnung anzuzeigen und zu überprüfen, müssen Sie ein Unternehmensadministrator sein. Weitere Informationen finden Sie unter [Informationen zu Azure Enterprise Agreement-Administratorrollen in Azure](understand-ea-roles.md). Wenn Sie nicht wissen, wer der Unternehmensadministrator für Ihre Organisation ist, erstellen Sie eine Supportanfrage im Azure-Portal.

## <a name="review-usage-charges"></a>Überprüfen der Nutzungsgebühren

Laden Sie den Bericht mit den Nutzungsdetails herunter, um die ausführliche Nutzung für bestimmte Konten anzuzeigen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Cost Management + Billing**, und wählen Sie diese Option aus.
1. Wählen Sie im Navigationsmenü die Option **Abrechnungsbereiche** und dann das Abrechnungskonto aus, mit dem Sie arbeiten möchten.
1. Wählen Sie im Navigationsmenü die Option **Nutzung + Gebühren** aus.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" alt-text="Screenshot der Seite „Nutzung + Gebühren“" lightbox="./media/direct-ea-azure-usage-charges-invoices/navigation-usage-charges.png" :::
1. Zum Anzeigen von Details vorheriger Jahre wählen Sie einen **Zeitraum** aus.

In der folgenden Tabelle sind die Begriffe und Beschreibungen auf der Seite „Nutzung + Gebühren“ im Azure-Portal aufgeführt. Die im Azure-Portal angezeigten Gebühren sind in US-Dollar angegeben.

| **Begriff** | **Beschreibung** |
| --- | --- |
| Month | Der Monat der Nutzung. |
| Gebühren für Guthaben | Das angewandte Guthaben für diesen spezifischen Zeitraum. |
| Dienstüberschreitung | Die Kosten für die Nutzung durch Ihre Organisation überschreiten Ihr Guthaben. |
| Separat in Rechnung gestellt | Die von Ihrer Organisation genutzten Dienste sind nicht durch das Guthaben abgedeckt. |
| Azure Marketplace | Käufe und Nutzung im Azure Marketplace sind nicht durch das Guthaben Ihrer Organisation abgedeckt und werden separat in Rechnung gestellt. |
| Gesamtgebühren | Gebühren für Guthaben + Dienstüberschreitung + Separat in Rechnung gestellt + Azure Marketplace. |

## <a name="download-usage-charges-csv-file"></a>Herunterladen der CSV-Datei mit Nutzungsgebühren

Unternehmensadministratoren verwenden die Seite „Nutzung + Gebühren herunterladen“, um die folgenden Berichte als CSV-Dateien herunterzuladen.

- **Nutzungsdetails**: Je nach Auswahl enthält die CSV-Datei alle Gebühren (Nutzung und Käufe) einschließlich der RI-Käufe (Reservierungen). Es können auch amortisierte Gebühren (Nutzung und Käufe) einschließlich der Reservierungskäufe angegeben werden.
- **Marketplace-Gebühren**: Die CSV-Datei mit den Marketplace-Gebühren liefert basierend auf dem angegebenen Abrechnungszeitraum eine Übersicht der nutzungsbasierten Marketplace-Gebühren nach Tag.
- **Preisblatt**: Die CSV-Datei mit dem Preisblatt enthält den zutreffenden Tarif für jede Verbrauchseinheit für den angegebenen Registrierungs- und Abrechnungszeitraum.
- **Saldo und Zusammenfassung**: Die CSV-Datei mit Saldo und Zusammenfassung enthält eine monatliche Übersicht über Salden, neue Käufe, Gebühren für den Marketplace-Dienst, Anpassungen und Überschreitungsgebühren.

1. Wählen Sie neben dem Bericht für den Monat das Symbol **Herunterladen** aus.  
:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-usage-charges-csv.png" alt-text="Screenshot der Option „Herunterladen“" :::
1. Wählen Sie auf der Seite „Nutzung + Gebühren herunterladen“ die Option **Dokument vorbereiten** für den Bericht aus, den Sie herunterladen möchten.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" alt-text="Screenshot der Seite „Dokument vorbereiten“" lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png" :::
1. Je nach monatlicher Nutzung kann es eine Weile dauern, bis Azure das Dokument zum Herunterladen vorbereitet hat. Wenn es zum Herunterladen bereit ist, wählen Sie **CSV herunterladen** aus.

Unternehmensadministratoren können auch eine allgemeine Zusammenfassung der Gebühren für den ausgewählten Zeitraum ganz unten auf der Seite „Nutzung + Gebühren“ anzeigen.

:::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/usage-charges-summary.png" alt-text="Screenshot einer Zusammenfassung der Nutzungsgebühren" lightbox="./media/direct-ea-azure-usage-charges-invoices/prepare-document.png":::

## <a name="download-or-view-your-azure-billing-invoice"></a>Herunterladen oder Anzeigen Ihrer Azure-Rechnung

Sie können Ihre Rechnung auch aus dem [Azure-Portal](https://portal.azure.com) herunterladen oder sie per E-Mail zugeschickt bekommen. Rechnungen werden an den Benutzer gesendet, der für den Empfang von Rechnungen für die Registrierung festgelegt wurde.

Nur ein Unternehmensadministrator hat die Berechtigung zum Anzeigen und Abrufen der Rechnung. Weitere Informationen zum Zugriff auf Abrechnungsinformationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Abrechnung mithilfe von Rollen](manage-billing-access.md).

Wenn eines der folgenden Ereignisse während des Abrechnungszyklus auftritt, erhalten Sie eine Azure-Rechnung:

- **Dienstüberschreitung**: Die Kosten für die Nutzung durch Ihre Organisation überschreiten Ihr Guthaben.
- **Separat abgerechnete Gebühren**: Die von Ihrer Organisation genutzten Dienste sind nicht durch das Guthaben abgedeckt. Die folgenden Dienste werden Ihnen unabhängig von Ihrem Guthaben in Rechnung gestellt:
  - Canonical
  - Citrix XenApp Essentials
  - Citrix XenDesktop
  - Registrierter Benutzer
  - OpenLogic
  - Registrierter Benutzer von Remote Access Rights XenApp Essentials
  - Ubuntu Advantage
  - Visual Studio Enterprise (monatlich)
  - Visual Studio Enterprise (jährlich)
  - Visual Studio Professional (monatlich)
  - Visual Studio Professional (jährlich)
- **Marketplace-Gebühren**: Käufe und Nutzung im Azure Marketplace sind nicht durch das Guthaben Ihrer Organisation abgedeckt. Daher werden Ihnen Marketplace-Gebühren unabhängig von Ihrem Guthaben in Rechnung gestellt. Im Azure-Portal kann ein Unternehmensadministrator Marketplace-Käufe aktivieren und deaktivieren.

Auf Ihrer Rechnung werden zuerst die Azure-Nutzungsgebühren mit den entsprechenden Kosten und dann die etwaigen Marketplace-Gebühren aufgeführt. Wenn Sie über ein Guthaben verfügen, wird es auf die Azure-Nutzung angewendet, und auf Ihrer Rechnung sind unten die Informationen zu Ihrer Azure-Nutzung und Marketplace-Nutzung ohne Kosten angegeben.

### <a name="download-your-azure-invoices-pdf"></a>Herunterladen Ihrer Azure-Rechnungen (PDF-Format)

Für die meisten Abonnements können Sie die Rechnung im Azure-Portal herunterladen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Cost Management + Billing**, und wählen Sie diese Option aus.
1. Wählen Sie im Navigationsmenü die Option **Abrechnungsbereiche** und dann das Abrechnungskonto aus, mit dem Sie arbeiten möchten.
1. Wählen Sie im Navigationsmenü die Option  **Rechnungen** aus. Auf der Seite „Rechnungen“ werden alle Rechnungen und Gutschriften angezeigt, die in den letzten 12 Monaten generiert wurden.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" alt-text="Screenshot der Seite „Rechnungen“" lightbox="./media/direct-ea-azure-usage-charges-invoices/invoices-page.png" :::
    
1. Suchen Sie auf der Rechnungsseite nach der Zeile der Rechnung, die Sie herunterladen möchten. Wählen Sie ganz rechts in der Zeile das Symbol mit den Auslassungspunkten (**...**) aus.
1. Wählen Sie im Kontextmenü die Option **Herunterladen** aus.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/download-context-menu.png" alt-text="Screenshot des Kontextmenüs mit der Option „Herunterladen“"  :::

Sie können einen Zeitraum auswählen, um die Rechnungsdetails der letzten drei Jahre anzuzeigen.

Wenn die Rechnung mehrere Positionen enthält, durch die 40 Seiten bei einer PDF-Datei überschritten werden, wird sie in mehrere Dokumentnummern aufgeteilt.

In der folgenden Tabelle sind die Begriffe und Beschreibungen auf der Seite „Rechnungen“ aufgeführt:

| **Begriff** | **Beschreibung** |
| --- | --- |
| Dokumentdatum | Datum, an dem die Rechnung oder Gutschrift generiert wurde. |
| Dokumentnummer | Nummer der Rechnung oder Gutschrift. |
| Billing Period | Abrechnungszeitraum der Rechnung oder Gutschrift. |
| Auftragsnummer | Auftragsnummer für die Rechnung oder Gutschrift. |
| Gesamtbetrag | Gesamtbetrag der Rechnung oder des Guthabens. |

## <a name="review-credit-charges"></a>Überprüfen von Guthaben

In diesem Abschnitt wird beschrieben, wie Sie den Anfangssaldo, den Endsaldo und die Guthabenanpassungen für Ihre Azure-Vorauszahlung (zuvor Mindestverbrauch genannt) anzeigen können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Cost Management + Billing**, und wählen Sie diese Option aus.
1. Wählen Sie im Navigationsmenü die Option **Abrechnungsbereiche** und dann das Abrechnungskonto aus, mit dem Sie arbeiten möchten.
1. Wählen Sie im Navigationsmenü die Option **Guthaben + Verpflichtungen** aus.
1. Auf der Registerkarte „Guthaben“ wird eine Aufschlüsselung Ihrer Guthaben und ein Diagramm Ihres Guthabens im Zeitverlauf angezeigt.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" alt-text="Screenshot der Registerkarte „Guthaben“" lightbox="./media/direct-ea-azure-usage-charges-invoices/credits-tab.png" :::

In der folgenden Tabelle sind die Begriffe und Beschreibungen auf der Registerkarte „Guthaben“ aufgeführt.

| **Begriff** | **Beschreibung** |
| --- | --- |
| Month | Monat des Guthabens. |
| Startguthaben | Startguthaben für diesen Monat. |
| Neues Guthaben | Neu hinzugefügtes Guthaben. |
| Adjustments | Im Monat vorgenommene Anpassungen. |
| Auf Gebühren angewendetes Guthaben | Gesamtbetrag der Rechnung oder des generierten Guthabens. |
| Verbleibendes Guthaben | Endsaldo des Guthabens. |

## <a name="review-reservation-transaction-details"></a>Überprüfen der Details zu Reservierungstransaktionen

Sie können alle Reservierungen für ein Enterprise Agreement im Azure-Portal anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Cost Management + Billing**, und wählen Sie diese Option aus.
1. Wählen Sie im Navigationsmenü die Option **Abrechnungsbereiche** und dann das Abrechnungskonto aus, mit dem Sie arbeiten möchten.
1. Wählen Sie im Navigationsmenü die Option **Reservierungstransaktionen** aus. Die in der folgenden Abbildung gezeigten Preise sind Beispiele.  
    :::image type="content" source="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" alt-text="Screenshot der Seite „Reservierungstransaktionen“" lightbox="./media/direct-ea-azure-usage-charges-invoices/reservation-transactions.png" :::

In der folgenden Tabelle sind die Begriffe und Beschreibungen auf der Seite „Reservierungstransaktionen“ aufgeführt.

| **Begriff** | **Beschreibung** |
| --- | --- |
| Date | Datum, an dem die Reservierung vorgenommen wurde. |
| Name | Name der Reservierung. |
| BESCHREIBUNG | Beschreibung der Reservierung. |
| Abonnement für Erwerb | Abonnement, unter dem die Reservierung vorgenommen wurde. |
| Konto für Erwerb | Das Konto, unter dem die Reservierung vorgenommen wurde. |
| Fakturierungsintervall | Fakturierungsintervall der Reservierung. |
| type | Der Typ der Transaktion, z. B. Kauf oder Erstattung. |
| Menge | Erworbene Reservierungsmenge. |
| Betrag (USD) | Reservierungskosten. |

## <a name="csv-report-formatting-issues"></a>Probleme bei der Formatierung von CSV-Berichten

Wenn Sie CSV-Berichte in Excel anzeigen und Ihre Abrechnungswährung Euro ist, können Formatierungsprobleme im Zusammenhang mit Kommas und Punkten auftreten.

Die gezeigten Kosten sind Beispiele.

Es wird beispielsweise Folgendes angezeigt:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
|---|--- | ---|---|
| Stunden | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Ihnen *sollte* jedoch Folgendes angezeigt werden:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Stunden | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Das Formatierungsproblem tritt aufgrund der Standardeinstellungen in der Importfunktion von Excel auf. Excel importiert alle Felder als *allgemeinen* Text und geht davon aus, dass eine Zahl nach mathematischem Standard getrennt wird. Beispiel: *1,000.00*.

Wenn für Ihre Währung ein Punkt (**.**) als Tausendertrennzeichen und ein Komma als Dezimaltrennzeichen (**,**) verwendet wird, wird sie nicht ordnungsgemäß angezeigt. Beispiel: *1.000,00*. Die Importergebnisse können je nach regionaler Spracheinstellung variieren.

So importieren Sie die CSV-Datei ohne Formatierungsprobleme:

1. Wechseln Sie in Microsoft Excel zu **Datei** > **Öffnen**. Der Textimport-Assistent wird angezeigt.
1. Wählen Sie unter **Ursprünglicher Datentyp** die Option **Mit Trennzeichen** aus. Der Standard ist **Feste Breite**.
1. Wählen Sie **Weiter** aus.
1. Aktivieren Sie unter **Trennzeichen** das Kontrollkästchen für **Komma**. Deaktivieren Sie **Tab**, falls ausgewählt.
1. Wählen Sie **Weiter** aus.
1. Scrollen Sie über die Spalten **ResourceRate** und **ExtendedCost**.
1. Wählen Sie die Spalte **ResourceRate** aus. Sie wird schwarz hervorgehoben angezeigt.
1. Wählen Sie im Abschnitt **Datenformat der Spalten** die Option **Text** anstelle von **Allgemein** aus. Die Spaltenüberschrift wird von **Allgemein** in **Text** geändert.
1. Wiederholen Sie die Schritte 8 und 9 für die Spalte **Erweiterte Kosten**, und wählen Sie dann **Fertigstellen** aus.

> [!TIP]
> Wenn Sie Excel so konfiguriert haben, dass CSV-Dateien automatisch geöffnet werden, müssen Sie stattdessen die Funktion **Öffnen** in Excel verwenden. Wechseln Sie in Excel zu **Datei** > **Öffnen**.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu allgemeinen Aufgaben, die ein direkter Unternehmensadministrator im Azure-Portal übernimmt, finden Sie unter [Azure-Verwaltung für direkte EAs](direct-ea-administration.md).