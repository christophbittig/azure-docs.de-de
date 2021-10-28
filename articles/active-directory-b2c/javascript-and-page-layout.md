---
title: JavaScript und Seitenlayoutversionen
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie in Azure Active Directory B2C JavaScript aktivieren und Seitenlayoutversionen verwenden.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2021
ms.custom: project-no-code, devx-track-js
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 59061c1f7c66558cc6daf1e50b7c9c2ad1378da3
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130217039"
---
# <a name="enable-javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Aktivieren von JavaScript und Seitenlayoutversionen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Mit [HTML-Vorlagen](customize-ui-with-html.md) in Azure Active Directory B2C (Azure AD B2C) können Sie die Identitätsfunktionen Ihrer Benutzer gestalten. Ihre HTML-Vorlagen können nur bestimmte HTML-Tags und -Attribute enthalten. HTML-Basistags wie &lt;b&gt;, &lt;i&gt;, &lt;u&gt;, &lt;h1&gt; und &lt;hr&gt; sind zulässig. Komplexere Tags wie &lt;script&gt; und &lt;iframe&gt; werden aus Sicherheitsgründen entfernt.

So können Sie JavaScript aktivieren und HTML-Tags und -Attribute erweitern:

::: zone pivot="b2c-user-flow"

* Wählen Sie ein [Seitenlayout](page-layout.md) aus.
* Aktivieren Sie es im Benutzerflow über das Azure-Portal.
* Verwenden Sie [b2clogin.com](b2clogin.md) in Ihren Anforderungen.

::: zone-end

::: zone pivot="b2c-custom-policy"

* Wählen Sie ein [Seitenlayout](page-layout.md) aus.
* Hinzufügen eines Elements zu Ihrer [benutzerdefinierten Richtlinie](custom-policy-overview.md)
* Verwenden Sie [b2clogin.com](b2clogin.md) in Ihren Anforderungen.

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="begin-setting-up-a-page-layout-version"></a>Starten der Einrichtung einer Seitenlayoutversion

Wenn Sie beabsichtigen, clientseitigen JavaScript-Code zu aktivieren, müssen die Elemente, auf denen der JavaScript-Code basiert, unveränderlich sein. Wenn sie nicht unveränderlich sind, könnten Änderungen zu einem unerwarteten Verhalten auf Ihren Benutzerseiten führen. Um diese Probleme zu vermeiden, erzwingen Sie die Verwendung eines Seitenlayouts, und geben Sie eine Seitenlayoutversion an, um sicherzustellen, dass die Inhaltsdefinitionen, auf denen Ihr JavaScript basiert, unveränderlich sind. Auch wenn Sie nicht beabsichtigen, JavaScript zu aktivieren, können Sie eine Seitenlayoutversion für Ihre Seiten angeben.

::: zone pivot="b2c-user-flow"

So geben Sie eine Seitenlayoutversion für die Seiten Ihres Benutzerflows an 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Wählen Sie eine Richtlinie aus (z.B. „B2C_1_SignupSignin“), um sie zu öffnen.
1. Wählen Sie **Seitenlayouts** aus. Wählen Sie einen **Layoutnamen** und dann **Seitenlayoutversion** aus.

Informationen zu den verschiedenen Seitenlayoutversionen finden Sie im [Versionsänderungsprotokoll für das Seitenlayout](page-layout.md).

![Seitenlayouteinstellungen im Portal mit der Dropdownliste für Seitenlayouts](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

So geben Sie eine Seitenlayoutversion für Ihre benutzerdefinierten Richtlinienseiten an:

1. Wählen Sie ein [Seitenlayout](contentdefinitions.md#select-a-page-layout) für die Benutzeroberflächenelemente Ihrer Anwendung aus.
1. Definieren Sie eine [Seitenlayoutversion](contentdefinitions.md#migrating-to-page-layout) mit der `contract`-Version der Seite für *alle* Inhaltsdefinitionen in Ihrer benutzerdefinierten Richtlinie. Das Format des Werts muss das Wort `contract` enthalten: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_. 

Im folgenden Beispiel werden die Inhaltsdefinitions-IDs mit dem zugehörigen **DataUri** für den Seitenvertrag gezeigt: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>Aktivieren von JavaScript

::: zone pivot="b2c-user-flow"

Im Benutzerflow **Eigenschaften** können Sie JavaScript aktivieren. Das Aktivieren von JavaScript erzwingt auch die Verwendung eines Seitenlayouts. Anschließend können Sie die Seitenlayoutversion für den Benutzerflow festlegen, wie im nächsten Abschnitt beschrieben.

![Benutzerablaufeigenschaften-Seite mit hervorgehobener Einstellung „JavaScript aktivieren“](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Sie aktivieren die Skriptausführung, indem Sie dem [RelyingParty](relyingparty.md)-Element das **ScriptExecution**-Element hinzufügen.

1. Öffnen Sie Ihre benutzerdefinierte Richtliniendatei. Beispiel: *SignUpOrSignin.xml*.
1. Fügen Sie dem **RelyingParty**-Element das **ScriptExecution**-Element hinzu:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Speichern Sie die Datei, und laden Sie sie hoch.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Richtlinien für die Verwendung von JavaScript

Beachten Sie die folgenden Richtlinien, wenn Sie die Benutzeroberfläche Ihrer Anwendung mit JavaScript anpassen:

- Nicht empfohlen 
    - Binden von click-Ereignissen an `<a>`-HTML-Elemente
    - Abhängigkeit von Azure AD B2C-Code oder -Kommentaren
    - Ändern der Reihenfolge oder Hierarchie von Azure AD B2C-HTML-Elementen. Verwenden Sie eine Azure AD B2C-Richtlinie, um die Reihenfolge der Benutzeroberflächenelemente zu steuern.
- Sie können jeden RESTful-Dienst unter Berücksichtigung der folgenden Überlegungen aufrufen:
    - Möglicherweise müssen Sie die CORS-Einstellung für den RESTful-Dienst so festlegen, dass clientseitige HTTP-Aufrufe zulässig sind.
    - Stellen Sie sicher, dass Ihr RESTful-Dienst sicher ist und nur das Protokoll HTTPS verwendet.
    - Verwenden Sie JavaScript nicht direkt, um Azure AD B2C-Endpunkte aufzurufen.
- Sie können Ihren JavaScript-Code einbetten oder einen Link zu externen JavaScript-Dateien hinzufügen. Stellen Sie bei Verwendung einer externen JavaScript-Datei sicher, dass Sie die absolute URL und keine relative URL verwenden.
- JavaScript-Frameworks:
    - Azure AD B2C verwendet eine [bestimmte Version von jQuery](page-layout.md#jquery-and-handlebars-versions). Fügen Sie keine weitere Version von jQuery hinzu. Wenn mehr als eine Version auf derselben Seite verwendet wird, treten Probleme auf.
    - Die Verwendung von RequireJS wird nicht unterstützt.
    - Die meisten JavaScript-Frameworks werden von Azure AD B2C nicht unterstützt.
- Azure AD B2C-Einstellungen (wie die aktuelle Sprache der Benutzeroberfläche) können durch Aufrufen der Objekte `window.SETTINGS` und `window.CONTENT` gelesen werden. Ändern Sie nicht den Wert dieser Objekte.
- Verwenden Sie zum Anpassen der Azure AD B2C-Fehlermeldung Lokalisierung in einer Richtlinie.
- Wenn etwas durch die Verwendung einer Richtlinie erreicht werden kann, ist dies im Allgemeinen die empfohlene Methode.

## <a name="javascript-samples"></a>JavaScript-Beispiele

### <a name="show-or-hide-a-password"></a>Anzeigen oder Ausblenden eines Kennworts

Eine gängige Methode zur Unterstützung Ihrer Kunden bei der erfolgreichen Registrierung besteht darin, zuzulassen, dass sie sehen können, was sie als Kennwort eingegeben haben. Diese Option unterstützt die Benutzer bei der Registrierung, indem die Benutzer auf einfache Weise ihr Kennwort sehen und bei Bedarf korrigieren können. Jedes Feld des Typs Kennwort verfügt über ein Kontrollkästchen mit der Bezeichnung **Kennwort anzeigen**.  Dadurch kann der Benutzer das Kennwort als Nur-Text anzeigen. Fügen Sie den folgenden Codeausschnitt in Ihre Registrierungs- oder Anmeldevorlage für eine selbstbestätigte Seite ein:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Hinzufügen von Nutzungsbedingungen

Fügen Sie den folgenden Code in die Seite ein, auf der Sie ein Kontrollkästchen für **Nutzungsbedingungen** hinzufügen möchten. Dieses Kontrollkästchen ist in der Regel auf Ihren Seiten für die Registrierung über ein lokales Konto und die Registrierung über ein Konto eines sozialen Netzwerks erforderlich.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Ersetzen Sie im Code den Platzhalter `termsOfUseUrl` durch den Link zu Ihren Nutzungsbedingungen. Erstellen Sie für Ihr Verzeichnis ein neues Benutzerattribut namens **termsOfUse**, und fügen Sie dann **termsOfUse** als Benutzerattribut ein.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über das [Anpassen der Benutzeroberfläche mit HTML-Vorlagen in Azure Active Directory B2C](customize-ui-with-html.md).
