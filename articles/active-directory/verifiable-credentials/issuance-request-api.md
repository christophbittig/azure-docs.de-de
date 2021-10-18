---
title: Geben Sie die Anforderung für den Dienste-REST-API-Ausgabe an
titleSuffix: Azure Active Directory Verifiable Credentials
description: Erfahren Sie, wie Sie eine von Ihnen ausgestellte Nachweis Bescheinigung ausstellen können
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 6b1a33ee563123d5fb724f0bc29c3e7c753a86ea
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811048"
---
# <a name="request-service-rest-api-issuance-specification-preview"></a>Spezifikation für die Ausgabe von Anforderungen an den Dienste-REST-API (Vorschau)

Azure Active Directory (Azure AD) verifizierbare Berechtigungsnachweise Anforderung Dienste-REST-API ermöglicht es Ihnen, einen verifizierbaren Berechtigungsnachweis auszustellen und zu überprüfen. In diesem Artikel wird die Dienste-REST-API für die Anforderung der Ausstellung von Dokumenten beschrieben.


## <a name="http-request"></a>HTTP-Anforderung

Die Anforderung des Dienste-REST-API unterstützt die folgende HTTP-Methode:

| Methode |Hinweise  |
|---------|---------|
|POST | Mit JSON-Nutzdaten, wie in diesem Artikel beschrieben. |

Für die Anforderung des Dienste-REST-API sind folgende HTTP-Header erforderlich:

| Methode |Wert  |
|---------|---------|
|`Authorization`| Fügen Sie das Zugriffstoken als Bearer-Token an den Authorization-Header einer HTTP-Anforderung an. Beispiel: `Authorization: Bearer <token>`.|
|`Content-Type`| `Application/json`|

Konstruieren Sie eine HTTP-POST-Anforderung an die Dienste-REST-API. Ersetzen Sie die `{tenantID}` durch Ihre [Mieter-ID](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application) oder Ihren Mieternamen.

```http
https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
```

Die folgende HTTP-Anforderung demonstriert eine HTTP-Anforderung an die Dienste-REST-API:

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

Für die Anforderung der Dienste-REST-API ist die folgende Berechtigung erforderlich. Weitere Informationen finden Sie unter [Erteilung von Berechtigungen zum Abrufen von Zugriffstokens](verifiable-credentials-configure-tenant.md#31-grant-permissions-to-get-access-tokens).

| Berechtigungstyp | Berechtigung  |
|---------|---------|
| Application | bbb94529-53a3-4be5-a069-7eaf2712b826/.default|

## <a name="issuance-request-payload"></a>Nutzdaten der Anforderung zur Ausstellung

Die Nutzdaten der Ausgabeanforderung enthalten Informationen über Ihre Anforderung zur Ausgabe von überprüfbaren Berechtigungsnachweisen. Das folgende Beispiel veranschaulicht eine Anforderung zur Ausstellung von Ausweisen unter Verwendung des PIN-Code-Flusses mit Benutzerangaben wie Vor- und Nachname. Das Ergebnis dieser Anforderung ist ein QR-Code mit einem Link zum Starten des Ausstellungsverfahrens.

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

Die Nutzlast enthält die folgenden Eigenschaften.  


|Parameter |type  | BESCHREIBUNG |
|---------|---------|---------|
| `includeQRCode` |  boolean |   Legt fest, ob ein QR-Code in der Antwort auf diese Anforderung enthalten ist. Präsentieren Sie den QR-Code und fordern Sie den Benutzer auf, ihn zu scannen. Durch das Scannen des QR-Codes wird die Authentifizierungs-App mit dieser Anforderung gestartet. Mögliche Werte `true` (Standard), oder `false`. Bei der Einstellung `false` wird die Eigenschaft return `url` verwendet, um den Deep Link darzustellen.  |
| `authority` | Zeichenfolge|  Der dezentrale Identifikator des Ausstellers. Weitere Informationen finden Sie unter [Sammeln Sie Anmeldeinformationen und Umgebungsdetails, um Ihre Beispielanwendung einzurichten](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application).|
| `registration` | [RequestRegistration](#requestregistration-type)|  Liefert Informationen über den Aussteller, die in der Authentifikator-App angezeigt werden können. |
| `issuance` | [RequestIssuance](#requestissuance-type)| Liefert Informationen über die Anforderung der Ausstellung.  |
|`callback`|  [Rückruf](#callback-type)| Ermöglicht es dem Entwickler, asynchron Informationen über den Ablauf während des Prozesses der Ausstellung von überprüfbaren Berechtigungsnachweisen zu erhalten. Zum Beispiel ein Aufruf, wenn der Benutzer den QR-Code gescannt hat.|

### <a name="requestregistration-type"></a>RequestRegistration-Typ

Der Typ RequestRegistration dient der Informationsregistrierung für den Ausgeber. Der RequestRegistration-Typ enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `clientName` | Zeichenfolge|  Ein Anzeigename des Ausstellers des Nachweises.  |
| `logoUrl` |  Zeichenfolge |  [Optional] Die URL für das Logo des Ausstellers.  |
| `termsOfServiceUrl` |  Zeichenfolge | [Optional] Die URL für die Nutzungsbedingungen für den von Ihnen ausgestellten Nachweis.  |

> [!NOTE]
> Derzeit werden die RequestRegistration-Informationen bei der Ausstellung in der Microsoft Authenticator-Anwendung nicht angezeigt, sie können jedoch in der Nutzlast verwendet werden.

### <a name="requestissuance-type"></a>RequestIssuance-Typ

Der RequestIssuance-Typ liefert Informationen, die für die überprüfbare Ausstellung von Berechtigungsnachweisen erforderlich sind. Derzeit gibt es drei Eingabetypen, die Sie in der RequestIssuance senden können. Diese Typen werden vom Dienst für die Ausstellung von prüfbaren Berechtigungsnachweisen verwendet, um Angaben in einen prüfbaren Berechtigungsnachweis einzufügen und diese Informationen mit der DID des Ausstellers zu bescheinigen. Im Folgenden sind die drei Typen aufgeführt:

- ID-Token
- Nachweise per verifizierbarer Bereitstellung
- Selbst bestätigte Ansprüche

Ausführliche Informationen zu den Eingabetypen finden Sie im Artikel [Anpassen des Nachweises](credential-design.md). 

Die RequestIssuance enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| type |  Zeichenfolge |  Der Typ des Nachweises. Sollte dem Typ entsprechen, der im Manifest des Nachweises definiert ist. Beispiel: `VerifiedCredentialExpert`. Weitere Informationen finden Sie unter [Erstellen der Expertenkarte für nachweisbare Berechtigungen in Azure](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application). |
| manifest | Zeichenfolge| URL des Dokuments zum Nachweis des Berechtigungsscheins. Weitere Informationen finden Sie unter [Sammeln Sie Anmeldeinformationen und Umgebungsdetails, um Ihre Beispielanwendung einzurichten](verifiable-credentials-configure-issuer.md#gather-credentials-and-environment-details-to-set-up-your-sample-application).|
| claims | Zeichenfolge| [Optional] Fügen Sie eine Sammlung von Behauptungen über das Subjekt in den Nachweis ein. Für den PIN-Code-Fluss ist es wichtig, dass Sie den Vor- und Nachnamen des Benutzers angeben. Weitere Informationen finden Sie unter [Namen der überprüfbaren Berechtigungsnachweise](verifiable-credentials-configure-issuer.md#verifiable-credential-names). |
| pin | [PIN](#pin-type)| [Optional] Eine PIN-Nummer, die zusätzliche Sicherheit bei der Ausstellung bietet. Für den PIN-Code-Fluss ist diese Eigenschaft erforderlich. Sie generieren einen PIN-Code und zeigen ihn dem Benutzer in Ihrer Anwendung an. Der Benutzer muss den von Ihnen generierten PIN-Code eingeben. |

### <a name="pin-type"></a>PIN-Typ

Der PIN-Typ definiert einen PIN-Code, der als Teil der Ausgabe angezeigt werden kann. Die PIN ist optional und sollte, wenn sie verwendet wird, immer "out of band" gesendet werden. Wenn Sie einen HASH-PIN-Code verwenden, müssen Sie die Eigenschaften salt, alg und iterations definieren. Die PIN enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `value` | Zeichenfolge| Enthält den PIN-Wert in Klartext. Bei Verwendung einer gehashten PIN enthält die Eigenschaft value den gesalzenen Hash, base64-kodiert.|
| `type` | Zeichenfolge|  Art des Pincodes. Möglicher Wert: `numeric` (Standard). |
| `length` | integer|  Die Länge des PIN-Codes.  Standardlänge: 6. Minimale Länge: 4 Maximale Länge: 16.|
| `salt` | Zeichenfolge|  Das Salz des gehashten PIN-Codes. Der Salt wird bei der Hash-Berechnung vorangestellt. Codierung: UTF-8. |
| `alg` | Zeichenfolge|  Der Hashing-Algorithmus für die gehashte PIN. Unterstützter Algorithmus: `sha256`. |
| `iterations` | integer| Die Anzahl der Hashing-Iterationen. Mögliche Werte: `1`.|


### <a name="callback-type"></a>Rückruftyp

Die Dienste-REST-API für Anforderungen erzeugt mehrere Ereignisse für den Callback-Endpunkt. Diese Ereignisse ermöglichen es Ihnen, die Benutzeroberfläche zu aktualisieren und den Prozess fortzusetzen, sobald die Ergebnisse an die Anwendung zurückgegeben werden. Der Typ Callback enthält die folgenden Eigenschaften:

|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `url` | Zeichenfolge| URI auf den Callback-Endpunkt Ihrer Anwendung. |
| `state` | Zeichenfolge| Assoziiert mit dem in der ursprünglichen Nutzlast übergebenen Zustand. |
| `headers` | Zeichenfolge| [Optional] Sie können eine Sammlung von HTTP-Headern einfügen, die von der empfangenden Seite der POST-Nachricht benötigt werden. Die Header sollten nur den Api-Schlüssel oder einen für die Autorisierung erforderlichen Header enthalten.|

## <a name="successful-response"></a>Erfolgreiche Antwort

Bei Erfolg gibt diese Methode einen HTTP 201 Created-Antwortcode und eine Sammlung von Ereignisobjekten im Antwortkörper zurück. Das folgende JSON zeigt eine erfolgreiche Antwort:

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
| `requestId`| Zeichenfolge | Autogenerierte Korrelations-ID. Der [Callback](#callback-events) verwendet die gleiche Anforderung. Ermöglicht die Verfolgung der Anforderung und ihrer Rückrufe. |
| `url`|  Zeichenfolge| Eine URL, mit der die Authenticator-App gestartet wird und der Ausstellungsprozess beginnt. Sie können dem Benutzer diese URL präsentieren, wenn er den QR-Code nicht scannen kann. |
| `expiry`| integer| Gibt an, wann die Antwort abläuft. |
| `qrCode`| Zeichenfolge | Ein QR-Code, den der Benutzer scannen kann, um den Ausgabestrom zu starten. |

Wenn Ihre App die Antwort erhält, muss sie dem Benutzer den QR-Code präsentieren. Der Benutzer scannt den QR-Code, wodurch die Authentifizierungs-App geöffnet wird und der Ausstellungsprozess beginnt.

## <a name="error-response"></a>Fehlerantwort

Auch Fehlerantworten können zurückgegeben werden, damit die Anwendung sie entsprechend behandeln kann. Das folgende JSON zeigt eine nicht autorisierte Fehlermeldung.


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
| `requestId`| Zeichenfolge | Automatisch generierte ID der Anforderung.|
| `date`| date| Der Zeitpunkt des Fehlers. |
| `error.code` | Zeichenfolge| Der zurückgegebene Fehlercode. |
| `error.message`| Zeichenfolge| Die Fehlermeldung. |

## <a name="callback-events"></a>Callback-Ereignisse

Der Callback-Endpunkt wird aufgerufen, wenn ein Benutzer den QR-Code scannt, den Deep Link seiner Authenticator-App verwendet oder den Ausstellungsprozess abschließt. 


|Eigenschaft |type |BESCHREIBUNG |
|---------|---------|---------|
| `requestId`| Zeichenfolge | Wird der ursprünglichen Anforderung zugeordnet, als die Nutzdaten an den Dienst für überprüfbare Nachweise gesendet wurden.|
| `code` |Zeichenfolge |Der Code, der zurückgegeben wird, wenn die Anforderung von der Authentifikator-App abgerufen wurde. Mögliche Werte: <ul><li>`request_retrieved` der Benutzer hat den QR-Code gescannt oder auf den Link geklickt, der den Ausstellungsablauf startet.</li><li>`issuance_successful` die Ausstellung der überprüfbaren Berechtigungsnachweise war erfolgreich.</li><li>`Issuance_error` bei der Ausstellung ist ein Fehler aufgetreten. Einzelheiten finden Sie in der Eigenschaft `error`.</li></ul>    |
| `state` |Zeichenfolge| Der Status gibt den Statuswert zurück, den Sie in der ursprünglichen Nutzlast übergeben haben.   |
| `error`| error | Wenn `code` `Issuance_error` ist, enthält diese Eigenschaft Informationen über den Fehler.| 
| `error.code` | Zeichenfolge| Der zurückgegebene Fehlercode. |
| `error.message`| Zeichenfolge| Die Fehlermeldung. |

Das folgende Beispiel demonstriert eine Callback-Payload, wenn die Anforderung zur Ausstellung durch die Authentifikator-App gestartet wird.

```json
{  
    "requestId":"aef2133ba45886ce2c38974339ba1057",  
    "code":"request_retrieved",  
    "state":"Wy0ThUz1gSasAjS1"
} 
```

Das folgende Beispiel zeigt eine Callback-Payload nach erfolgreichem Abschluss des Ausstellungsprozesses durch den Benutzer.

```json
{  
    "requestId":"87e1cb24-9096-409f-81cb-9e645f23a4ba",
    "code":"issuance_successful",
    "state":"f3d94e35-ca5f-4b1b-a7d7-a88caa05e322",
} 
```

### <a name="callback-errors"></a>Callback-Fehler  

Der Callback-Endpunkt kann mit einer Fehlermeldung aufgerufen werden.

Die folgende Tabelle listet die Fehlercodes auf. Diese fehlerspezifischen Details fassen die meisten Fehler, die während der Ausstellung auftreten können, allgemein zusammen.

|Nachricht  |Definition    |
|---------|---------|
| `fetch_contract_error*`| Der Vertrag über den Nachweis kann nicht abgerufen werden. Dieser Fehler tritt normalerweise auf, wenn die API das in der Nutzlast der Anforderung angegebene Manifest nicht abrufen kann [RequestIssuance object](#requestissuance-type)|
| `issuance_service_error*` | Der Dienst für überprüfbare Berechtigungsnachweise war nicht in der Lage, die Anforderungen zu validieren, oder auf Seiten des Dienstes für überprüfbare Berechtigungsnachweise ist ein Fehler aufgetreten.|
| `unspecified_error`| Es ist etwas schief gelaufen, das nicht in diesen Bereich fällt. Diese Fehlermeldung sollte nicht häufig auftreten, sollte aber immer untersucht werden. |

Das folgende Beispiel zeigt eine Callback-Payload, wenn ein Fehler aufgetreten ist.

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

Lernen Sie [, wie man die Dienste-REST-API für Anforderungen aufruft](get-started-request-api.md)
