---
title: SaaS-Fulfillment-Vorgangs-APIs der Version 2 im kommerziellen Microsoft-Marketplace
description: Erfahren Sie, wie Sie die Vorgangs-APIs, die Teil der SaaS-Fulfillment-APIs der Version 2 sind, zum Verwalten eines SaaS-Angebots in Microsoft AppSource, im Azure Marketplace und im Azure-Portal verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 11/10/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: ef0e735206df6556bd7a7a7b2cf584bc79566a0f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063971"
---
# <a name="saas-fulfillment-operations-apis-v2-in-the-microsoft-commercial-marketplace"></a>SaaS-Fulfillment-Vorgangs-APIs der Version 2 im kommerziellen Microsoft-Marketplace

In diesem Artikel wird Version 2 der SaaS-Fulfillment-Vorgangs-APIs beschrieben.

## <a name="list-outstanding-operations"></a>Auflisten ausstehender Vorgänge

Ruft die Liste der ausstehenden Vorgänge für das angegebene SaaS-Abonnement ab. Zurückgegebene Vorgänge müssen vom Herausgeber bestätigt werden, indem die [API zum Patchen von Vorgängen](#update-the-status-of-an-operation) aufgerufen wird.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Verwenden Sie 2018-08-31.         |
|    `subscriptionId` | Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde.  |

*Anforderungsheader:*
 
|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Antwortcodes:*

Code: 200 – Gibt ausstehende Vorgänge für das angegebene SaaS-Abonnement zurück.

*Beispiel einer Antwortnutzlast:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Gibt eine leere JSON-Datei zurück, wenn keine Vorgänge ausstehen.

Code: 400 – Ungültige Anforderung: Fehler bei der Überprüfung.

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden.  Das SaaS-Abonnement mit `subscriptionId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler. Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="get-operation-status"></a>Abrufen des Vorgangsstatus

Diese API ermöglicht dem Herausgeber das Nachverfolgen des Status des angegebenen asynchronen Vorgangs:  **Unsubscribe** (Kündigen), **ChangePlan** (Plan ändern) oder **ChangeQuantity** (Menge ändern).

Die `operationId` für diesen API-Aufruf kann von dem Wert abgerufen werden, der von **Operation-Location** zurückgegeben wird, dem API-Aufruf zum Abrufen ausstehender Vorgänge oder dem vom Wert des Parameters `<id>`, der in einem Webhookaufruf empfangen wird.

### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Verwenden Sie 2018-08-31.  |
|  `subscriptionId`    |  Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde. |
|  `operationId`       |  Der eindeutige Bezeichner des Vorgangs, der gerade abgerufen wird. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt.  |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist.  |

*Antwortcodes:*

Code: 200 – Ruft Details für den angegebenen SaaS-Vorgang ab. 

*Beispiel einer Antwortnutzlast:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Code: 403 – Unzulässig. Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden.  

* Abonnement mit `subscriptionId` wurde nicht gefunden.
* Vorgang mit `operationId` wurde nicht gefunden.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="update-the-status-of-an-operation"></a>Aktualisieren des Status eines Vorgangs

Diese API dient zum Aktualisieren des Status eines ausstehenden Vorgangs, um anzugeben, ob der Vorgang auf Herausgeberseite erfolgreich oder fehlerhaft war.

Die `operationId` für diesen API-Aufruf kann von dem Wert abgerufen werden, der von **Operation-Location** zurückgegeben wird, dem API-Aufruf zum Abrufen ausstehender Vorgänge oder dem vom Wert des Parameters `<id>`, der in einem Webhookaufruf empfangen wird.

### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Abfrageparameter:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Verwenden Sie 2018-08-31.  |
|   `subscriptionId`   |  Der eindeutige Bezeichner des erworbenen SaaS-Abonnements.  Diese ID wird abgerufen, nachdem das Autorisierungstoken für den kommerziellen Marketplace mithilfe der Auflösungs-API aufgelöst wurde.  |
|   `operationId`      |  Der eindeutige Bezeichner des Vorgangs, der gerade abgeschlossen wird. |

*Anforderungsheader:*

|  Parameter         | Wert             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Ein eindeutiger Zeichenfolgenwert für die Nachverfolgung der Anforderung vom Client, vorzugsweise eine GUID.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|   `x-ms-correlationid` |  Ein eindeutiger Zeichenfolgenwert für den Vorgang auf dem Client.  Dieser Parameter korreliert alle Ereignisse des Clientvorgangs mit serverseitigen Ereignissen.  Wenn dieser Wert nicht angegeben wird, wird einer generiert und in den Antwortheadern bereitgestellt. |
|  `authorization`     |  Ein eindeutiges Zugriffstoken, das den Herausgeber identifiziert, der diesen API-Aufruf sendet.  Das Format ist `"Bearer <access_token>"`, wenn der Tokenwert vom Herausgeber abgerufen wird, wie es unter [Abrufen eines Tokens basierend auf der Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post) beschrieben ist. |

*Beispiel einer Anforderungsnutzlast:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Antwortcodes:*

Code: 200 – Ein Aufruf, um über den Abschluss eines Vorgangs auf der Seite des Partners zu informieren.  Diese Antwort kann z. B. den Abschluss einer Änderung der Arbeitsplätze oder Pläne auf Herausgeberseite anzeigen.

Code: 403
- Unzulässig.  Das Autorisierungstoken ist nicht verfügbar, ungültig oder abgelaufen. Die Anforderung versucht möglicherweise, auf ein Abonnement zuzugreifen, das nicht dem aktuellen Herausgeber gehört.
- Unzulässig.  Das Autorisierungstoken ist ungültig, abgelaufen oder wurde nicht angegeben. Die Anforderung versucht, auf ein SaaS-Abonnement für ein Angebot zuzugreifen, das mit einer anderen Azure AD-App-ID veröffentlicht ist als der, die zum Erstellen des Autorisierungstokens verwendet wurde.

Dieser Fehler ist häufig ein Symptom dafür, dass die [SaaS-Registrierung](pc-saas-registration.md) nicht ordnungsgemäß ausgeführt wird.

Code: 404 – Nicht gefunden.

* Abonnement mit `subscriptionId` wurde nicht gefunden.
* Vorgang mit `operationId` wurde nicht gefunden.

Code: 409 – Konflikt.  Es wurde z. B. bereits eine neuere Transaktion abgeschlossen.

Code: 500 – Interner Serverfehler.  Wiederholen Sie den API-Aufruf.  Wenn der Fehler weiterhin auftritt, wenden Sie sich an den [Microsoft-Support](https://go.microsoft.com/fwlink/?linkid=2165533).

## <a name="next-steps"></a>Nächste Schritte

Weitere Optionen für SaaS-Angebote im kommerziellen Marketplace finden Sie unter [APIs für getaktete Abrechnung im Marketplace](../marketplace-metering-service-apis.md).

Überprüfen und verwenden Sie die [Clients für verschiedene Programmiersprachen und Beispiele](https://github.com/microsoft/commercial-marketplace-samples).
