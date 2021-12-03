---
title: Implementieren eines Webhooks für den SaaS-Dienst | Kommerzieller Microsoft-Marketplace
description: Erfahren Sie, wie Sie mithilfe der Fulfillment-APIs der Version 2 einen Webhook für den SaaS-Dienst implementieren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: 6cf50327719b434cda7d5df4e3df99b9d96a1dcd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063974"
---
# <a name="implementing-a-webhook-on-the-saas-service"></a>Implementieren eines Webhooks für den SaaS-Dienst

Beim Erstellen eines transaktionsfähigen SaaS-Angebots im Partner Center stellt der Partner die URL des **Verbindungswebhooks** bereit, die als HTTP-Endpunkt verwendet werden soll.  Dieser Webhook wird von Microsoft mithilfe des HTTP-Aufrufs POST aufgerufen, um die Herausgeberseite über die folgenden Ereignissen zu informieren, die auf Microsoft-Seite auftreten:

* Wenn das SaaS-Abonnement den Status *Abonniert* hat:
    * ChangePlan (Plan ändern)
    * ChangeQuantity (Menge ändern)
    * Erneuern
    * Angehalten
    * Abbestellen
* Wenn das SaaS-Abonnement den Status *Ausgesetzt* hat:
    * Reinstate (Reaktivieren)
    * Abbestellen

Der Herausgeber muss einen Webhook im SaaS-Dienst implementieren, um den Status des SaaS-Abonnements mit der Microsoft-Seite konsistent zu halten.  Der SaaS-Dienst muss die API zum Abrufen des Vorgangs aufrufen, um den Webhookaufruf und die Nutzlastdaten zu überprüfen und zu autorisieren, bevor Aktionen auf Grundlage der Webhookbenachrichtigung durchgeführt werden.  Der Herausgeber sollte eine HTTP-200-Antwort an Microsoft zurückgeben, sobald der Webhookaufruf verarbeitet wird.  Dieser Wert bestätigt, dass der Webhookaufruf vom Herausgeber erfolgreich empfangen wurde.

> [!IMPORTANT]
> Der Webhook-URL-Dienst muss rund um die Uhr aktiv sein und jederzeit neue Aufrufe von Microsoft empfangen können.  Microsoft verfügt über eine Wiederholungsrichtlinie für den Webhook-Befehl (500 Wiederholungen in acht Stunden). Doch wenn der Herausgeber den Befehl nicht akzeptiert und keine Antwort zurückgibt, schlägt der Vorgang, über den der Webhook benachrichtigt wird, schließlich seitens Microsoft fehl.

*Beispiel für Webhook-Nutzdaten für ein Kaufereignis:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": "25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

*Beispiel für Webhook-Nutzdaten für ein Abonnement-Wiederherstellungsereignis:*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": "20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "InProgress"
}
```

*Beispiel für Webhook-Nutzdaten für ein Verlängerungsereignis:*

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer1 ",
  "planId": "silver",
  "quantity": "25",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Renew",
  "status": "Success"
}
```

## <a name="development-and-testing"></a>Entwickeln und Testen

Zu Beginn des Entwicklungsprozesses wird empfohlen, API-Pseudoantworten auf Herausgeberseite zu erstellen.  Diese Antworten können auf Beispielantworten in diesem Artikel basieren.

Wenn der Herausgeber für End-to-End-Tests bereit ist, sollten folgende Schritte ausgeführt werden:

* Veröffentlichen Sie ein SaaS-Angebot für eine begrenzte Vorschauzielgruppe, und belassen Sie es in der Vorschauphase.
* Legen Sie den Planpreis auf 0 fest, um zu vermeiden, dass beim Testen die tatsächliche Abrechnung von Kosten ausgelöst wird.  Eine andere Möglichkeit besteht darin, einen anderen Preis als 0 (null) festzulegen und alle Testkäufe innerhalb von 24 Stunden zu stornieren.
* Stellen Sie sicher, dass alle Flows von Anfang bis Ende aufgerufen werden, um ein reales Kundenszenario zu simulieren.
* Wenn der Partner den vollständigen Kauf- und Abrechnungsflow testen möchte, verwenden Sie hierfür ein Angebot mit einem höheren Preis als 0 €.  Der Kauf wird abgerechnet, und eine Rechnung wird generiert.

Je nachdem, wo das Angebot veröffentlicht wird, kann ein Kaufablauf über das Azure-Portal oder Microsoft AppSource ausgelöst werden.

Die Aktionen *Plan ändern*, *Menge ändern* und *Kündigen* werden auf Herausgeberseite getestet.  Auf Microsoft-Seite kann *Kündigen* sowohl über das Azure-Portal als auch das Admin Center (das Portal, in dem Microsoft AppSource-Käufe verwaltet werden) ausgelöst werden.  *Menge und Plan ändern* kann nur im Admin Center ausgelöst werden.

## <a name="get-support"></a>Support

Supportoptionen für Herausgeber finden Sie unter [Support für das Programm „Kommerzieller Marketplace“ im Partner Center](../support.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Optionen für SaaS-Angebote im kommerziellen Marketplace finden Sie unter [APIs für getaktete Abrechnung im Marketplace](../marketplace-metering-service-apis.md).

Überprüfen und verwenden Sie die [Clients für verschiedene Programmiersprachen und Beispiele](https://github.com/microsoft/commercial-marketplace-samples).
