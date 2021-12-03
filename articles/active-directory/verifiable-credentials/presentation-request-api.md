---
title: Angeben der Überprüfungsanforderung der Anforderungsdienst-REST-API (Vorschau)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Informationen zum Starten einer Präsentationsanforderung im Nachweisdienst
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: c670f060e7849f844997c0feefd60229b90e5202
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474001"
---
# <a name="request-service-rest-api-presentation-specification-preview"></a>Spezifikation der Anforderungsdienst-REST-API-Präsentation (Vorschau)

Der Azure Active Directory-Nachweisdienst enthält die Anforderungsdienst-REST-API. Mit dieser API können Sie Nachweise (Verifiable Credentials) ausstellen und überprüfen. In diesem Artikel wird die Anforderungsdienst-REST-API für eine Präsentationsanforderung beschrieben. Die Präsentationsanforderung fordert den Benutzer auf, einen Nachweis zu präsentieren und diesen dann zu überprüfen.

## <a name="http-request"></a>HTTP-Anforderung

Die Präsentationsanforderung der Anforderungsdienst-REST-API unterstützt die folgende HTTP-Methode:

| Methode |Hinweise  |
|---------|---------|
|POST | Mit JSON-Nutzdaten, wie in diesem Abschnitt beschrieben. |

Die Präsentationsanforderung der Anforderungsdienst-REST-API erfordert die folgenden HTTP-Header:

| Methode |Wert  |
|---------|---------|
|`Authorization`| Fügen Sie das Zugriffstoken als Bearertoken an den Autorisierungsheader einer HTTP-Anforderung an. Beispiel: `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Erstellen Sie eine HTTP POST-Anforderung für die Anforderungsdienst-REST-API. Ersetzen Sie die `{tenantID}` durch Ihre Mandanten-ID oder durch den Namen Ihres Mandanten.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

Die folgende HTTP-Anforderung veranschaulicht eine Präsentationsanforderung an die Anforderungsdienst-REST-API:

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{  
    "includeQRCode": true,  
    "callback": {  
    "url": "https://www.contoso.com/api/verifier/presentationCallbac",  
    "state": "11111111-2222-2222-2222-333333333333",  
      "headers": {  
        "api-key": "an-api-key-can-go-here"  
      }  
    },  
    ...
} 
```  

Zum Aufrufen der Anforderungsdienst-REST-API ist die folgende Berechtigung erforderlich. Weitere Informationen finden Sie unter [Erteilen von Berechtigungen zum Abrufen von Zugriffstoken](verifiable-credentials-configure-tenant.md#grant-permissions-to-get-access-tokens).

| Berechtigungstyp | Berechtigung  |
|---------|---------|
| Application | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="presentation-request-payload"></a>Nutzdaten der Präsentationsanforderung

Die Nutzdaten der Präsentationsanforderung enthalten Informationen zu Ihrer Nachweispräsentationsanforderung. Im folgenden Beispiel wird eine Präsentationsanforderung von einem bestimmten Aussteller dargestellt. Das Ergebnis dieser Anforderung gibt einen QR-Code mit einem Link zum Starten des Präsentationsprozesses zurück.

```json
{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

Die Nutzdaten enthalten die folgenden Eigenschaften.  

|Parameter |type  | Beschreibung |
|---------|---------|---------|
| `includeQRCode` |  Boolesch |   Legt fest, ob ein QR-Code in der Antwort auf diese Anforderung enthalten ist. Präsentieren Sie den QR-Code, und fordern Sie den Benutzer auf, ihn zu scannen. Durch das Scannen des QR-Codes wird die Authentifikator-App mit dieser Präsentationsanforderung gestartet. Mögliche Werte sind: `true` (Standard) oder `false`. Wenn der Parameter auf `false` festgelegt ist, verwenden Sie die `url`-Rückgabeeigenschaft, um einen Deep-Link zu rendern.  |
| `authority` | string|  Ihr dezentralisierter Bezeichner (DID) Ihres Azure AD-Prüfungsmandanten. Weitere Informationen finden Sie unter [Erfassen von Mandantendetails zum Einrichten Ihrer Beispielanwendung](verifiable-credentials-configure-verifier.md#gather-tenant-details-to-set-up-your-sample-application).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Stellt Informationen zum Prüfer bereit. |
| `presentation` | [RequestPresentation](#requestpresentation-type)| Stellt Informationen zur Nachweispräsentationsanforderung bereit.  |
|`callback`|  [Callback](#callback-type)| Ermöglicht dem Entwickler, die Benutzeroberfläche während des Nachweispräsentationsprozesses zu aktualisieren. Wenn der Benutzer den Prozess abgeschlossen hat, setzen Sie den Prozess fort, nachdem die Ergebnisse an die Anwendung zurückgegeben werden.|

### <a name="requestregistration-type"></a>RequestRegistration-Typ

Der Typ `RequestRegistration` stellt Informationen zur Registrierung für den Aussteller bereit. Die Typ `RequestRegistration` enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `clientName` | Zeichenfolge|  Ein Anzeigename des Ausstellers des Nachweises. Dieser Name wird dem Benutzer in der Authentifikator-App angezeigt. |

Der folgende Screenshot zeigt die `clientName`-Eigenschaft und den Anzeigenamen der `authority` (des Prüfers) in der Präsentationsanforderung.

![Screenshot: Genehmigen der Präsentationsanforderung](media/presentation-request-api/approve-presentation-request.jpg)

### <a name="requestpresentation-type"></a>Typ „RequestPresentation“

Der Typ `RequestPresentation` liefert Informationen, die für die Präsentation von Nachweisen erforderlich sind. `RequestPresentation` enthält die folgenden Eigenschaften:

|Eigenschaft |type |Beschreibung |
|---------|---------|---------|
| `includeReceipt` |  Boolesch | Legt fest, ob ein Beleg in der Antwort dieser Anforderung enthalten sein soll. Mögliche Werte sind `true` oder `false` (Standardwert). Der Beleg enthält die ursprünglichen Nutzdaten, die vom Authentifikator an den Nachweisdienst gesendet wurden. Der Beleg ist für die Problembehandlung nützlich und sollte nicht standardmäßig festgelegt werden. In der `OpenId Connect SIOP`-Anforderung enthält der Beleg das ID-Token aus der ursprünglichen Anforderung. |
| `requestedCredentials` | collection| Eine Sammlung von [RequestCredential](#requestcredential-type)-Objekten.|

### <a name="requestcredential-type"></a>Typ „RequestCredential“

Der Typ `RequestCredential` stellt Informationen zu den angeforderten Anmeldeinformationen bereit, die der Benutzer angeben muss. `RequestCredential` enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `type`| Zeichenfolge| Der Typ des Nachweises. Der `type` muss dem Typ entsprechen, der im `issuer`-Nachweismanifest definiert ist (z. B. `VerifiedCredentialExpert`). Um das Ausstellermanifest zu erhalten, lesen Sie [Erfassen von Nachweisen und Umgebungsdetails zum Einrichten Ihrer Beispielanwendung](verifiable-credentials-configure-issuer.md). Kopieren Sie die **Ausstelleranmeldeinformationen-URL**, öffnen Sie die URL in einem Webbrowser und überprüfen Sie die **ID**-Eigenschaft. |
| `purpose`| Zeichenfolge | Geben Sie Informationen zum Zweck der Anforderung dieses Nachweises an. |
| `acceptedIssuers`| Zeichenfolgensammlung | Eine Sammlung von Aussteller-DIDs, die den Nachweistyp ausstellen könnten, der von Antragstellern präsentiert wird. Um Ihren Aussteller-DID zu erhalten, lesen Sie [Erfassen von Nachweisen und Umgebungsdetails zum Einrichten Ihrer Beispielanwendung](verifiable-credentials-configure-issuer.md), und kopieren Sie den Wert des **dezentralisierten Bezeichners (Decentralized Identifier, DID)** . |

### <a name="callback-type"></a>Rückruftyp

Die Anforderungsdienst-REST-API generiert mehrere Ereignisse für den Rückrufendpunkt. Mit diesen Ereignissen können Sie die Benutzeroberfläche aktualisieren und den Prozess fortsetzen, nachdem die Ergebnisse an die Anwendung zurückgegeben werden. Die Typ `Callback` enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `url` | Zeichenfolge| URI zum Rückrufendpunkt Ihrer Anwendung. |
| `state` | Zeichenfolge| Assoziiert mit dem in den ursprünglichen Nutzdaten übergebenen Zustand. |
| `headers` | Zeichenfolge| Optional. Sie können eine Sammlung von HTTP-Headern einfügen, die von der empfangenden Seite der POST-Nachricht benötigt werden. Die Header dürfen nur den `api-key` oder einen für die Autorisierung erforderlichen Header enthalten.|

## <a name="successful-response"></a>Erfolgreiche Antwort

Bei Erfolg gibt diese Methode einen *HTTP 201 Created*-Antwortcode und eine Sammlung von Ereignisobjekten im Antworttext zurück. Im folgenden JSON-Beispiel wird eine erfolgreiche Antwort dargestellt:

```json
{  
    "requestId": "e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "url": "openid://vc/?request_uri=https://beta.did.msidentity.com/v1.0/87654321-0000-0000-0000-000000000000/verifiablecredentials/request/e4ef27ca-eb8c-4b63-823b-3b95140eac11",
    "expiry": 1633017751,
    "qrCode": "data:image/png;base64,iVBORw0KGgoA<SNIP>"
} 
```

Die Antwort enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `requestId`| string | Automatisch generierte Korrelations-ID. Die [Rückruffunktion (Callback)](#callback-events) verwendet dieselbe Anforderung, sodass Sie die Präsentationsanforderung und ihre Rückruffunktionen nachverfolgen können. |
| `url`|  Zeichenfolge| Eine URL, die zuerst die Authentifikator-App und dann den Präsentationsprozess startet. Sie können dem Benutzer diese URL präsentieren, wenn er den QR-Code nicht scannen kann. |
| `expiry`| integer| Gibt an, wann die Antwort abläuft. |
| `qrCode`| string | Ein QR-Code, den der Benutzer scannen kann, um den Präsentationsflow zu starten. |

Wenn Ihre App die Antwort erhält, muss sie dem Benutzer den QR-Code präsentieren. Der Benutzer scannt den QR-Code, wodurch die Authentifikator-App geöffnet wird und der Präsentationsprozess gestartet wird.

## <a name="error-response"></a>Fehlerantwort

Auch Fehlerantworten können zurückgegeben werden, damit die Anwendung sie entsprechend behandeln kann. Im folgenden JSON-Beispiel wird eine nicht autorisierte Fehlermeldung dargestellt:


```json
{
    "requestId": "fb888ac646c96083de83b099b2678de0",
    "date": "Wed, 29 Sep 2021 21:49:00 GMT",
    "error": {
        "code": "unauthorized",
        "message": "Failed to authenticate the request."
    }
}
```

Die Antwort enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `requestId`| string | Automatisch generierte Anforderungs-ID.|
| `date`| date | Der Zeitpunkt des Fehlers. |
| `error.code` | Zeichenfolge | Der zurückgegebene Fehlercode. |
| `error.message`| Zeichenfolge | Die Fehlermeldung. |

## <a name="callback-events"></a>Callback-Ereignisse

Der Callback-Endpunkt wird aufgerufen, wenn ein Benutzer den QR-Code scannt, den Deep Link seiner Authentifikator-App verwendet oder den Präsentationsprozess abschließt. 

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `requestId`| Zeichenfolge | Wird der ursprünglichen Anforderung zugeordnet, wenn die Nutzdaten an den Nachweisdienst gesendet wurden.|
| `code` |Zeichenfolge |Der Code, der zurückgegeben wird, wenn die Anforderung von der Authentifikator-App abgerufen wurde. Mögliche Werte: <ul><li>`request_retrieved`: Der Benutzer hat den QR-Code gescannt oder den Link ausgewählt, der den Präsentationsflow startet.</li><li>`presentation_verified`: Die Überprüfung der Nachweise wurde erfolgreich abgeschlossen.</li></ul>    |
| `state` |string| Gibt den Statuswert zurück, den Sie in den ursprünglichen Nutzdaten übergeben haben.   |
| `subject`|Zeichenfolge | Der Benutzer-DID des Nachweises.|
| `issuers`| array |Gibt ein Array von angeforderten Nachweisen zurück. Für jeden Nachweis wird Folgendes angegeben: </li><li>Der Typ des Nachweises.</li><li>Die abgerufenen Ansprüche.</li><li>Die Domäne des Nachweisausstellers. </li><li>Der Überprüfungsstatus der Domäne des Nachweisausstellers. </li></ul> |
| `receipt`| Zeichenfolge | Optional. Der Beleg enthält die ursprünglichen Nutzdaten, die vom Authentifikator an den Nachweisdienst gesendet wurden.  |

Das folgende Beispiel demonstriert Callback-Nutzdaten, wenn die Präsentationsanforderung durch die Authentifikator-App gestartet wird:

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

Das folgende Beispiel demonstriert Callback-Nutzdaten, nachdem die Präsentation der Nachweise erfolgreich abgeschlossen wurde.

```json
{
  "requestId": "87e1cb24-9096-409f-81cb-9e645f23a4ba",
  "code": "presentation_verified",
  "state": "f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
  "subject": "did:ion:EiAlrenrtD3Lsw0GlbzS1O2YFdy3Xtu8yo35W<SNIP>…",
  "issuers": [
    {
      "type": [
        "VerifiableCredential",
        "VerifiedCredentialExpert"
      ],
      "claims": {
        "firstName": "John",
        "lastName": "Doe"
      },
      "domain": "https://contoso.com/",
      "verified": "DNS",
      "issuer": "did:ion:….."
    }
  ],
  "receipt": {
    "id_token": "eyJraWQiOiJkaWQ6aW<SNIP>"
  }
} 
```

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Informationen zum [Aufrufen der Anforderungsdienst-REST-API](get-started-request-api.md).
