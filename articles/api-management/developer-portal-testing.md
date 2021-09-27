---
title: Testen des selbstgehosteten Entwicklerportals
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Sie Komponententests und End-to-End-Tests für Ihr selbstgehostetes API Management-Portal einrichten.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 62c5ab98e067bb735bcfa1510cae405b17461cac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646437"
---
# <a name="test-the-self-hosted-developer-portal"></a>Testen des selbstgehosteten Entwicklerportals

In diesem Artikel erfahren Sie, wie Sie Komponententests und End-to-End-Tests für Ihr [selbstgehostetes Portal](developer-portal-self-host.md) einrichten.

## <a name="unit-tests"></a>Komponententests

Ein Komponententest ist ein Ansatz zum Überprüfen kleiner Funktionseinheiten. Dies erfolgt isoliert von anderen Teilen der Anwendung.

### <a name="example-scenario"></a>Beispielszenario

In diesem Szenario testen Sie eine Kennworteingabesteuerung. Es werden nur Kennwörter akzeptiert, die mindestens Folgendes enthalten:

- Einen Buchstaben

- Eine Zahl

- Ein Sonderzeichen
 
Der Test zum Überprüfen dieser Anforderungen sieht also folgendermaßen aus:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Projektstruktur

Es ist üblich, einen Komponententest neben der Komponente zu belassen, die überprüft werden soll.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>Simulierte HTTP-Anforderungen

In manchen Fällen wird erwartet, dass eine Komponente HTTP-Anforderungen ausführt. Die Komponente sollte ordnungsgemäß auf verschiedene Arten von Antworten reagieren. Verwenden Sie `MockHttpClient`, um bestimmte HTTP-Antworten zu simulieren. Die Schnittstelle `HttpClient` wird implementiert, die von vielen anderen Komponenten des Projekts verwendet wird:

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>End-to-End-Tests

Bei einem End-to-End-Test wird ein bestimmtes Benutzerszenario ausgeführt, wobei genau die Schritte unternommen werden, die der Benutzer ausführen soll. In einer Webanwendung wie dem Azure API Management-Entwicklerportal scrollt der Benutzer durch den Inhalt und wählt Optionen aus, um bestimmte Ergebnisse zu erzielen. 

Zum Replizieren der Benutzernavigation können Sie Hilfsbibliotheken für die Browserbearbeitung wie [Puppeteer](https://github.com/puppeteer/puppeteer) verwenden. Sie können Benutzeraktionen simulieren und angenommene Szenarien automatisieren. Puppeteer erstellt außerdem in jeder Phase des Tests automatisch Screenshots von Seiten oder Komponenten. Vergleichen Sie sie später mit vorherigen Ergebnissen, um Abweichungen und potenzielle Regressionen zu erkennen.

### <a name="example-scenario"></a>Beispielszenario

In diesem Szenario müssen Sie einen Benutzeranmeldeflow überprüfen. Für dieses Szenario sind die folgenden Schritte erforderlich:

1. Öffnen Sie den Browser, und navigieren Sie zur Anmeldeseite.

1. Geben Sie die E-Mail-Adresse ein.

1. Geben Sie das Kennwort ein.

1. Wählen Sie **Anmeldung** aus.

1. Vergewissern Sie sich, dass der Benutzer zur Homepage umgeleitet wurde.

1. Stellen Sie sicher, dass die Seite das Menüelement **Profil** enthält. Dies ist einer der möglichen Indikatoren, dass Sie sich erfolgreich angemeldet haben.

Erstellen Sie mit genau den gleichen Schritten ein Skript, um den Test automatisch auszuführen:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Zeichenfolgen wie „#email“, „#password“ und „#signin“ sind CSS-ähnliche Selektoren, die HTML-Elemente auf der Seite identifizieren. Weitere Informationen finden Sie in der W3C-Spezifikation [Selectors Level 3](https://www.w3.org/TR/selectors-3/).

### <a name="ui-component-maps"></a>Zuordnungen von Benutzeroberflächenkomponenten

Benutzerflows durchlaufen häufig die gleichen Seiten oder Komponenten. Ein gutes Beispiel ist das Hauptmenü der Website, das auf jeder Seite vorhanden ist. 

Erstellen Sie eine Zuordnung der Benutzeroberflächenkomponenten, um zu vermeiden, dass die gleichen Selektoren für jeden Test konfiguriert und aktualisiert werden. Beispielsweise könnten Sie die Schritte 2 bis 6 im vorherigen Beispiel durch nur zwei Zeilen ersetzen:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Testkonfiguration

Für bestimmte Szenarien sind möglicherweise vorab erstellte Daten oder Konfigurationen erforderlich. Beispielsweise müssen Sie unter Umständen die Benutzeranmeldung mit Social Media-Konten automatisieren. Diese Daten schnell oder einfach zu erstellen ist schwierig.

Zu diesem Zweck können Sie Ihrem Testszenario eine spezielle Konfigurationsdatei hinzufügen. Die Testskripts können die erforderlichen Daten aus der Datei auswählen. Je nach Build- und Testpipeline können die Tests die Geheimnisse aus einem benannten sicheren Speicher pullen.

Im Folgenden finden Sie ein Beispiel für eine Datei vom Typ `validate.config.json`, die im `src`-Ordner Ihres Projekts gespeichert wird:

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Monitorlose Tests im Vergleich zu normalen Tests

Mit modernen Browsern wie Chrome oder Microsoft Edge können Sie die Automatisierung sowohl im monitorlosen Modus als auch im normalen Modus ausführen. Der Browser wird im monitorlosen Modus ohne grafische Benutzeroberfläche ausgeführt. Er führt weiterhin die gleichen Seiten- und DOM-Bearbeitungen (Dokumentobjektmodell) durch. Die Browserbenutzeroberfläche wird in der Regel nicht in Bereitstellungspipelines benötigt. In diesem Fall ist das Ausführen von Tests im monitorlosen Modus eine gute Option.

Beim Entwickeln eines Testskripts ist es hilfreich zu sehen, was genau im Browser passiert. Dies ist ein guter Zeitpunkt, um den normalen Modus zu verwenden.

Ändern Sie die Option `headless` in der Datei `constants.ts`, um zwischen den Modi zu wechseln. Sie befindet sich im Ordner `tests` in Ihrem Projekt:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Eine weitere nützliche Option ist `slowMo`. Die Ausführung des Tests zwischen den einzelnen Aktionen wird angehalten:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Tests durchführen

In diesem Projekt stehen zwei integrierte Möglichkeiten zum Ausführen von Tests zur Verfügung:

**npm-Befehl**

```console
npm run test
```

**Test-Explorer**

Die Test-Explorer-Erweiterung für VS Code (z. B. [Mocha Test Explorer](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)) verfügt über eine praktische Benutzeroberfläche und eine Option zum automatischen Ausführen von Tests bei jeder Änderung des Quellcodes:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Screenshot: Test-Explorer von Visual Studio Code":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)

- [Selbsthosten des API Management-Entwicklerportals](developer-portal-self-host.md)
