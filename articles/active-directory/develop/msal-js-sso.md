---
title: Einmaliges Anmelden (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie etwas über das Erstellen von Umgebungen mit einmaligem Anmelden mithilfe der Microsoft-Authentifizierungsbibliothek für JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: e715b0b2176564c8a492df20b9193d66a8fb85c8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466084"
---
# <a name="single-sign-on-with-msaljs"></a>Einmaliges Anmelden mit MSAL.js

Einmaliges Anmelden (Single Sign-On, SSO) ermöglicht Benutzern, ihre Anmeldeinformationen einmal für die Anmeldung zu verwenden und dann eine Sitzung einzurichten, die in mehreren Anwendungen wiederverwendet werden kann, ohne dass eine erneute Authentifizierung erforderlich wird. Damit wird den Benutzern ein nahtloses Arbeiten ermöglicht, da sie nicht ständig zur Eingabe von Anmeldeinformationen aufgefordert werden.

Das Azure Active Directory (Azure AD) bietet SSO-Funktionen für Anwendungen, indem diese ein Sitzungscookie ablegen, wenn sich ein Benutzer das erste Mal authentifiziert. Mit der Bibliothek MSAL.js können Anwendungen ein Sitzungscookie auf verschiedene Weise anwenden.

## <a name="sso-between-browser-tabs"></a>Einmaliges Anmelden zwischen Browserregisterkarten

Wenn Ihre Anwendung in mehreren Registerkarten geöffnet ist und sich der Benutzer zunächst auf einer Registerkarte anmeldet, wird er auch auf den anderen Registerkarten angemeldet, ohne dazu aufgefordert zu werden. MSAL.js speichert das ID-Token für den Benutzer im `localStorage` des Browsers und meldet den Benutzer auf den anderen offenen Registerkarten bei der Anwendung an.

Standardmäßig verwendet MSAL.js `sessionStorage`, der keine gemeinsame Verwendung der Sitzung zwischen den Registerkarten erlaubt. Um SSO zwischen Registerkarten zu ermöglichen, müssen Sie `cacheLocation` in MSAL.js auf `localStorage` festlegen, wie unten dargestellt.

```javascript
const config = {
  auth: {
    clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg",
  },
  cache: {
    cacheLocation: "localStorage",
  },
};

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Einmaliges Anmelden zwischen Apps

Beim Authentifizieren eines Benutzers wird in der Azure AD-Domäne im Browser ein Sitzungscookie festgelegt. MSAL.js benötigt dieses Sitzungscookie, um einmaliges Anmelden für den Benutzer zwischen verschiedenen Anwendungen bereitzustellen. Außerdem speichert MSAL.js die ID- und Zugriffstoken des Benutzers im Browserspeicher in den einzelnen Anwendungsdomänen zwischen. Daher weicht das SSO-Verhalten in verschiedenen Situationen ab:

### <a name="applications-on-the-same-domain"></a>Anwendungen in derselben Domäne

Wenn Anwendungen in derselben Domäne gehostet werden, kann sich der Benutzer einmal bei einer App anmelden und wird dann ohne Aufforderung bei den anderen Apps authentifiziert. MSAL.js nutzt die für den Benutzer in der Domäne zwischengespeicherten Token, um ein einmaliges Anmelden (SSO) zu ermöglichen.

### <a name="applications-on-different-domain"></a>Anwendungen in unterschiedlichen Domänen

Wenn Anwendungen in unterschiedlichen Domänen gehostet werden, kann MSAL.js in Domäne B nicht auf Token zugreifen, die in Domäne A zwischengespeichert wurden.

Wenn ein Benutzer in Domäne A angemeldet ist, navigieren Sie zu einer Anwendung in Domäne B, sodann wird der Benutzer umgeleitet oder zur Eingabe auf der Anmeldeseite aufgefordert. Da Azure AD weiterhin über das Benutzersitzungscookie verfügt, wird der Benutzer angemeldet und es werden keine Anmeldeinformationen abgefragt.

Wenn der Benutzer in der Azure AD-Sitzung über mehrere Benutzerkonten verfügt, wird er aufgefordert, das entsprechende Konto für die Anmeldung auszuwählen.

### <a name="automatically-select-account-on-azure-ad"></a>Automatisches Auswählen eines Kontos in Azure AD

In bestimmten Fällen hat die Anwendung Zugriff auf den Kontext der Benutzerauthentifizierung und möchte verhindern, dass der Benutzer zur Auswahl des Azure AD-Kontos aufgefordert wird, wenn mehrere Konten angemeldet sind. Das Umgehen der Eingabeaufforderung für die Azure AD Kontoauswahl kann auf verschiedene Weise erfolgen:

**Mit der Sitzungs-ID**

Die Sitzungs-ID (SID) ist ein [optionaler Anspruch](active-directory-optional-claims.md), der in den ID-Token konfiguriert werden kann. Dieser Anspruch ermöglicht es der Anwendung, die Azure AD-Sitzung des Benutzers zu ermitteln – und zwar unabhängig vom Kontonamen des Benutzers oder vom Benutzernamen. Sie können die SID in den Anforderungsparametern des `acquireTokenSilent`-Aufrufs übergeben. Die `acquireTokenSilent` in den Anforderungsparametern ermöglicht es Azure AD, die Kontoauswahl zu umgehen. Die SID ist an das Sitzungscookie gebunden und kann nicht für mehrere Browserkontexte verwendet werden.

```javascript
var request = {
  scopes: ["user.read"],
  sid: sid,
};

userAgentApplication
  .acquireTokenSilent(request)
  .then(function (response) {
    const token = response.accessToken;
  })
  .catch(function (error) {
    //handle error
  });
```

Die SID kann nur mit Authentifizierungsanforderungen im Hintergrund verwendet werden, die mit einem `acquireTokenSilent`-Aufruf in MSAL.js durchgeführt werden. Sie finden die Schritte zum Konfigurieren optionaler Ansprüche in Ihrem Anwendungsmanifest unter [Bereitstellen optionaler Ansprüche für Ihre Anwendung](active-directory-optional-claims.md).

**Mit einem Anmeldehinweis**

Wenn kein SID-Anspruch konfiguriert wurde oder die Aufforderung zur Kontoauswahl auch bei interaktiven Authentifizierungen umgangen werden soll, geben Sie in den Anforderungsparametern einen `login_hint` an, und stellen Sie optional einen `domain_hint` als `extraQueryParameters` in den interaktiven MSAL.js-Methoden (`loginPopup`, `loginRedirect`, `acquireTokenPopup` und `acquireTokenRedirect`) bereit. Beispiel:

```javascript
var request = {
  scopes: ["user.read"],
  loginHint: preferred_username,
  extraQueryParameters: { domain_hint: "organizations" },
};

userAgentApplication.loginRedirect(request);
```

Sie erhalten die Werte für „login_hint“ und „domain_hint“ durch Auslesen der Ansprüche, die im ID-Token für den Benutzer zurückgegeben werden.

- **login_hint** sollte auf den `preferred_username`-Anspruch im ID-Token festgelegt sein.

- **domain_hint** muss nur übergeben werden, wenn Sie die /common-Autorität verwenden. Der Domänenhinweis (domain_hint) wird durch die Mandanten-ID (TID) bestimmt. Wenn der `tid`-Anspruch im ID-Token `9188040d-6c67-4c5b-b112-36a304b66dad` lautet, handelt es sich um Consumer. Andernfalls sind es Organisationen.

Weitere Informationen zu **login_hint** und **domain_hint** finden Sie unter [Flow für implizite Genehmigung (Implicit Grant Flow)](v2-oauth2-implicit-grant-flow.md).

## <a name="sso-without-msaljs-login"></a>Einmaliges Anmelden ohne MSAL.js-Anmeldung

Standardmäßig erfordert MSAL.js, dass eine Anmeldemethode aufgerufen wird, um einen Benutzerkontext einzurichten, bevor Token für APIs abgerufen werden. Da Anmeldemethoden interaktiv sind, wird dem Benutzer eine Aufforderung angezeigt.

Es gibt bestimmte Fälle, in denen Anwendungen Zugriff auf den authentifizierten Benutzerkontext oder das ID-Token durch eine Authentifizierung haben, die in einer anderen Anwendung initiiert wurde, und die das einmalige Anmelden (SSO) verwenden möchten, um Token ohne eine vorherige Anmeldung über MSAL.js abzurufen.

Beispiel: Ein Benutzer ist bei einem Microsoft-Konto in einem Browser angemeldet, der eine andere JavaScript-Anwendung hostet, die als Add-On oder Plug-In ausgeführt wird, was eine Microsoft-Konto-Anmeldung erforderlich macht.

In diesem Szenario kann einmaliges Anmelden wie folgt erreicht werden:

Übergeben Sie wie folgt die `sid` (sofern vorhanden – oder `login_hint` und optional `domain_hint`) als Anforderungsparameter für den MSAL.js-Aufruf von `acquireTokenSilent`:

```javascript
var request = {
  scopes: ["user.read"],
  loginHint: preferred_username,
  extraQueryParameters: { domain_hint: "organizations" },
};

userAgentApplication
  .acquireTokenSilent(request)
  .then(function (response) {
    const token = response.accessToken;
  })
  .catch(function (error) {
    //handle error
  });
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Einmaliges Anmelden in ADAL.js mit Update auf MSAL.js

MSAL.js bietet Featureparität mit ADAL.js für Azure AD-Authentifizierungsszenarien. Um die Migration von ADAL.js zu MSAL.js zu vereinfachen und zu vermeiden, dass Ihre Benutzer zur erneuten Anmeldung aufgefordert werden, liest die Bibliothek das ID-Token für die Sitzung des Benutzers im Cache von ADAL.js und meldet den Benutzer nahtlos in MSAL.js an.

Um das einmalige Anmelden (SSO) bei einer Aktualisierung von ADAL.js nutzen zu können, müssen Sie sicherstellen, dass die Bibliotheken zum Zwischenspeichern von Token `localStorage` verwenden. Legen Sie bei der Initialisierung `cacheLocation` in den Konfigurationen von MSAL.js und ADAL.js wie folgt auf `localStorage` fest:

```javascript
// In ADAL.js
window.config = {
  clientId: "g075edef-0efa-453b-997b-de1337c29185",
  cacheLocation: "localStorage",
};

var authContext = new AuthenticationContext(config);

// In latest MSAL.js version
const config = {
  auth: {
    clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg",
  },
  cache: {
    cacheLocation: "localStorage",
  },
};

const myMSALObj = new UserAgentApplication(config);
```

Sobald `cacheLocation` konfiguriert ist, kann MSAL.js den zwischengespeicherten Zustand des authentifizierten Benutzers in ADAL.js lesen und dazu verwenden, einmaliges Anmelden in MSAL.js zur Verfügung zu stellen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum einmaligen Anmelden (SSO) finden Sie unter:

- [SAML-Protokoll für einmaliges Anmelden](single-sign-on-saml-protocol.md)
- [Konfigurierbare Tokengültigkeitsdauer](active-directory-configurable-token-lifetimes.md)
