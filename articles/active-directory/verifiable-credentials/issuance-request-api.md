---
title: Angeben der Ausstellungsanforderung für die Anforderungsdienst-REST-API (Vorschau)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Erfahren Sie, wie Sie einen von Ihnen ausgestellten Nachweis (Verifiable Credential) ausstellen können.
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 40515c37de50379f84475ad5474fb54fbbfa5f0d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440524"
---
# <a name="request-service-rest-api-issuance-specification-preview"></a>Spezifikation der Ausstellung für die Anforderungsdienst-REST-API (Vorschau)

Das Azure Active Directory (Azure AD) Nachweise (Verifiable Credentials bzw. überprüfbare Anmeldeinformationen) enthalten die Anforderungs-Dienste-REST-API. Mit dieser API können Sie Anmeldeinformationen ausstellen und überprüfen. In diesem Abschnitt wird die Anforderungs-Dienst-REST-API für eine Anforderung der Ausstellung beschrieben.

## <a name="http-request"></a>HTTP-Anforderung

Die Anforderung des Dienste-REST-API unterstützt die folgende HTTP-Methode:

| Methode |Hinweise  |
|---------|---------|
|POST | Mit JSON-Nutzdaten, wie in diesem Abschnitt beschrieben. |

Für die Anforderung des Dienste-REST-API sind folgende HTTP-Header erforderlich:

| Methode |Wert  |
|---------|---------|
|`Authorization`| Fügen Sie das Zugriffstoken als Bearertoken an den Autorisierungsheader einer HTTP-Anforderung an. Beispiel: `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Erstellen Sie eine HTTP POST-Anforderung für die Anforderungsdienst-REST-API. Ersetzen Sie die `{tenantID}` durch Ihre Mandanten-ID oder durch den Mandantennamen.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

Die folgende HTTP-Anforderung demonstriert eine Anforderung an die Anforderungs-Dienst-REST-API:

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true, 
        "callback": {  
            "url": "https://wwww.contoso.com/vc/callback",  
            "state": "Aaaabbbb11112222", 
            "headers": { 
                "api-key": "an-api-key-can-go-here" 
              }  
        }, 
    ... 
}
```  

Für die Anforderung der Dienste-REST-API ist die folgende Berechtigung erforderlich. Weitere Informationen finden Sie unter [Erteilen von Berechtigungen zum Abrufen von Zugriffstoken](verifiable-credentials-configure-tenant.md#grant-permissions-to-get-access-tokens).

| Berechtigungstyp | Berechtigung  |
|---------|---------|
| Application | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="issuance-request-payload"></a>Nutzdaten der Anforderung zur Ausstellung

Die Nutzdaten der Ausgabeanforderung enthalten Informationen über Ihre Anforderung zur Ausgabe von überprüfbaren Berechtigungsnachweisen. Das folgende Beispiel veranschaulicht eine Anforderung zur Ausstellung unter Verwendung des PIN-Code-Flusses mit Benutzerangaben wie Vor- und Nachname. Das Ergebnis dieser Anforderung ist ein QR-Code mit einem Link zum Starten des Ausstellungsverfahrens.

```json
{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```

Die Nutzdaten enthalten die folgenden Eigenschaften:  

|Parameter |type  | Beschreibung |
|---------|---------|---------|
| `includeQRCode` |  Boolesch |   Legt fest, ob ein QR-Code in der Antwort auf diese Anforderung enthalten ist. Präsentieren Sie den QR-Code und fordern Sie den Benutzer auf, ihn zu scannen. Durch das Scannen des QR-Codes wird die Authentifizierungs-App mit dieser Anforderung gestartet. Mögliche Werte sind: `true` (Standard) oder `false`. Wenn der Parameter auf `false` festgelegt ist, verwenden Sie die `url`Rückgabeeigenschaft, um einen Deep Link zu rendern.  |
| `authority` | Zeichenfolge|  Der dezentrale Identifikation (DID) des Ausstellers. Weitere Informationen finden Sie unter [Sammeln Sie Anmeldeinformationen und Umgebungsdetails, um Ihre Beispielanwendung einzurichten](verifiable-credentials-configure-issuer.md).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Liefert Informationen über den Aussteller, die in der Authentifikator-App angezeigt werden können. |
| `issuance` | [RequestIssuance](#requestissuance-type)| Liefert Informationen über die Anforderung der Ausstellung.  |
|`callback`|  [Rückruf](#callback-type)| Ermöglicht es dem Entwickler, asynchron Informationen über den Ablauf während des Prozesses der Ausstellung von überprüfbaren Berechtigungsnachweisen zu erhalten. Der Entwickler möchte beispielsweise einen Anruf, wenn der Benutzer den QR-Code gescannt hat.|

### <a name="requestregistration-type"></a>RequestRegistration-Typ

Der `RequestRegistration` Typ stellt Informationen zur Registrierung für den Aussteller bereit. Die `RequestRegistration` Typ enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `clientName` | Zeichenfolge|  Ein Anzeigename des Ausstellers des Nachweises.  |
| `logoUrl` |  Zeichenfolge |  Optional. Die URL für das Logo des Ausstellers.  |
| `termsOfServiceUrl` |  Zeichenfolge | Optional. Die URL für die Nutzungsbedingungen für den von Ihnen ausgestellten Nachweis (Verifiable Credential).  |

> [!NOTE]
> Derzeit werden die `RequestRegistration` Informationen nicht während der Ausstellung in der Microsoft Authentifizierungs-App angezeigt. Diese Informationen können jedoch in der Nutzlast verwendet werden.

### <a name="requestissuance-type"></a>RequestIssuance-Typ

Der `RequestIssuance` Typ liefert Informationen, die für die Ausstellung von Berechtigungsnachweisen (Verifiable Credential) erforderlich sind. Derzeit gibt es drei Eingabetypen, die Sie in `RequestIssuance` senden können. Diese Typen werden vom Azure AD Verifiable Credentials für die Ausstellung von prüfbaren Berechtigungsnachweisen verwendet (Verifiable Credentials), um Angaben in einen Nachweis (Verifiable Credential) einzufügen und diese Informationen mit der DID des Ausstellers zu bescheinigen. Im Folgenden sind die drei Typen aufgeführt:

- ID-Token
- Nachweise (Verifiable Credentials) per verifizierbarer Bereitstellung
- Selbst bestätigte Ansprüche

Ausführliche Informationen zu den Eingabetypen finden Sie im Abschnitt [Anpassen Ihres Nachweises (Verifiable Credential)](credential-design.md). 

Die `RequestIssuance` Typ enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `type` |  Zeichenfolge |  Der Typ des Nachweises. Sollte dem Typ entsprechen, der im Manifest des Nachweises definiert ist. Beispiel: `VerifiedCredentialExpert`. Weitere Informationen finden Sie unter [Erstellen der Expertenkarte für den Nachweis (Verified Credential) in Azure](verifiable-credentials-configure-issuer.md). |
| `manifest` | string| Die URL des Manifest-Dokuments des Nachweises (Verified Credential). Weitere Informationen finden Sie unter [Sammeln Sie Anmeldeinformationen und Umgebungsdetails, um Ihre Beispielanwendung einzurichten](verifiable-credentials-configure-issuer.md).|
| `claims` | Zeichenfolge| Optional. Fügen Sie eine Sammlung von Assertions (Annahmen) über das Subjekt in den Nachweis (Verifiable Credential) ein. Für den PIN-Code-Fluss ist es wichtig, dass Sie den Vor- und Nachnamen des Benutzers angeben. Weitere Informationen finden Sie unter [Namen der Nachweise (Verifiable Credentials)](verifiable-credentials-configure-issuer.md#verifiable-credential-names). |
| `pin` | [PIN](#pin-type)| Optional. Eine PIN-Nummer, die zusätzliche Sicherheit bei der Ausstellung bietet. Für den PIN-Code-Fluss ist diese Eigenschaft erforderlich. Sie generieren einen PIN-Code und zeigen ihn dem Benutzer in Ihrer Anwendung an. Der Benutzer muss den von Ihnen generierten PIN-Code angeben. |

### <a name="pin-type"></a>PIN-Typ

Der `pin` Typ definiert einen PIN-Code, der als Teil der Ausstellung angezeigt werden kann. `pin` ist optional und sollte bei Verwendung immer bandextern (out-of-band) gesendet werden. Wenn Sie einen HASH-PIN-Code verwenden, müssen Sie die Eigenschaften `salt`, `alg` und `iterations` definieren. `pin` enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `value` | Zeichenfolge| Enthält den PIN-Wert in Klartext. Bei Verwendung einer gehashten PINs enthält die Eigenschaft Wert den Salted Hash, base64-kodiert.|
| `type` | Zeichenfolge|  Der Typ des PIN-Codes. Möglicher Wert: `numeric` (Standard). |
| `length` | integer|  Die Länge des PIN-Codes. Die Standardlänge ist 6, der Mindeswert ist 4 und der Höchstwert ist 16.|
| `salt` | string|  Der Salt-Wert des Hash-PIN-Codes. Der Salt wird bei der Hash-Berechnung vorangestellt. Codierung: UTF-8. |
| `alg` | Zeichenfolge|  Der Hashing-Algorithmus für die gehashte PIN. Unterstützter Algorithmus: `sha256`. |
| `iterations` | integer| Die Anzahl der Hashing-Iterationen. Möglicher Wert: `1`.|

### <a name="callback-type"></a>Rückruftyp

Die Anforderungsdienst-REST-API generiert mehrere Ereignisse für den Rückrufendpunkt. Mit diesen Ereignissen können Sie die Benutzeroberfläche aktualisieren und den Prozess fortsetzen, sobald die Ergebnisse an die Anwendung zurückgegeben werden. Die `Callback` Typ enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `url` | Zeichenfolge| URI zum Rückrufendpunkt Ihrer Anwendung. |
| `state` | Zeichenfolge| Assoziiert mit dem in den ursprünglichen Nutzdaten übergebenen Zustand. |
| `headers` | Zeichenfolge| Optional. Sie können eine Sammlung von HTTP-Headern einfügen, die von der empfangenden Seite der POST-Nachricht benötigt werden. Die Header dürfen nur den `api-key` oder einen für die Autorisierung erforderlichen Header enthalten.|

## <a name="successful-response"></a>Erfolgreiche Antwort

Bei Erfolg gibt diese Methode einen *HTTP 201 Created*-Antwortcode und eine Sammlung von Ereignisobjekten im Antworttext zurück. Im folgenden JSON-Beispiel wird eine erfolgreiche Antwort dargestellt:

```json
{  
    "requestId": :"799f23ea-5241-45af-99ad-cf8e5018814e",  
    "url": "openid://vc?request_uri=https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiablecredentials/request/178319f7-20be-4945-80fb-7d52d47ae82e",  
    "expiry": 1622227690,  
    "qrCode": "data:image/png;base64,iVBORw0KggoA<SNIP>"  
} 
```

Die Antwort enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `requestId`| Zeichenfolge | Automatisch generierte Korrelations-ID. Die [Rückruffunktion (Callback)](#callback-events) verwendet dieselbe Anforderung, sodass Sie die Ausstellungsanforderung und ihre Rückruffunktionen nachverfolgen können. |
| `url`|  Zeichenfolge| Eine URL, mit der die Authenticator-App gestartet wird und der Ausstellungsprozess beginnt. Sie können dem Benutzer diese URL präsentieren, wenn er den QR-Code nicht scannen kann. |
| `expiry`| integer| Gibt an, wann die Antwort abläuft. |
| `qrCode`| Zeichenfolge | Ein QR-Code, den der Benutzer scannen kann, um den Ausgabestrom zu starten. |

Wenn Ihre App die Antwort erhält, muss sie dem Benutzer den QR-Code präsentieren. Der Benutzer scannt den QR-Code, wodurch die Authentifizierungs-App geöffnet wird und der Ausstellungsprozess beginnt.

## <a name="error-response"></a>Fehlerantwort

Auch Fehlerantworten können zurückgegeben werden, damit die Anwendung sie entsprechend behandeln kann. Im folgenden JSON-Beispiel wird eine nicht autorisierte Fehlermeldung dargestellt:


```json
{
    "requestId": "d60b068e7fbd975896e179b99347866a",
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
| `date`| date| Der Zeitpunkt des Fehlers. |
| `error.code` | Zeichenfolge| Der zurückgegebene Fehlercode. |
| `error.message`| Zeichenfolge| Die Fehlermeldung. |

## <a name="callback-events"></a>Callback-Ereignisse

Der Callback-Endpunkt wird aufgerufen, wenn ein Benutzer den QR-Code scannt, den Deep Link seiner Authentifizierungs-App verwendet oder den Ausstellungsprozess abschließt. 

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `requestId`| Zeichenfolge | Wird der ursprünglichen Anforderung zugeordnet, wenn die Nutzdaten an den Nachweisdienst (Verifiable-Credentials-Dienst) gesendet wurden.|
| `code` |Zeichenfolge |Der Code, der zurückgegeben wird, wenn die Anforderung von der Authentifikator-App abgerufen wurde. Mögliche Werte: <ul><li>`request_retrieved`: Der Benutzer hat den QR-Code gescannt oder auf den Link geklickt, der den Ausstellungsablauf startet.</li><li>`issuance_successful`: Die Ausstellung der Nachweise (Verifiable Credentials) war erfolgreich.</li><li>`Issuance_error`: Bei der Ausstellung ist ein Fehler aufgetreten. Ausführliche Informationen finden Sie unter der `error`-Eigenschaft.</li></ul>    |
| `state` |Zeichenfolge| Gibt den Statuswert zurück, den Sie in den ursprünglichen Nutzdaten übergeben haben.   |
| `error`| error | Wenn der `code` Eigenschaftswert `Issuance_error` ist, enthält diese Eigenschaft Informationen über den Fehler.| 
| `error.code` | Zeichenfolge| Der zurückgegebene Fehlercode. |
| `error.message`| Zeichenfolge| Die Fehlermeldung. |

Das folgende Beispiel demonstriert Callback-Nutzdaten, wenn die Anforderung zur Ausstellung durch die Authentifizierungs-App gestartet wird:

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

Das folgende Beispiel zeigt Callback-Nutzdaten nach erfolgreichem Abschluss des Ausstellungsprozesses durch den Benutzer:

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",
    "code":"issuance_successful",
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
} 
```

### <a name="callback-errors"></a>Callback-Fehler  

Der Callback-Endpunkt kann mit einer Fehlermeldung aufgerufen werden. Die folgende Tabelle listet die Fehlercodes auf:

|Nachricht  |Definition    |
|---------|---------|
| `fetch_contract_error*`| Der Vertrag über den Nachweis kann nicht abgerufen werden. Dieser Fehler tritt normalerweise auf, wenn die API das Manifest nicht abrufen kann, das in der Nutzlast der Anforderungsdaten [RequestIssuance object (Ausstellungsobjekt anfordern)](#requestissuance-type) von Ihnen angegeben wurde.|
| `issuance_service_error*` | Der Dienst Nachweise (Verifiable Credentials) kann die Anforderungen nicht überprüfen oder es ist ein Fehler in den Nachweisen (Verifiable Credentials) aufgetreten.|
| `unspecified_error`| Dieser Fehler ist ungewöhnlich, aber es lohnt sich, ihn zu untersuchen. |

Das folgende Beispiel zeigt Callback-Nutzdaten, wenn ein Fehler aufgetreten ist:

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",  
    "code": "issuance_error",  
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",  
    "error": { 
      "code":"IssuanceFlowFailed", 
      "message":"issuance_service_error”, 
    } 
} 
``` 

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die Informationen zum [Aufrufen der Anforderungsdienst-REST-API](get-started-request-api.md).
