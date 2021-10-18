---
title: Azure Fluid Relay-Tokenvertrag
description: Besseres Verständnis der in Azure Fluid Relay verwendeten JSON Web Token
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: 3fe063a6d5b3c76d300a088372e9dee08d9d79c0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661814"
---
# <a name="azure-fluid-relay-token-contract"></a>Azure Fluid Relay-Tokenvertrag

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Anforderungen, die an Azure Fluid Relay gesendet werden, sollten ein JWT-Token im Autorisierungsheader enthalten. Dieses Token muss [vom Mandantenschlüssel signiert](../concepts/authentication-authorization.md)werden.

## <a name="claims"></a>Ansprüche

JSON Web Token (JWTs) sind in drei Teile unterteilt: 

- **Header**: Enthält Informationen dazu, wie Sie das Token überprüfen können. Dazu zählen auch Informationen zum Typ des Tokens und zu seiner Signierung. 
- **Nutzdaten**: Enthält alle wichtigen Daten über den Benutzer oder die App, der bzw. die Ihren Dienst aufzurufen versucht. 
- **Signatur**: Dies ist das Rohmaterial, das zum Überprüfen des Tokens verwendet wird. 

Diese Teile sind jeweils durch einen Punkt (.) getrennt und werden separat Base64-codiert. 

## <a name="header-claims"></a>Headeransprüche

| Anspruch | Format | BESCHREIBUNG  |
|-------|--------|--------------|
| alg   | Zeichenfolge | Der zum Signieren des Tokens verwendete Algorithmus. Beispiel: HS256 |
| Typ   | Zeichenfolge | Dieser Wert sollte immer „JWT“ sein. |

## <a name="payload-claims"></a>Nutzlastansprüche

| Anspruch      | Format                   | Beschreibung |
|------------|--------------------------|-------------|
| documentId | Zeichenfolge                   | Wird von FRS generiert und identifiziert das Dokument, für das das Token generiert wird. |
| scope      | string[]                 | Gibt an, welche Berechtigungen der Client für das Dokument oder die Zusammenfassung benötigt. Für jeden Bereich können Sie die Berechtigungen definieren, die Sie dem Client erteilen möchten.  |
| tenantId   | Zeichenfolge                   | Gibt den Mandanten an. |
| user       | JSON                     | *Optional* `{ displayName: <display_name>, id: <user_id>, name: <user_name>, }` Gibt die Benutzer Ihrer Anwendung an. Diese wird von Alfred, dem Ordering Service, an Ihre Anwendung zurückgesendet.  Sie kann von Ihrer Anwendung verwendet werden, um Ihre Benutzer anhand der von Alfred erhaltenen Antworten zu identifizieren. Azure Fluid Relay überprüft diese Informationen nicht. |
| iat        | Eine Zahl, ein UNIX-Zeitstempel | „Issued At“ gibt an, wann die Authentifizierung für dieses Token erfolgt ist. |
| exp        | Eine Zahl, ein UNIX-Zeitstempel | Der Anspruch „exp“ (Ablaufzeit) gibt die Ablaufzeit an, ab oder nach der das JWT NICHT für die Bearbeitung akzeptiert werden darf. Die Tokenlebensdauer darf nicht mehr als 1 Stunde betragen. |
| ver        | Zeichenfolge                   | Gibt die Version des Zugriffstokens an. Dieser Wert muss `1.0` sein. |
| jti        | Zeichenfolge                   | *Optional.*  Der Anspruch "jti" (JWT-ID) stellt einen eindeutigen Bezeichner für das JWT bereit. Der Bezeichnerwert MUSS auf eine Art und Weise zugewiesen werden, die sicherstellt, dass eine geringe Wahrscheinlichkeit besteht, dass der gleiche Wert versehentlich einem anderen Datenobjekt zugewiesen ist. Wir empfehlen Ihnen, diesen Anspruch zu verwenden, um Fehler aufgrund der Verwendung desselben Tokens für die Dokumenterstellung zu vermeiden.  |

## <a name="a-sample-azure-fluid-relay-token"></a>Azure Fluid Relay-Beispieltoken

```typescript
{ 
  "alg": "HS256",  
  "typ": "JWT" 
}.{ 
  "documentId": "746c4a6f-f778-4970-83cd-9e21bf88326c", 
  "scopes": [ "doc:read", "doc:write", "summary:write" ],   
  "iat": 1599098963,  
  "exp": 1599098963,  
  "tenantId": "AzureFluidTenantId",  
  "ver": "1.0",
  "jti": "d7cd6602-2179-11ec-9621-0242ac130002"
}.[Signature] 
```

## <a name="how-can-you-generate-an-azure-fluid-relay-token"></a>Wie können Sie ein Azure Fluid Relay-Token generieren? 

Sie können das npm-Paket [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) verwenden und Ihr Token mit dieser Methode signieren.

```typescript
export function getSignedToken(
    tenantId: string,
    documentId: string,
    tokenLifetime: number = 60 * 60,
    ver: string = "1.0") {
        jwt.sign(
            {
                documentId, 
                user: {
                    displayName: "displayName", 
                    id: "userId", 
                    name: "userName" 
                }, 
                scopes: ["doc:read", "doc:write", "summary:write"], 
                iat: Math.round((new Date()).getTime() / 1000), 
                exp: Math.round((new Date()).getTime() / 1000) + tokenLifetime, //set the expiry date based on your needs but max-limit is one hour.
                tenantId, 
                ver,
                jti: uuid(), 
            },
            "<tenant_key>");
    }
```
