---
title: SaaS-Fulfillment-Abonnement-APIs der Version 2 im kommerziellen Microsoft-Marketplace
description: Erfahren Sie, wie Sie die Abonnement-APIs, die Teil der SaaS-Fulfillment-APIs der Version 2 sind, zum Verwalten eines SaaS-Angebots in Microsoft AppSource, im Azure Marketplace und im Azure-Portal verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ace7aac5a308621ca8032677e7ca7eec3858bdb7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063975"
---
# <a name="saas-fulfillment-subscription-apis-v2-in-microsoft-commercial-marketplace"></a>SaaS-Fulfillment-Abonnement-APIs der Version 2 im kommerziellen Microsoft-Marketplace

In diesem Artikel wird Version 2 der SaaS-Fulfillment-Abonnement-APIs beschrieben.

## <a name="resolve-a-purchased-subscription"></a>Auflösen eines erworbenen Abonnements

Der Auflösungsendpunkt ermöglicht dem Herausgeber, das Identifizierungstoken für den Kauf aus dem kommerziellen Marketplace (im Abschnitt [Gekauft, aber noch nicht aktiviert](pc-saas-fulfillment-life-cycle.md#purchased-but-not-yet-activated-pendingfulfillmentstart) als *Token* bezeichnet) gegen eine persistente ID für das erworbene SaaS-Abonnement und dessen Details auszutauschen.

Wenn ein Kunde zur URL der Angebotsseite des Partners weitergeleitet wird, wird das Identifizierungstoken des Kunden in diesem URL-Aufruf als Parameter *token* übergeben. Es wird erwartet, dass der Partner dieses Token verwendet und dessen Auflösung anfordert. Die Antwort der Auflösungs-API enthält die SaaS-Abonnement-ID und andere Details zur eindeutigen Identifizierung des Kaufs. Das mit dem Aufruf der URL der Angebotsseite angegebene *Token* ist in der Regel 24 Stunden gültig. Wenn das von Ihnen erhaltene *Token* bereits abgelaufen ist, wird empfohlen, dem Endkunden die folgenden Anweisungen bereitzustellen:

„Dieser Kauf konnte nicht identifiziert werden. Öffnen Sie dieses Saas-Abonnement im Azure-Portal oder Microsoft 365 Admin Center, und wählen Sie nochmals „Konto konfigurieren“ oder „Konto verwalten“ aus.

Durch das Aufrufen der Auflösungs-API werden Abonnementdetails und der Status von SaaS-Abonnements in allen unterstützten Status zurückgegeben.

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.   |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client. Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet. Das Format ist `"Bearer <accessaccess_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |
|  `x-ms-marketplace-token`  | Der aufzulösende Parameter *token* zur Identifizierung des Kaufs.  Das Token wird im Aufruf der URL der Angebotsseite übergegeben, wenn der Kunde zur Website des SaaS-Partners weitergeleitet wird (z. B. `https://contoso.com/signup?token=<token><authorization_token>`). <br> <br>  Beachten Sie, dass der Wert von *token* als Teil der URL der Angebotsseite codiert ist und daher decodiert werden muss, bevor er als Parameter in diesem API-Aufruf verwendet wird.  <br> <br> Ein Beispiel einer codierten Zeichenfolge in der URL sieht wie folgt aus: `contoso.com/signup?token=ab%2Bcd%2Fef`, wobei `ab%2Bcd%2Fef` das *Token* ist.  Das gleiche Token sieht decodiert wie folgt aus: `Ab+cd/ef` |
| | |

*Antwortcodes:*

Code: 200 – Gibt eindeutige SaaS-Abonnement-IDs basierend auf dem bereitgestellten `x-ms-marketplace-token` zurück.

Beispiel für einen Antworttext:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Code: 400 – Ungültige Anforderung. `x-ms-marketplace-token` fehlt, ist falsch formatiert, ungültig oder abgelaufen.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht wurde als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="activate-a-subscription"></a>Aktivieren eines Abonnements

Nachdem das SaaS-Konto für einen Endkunden konfiguriert wurde, muss der Herausgeber die API zum Aktivieren des Abonnements auf Microsoft-Seite aufrufen.  Dem Kunden werden erst dann Beträge in Rechnung gestellt, wenn dieser API-Aufruf erfolgreich war.

### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Verwenden Sie 2018-08-31.   |
| `subscriptionId` | Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der [Auflösungs-API](#resolve-a-purchased-subscription) aufgelöst wurde.
 |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Diese Zeichenfolge korreliert alle Ereignisse des Clientvorgangs mit Ereignissen auf der Serverseite.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `authorization`      |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet. Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Beispiel einer Anforderungsnutzlast:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Antwortcodes:*

Code: 200: Das Abonnement wurde auf Microsoft-Seite als „Abonniert“ gekennzeichnet.

Es ist kein Antworttext für diesen Aufruf vorhanden.

Code: 400 – Ungültige Anforderung: Fehler bei der Überprüfung.

* `planId` ist nicht in den Nutzdaten der Anforderung vorhanden.
* `planId` in den Nutzdaten der Anforderung stimmt nicht mit derjenigen ein, die erworben wurde.
* `quantity` in den Nutzdaten der Anforderung stimmt nicht mit derjenigen ein, die erworben wurde.
* Das SaaS-Abonnement weist den Status *Abonniert* oder *Ausgesetzt* auf.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht wurde als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden. Das SaaS-Abonnement weist den Status *Gekündigt* auf.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-list-of-all-subscriptions"></a>Abrufen einer Liste aller Abonnements

Diese API ruft eine Liste aller erworbenen SaaS-Abonnements für alle Angebote ab, die der Herausgeber im kommerziellen Marketplace veröffentlicht.  SaaS-Abonnements in allen möglichen Status werden zurückgegeben. Gekündigte SaaS-Abonnements werden ebenfalls zurückgegeben, da diese Informationen seitens Microsoft nicht gelöscht werden.

Diese API gibt paginierte Ergebnisse zurück (100 pro Seite).

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Verwenden Sie 2018-08-31.  |
| `continuationToken`  | Dieser Parameter ist optional. Wenn Sie die erste Seite der Ergebnisse abrufen möchten, lassen Sie den Parameter leer.  Verwenden Sie den Wert, der im Parameter `@nextLink` zurückgegeben wird, um die nächste Seite abzurufen. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
| `authorization`      |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Antwortcodes:*

Code: 200: Gibt die Liste aller vorhandenen Abonnements für alle Angebote dieses Herausgebers basierend auf dessen Autorisierungstoken zurück.

*Beispiel für einen Antworttext:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Wenn keine erworbenen SaaS-Abonnements für diesen Herausgeber gefunden werden, wird ein leerer Antworttext zurückgegeben.

Code: 403 – Unzulässig. Das Autorisierungstoken ist nicht verfügbar, ungültig oder abgelaufen.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 500 – Interner Serverfehler. Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-subscription"></a>Abrufen eines Abonnements

Diese API ruft ein angegebenes SaaS-Abonnements für ein SaaS-Angebot ab, das der Herausgeber im kommerziellen Marketplace veröffentlicht. Rufen Sie mithilfe dieses Aufrufs alle verfügbaren Informationen zu einem bestimmten SaaS-Abonnement anhand seiner ID ab, anstatt die API aufzurufen, mit der eine Liste aller Abonnements abgerufen werden kann.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Verwenden Sie 2018-08-31. |
| `subscriptionId`     |  Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `authorization`     | Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet. Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Antwortcodes:*

Code: 200 – Gibt basierend auf der bereitgestellten `subscriptionId` Details für ein SaaS-Abonnement zurück.

*Beispiel für einen Antworttext:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit der angegebenen `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="list-available-plans"></a>Auflisten verfügbarer Pläne

Diese API ruft alle Pläne für ein SaaS-Angebot ab, das durch die `subscriptionId` eines bestimmten Erwerbs dieses Angebots bestimmt wird.  Verwenden Sie diesen Aufruf, um eine Liste aller privaten und öffentlichen Pläne abzurufen, die der Begünstigte eines SaaS-Abonnements für das Abonnement aktualisieren kann.  Die zurückgegebenen Pläne sind in derselben Region wie der bereits erworbene Plan verfügbar.

Dieser Aufruf gibt eine Liste der Pläne zurück, die für diesen Kunden zusätzlich zu dem bereits erworbenen Plan verfügbar sind.  Die Liste kann einem Endbenutzer auf der Website des Herausgebers angezeigt werden.  Ein Endbenutzer kann den Abonnementplan in einen beliebigen Plan in der zurückgegebenen Liste ändern.  Beim Ändern des Plans in einen nicht in der Liste aufgeführten Plan tritt ein Fehler auf.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
|  `subscriptionId`    |  Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `x-ms-correlationid`  |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Antwortcodes:*

Code: 200 – Gibt eine Liste aller verfügbaren Pläne für ein vorhandenes SaaS-Abonnement einschließlich des bereits erworbenen Plans zurück.

Beispiel für einen Antworttext:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Wenn `subscriptionId` nicht gefunden wird, wird ein leerer Antworttext zurückgegeben.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht möglicherweise, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="change-the-plan-on-the-subscription"></a>Ändern des Plans für das Abonnement

Diese API dient zum Ändern des vorhandenen Plans, der für ein SaaS-Abonnement erworben wurde, in einen neuen Plan (öffentlich oder privat).  Der Herausgeber muss diese API aufrufen, wenn ein Plan auf Herausgeberseite für ein im kommerziellen Marketplace erworbenes SaaS-Abonnement geändert wird.

Diese API kann nur für *aktive* Abonnements aufgerufen werden.  Jeder Plan kann in einen anderen vorhandenen Plan (öffentlich oder privat), aber nicht in sich selbst geändert werden.  Bei privaten Plänen muss der Mandant des Kunden im Partner Center als Teil der Zielgruppe für den Plan definiert werden.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
| `subscriptionId`     | Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde. |

*Anforderungsheader:*
 
|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID. Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `x-ms-correlationid`  | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Beispiel einer Anforderungsnutzlast:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Antwortcodes:*

Code: 202 – Die Anforderung zum Ändern des Plans wurde akzeptiert und asynchron verarbeitet.  Vom Partner wird erwartet, dass er die **Standort-URL für den Vorgang** abfragt, um zu ermitteln, ob die Änderungsanforderung für den Plan erfolgreich war oder fehlgeschlagen ist.  Die Abfrage sollte alle paar Sekunden durchgeführt werden, bis der endgültige Status *Fehler*, *Erfolgreich* oder *Konflikt* für den Vorgang empfangen wird.  Der endgültige Vorgangsstatus sollte schnell zurückgegeben werden, doch kann dies in einigen Fällen mehrere Minuten dauern.

Der Partner erhält auch eine Webhookbenachrichtigung, wenn die Aktion auf Seite des kommerziellen Marketplace erfolgreich abgeschlossen werden kann.  Erst dann sollte der Herausgeber die Planänderung auf Herausgeberseite vornehmen.

*Antwortheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL zum Abrufen des Vorgangsstatus.  Beispiel: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 – Ungültige Anforderung: Fehler bei der Überprüfung.

* Der neue Plan ist nicht vorhanden oder für dieses bestimmte SaaS-Abonnement nicht verfügbar.
* Der neue Plan ist mit dem aktuellen Plan identisch.
* Der Status des SaaS-Abonnements ist nicht *Abonniert*.
* Der Aktualisierungsvorgang für ein SaaS-Abonnement ist nicht in `allowedCustomerOperations` enthalten.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

>[!NOTE]
>Es kann jeweils nur der Plan oder die Menge von Arbeitsplätzen geändert werden, nicht beides gleichzeitig.

>[!Note]
>Diese API kann nur nach ausdrücklicher Genehmigung der Änderung durch den Endbenutzer aufgerufen werden.

## <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Ändern der Menge von Arbeitsplätzen für das SaaS-Abonnement

Diese API dient zum Ändern (Erhöhen oder Verringern) der Menge von Arbeitsplätzen, die für ein SaaS-Abonnement erworben wurden.  Der Herausgeber muss diese API aufrufen, wenn die Menge der Arbeitsplätze für ein im kommerziellen Marketplace erstelltes SaaS-Abonnement über die Herausgeberseite geändert wird.

Die Menge der Arbeitsplätze darf die im aktuellen Plan zulässigen Menge nicht übersteigen.  In diesem Fall muss der Herausgeber den Plan ändern, bevor die Anzahl der Arbeitsplätze geändert wird.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
|  `subscriptionId`     | Ein eindeutiger Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde.  |

*Anforderungsheader:*
 
|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `x-ms-correlationid`  | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     | Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Beispiel einer Anforderungsnutzlast:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Antwortcodes:*

Code: 202 – Die Anforderung zum Ändern der Menge wurde akzeptiert und asynchron verarbeitet. Vom Partner wird erwartet, dass er die **Standort-URL für den Vorgang** abfragt, um zu ermitteln, ob die Änderungsanforderung für die Menge erfolgreich war oder fehlgeschlagen ist.  Die Abfrage sollte alle paar Sekunden durchgeführt werden, bis der endgültige Status *Fehler*, *Erfolgreich* oder *Konflikt* für den Vorgang empfangen wird.  Der endgültige Vorgangsstatus sollte schnell zurückgegeben werden, doch kann dies in einigen Fällen mehrere Minuten dauern.

Der Partner erhält auch eine Webhookbenachrichtigung, wenn die Aktion auf Seite des kommerziellen Marketplace erfolgreich abgeschlossen werden kann.  Erst dann sollte der Herausgeber die Mengenänderung auf Herausgeberseite vornehmen.

*Antwortheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Link zu einer Ressource, um den Vorgangsstatus abzurufen.  Beispiel: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Code: 400 – Ungültige Anforderung: Fehler bei der Überprüfung.

* Die neue Menge ist größer oder kleiner als das Limit für den aktuellen Plan.
* Die neue Menge fehlt.
* Die neue Menge ist identisch mit der aktuellen Menge.
* Der Status des SaaS-Abonnements ist nicht „Abonniert“.
* Der Aktualisierungsvorgang für ein SaaS-Abonnement ist nicht in `allowedCustomerOperations` enthalten.

Code: 403 – Unzulässig.  Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben.  Die Anforderung versucht, auf ein Abonnement zuzugreifen, das nicht dem aktuellen Herausgeber gehört.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird. 

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

>[!Note]
>Es kann jeweils nur ein Plan oder eine Menge geändert werden, nicht beides gleichzeitig.

>[!Note]
>Diese API kann nur nach ausdrücklicher Genehmigung der Änderung durch den Endbenutzer aufgerufen werden.

## <a name="cancel-a-subscription"></a>Kündigen eines Abonnements

Diese API dient zum Kündigen eines angegebenen SaaS-Abonnements.  Der Herausgeber muss diese API nicht verwenden, und es wird empfohlen, die Kunden zum Kündigen von SaaS-Abonnements an den kommerziellen Marketplace weiterzuleiten.

Wenn der Herausgeber beschließt, die Kündigung von im kommerziellen Marketplace erworbenen SaaS-Abonnements auf seiner Website zu implementieren, muss er diese API aufrufen.  Nach Abschluss dieses Aufrufs lautet der Status des Abonnements auf Microsoft-Seite *Gekündigt*.

Wenn ein Abonnement innerhalb der folgenden Toleranzperiode gekündigt wird, wird es dem Kunden nicht in Rechnung gestellt:

* Bei einem Monatsabonnement 24 Stunden nach Aktivierung
* Bei einem Jahresabonnement 14 Tage nach Aktivierung

Bei Kündigung eines Abonnements nach Ablauf der oben genannten Toleranzperioden wird das Abonnement dem Kunden in Rechnung gestellt.  Der Kunde verliert unmittelbar nach der Kündigung den Zugriff auf das SaaS-Abonnement auf Microsoft-Seite. 

### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Delete `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
|  `subscriptionId`     | Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde.  |

*Anforderungsheader:*
 
|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `x-ms-correlationid`  | Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Antwortcodes:*

Code: 202 – Die Anforderung zum Kündigen wurde akzeptiert und asynchron verarbeitet.  Vom Partner wird erwartet, dass er die **Standort-URL für den Vorgang** abfragt, um zu ermitteln, ob diese Anforderung erfolgreich war oder fehlgeschlagen ist.  Die Abfrage sollte alle paar Sekunden durchgeführt werden, bis der endgültige Status *Fehler*, *Erfolgreich* oder *Konflikt* für den Vorgang empfangen wird.  Der endgültige Vorgangsstatus sollte schnell zurückgegeben werden, doch kann dies in einigen Fällen mehrere Minuten dauern.

Der Partner erhält auch eine Webhookbenachrichtigung, wenn die Aktion auf Seite im kommerziellen Marketplace erfolgreich abgeschlossen wurde.  Erst dann sollte der Herausgeber das Abonnement auf Herausgeberseite kündigen.

*Antwortheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Link zu einer Ressource, um den Vorgangsstatus abzurufen.  Beispiel: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 – Ungültige Anforderung.  „Delete“ ist nicht in der Liste `allowedCustomerOperations` für dieses SaaS-Abonnement enthalten.

Code: 403 – Unzulässig.  Das Autorisierungstoken ist ungültig, abgelaufen oder nicht verfügbar. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler. Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="next-steps"></a>Nächste Schritte

Weitere Optionen für SaaS-Angebote im kommerziellen Marketplace finden Sie unter [APIs für getaktete Abrechnung im Marketplace](../marketplace-metering-service-apis.md).

Überprüfen und verwenden Sie die [Clients für verschiedene Programmiersprachen und Beispiele](https://github.com/microsoft/commercial-marketplace-samples).
