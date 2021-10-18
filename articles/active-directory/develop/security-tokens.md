---
title: Sicherheitstoken | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Grundlagen von Sicherheitstoken in Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 2567a75c0accd7e2bb932d8578e1a225518f5ce3
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092754"
---
# <a name="security-tokens"></a>Sicherheitstoken

Ein zentraler Identitätsanbieter ist besonders nützlich für Apps mit weltweit verteilten Benutzern, die sich nicht immer über das Netzwerk des Unternehmens anmelden. Microsoft Identity Platform authentifiziert Benutzer und stellt Sicherheitstoken bereit, wie z. B. [Zugriffstoken](developer-glossary.md#access-token), [Aktualisierungstoken](developer-glossary.md#refresh-token) und [ID-Token](developer-glossary.md#id-token). Mit Sicherheitstoken kann eine [Clientanwendung](developer-glossary.md#client-application) auf geschützte Ressourcen auf einem [Ressourcenserver](developer-glossary.md#resource-server) zugreifen.

**Zugriffstoken**: Ein Zugriffstoken ist ein Sicherheitstoken, das von einem [Autorisierungsserver](developer-glossary.md#authorization-server) im Rahmen eines [OAuth 2.0](active-directory-v2-protocols.md)-Flows ausgestellt wird. Es enthält Informationen zum Benutzer und zu der Ressource, für die das Token vorgesehen ist. Die Informationen können für den Zugriff auf Web-APIs und andere geschützte Ressourcen verwendet werden. Zugriffstoken werden von Ressourcen überprüft, um einer Client-App Zugriff zu gewähren. Weitere Informationen dazu, wie Microsoft Identity Platform Zugriffstoken ausstellt, finden Sie unter [Zugriffstoken](access-tokens.md).

**Aktualisierungstoken:** Da Zugriffstoken nur kurze Zeit gültig sind, stellen Autorisierungsserver manchmal gleichzeitig mit dem Zugriffstoken auch ein Aktualisierungstoken aus. Die Clientanwendung kann dann dieses Aktualisierungstoken bei Bedarf gegen ein neues Zugriffstoken austauschen. Weitere Informationen dazu, wie Microsoft Identity Platform Aktualisierungstoken zum Widerrufen von Berechtigungen verwendet, finden Sie unter [Aktualisierungstoken](refresh-tokens.md).

**ID-Token:** ID-Token werden als Teil eines [OpenID Connect](v2-protocols-oidc.md)-Flows an die Clientanwendung gesendet. Sie können zusammen mit einem Zugriffstoken oder anstelle dessen gesendet werden. Mithilfe des ID-Tokens authentifiziert der Client den Benutzer. Weitere Informationen dazu, wie Microsoft Identity Platform ID-Token ausstellt, finden Sie unter [ID-Token](id-tokens.md).

> [!NOTE]
> In diesem Artikel werden Sicherheitstoken erläutert, die von den OAuth2- und OpenID Connect-Protokollen verwendet werden. Viele Unternehmensanwendungen verwenden SAML zum Authentifizieren von Benutzern. Weitere Informationen zu SAML-Assertionen finden Sie in der [Azure Active Directory-SAML-Tokenreferenz](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Überprüfen von Sicherheitstoken

Die Überprüfung des Tokens wird auf eine der folgenden Arten durchgeführt: von der App, für die das Token generiert wurde, der Web-App, mit der der Benutzer angemeldet wurde, oder der Web-API, die für die Überprüfung des Tokens aufgerufen wird. Das Token wird vom Autorisierungsserver mit einem privaten Schlüssel signiert. Der Autorisierungsserver veröffentlicht den entsprechenden öffentlichen Schlüssel. Zur Überprüfung eines Tokens verifiziert die App die Signatur, indem mit dem öffentlichen Schlüssel vom Autorisierungsserver überprüft wird, ob die Signatur mit dem privaten Schlüssel erstellt wurde.

Token sind nur für einen begrenzten Zeitraum gültig. In der Regel stellt der Autorisierungsserver ein Tokenpaar bereit, z. B.:

* Ein Zugriffstoken für den Zugriff auf die Anwendung oder die geschützte Ressource
* Ein Aktualisierungstoken zum Aktualisieren des Zugriffstokens, wenn dieses demnächst abläuft

Zugriffstoken werden als Bearertoken im `Authorization`-Header an eine Web-API übergeben. Eine App kann ein Aktualisierungstoken an den Autorisierungsserver übergeben. Wenn der Benutzerzugriff auf die App nicht widerrufen wurde, erhält sie ein neues Zugriffstoken und ein neues Aktualisierungstoken zurück. So wird ein Fall behandelt, bei dem eine Person aus dem Unternehmen ausscheidet. Wenn der Autorisierungsserver ein Aktualisierungstoken empfängt, stellt er kein weiteres gültiges Zugriffstoken aus, falls der Benutzer nicht mehr autorisiert ist.

## <a name="json-web-tokens-and-claims"></a>JSON Web Token und Ansprüche

Microsoft Identity Platform implementiert Sicherheitstoken als JSON Web Token (JWTs), die *Ansprüche* enthalten. Da JWT-Token als Sicherheitstoken verwendet werden, wird diese Art der Authentifizierung manchmal auch als *JWT-Authentifizierung* bezeichnet.

Ein [Anspruch](developer-glossary.md#claim) stellt Assertionen zu einer Entität (z. B. Clientanwendung oder [Ressourcenbesitzer](developer-glossary.md#resource-owner)) für eine andere Entität (z. B. Ressourcenserver) bereit. Ein Anspruch kann auch als JWT-Anspruch bzw. JSON Web Token-Anspruch bezeichnet werden.

Ansprüche sind Name-Wert-Paare zur Weitergabe von Informationen zum Tokenantragsteller. Ein Anspruch kann beispielsweise Informationen zum Sicherheitsprinzipal enthalten, der vom Autorisierungsserver authentifiziert wurde. Welche Ansprüche in einem Token enthalten sind, hängt von verschiedenen Dingen ab. Hierzu zählen unter anderem die Art des Tokens, die Art der Anmeldeinformationen für die Authentifizierung des Antragstellers und die Anwendungskonfiguration.

Anwendungen können Ansprüche für verschiedene Aufgaben verwenden, z. B.:

* Überprüfen des Tokens
* Identifizieren des [Mandanten](developer-glossary.md#tenant) des Tokenantragstellers
* Anzeigen von Benutzerinformationen
* Bestimmen der Autorisierung des Antragstellers

Ein Anspruch besteht aus Schlüssel-Wert-Paaren, mit denen beispielsweise die folgenden Informationen bereitgestellt werden:

* Sicherheitstokenserver, der das Token generiert hat
* Datum, an dem das Token generiert wurde
* Antragsteller (z. B. der Benutzer – mit Ausnahme von Daemons)
* Zielgruppe, d. h. App, für die das Token generiert wurde
* App (der Client), die das Token angefordert hat. Bei Web-Apps kann diese App mit der Zielgruppe identisch sein.

Weitere Informationen dazu, wie Microsoft Identity Platform Token und Anspruchsinformationen implementiert, finden Sie unter [Zugriffstoken](access-tokens.md) und [ID-Token](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Ausgabe von Token und Codes für die einzelnen Abläufe

Je nach Art Ihres Clients kann einer (oder auch mehrere) der Authentifizierungsabläufe verwendet werden, die von der Microsoft Identity Platform unterstützt werden. Diese Flows können verschiedene Token (ID-Token, Aktualisierungstoken, Zugriffstoken) und Autorisierungscodes erzeugen. Sie erfordern unterschiedliche Token, damit sie funktionieren. Diese Tabelle enthält eine Übersicht.

|Flow | Erforderlich | ID-Token | Zugriffstoken | Aktualisierungstoken | Authorization code (Autorisierungscode) |
|-----|----------|----------|--------------|---------------|--------------------|
|[Autorisierungscodeflow](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Impliziter Flow](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrid-OIDC-Ablauf](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Einlösung des Aktualisierungstokens](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Aktualisierungstoken | x | x | x| |
|[„Im Auftrag von“-Ablauf](v2-oauth2-on-behalf-of-flow.md) | Zugriffstoken| x| x| x| |
|[Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) | | | x (nur App)| | |

Für Token, die im impliziten Modus ausgestellt werden, gilt eine Längenbeschränkung, da sie per URL zurück an den Browser übergeben werden (`response_mode` ist hierbei `query` oder `fragment`). Bei einigen Browsern gilt eine Größenbeschränkung für die URL, die in die Browserleiste eingefügt werden kann. Es tritt ein Fehler auf, wenn die URL zu lang ist. Daher verfügen diese Token nicht über die Ansprüche `groups` oder `wids`.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Authentifizierung und Autorisierung in der Microsoft Identity Platform finden Sie in den folgenden Artikeln:

* Informationen zu den grundlegenden Konzepten für die Authentifizierung und Autorisierung finden Sie unter [Authentifizierung im Vergleich zu Autorisierung](authentication-vs-authorization.md).
* Informationen zum Registrieren Ihrer Anwendung für die Integration finden Sie unter [Anwendungsmodell](application-model.md).
* Informationen zum Anmeldeablauf für Web-, Desktop- und mobile Apps finden Sie unter [App-Anmeldeflow mit Microsoft Identity Platform](app-sign-in-flow.md).
