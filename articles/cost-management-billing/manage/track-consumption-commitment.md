---
title: Nachverfolgen von Microsoft Azure Consumption Commitment (MACC)
description: Erfahren Sie, wie Sie Ihr Microsoft Azure Consumption Commitment (MACC) für eine Microsoft-Kundenvereinbarung nachverfolgen.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/11/2021
ms.author: banders
ms.openlocfilehash: 741efbce1f8578ca425059b2b04b64d9892112cb
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857600"
---
# <a name="track-your-microsoft-azure-consumption-commitment-macc"></a>Nachverfolgen von Microsoft Azure Consumption Commitment (MACC)

Das Microsoft Azure Consumption Commitment (MACC) ist eine vertragliche Verpflichtung, die Ihre Organisation möglicherweise für einen bestimmten Zeitraum mit Microsoft Azure eingegangen ist. Wenn Ihr Unternehmen eine MACC für ein Microsoft-Kundenvereinbarung (MCA)-Abrechnungskonto oder ein Enterprise Agreement (EA)-Abrechnungskonto hat, können Sie im Azure-Portal oder über REST-APIs wichtige Aspekte Ihrer Verpflichtung überprüfen, einschließlich Start- und Enddatum, verbleibende Verpflichtung und zulässige Ausgaben.

## <a name="track-your-macc-commitment"></a>Nachverfolgen Ihrer MACC-Verpflichtung

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    :::image type="content" source="./media/track-consumption-commitment/billing-search-cost-management-billing.png" alt-text="Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ im Portal." lightbox="./media/track-consumption-commitment/billing-search-cost-management-billing.png" :::
3. Wählen Sie auf der Abrechnungsbereicheseite das Abrechnungskonto aus, für das Sie die Verpflichtung nachverfolgen möchten. Der Abrechnungskontotyp muss **Microsoft Customer Agreement** für Microsoft-Kundenvereinbarung (MCA)-Kunden oder **Enterprise Agreement** für EA-Kunden sein.   
    :::image type="content" source="./media/track-consumption-commitment/list-of-scopes.png" alt-text="Screenshot von Abrechnungsbereichen" lightbox="./media/track-consumption-commitment/list-of-scopes.png" :::
    > [!NOTE]
     > Das Azure-Portal speichert den letzten Abrechnungsbereich, auf den Sie zugreifen, und zeigt den Bereich an, wenn Sie das nächste Mal auf die Seite „Kostenverwaltung + Abrechnung“ gelangen. Wenn Sie „Kostenverwaltung + Abrechnung“ bereits besucht haben, wird die Abrechnungsbereicheseite nicht angezeigt. Wenn dies der Fall ist, überprüfen Sie, ob Sie sich im [richtigen Bereich](#check-access-to-a-microsoft-customer-agreement) befinden. Wenn nicht, [wechseln Sie den Bereich](view-all-accounts.md#switch-billing-scope-in-the-azure-portal), um das Abrechnungskonto für eine Microsoft-Kundenvereinbarung auszuwählen.
4. Führen Sie je nach Vereinbarung eine der folgenden Aktionen aus:
    - Für MCA-Kunden wählen Sie **Eigenschaften** auf der linken Seite und wählen Sie dann **Microsoft Azure Consumption Commitment (MACC)** .  
        :::image type="content" source="./media/track-consumption-commitment/select-macc-tab.png" alt-text="Screenshot, der die Auswahl der Registerkarte MACC für MCA zeigt." lightbox="./media/track-consumption-commitment/select-macc-tab.png" :::
    - Für EA-Kunden wählen Sie **Credits + Commitments** im linken Navigationsmenü und wählen Sie dann **Microsoft Azure Consumption Commitment (MACC)** .  
        :::image type="content" source="./media/track-consumption-commitment/select-macc-tab-ea.png" alt-text="Screenshot, der die Auswahl der Registerkarte MACC für EA zeigt." lightbox="./media/track-consumption-commitment/select-macc-tab-ea.png" :::
1. Die Registerkarte „Microsoft Azure Consumption Commitment (MACC)“ enthält die folgenden Abschnitte.

#### <a name="remaining-commitment"></a>Verbleibende Verpflichtung 

Als verbleibende Verpflichtung wird der seit Ihrer letzten Rechnung verbliebene Verpflichtungsbetrag angezeigt.

:::image type="content" source="./media/track-consumption-commitment/macc-remaining-commitment.png" alt-text="Screenshot der verbleibenden Verpflichtung für einen MACC." lightbox="./media/track-consumption-commitment/macc-remaining-commitment.png" :::

#### <a name="details"></a>Details

Im Abschnitt „Details“ werden weitere wichtige Aspekte Ihrer Verpflichtung angezeigt.

:::image type="content" source="./media/track-consumption-commitment/macc-details.png" alt-text="Screenshot: MACC-Details" lightbox="./media/track-consumption-commitment/macc-details.png" :::

| Begriff | Definition |
|---|---|
| ID | Ein Bezeichner, der Ihre MACC eindeutig identifiziert. Dieser Bezeichner wird verwendet, um Ihre MACC-Informationen über REST-APIs abzurufen. |
| Kaufdatum | Das Datum, an dem Sie die Verpflichtung eingegangen sind. |
| Startdatum | Das Datum, an dem die Verpflichtung wirksam wurde. |
| Enddatum | Das Datum, an dem die Verpflichtung abgelaufen ist. |
| Verpflichtungsbetrag | Der Betrag, zu dessen Ausgabe für MACC-berechtigte Produkte/Dienste Sie sich verpflichtet haben. |
| Status | Der Status Ihrer Verpflichtung. |

Ihre MACC kann einen der folgenden Status aufweisen:

- Aktiv: MACC ist aktiv. Alle berechtigten Ausgaben tragen zu Ihrer MACC-Verpflichtung bei.
- Abgeschlossen: Sie haben Ihre MACC-Verpflichtung abgeschlossen. 
- Abgelaufen: MACC ist abgelaufen. Weitere Informationen erhalten Sie von Ihrem Microsoft-Kontoteam. 
- Abgebrochen: MACC wurde abgebrochen. Neue Azure-Ausgaben tragen nicht zu Ihrer MACC-Verpflichtung bei.

#### <a name="events"></a>Events

Im Abschnitt „Ereignisse“ werden Ereignisse (in Rechnung gestellte Ausgaben) angezeigt, die Ihre MACC-Verpflichtung verringert haben.

:::image type="content" source="./media/track-consumption-commitment/macc-events.png" alt-text="Screenshot: MACC-Ereignisse." lightbox="./media/track-consumption-commitment/macc-events.png" :::

| Begriff | Definition |
|---|---|
| Date | Das Datum, an dem das Ereignis eingetreten ist. |
| BESCHREIBUNG | Eine Beschreibung des Ereignisses. |
| Abrechnungsprofil | Das Abrechnungsprofil, für das der Vorfall eingetreten ist. Das Abrechnungsprofil gilt nur für Microsoft-Kundenvereinbarungen. Wenn Sie ein EA-Abonnement haben, wird das Abrechnungsprofil nicht angezeigt. |
| MACC-Dekrement | Die Höhe des MACC-Dekrements aus dem Ereignis. |
| Verbleibende Verpflichtung | Die nach dem Ereignis verbleibende MACC-Verpflichtung. |

### <a name="rest-api"></a>[REST-API](#tab/rest)

Mithilfe der Azure-APIs für die [Abrechnung](/rest/api/billing/) und [Nutzung](/rest/api/consumption/) können Sie die Microsoft Azure Consumption Commitment (MACC) für Ihr Abrechnungskonto programmgesteuert abrufen.

In den weiter unten bereitgestellten Beispielen werden REST-APIs verwendet. PowerShell und Azure CLI werden derzeit nicht unterstützt. Die Beispielausgabe bezieht sich auf Microsoft-Kundenvereinbarungen, die Ausgabe für Unternehmensvereinbarungen weicht daher ab.

### <a name="find-billing-accounts-you-have-access-to"></a>Suchen nach Abrechnungskonten, auf die Sie Zugriff haben

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2020-05-01
```
Die API-Antwort gibt eine Liste von Abrechnungskonten zurück.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Contoso",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "hasReadAccess": true,
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Kayla Lewis",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Individual",
        "hasReadAccess": true,
      }
    }
  ]
}
```

Verwenden Sie die `displayName`-Eigenschaft des Abrechnungskontos, um das Abrechnungskonto zu identifizieren, für das Sie MACC nachverfolgen möchten. Kopieren Sie den `name` des Abrechnungskontos. Wenn Sie MACC beispielsweise für das **Contoso**-Abrechnungskonto nachverfolgen möchten, kopieren Sie `9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`. Halten Sie diesen Wert für den nächsten Schritt bereit.

### <a name="get-a-list-of-microsoft-azure-consumption-commitments"></a>Abrufen einer Liste von Microsoft Azure Consumption Commitments

Führen Sie die folgende Anforderung aus, und ersetzen Sie dabei `<billingAccountName>` durch den `name`-Wert, den Sie im ersten Schritt (`9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`) kopiert haben.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/lots?api-version=2021-05-01&$filter=source%20eq%20%27ConsumptionCommitment%27
```
Die API-Antwort gibt Listen mit MACCs für Ihr Abrechnungskonto zurück.

```json
    {
    "value": [
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "name": "G2021032459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Active",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 9899.42
          },
          "source": "ConsumptionCommitment",
          "startDate": "2021-03-01T00:00:00.0000000Z",
          "expirationDate": "2024-02-28T00:00:00.0000000Z"
        }
      },
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G1011082459206000XXXX",
        "name": "G1011082459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Complete",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 0.00
          },
          "source": "ConsumptionCommitment",
          "startDate": "2020-03-01T00:00:00.0000000Z",
          "expirationDate": "2021-02-28T00:00:00.0000000Z"
        }
      }
    ]
  }
```

| Elementname  | BESCHREIBUNG                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `purchasedDate`  | Das Datum, an dem die MACC erworben wurde.   |
| `status`  | Der Status Ihrer Verpflichtung. |
| `originalAmount` | Der ursprüngliche Verpflichtungsbetrag. |
| `closedBalance`   | Die seit der letzten Rechnung verbliebene Verpflichtung.    |
| `source`      | Für MACC ist die Quelle immer ConsumptionCommitment. |
| `startDate`      |  Das Datum, an dem die MACC aktiv wurde.  |
| `expirationDate`  | Das Datum, an dem die MACC abläuft.   |

Ihre MACC kann einen der folgenden Status aufweisen: 

- Aktiv: MACC ist aktiv. Alle berechtigten Ausgaben tragen zu Ihrer MACC-Verpflichtung bei.
- Abgeschlossen: Sie haben Ihre MACC-Verpflichtung abgeschlossen. 
- Abgelaufen: MACC ist abgelaufen. Weitere Informationen erhalten Sie von Ihrem Microsoft-Kontoteam. 
- Abgebrochen: MACC wurde abgebrochen. Neue Azure-Ausgaben tragen nicht zu Ihrer MACC-Verpflichtung bei. 

### <a name="get-events-that-affected-macc-commitment"></a>Abrufen von Ereignissen, die sich auf die MACC-Verpflichtung ausgewirkt haben

Führen Sie die folgende Anforderung aus, und ersetzen Sie dabei `<billingAccountName>` durch den `name`-Wert, den Sie im ersten Schritt (`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`) kopiert haben. Sie müssen ein Startdatum (**startDate**) und ein Enddatum (**endDate**) übergeben, um Ereignisse für den gewünschten Zeitraum abzurufen.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/events?api-version=2021-05-01&startDate=<startDate>&endDate=<endDate>&$filter=lotsource%20eq%20%27ConsumptionCommitment%27
```

Die API-Antwort gibt alle Ereignisse zurück, die sich auf Ihre MACC-Verpflichtung ausgewirkt haben.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Finance",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-05-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00075XXXX",
        "charges": {
          "currency": "USD",
          "value": -100.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9899.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00075XXXX"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Engineering",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-04-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00074XXXX",
        "charges": {
          "currency": "USD",
          "value": -0.29
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9999.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00074XXXX"
      }
    }
  ]
}

```
| Elementname  | BESCHREIBUNG                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `billingProfileId` | Der eindeutige Bezeichner für das Abrechnungsprofil, für das das Ereignis eingetreten ist. |
| `billingProfileDisplayName` | Der Anzeigename für das Abrechnungsprofil, für das das Ereignis eingetreten ist. |
| `lotId`   | Der eindeutige Bezeichner für die MACC.    |
| `lotSource`      | Für MACC ist dies ConsumptionCommitment. |
| `transactionDate`      |  Das Datum, an dem das Ereignis eingetreten ist.  |
| `description`  | Die Beschreibung des Ereignisses.   |
| `charges`  | Der Betrag des MACC-Dekrements.   |
| `closedBalance`  | Das Guthaben nach dem Ereignis.   |
| `eventType`  | Für MACC werden nur SettledCharges-Ereignisse unterstützt.   |
| `invoiceNumber`  | Die eindeutige ID der Rechnung, deren Gebühren MACC verringert haben.   |

---

## <a name="azure-services-and-marketplace-offers-that-are-eligible-for-macc"></a>Für MACC geeignete Azure-Dienste und Marketplace-Angebote

Sie können im Azure-Portal ermitteln, welche Azure-Dienste und Marketplace-Angebote für das MACC-Dekrement berechtigt sind. Weitere Informationen finden Sie unter [Bestimmen, welche Angebote für den Azure Consumption Commitment (MACC/CtC)-Vorteil berechtigt sind](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc).

## <a name="azure-credits-and-macc"></a>Azure-Guthaben und MACC

Wenn Ihre Organisation Azure-Gutschriften von Microsoft erhalten hat, tragen der Verbrauch oder die Käufe, die durch Gutschriften abgedeckt werden, nicht zu Ihrer MACC-Verpflichtung bei.

Wenn Ihre Organisation Azure-Vorauszahlung erworben hat, tragen der Verbrauch oder die Käufe, die durch Gutschriften abgedeckt werden, nicht zu Ihrer MACC-Verpflichtung bei.  Der eigentliche Vorauszahlungserwerb selbst verringert jedoch Ihre MACC-Verpflichtung.

Contoso ist beispielsweise im Mai eine MACC-Verpflichtung von 50.000 USD eingegangen. Im Juni wurde eine Azure-Vorauszahlung von 10.000 USD erworben. Durch den Kauf wird die MACC-Verpflichtung herabgesetzt, und die verbleibende Verpflichtung beträgt 40.000 USD. Im Juni hat Contoso für die Azure-Vorauszahlung geeignete Dienste im Wert von 10.000 USD in Anspruch genommen. Die Dienstgebühren werden durch die Azure-Vorauszahlung abgedeckt, verringern jedoch nicht die MACC-Verpflichtung. Sobald die Azure-Vorauszahlung vollständig genutzt wurde, reduzieren jede Nutzung von Azure-Diensten und andere berechtigte Käufe die MACC-Verpflichtung.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

- [Bestimmen, welche Angebote für den Azure Consumption Commitment (MACC/CtC)-Vorteil berechtigt sind](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)
- [Nachverfolgen Ihres Azure-Guthabens](mca-check-azure-credits-balance.md)