---
title: Anzeigen und Herunterladen der Azure-Nutzung und -Gebühren
description: Erfahren Sie, wie Sie Ihre tägliche Azure-Nutzung und -Gebühren herunterladen oder anzeigen, und zeigen Sie zusätzliche verfügbare Ressourcen an.
keywords: Nutzungsabrechnung, Nutzungsgebühren, Nutzung herunterladen, Nutzung anzeigen, Azure-Rechnung, Azure-Nutzung
author: bandersmsft
ms.author: banders
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.date: 10/22/2021
ms.openlocfilehash: e7f4a5f12ec9e1be5c7129d12ed519bc4c781d61
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255402"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Anzeigen und Herunterladen der Azure-Nutzung und -Gebühren

Sie können eine tägliche Aufschlüsselung Ihrer Azure-Nutzung und -Gebühren vom Azure-Portal herunterladen. Sie können Ihre Nutzungsdaten auch mithilfe der Azure CLI abrufen. Nur bestimmte Rollen (z. B. „Kontoadministrator“ oder „Unternehmensadministrator“) verfügen über die Berechtigung zum Abrufen von Azure-Nutzungsinformationen. Weitere Informationen zum Zugriff auf Abrechnungsinformationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Abrechnung mithilfe von Rollen](../manage/manage-billing-access.md).

Wenn Sie über eine Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) verfügen, müssen Sie über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen, um Ihre Azure-Nutzung und -Gebühren anzeigen zu können.  Bei einer Microsoft-Partnervereinbarung (Microsoft Partner Agreement, MPA) können nur der globale Administrator und Administrator-Agent in der Partnerorganisation Azure-Rechnungen anzeigen und herunterladen.

Basierend auf dem von Ihnen verwendeten Abonnementtyp variieren die Optionen zum Herunterladen Ihrer Nutzung und Gebühren.

## <a name="download-usage-from-the-azure-portal-csv"></a>Herunterladen der Nutzung aus dem Azure-Portal (CSV-Datei)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach *Kostenverwaltung + Abrechnung*.  
    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise ein Abrechnungskonto oder ein Abrechnungsprofil auswählen.
1. Wählen Sie im Menü auf der linken Seite die Option **Rechnungen** unter **Abrechnung** aus.
1. Suchen Sie im Rechnungsraster nach der Zeile des Abrechnungszeitraums, der der Nutzung entspricht, die Sie herunterladen möchten.
1. Wählen Sie das **Downloadsymbol** oder die Auslassungspunkte (`...`) rechts aus.  
  ![Screenshot der Seite „Kostenverwaltung + Abrechnung“ mit der Option zum Herunterladen](./media/download-azure-daily-usage/download-usage-others.png)  
1. Der Downloadbereich wird rechts geöffnet. Wählen Sie **Download** im Abschnitt **Nutzungsdetails** aus.  

## <a name="download-usage-for-ea-customers"></a>Herunterladen von Nutzungsdaten für EA-Kunden

Sie müssen Unternehmensadministrator, Kontobesitzer oder Abteilungsadministrator sein, um Nutzungsdaten als EA-Kunde anzeigen und herunterladen zu können. Außerdem muss die Richtlinie zum Anzeigen von Gebühren aktiviert sein.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach *Kostenverwaltung + Abrechnung*.  
    ![Screenshot der Suche im Azure-Portal](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Wenn Sie über Zugriff auf mehrere Abrechnungskonten verfügen, wählen Sie den Abrechnungsbereich für Ihr EA-Abrechnungskonto aus.
1. Wählen Sie **Verbrauch + Gebühren** aus.
1. Wählen Sie für den Monat, den Sie herunterladen möchten, **Herunterladen** aus.  
    ![Screenshot der Seite „Kostenverwaltung + Abrechnung“ für E A-Kunden](./media/download-azure-daily-usage/download-usage-ea.png)
1. Wählen Sie auf der Seite „Nutzung und Gebühren herunterladen“ in der Liste unter „Nutzungsdetails“ die Art der Gebühren aus, die Sie herunterladen möchten. Je nach Auswahl enthält die CSV-Datei alle Gebühren (Nutzung und Käufe) einschließlich der RI-Käufe (Reservierungen). Es können auch amortisierte Gebühren (Nutzung und Käufe) einschließlich der Reservierungskäufe angegeben werden. 
    :::image type="content" source="./media/download-azure-daily-usage/select-usage-detail-charge-type.png" alt-text="Screenshot der Auswahl des Gebührentyps für Nutzungsdetails zum Herunterladen" :::
1. Wählen Sie **Dokument vorbereiten** aus.
1.  Je nach monatlicher Nutzung kann es eine Weile dauern, bis Azure das Dokument zum Herunterladen vorbereitet hat. Wenn es zum Herunterladen bereit ist, wählen Sie **CSV herunterladen** aus.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Herunterladen der Nutzungsdaten für Ihre Microsoft-Kundenvereinbarung

Sie müssen über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen, um Nutzungsdaten für ein Abrechnungsprofil anzeigen oder herunterladen zu können.

### <a name="download-usage-for-billed-charges"></a>Herunterladen der Nutzung für in Rechnung gestellte Gebühren

1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
2. Wählen Sie ein Abrechnungsprofil aus.
3. Wählen Sie **Rechnungen** aus.
4. Suchen Sie im Rechnungsraster nach der Zeile der Rechnung, die der Nutzung entspricht, die Sie herunterladen möchten.
5. Klicken Sie am Ende der Zeile auf die Auslassungspunkte (`...`).
6. Wählen Sie im Kontextmenü „Download“ die Option **Azure-Nutzung und -Gebühren** aus.

### <a name="download-usage-for-open-charges"></a>Herunterladen der Nutzung für offene Gebühren

Sie können ebenfalls die Monat-bis-Datum-Nutzung für den aktuellen Abrechnungszeitraum herunterladen, also für die Gebühren, die noch nicht abgerechnet wurden.

1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
2. Wählen Sie ein Abrechnungsprofil aus.
3. Klicken Sie auf dem Blatt **Übersicht** auf **Azure-Nutzung und -Gebühren herunterladen**.

### <a name="download-usage-for-pending-charges"></a>Herunterladen der ausstehenden Nutzungsgebühren

Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, können Sie die Nutzung für den bisherigen Kalendermonat für den aktuellen Abrechnungszeitraum herunterladen. Diese Nutzungsgebühren wurden noch nicht in Rechnung gestellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie nach *Kostenverwaltung + Abrechnung*.
3. Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto auswählen.
4. Suchen Sie im Bereich **Übersicht** nach den Downloadlinks unter den aktuellen Gebühren.
5. Wählen Sie **Nutzung und Preise herunterladen** aus.

## <a name="get-usage-data-with-azure-cli"></a>Abrufen von Nutzungsdaten mit der Azure CLI

Bereiten Sie zunächst Ihre Umgebung für die Azure-Befehlszeilenschnittstelle vor:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Nachdem Sie sich angemeldet haben, verwenden Sie den Befehl [az costmanagement query](/cli/azure/costmanagement#az_costmanagement_query), um Nutzungsinformationen für Ihr Abonnement für den bisherigen Kalendermonat abzufragen:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage --dataset-aggregation '{\"totalCost\":{\"name\":\"PreTaxCost\",\"function\":\"Sum\"}}' --dataset-grouping name="ResourceGroup" type="Dimension"
   --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

Sie können die Abfrage auch mithilfe des Parameters **--dataset-filter** oder anderer Parameter eingrenzen:

```azurecli
'{\"totalCost\":{\"name\":\"PreTaxCost\",\"function\":\"Sum\"}}' --dataset-grouping name="ResourceGroup" type="Dimension"
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --dataset-filter "{\"and\":[{\"or\":[{\"dimension\":{\"name\":\"ResourceLocation\",\"operator\":\"In\",\"values\":[\"East US\",\"West Europe\"]}},{\"tag\":{\"name\":\"Environment\",\"operator\":\"In\",\"values\":[\"UAT\",\"Prod\"]}}]},{\"dimension\":{\"name\":\"ResourceGroup\",\"operator\":\"In\",\"values\":[\"API\"]}}]}"
```

Mit dem Parameter **--dataset-filter** wird eine JSON-Zeichenfolge oder `@json-file` übernommen.

Sie haben auch die Möglichkeit, die [az costmanagement export](/cli/azure/costmanagement/export)-Befehle zu verwenden, um Nutzungsdaten in ein Azure-Speicherkonto zu exportieren. Sie können die Daten hier herunterladen.

1. Erstellen Sie eine Ressourcengruppe, oder verwenden Sie eine vorhandene Ressourcengruppe. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen:

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Erstellen Sie ein Speicherkonto zum Empfangen der Exporte, oder verwenden Sie ein vorhandenes Speicherkonto. Zum Erstellen eines Kontos verwenden Sie den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create):

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Führen Sie den Befehl [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) aus, um den Export zu erstellen:

   ```azurecli
   az costmanagement export create --name DemoExport --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --storage-directory demodirectory
   ```

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um mehr über Ihre Rechnung und Nutzungsgebühren zu erfahren:

- [Grundlegendes über Benennungen zu den Gebühren in der Datei mit ausführlichen Nutzungsdaten zu Microsoft Azure](understand-usage.md)
- [Grundlegendes zu Ihrer Rechnung für Microsoft Azure](review-individual-bill.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](download-azure-invoice.md)
- [Anzeigen und Herunterladen der Azure-Preise für Ihre Organisation](../manage/ea-pricing.md)

Wenn Sie eine Microsoft-Kundenvereinbarung abgeschlossen haben, lesen Sie die folgenden Artikel:

- [Grundlegendes zu den Begriffen in der CSV-Datei zu Azure-Nutzung und -Gebühren für eine Microsoft-Kundenvereinbarung](mca-understand-your-usage.md)
- [Grundlegendes zu den Gebühren auf der Rechnung für eine Microsoft-Kundenvereinbarung](review-customer-agreement-bill.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](download-azure-invoice.md)
- [Anzeigen und Herunterladen von Steuerdokumenten für Ihre Microsoft-Kundenvereinbarung](mca-download-tax-document.md)
- [Anzeigen und Herunterladen der Azure-Preise für Ihre Organisation](../manage/ea-pricing.md)
