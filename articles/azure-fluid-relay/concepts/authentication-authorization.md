---
title: Authentifizierung und Autorisierung in Ihrer App
description: Übersicht über die Verwendung der Authentifizierung und Autorisierung mit einem Azure Fluid Relay-Dienst.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/auth/
ms.openlocfilehash: e2f24c5455548980318c4536b5c65f84ea6a7dfb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893821"
---
# <a name="authentication-and-authorization-in-your-app"></a>Authentifizierung und Autorisierung in Ihrer App

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Sicherheit ist für moderne Webanwendungen von entscheidender Bedeutung. Fluid Framework ist als Teil Ihrer Webanwendungsarchitektur ein wichtiger Bestandteil der zu schützenden Infrastruktur. Fluid Framework ist eine Architektur mit mehreren Ebenen. Authentifizierungskonzepte werden basierend auf dem Fluid-Dienst implementiert, mit dem eine Verbindung besteht. Das heißt, dass sich die Details und Besonderheiten für jeden Dienst unterscheiden, obwohl es allgemeine Authentifizierungsdesigns für alle Fluid-Dienste gibt.

## <a name="azure-fluid-relay-service"></a>Azure Fluid Relay-Dienst

Jedem erstellten Azure Fluid Relay-Dienstmandanten werden eine **Mandanten-ID** und ein eigener eindeutiger **geheimer Mandantenschlüssel** zugewiesen.

Der geheime Schlüssel ist ein **freigegebener geheimer Schlüssel**. Ihre App/Ihr Dienst und der Azure Fluid Relay-Dienst kennen ihn. Da der geheime Mandantenschlüssel eindeutig an Ihren Mandanten gebunden ist, garantiert die Verwendung dieses Schlüssels zum Signieren von Anforderungen an den Azure Fluid Relay-Dienst, dass die Anforderungen von einem autorisierten Benutzer des Mandanten kommen.

Durch den geheimen Schlüssel erkennt der Azure Fluid Relay-Dienst, dass Anforderungen von Ihrer App oder Ihrem Dienst kommen. Dies ist wichtig, da der Azure Fluid Relay-Dienst, sobald er darauf vertrauen kann, dass *Ihre App* die Anforderungen sendet, den gesendeten Daten vertrauen kann. Aus diesem Grund ist es auch wichtig, dass das Geheimnis sicher behandelt wird.

> [!CAUTION]
> Jeder Benutzer mit Zugriff auf das Geheimnis kann bei der Kommunikation mit dem Azure Fluid Relay-Dienst die Identität Ihrer Anwendung wechseln.

## <a name="json-web-tokens-and-azure-fluid-relay-service"></a>JSON Web Tokens und Azure Fluid Relay-Dienst

Azure Fluid Relay verwendet [JSON Web Tokens (JWTs),](https://jwt.io/) um Daten zu codieren und zu überprüfen, die mit Ihrem geheimen Schlüssel signiert wurden. JSON-Web Tokens sind ein signiertes JSON-Bit, das zusätzliche Informationen zu Rechten und Berechtigungen enthalten kann.

> [!NOTE]
> Die Besonderheiten von JWTs gehen über den Rahmen dieses Artikels hinaus. Weitere Informationen zum JWT-Standard finden Sie unter <https://jwt.io/introduction>.

Obwohl sich die Details der Authentifizierung zwischen Fluid-Diensten unterscheiden, müssen immer mehrere Werte vorhanden sein.

- **containerId**  Der Fluid-Dienst benötigt die Container-ID, um zu ermitteln, welcher Dienst dem aufrufenden Container entspricht. *Hinweis*: JWT ruft dieses Feld „documentId“ auf, aber der Fluid-Dienst erwartet in diesem Feld eine Container-ID.
- **tenantId**: Der Azure Fluid Relay-Dienst verwendet die Mandanten-ID, um das gemeinsame Geheimnis abzurufen, das er zum Authentifizieren Ihrer Anforderung verwendet. 
- **scopes**: Bereiche definieren die Berechtigungen des aufrufenden Containers. Der Inhalt des Bereichsfelds ist flexibel, sodass Sie eigene benutzerdefinierte Berechtigungen erstellen können.

```json {linenos=inline,hl_lines=["5-6",13]}
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "documentId": "azureFluidDocumentId",
  "scopes": [ "doc:read", "doc:write", "summary:write" ],
  "user": {
    "name": "TestUser",
    "id": "Test-Id-123"
  },
  "iat": 1599098963,
  "exp": 1599098963,
  "tenantId": "AzureFluidTenantId",
  "ver": "1.0"
}.[Signature]
```

Der Modus des Benutzers gibt an, ob sich die Verbindung im Lese- oder Lese-/Schreibmodus befindet. Dies kann über das Feld `connections` in `AzureAudience` angezeigt werden. Die Berechtigungen für den Tokenbereich können in Ihrer serverlosen Azure-Funktion unter der `generateToken`-Funktion aktualisiert werden.

```ts
const token = generateToken(
  tenantId,
  documentId,
  key,
  scopes ?? [ "Token Scope" ],
  user
);
```

Die Tokenbereiche sowie das Containerverhalten und die Modi lauten wie folgt:

| Tokenbereich | Mein Dokumentverhalten | Verhalten von Zielgruppendokumenten | 
|-------------|----------------------|----------------------------|
| DocRead     | Lese- und Schreibzugriff auf das Dokument. Änderungen am Dokument werden nicht in anderen Zielgruppendokumenten widergespiegelt. <br /> Modus: Lesen | Lese- und Schreibzugriff auf das Dokument. Änderungen werden nicht in anderen Zielgruppendokumenten widergespiegelt. <br /> Modus: Schreiben | 
| DocWrite    | Lese- und Schreibzugriff auf das Dokument. Vorgenommene Änderungen werden in allen anderen Zielgruppendokumenten widergespiegelt. <br />Modus: Schreiben | Lese- und Schreibzugriff auf das Dokument. Vorgenommene Änderungen werden in allen anderen Zielgruppendokumenten widergespiegelt. <br />Modus: Schreiben |
| DocRead, DocWrite | Lese- und Schreibzugriff auf das Dokument. Vorgenommene Änderungen werden in allen anderen Zielgruppendokumenten widergespiegelt. <br />Modus: Schreiben | Lese- und Schreibzugriff auf das Dokument. Vorgenommene Änderungen werden in allen anderen Zielgruppendokumenten widergespiegelt. <br />Modus: Schreiben |

> [!NOTE]
> Beachten Sie, dass das Token auch Benutzerinformationen enthält (siehe Zeilen 7–9 oben). Sie können dies verwenden, um die Benutzerinformationen zu vergrößern, die für Fluid-Code mithilfe des [Zielgruppenfeatures](../how-tos/connect-fluid-azure-service.md#getting-audience-details) automatisch verfügbar sind. Weitere Informationen finden Sie unter [Hinzufügen benutzerdefinierter Daten zu Token](../how-tos/connect-fluid-azure-service.md#adding-custom-data-to-tokens).

## <a name="the-token-provider"></a>Der Tokenanbieter

Jede Anforderung an Azure Fluid Relay muss mit einem gültigen JWT signiert werden. Fluid delegiert die Verantwortung für das Erstellen und Signieren dieser Token an Tokenanbieter.

Ein Tokenanbieter ist für das Erstellen und Signieren von Token verantwortlich, die vom `@fluidframework/azure-client` verwendet werden, um Anforderungen an den Azure Fluid Relay-Dienst zu senden. Sie müssen Ihre eigene Implementierung eines Sicherheitstokenanbieters bereitstellen. Fluid stellt jedoch einen `InsecureTokenProvider` bereit, der Ihr Mandantengeheimnis akzeptiert und dann lokal ein signiertes Token generiert und zurückgibt. Dieser Tokenanbieter ist für Tests nützlich, kann aber nicht in Produktionsszenarien verwendet werden.

### <a name="a-secure-serverless-token-provider"></a>Ein sicherer serverloser Tokenanbieter

Eine Möglichkeit zum Erstellen eines sicheren Tokenanbieters besteht in der Erstellung einer serverlosen Azure-Funktion und deren Bereitstellung als Tokenanbieter. Dadurch können Sie den *geheimen Mandantenschlüssel* auf einem sicheren Server speichern. Ihre Anwendung ruft dann die Azure-Funktion auf, um Token zu generieren, anstatt sie wie der `InsecureTokenProvider` lokal zu signieren. Weitere Informationen finden Sie unter [Schreiben eines TokenProvider-Elements mit einer Azure-Funktion](../how-tos/azure-function-token-provider.md).

## <a name="connecting-user-auth-to-fluid-service-auth"></a>Verbinden der Benutzerauthentifizierung mit der Fluid-Dienstauthentifizierung

Fluid-Dienste authentifizieren eingehende Aufrufe mit einem freigegebenen geheimen Clientgeheimnis, das nicht an einen bestimmten Benutzer gebunden ist. Die Benutzerauthentifizierung kann basierend auf den Details Ihres Fluid-Diensts hinzugefügt werden. 

Eine einfache Option für die Benutzerauthentifizierung wäre, einfach eine [Azure-Funktion](../../azure-functions/index.yml) als Tokenanbieter zu verwenden und die Benutzerauthentifizierung als Bedingung zum Abrufen eines Tokens zu erzwingen. Wenn eine Anwendung versucht, die Funktion aufzurufen, würde dies fehlschlagen, außer sie wurde mit Ihrem Authentifizierungssystem authentifiziert. Wenn Sie beispielsweise Azure Active Directory (Azure AD) verwenden, erstellen Sie möglicherweise eine Azure AD-Anwendung für Ihre Azure-Funktion und binden sie an das Authentifizierungssystem Ihrer Organisation.

In diesem Fall würde sich der Benutzer mithilfe von Azure AD bei Ihrer Anwendung anmelden, über die Sie ein Token zum Aufrufen Ihrer Azure-Funktion abrufen. Die Azure-Funktion selbst verhält sich identisch, aber sie ist jetzt nur noch für Personen zugänglich, die sich auch mit Azure AD authentifiziert haben.

Da die Azure-Funktion jetzt Ihr Einstiegspunkt für das Abrufen eines gültigen Tokens ist, können nur Benutzer, die sich ordnungsgemäß bei der Funktion authentifiziert haben, dieses Token dem Azure Fluid Relay-Dienst aus ihrer Clientanwendung bereitstellen. Mit diesem zweistufigen Ansatz können Sie Ihren eigenen benutzerdefinierten Authentifizierungsprozess in Verbindung mit dem Azure Fluid Relay-Dienst verwenden.

## <a name="see-also"></a>Siehe auch

- [Einführung in JWTs](https://jwt.io/introduction)
- [Erstellen eines JSON Web Token](../how-tos/fluid-json-web-token.md)
- [Nutzlastansprüche in Azure Fluid Relay](../how-tos/fluid-json-web-token.md#payload-claims)
