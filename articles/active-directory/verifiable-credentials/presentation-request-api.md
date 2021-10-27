---
title: Angeben der Überprüfungsanforderung der Anforderungsdienst-REST-API (Vorschau)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Informationen zum Starten einer Präsentationsanforderung für Nachweise
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 30da74a6d94f2460a980737670d65442c0ddb3ac
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984400"
---
# <a name="request-service-rest-api-presentation-specification-preview"></a>Spezifikation der Anforderungsdienst-REST-API-Präsentation (Vorschau)

Mit der Anforderungsdienst-REST-API für Nachweise von Azure Active Directory (Azure AD) können Sie einen Nachweis ausstellen und überprüfen. In diesem Artikel wird die Anforderungsdienst-REST-API für eine Präsentationsanforderung beschrieben. Die Präsentationsanforderung fordert den Benutzer auf, einen Nachweis zu präsentieren und dann die Anmeldeinformationen zu überprüfen.


## <a name="http-request"></a>HTTP-Anforderung

Die Präsentationsanforderung der Anforderungsdienst-REST-API unterstützt die folgende HTTP-Methode:

| Methode |Hinweise  |
|---------|---------|
|POST | Mit JSON-Nutzdaten, wie in diesem Artikel beschrieben. |

Die Präsentationsanforderung der Anforderungsdienst-REST-API erfordert die folgenden HTTP-Header:

| Methode |Wert  |
|---------|---------|
|`Authorization`| Fügen Sie das Zugriffstoken als Bearertoken an den Autorisierungsheader einer HTTP-Anforderung an. Beispiel: `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Erstellen Sie eine HTTP POST-Anforderung für die Anforderungsdienst-REST-API. Ersetzen Sie die `{tenantID}` durch Ihre [Mandanten-ID](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application) oder durch den Namen Ihres Mandanten.

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

Zum Aufrufen der Anforderungsdienst-REST-API ist die folgende Berechtigung erforderlich. Weitere Informationen finden Sie unter [Erteilen von Berechtigungen zum Abrufen von Zugriffstoken](verifiable-credentials-configure-tenant.md#31-grant-permissions-to-get-access-tokens).

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

|Parameter |type  | BESCHREIBUNG |
|---------|---------|---------|
| `includeQRCode` |  boolean |   Legt fest, ob ein QR-Code in der Antwort auf diese Anforderung enthalten ist. Präsentieren Sie den QR-Code, und fordern Sie den Benutzer auf, ihn zu scannen. Durch das Scannen des QR-Codes wird die Authentifikator-App mit dieser Präsentationsanforderung gestartet. Mögliche Werte sind `true` (Standardwert) oder `false`. Wenn der Parameter auf `false` festgelegt ist, verwenden Sie die `url`-Rückgabeeigenschaft, um einen Deep Link zu rendern.  |
| `authority` | Zeichenfolge|  Ihr dezentralisierter Bezeichner Ihres Azure AD-Prüfungsmandanten. Weitere Informationen finden Sie unter [Erfassen von Mandantendetails zum Einrichten Ihrer Beispielanwendung](verifiable-credentials-configure-verifier.md#gather-tenant-details-to-set-up-your-sample-application).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Stellt Informationen zum Prüfer bereit. |
| `presentation` | [RequestPresentation](#requestpresentation-type)| Stellt Informationen zur Nachweispräsentationsanforderung bereit.  |
|`callback`|  [Callback](#callback-type)| Ermöglicht dem Entwickler, die Benutzeroberfläche während des Nachweispräsentationsprozesses zu aktualisieren. Wenn der Benutzer den Prozess abgeschlossen hat, setzen Sie den Prozess fort, sobald die Ergebnisse an die Anwendung zurückgegeben werden.|

### <a name="requestregistration-type"></a>Typ „RequestRegistration“

Der Typ „RequestRegistration“ stellt Informationen zur Registrierung für den Aussteller bereit. Der Typ „RequestRegistration“ enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `clientName` | Zeichenfolge|  Ein Anzeigename des Ausstellers des Nachweises. Dieser Name wird dem Benutzer in der Authentifikator-App angezeigt. |

Der folgende Screenshot zeigt die `clientName`-Eigenschaft und den Anzeigenamen der `authority` (des Prüfers) in der Präsentationsanforderung.

![Screenshot: Genehmigen der Präsentationsanforderung](media/presentation-request-api/approve-presentation-request.jpg)

### <a name="requestpresentation-type"></a>Typ „RequestPresentation“

Der Typ „RequestPresentation“ stellt Informationen bereit, die für die Nachweispräsentation erforderlich sind. Der Typ „RequestPresentation“ enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `includeReceipt` |  boolean | Legt fest, ob ein Beleg in der Antwort dieser Anforderung enthalten sein soll. Mögliche Werte sind `true` oder `false` (Standardwert). Der Beleg enthält die ursprünglichen Nutzdaten, die vom Authentifikator an den Nachweisdienst gesendet wurden.  Der Beleg ist für die Problembehandlung nützlich und sollte nicht standardmäßig festgelegt werden. In der OpenID Connect-SIOP-Anforderung enthält der Beleg das ID-Token aus der ursprünglichen Anforderung. |
| `requestedCredentials` | collection| Eine Sammlung von [RequestCredential](#requestcredential-type)-Objekten.|

### <a name="requestcredential-type"></a>Typ „RequestCredential“

Der Typ „RequestCredential“ stellt Informationen zu den angeforderten Anmeldeinformationen bereit, die der Benutzer angeben muss. Der Typ „RequestCredential“ enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `type`| Zeichenfolge| Der Typ des Nachweises. Der `type` muss dem Typ entsprechen, der im Nachweismanifest des **Ausstellers** definiert ist. Beispiel: `VerifiedCredentialExpert`. Um das Ausstellermanifest zu erhalten, folgen Sie den Anweisungen unter [Erfassen von Anmeldeinformationen und Umgebungsdetails zum Einrichten Ihrer Beispielanwendung](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application). Kopieren Sie die **Ausstelleranmeldeinformationen-URL**, öffnen Sie die URL in einem Webbrowser, und überprüfen Sie die **ID**-Eigenschaft. |
| `purpose`| Zeichenfolge | Geben Sie Informationen zum Zweck der Anforderung dieses Nachweises an. |
| `acceptedIssuers`| Zeichenfolgensammlung | Eine Sammlung von DIDs von Ausstellern, die den Typ des Nachweises ausstellen könnten, den Antragsteller präsentieren können. Um Ihren Aussteller-DID zu erhalten, folgen Sie den Anweisungen unter [Erfassen von Anmeldeinformationen und Umgebungsdetails zum Einrichten Ihrer Beispielanwendung](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application), und kopieren Sie den Wert des **dezentralisierten Bezeichners (Decentralized Identifier, DID)** . |


### <a name="callback-type"></a>Rückruftyp

Die Anforderungsdienst-REST-API generiert mehrere Ereignisse für den Rückrufendpunkt. Mit diesen Ereignissen können Sie die Benutzeroberfläche aktualisieren und den Prozess fortsetzen, sobald die Ergebnisse an die Anwendung zurückgegeben werden. Der Typ „Callback“ (Rückruf) enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `url` | Zeichenfolge| URI zum Rückrufendpunkt Ihrer Anwendung. |
| `state` | Zeichenfolge| Assoziiert mit dem in den ursprünglichen Nutzdaten übergebenen Zustand. |
| `headers` | Zeichenfolge| [Optional] Sie können eine Sammlung von HTTP-Headern einfügen, die von der empfangenden Seite der POST-Nachricht benötigt werden. Die Header dürfen nur den API-Schlüssel oder einen für die Autorisierung erforderlichen Header enthalten.|

## <a name="successful-response"></a>Erfolgreiche Antwort

Bei Erfolg gibt diese Methode einen „HTTP 201 Created“-Antwortcode und eine Sammlung von Ereignisobjekten im Antworttext zurück. Im folgenden JSON-Beispiel wird eine erfolgreiche Antwort dargestellt:

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
| `requestId`| Zeichenfolge | Automatisch generierte Korrelations-ID. Der [Rückruf](#callback-events) verwendet dieselbe Anforderung. Damit können Sie die Präsentationsanforderung und deren Rückrufe nachverfolgen. |
| `url`|  Zeichenfolge| Eine URL, die zuerst die Authentifikator-App und dann den Präsentationsprozess startet. Sie können dem Benutzer diese URL präsentieren, wenn er den QR-Code nicht scannen kann. |
| `expiry`| integer| Gibt an, wann die Antwort abläuft. |
| `qrCode`| Zeichenfolge | Ein QR-Code, den der Benutzer scannen kann, um den Präsentationsflow zu starten. |

Wenn Ihre App die Antwort erhält, muss sie dem Benutzer den QR-Code präsentieren. Der Benutzer scannt den QR-Code, wodurch die Authentifikator-App geöffnet wird und der Präsentationsprozess gestartet wird.

## <a name="error-response"></a>Fehlerantwort

Auch Fehlerantworten können zurückgegeben werden, damit die Anwendung sie entsprechend behandeln kann. Im folgenden JSON-Beispiel wird eine nicht autorisierte Fehlermeldung dargestellt.


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
| `requestId`| Zeichenfolge | Automatisch generierte Anforderungs-ID.|
| `date`| date| Der Zeitpunkt des Fehlers. |
| `error.code` | Zeichenfolge| Der zurückgegebene Fehlercode. |
| `error.message`| Zeichenfolge| Die Fehlermeldung. |

## <a name="callback-events"></a>Rückrufereignisse

Der Rückrufendpunkt wird aufgerufen, wenn ein Benutzer den QR-Code scannt, den Deep Link seiner Authentifikator-App verwendet oder den Präsentationsprozess abschließt. 

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `requestId`| Zeichenfolge | Wird der ursprünglichen Anforderung zugeordnet, wenn die Nutzdaten an den Nachweisdienst gesendet werden.|
| `code` |Zeichenfolge |Der Code, der zurückgegeben wird, wenn die Anforderung von der Authentifikator-App abgerufen wurde. Mögliche Werte: <ul><li>`request_retrieved`: Der Benutzer hat den QR-Code gescannt oder auf den Link geklickt, der den Präsentationsflow startet.</li><li>`presentation_verified`: Die Nachweisüberprüfung wurde erfolgreich abgeschlossen.</li></ul>    |
| `state` |Zeichenfolge| Der Status gibt den Statuswert zurück, den Sie in den ursprünglichen Nutzdaten übergeben haben.   |
| `subject`|Zeichenfolge | Der Benutzer-DID des Nachweises.|
| `issuers`| array |Gibt ein Array von angeforderten Nachweisen zurück. Für jeden Nachweis wird Folgendes angegeben: </li><li>Der Typ des Nachweises.</li><li>Die abgerufenen Ansprüche.</li><li>Die Domäne des Nachweisausstellers. </li><li>Der Überprüfungsstatus der Domäne des Nachweisausstellers. </li></ul> |
| `receipt`| Zeichenfolge | [Optional] Der Beleg enthält die ursprünglichen Nutzdaten, die vom Authentifikator an den Nachweisdienst gesendet wurden.  |

Im folgenden Beispiel sehen Sie die Rückrufnutzdaten, wenn die Präsentationsanforderung von der Authentifikator-App gestartet wird.

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

Im folgenden Beispiel sehen Sie die Rückrufnutzdaten, nachdem die Nachweispräsentation erfolgreich abgeschlossen wurde.

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
