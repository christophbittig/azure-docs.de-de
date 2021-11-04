---
title: Sparen bei SAP HANA (große Instanzen) mit einer Azure-Reservierung
description: Hier finden Sie die benötigten Informationen, bevor Sie eine Reservierung für HANA (große Instanzen) erwerben, sowie Informationen zum Kauf.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/19/2021
ms.author: banders
ms.openlocfilehash: a7e5838ca79c3f90bf7dd75d2300f346b65588a6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457865"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Sparen bei SAP HANA (große Instanzen) mit einer Azure-Reservierung

Sie können Kosten für SAP HANA (große Instanzen, HLI) sparen, wenn Sie Azure-Reservierungen für ein oder drei Jahre vorab erwerben. Der Reservierungsrabatt wird auf die bereitgestellte HLI-SKU angewendet, die der erworbenen reservierten Instanz entspricht. Dieser Artikel enthält die benötigten Informationen, bevor Sie eine Reservierung erwerben, sowie Informationen zum Kauf.

Mit dem Kauf einer Reservierung verpflichten Sie sich zur Nutzung der HLI für ein oder drei Jahre. Der Erwerb reservierter HLI-Kapazität umfasst den in der SKU enthaltenen Compute- und NFS-Speicher. Die Reservierung beinhaltet keine Softwarelizenzkosten wie z. B. für das Betriebssystem, SAP oder zusätzliche Speicherkosten. Der Reservierungsrabatt wird automatisch auf die bereitgestellte SAP-HLI angewendet. Wenn der Reservierungszeitraum endet, gelten die Sätze für nutzungsbasierte Bezahlung für Ihre bereitgestellte Ressource.

## <a name="purchase-considerations"></a>Überlegungen zum Kauf

Es muss eine HLI-SKU bereitgestellt werden, bevor Sie reservierte Kapazität erwerben können. Die Reservierung wird im Voraus oder monatlich bezahlt. Für reservierte HLI-Kapazität gelten die folgenden Einschränkungen:

- Reservierungsrabatte gelten nur für Abonnements im Rahmen eines Enterprise Agreement oder einer Microsoft-Kundenvereinbarung. Andere Abonnements werden nicht unterstützt.
- Flexibilität der Instanzgröße wird für reservierte HLI-Kapazität nicht unterstützt. Eine Reservierung gilt nur für die SKU und die Region, für die sie erworben wird.
- Self-Service-Stornierung und -Umtausch werden nicht unterstützt.
- Der reservierte Kapazitätsbereich ist ein einzelner Bereich und gilt daher für ein einzelnes Abonnement und eine einzelne Ressourcengruppe. Die erworbene Kapazität kann nicht für die Verwendung durch ein anderes Abonnement aktualisiert werden.
- Sie können nicht über einen gemeinsam genutzten Reservierungsbereich für reservierte HANA-Kapazität verfügen. Sie können den Reservierungsbereich nicht teilen, zusammenführen oder aktualisieren.
- Mithilfe der API-Aufrufe für reservierte Kapazität können Sie jeweils eine einzelne HLI erwerben. Führen Sie zusätzliche API-Aufrufe durch, um zusätzliche Mengen zu erwerben.

Sie können reservierte Kapazität im Azure-Portal oder mithilfe der [REST-API](/rest/api/reserved-vm-instances/reservationorder/purchase) erwerben.

## <a name="buy-a-hana-large-instance-reservation"></a>Erwerben einer Reservierung für HANA (große Instanzen)

Verwenden Sie die folgenden Informationen, um eine HLI-Reservierung mit den [REST-APIs für einen Reservierungsauftrag](/rest/api/reserved-vm-instances/reservationorder/purchase) zu erwerben.

### <a name="get-the-reservation-order-and-price"></a>Abrufen von Reservierungsauftrag und Preis

Rufen Sie zunächst den Reservierungsauftrag und den Preis für die bereitgestellte SKU von HANA (große Instanzen) mithilfe der API für die [Preisberechnung](/rest/api/reserved-vm-instances/reservationorder/calculate) ab.

Im folgenden Beispiel wird [armclient](https://github.com/projectkudu/ARMClient) für REST-API-Aufrufe mit PowerShell verwendet. Nachfolgend sehen Sie, wie der Reservierungsauftrag, die API-Anforderung zur Preisberechnung und der Anforderungstext aussehen sollten:

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'billingplan': 'Monthly'
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

Weitere Informationen zu Datenfeldern und deren Beschreibungen finden Sie unter [HLI-Reservierungsfelder](#hli-reservation-fields).

Die folgende Beispielantwort zeigt die Rückgabe. Notieren Sie sich den Wert, der für `quoteId` zurückgegeben wird.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>Durchführen des Kaufs

Führen Sie den Kauf mithilfe der zurückgegebenen `quoteId` und der `reservationOrderId` aus dem vorherigen Abschnitt [Abrufen von Reservierungsauftrag und Preis](#get-the-reservation-order-and-price) durch.

Hier sehen Sie eine Beispielanforderung:

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2019-04-01  "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
       'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
               'billingplan': 'Monthly'

        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

Hier sehen Sie eine Beispielantwort. Wenn der Auftrag erfolgreich erteilt ist, muss `provisioningState` den Status `creating`aufweisen.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>Überprüfen des Status für erfolgreichen Erwerb

Führen Sie die GET-Anforderung für den Reservierungsauftrag aus, um den Status des Auftrags anzuzeigen. `provisioningState` muss `Succeeded` lauten.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

Die Antwort sollte dem folgenden Beispiel ähneln.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>HLI-Reservierungsfelder

Nachfolgend wird die Bedeutung verschiedener Reservierungsfelder erläutert.

  **SKU**: Name der HLI-SKU. Dieser entspricht dem Format `SAP_HANA_On_Azure_<SKUname>`.

  **Standort**: Verfügbare HLI-Regionen. Informationen zu verfügbaren Regionen finden Sie unter [SKUs für SAP HANA in Azure (große Instanzen)](../../virtual-machines/workloads/sap/hana-available-skus.md). Zum Abrufen des Zeichenfolgenformats für den Standort verwenden Sie den [API-Aufruf für Standorte](/rest/api/resources/subscriptions/listlocations#locationlistresult).

  **Reservierter Ressourcentyp**: `SapHana`

  **Abonnement**: Das zum Bezahlen für die Reservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Zahlungen für die Reservierung belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Microsoft-Kundenvereinbarung“ lauten. Die Gebühren werden ggf. von der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) abgezogen oder als Überschreitung belastet.

  **Bereich**: Der Bereich der Reservierung muss ein einzelner Bereich sein.

  **Laufzeit**: Ein Jahr oder drei Jahre. Dies entspricht `P1Y` oder `P3Y`.

  **Menge**: Die Anzahl von Instanzen, die für die Reservierung erworben wird. Die zu erwerbende Menge ist jeweils eine einzelne HLI. Für zusätzliche Reservierungen wiederholen Sie den API-Aufruf mit entsprechenden Feldern.

## <a name="troubleshoot-errors"></a>Beheben von Fehlern

Bei einem Reservierungserwerb können Sie eine Fehlermeldung wie im folgenden Beispiel erhalten. Die mögliche Ursache ist, dass die HLI nicht zum Erwerb bereitgestellt ist. In diesem Fall wenden Sie sich an Ihr Microsoft-Konto-Team, damit eine HLI bereitgestellt wird, bevor Sie einen Reservierungserwerb durchführen.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Aufrufen von Azure-REST-APIs mit Postman und cURL](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman).
- Eine Liste der verfügbaren SKUs und Regionen finden Sie unter [SKUs für SAP HANA in Azure (große Instanzen)](../../virtual-machines/workloads/sap/hana-available-skus.md).