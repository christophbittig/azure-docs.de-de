---
title: Einbetten der Azure Active Directory B2C-Benutzeroberfläche in Ihre App mit einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die Azure Active Directory B2C-Benutzeroberfläche mit einer benutzerdefinierten Richtlinie in Ihre App einbetten.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7874224304f5466c8b166c1e5bd709b915a5d325
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356588"
---
# <a name="embedded-sing-up-or-sign-in-experience"></a>Eingebettetes Registrierungs- oder Anmeldeverfahren

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Um die Registrierung oder Anmeldung zu vereinfachen, können Sie verhindern, dass Benutzer zu einer separaten Registrierungs- oder Anmeldeseite umgeleitet werden oder aber dass ein Popupfenster generiert wird. Mithilfe des HTML-Inlineframe-Elements &lt;iframe&gt; können Sie die Azure AD B2C-Anmeldebenutzeroberfläche direkt in Ihre Webanwendung einbetten. 

> [!TIP]
> Mithilfe des HTML-Elements &lt;iframe&gt; können Sie die [Registrierung oder Anmeldung](add-sign-up-and-sign-in-policy.md) einbetten, [ein Profil bearbeiten ](add-profile-editing-policy.md) oder aber bei benutzerdefinierten Richtlinien in Ihrer Web- oder Single-Page-App [das Kennwort ändern](add-password-change-policy.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Eingebettete Anmeldung in einer Webanwendung

Das Inlineframeelement `<iframe>` wird zum Einbetten eines Dokuments in eine HTML5-Webseite verwendet. Sie können das iframe-Element verwenden, um die Azure AD B2C-Anmeldebenutzeroberfläche wie im folgenden Beispiel gezeigt direkt in Ihre Webanwendung einzubetten:

![Anmelden mit Mauszeiger-DIV](media/embedded-login/login-hovering.png)

Bei der Verwendung des iframe-Elements ist Folgendes zu beachten:

- Bei der eingebetteten Registrierung oder Anmeldung werden nur lokale Konten unterstützt. Die meisten Identitätsanbieter für soziale Netzwerke (z. B. Google und Facebook) verhindern, dass ihre Anmeldeseiten in Inlineframes dargestellt werden können.
- Da Azure AD B2C-Sitzungscookies in einem iframe als Cookies von Drittanbietern gelten, können sie in bestimmten Browsern (z. B. Safari oder Chrome im Inkognito-Modus) entweder blockiert oder gelöscht werden. Dies führt zu unerwünschten Ergebnissen für Benutzer. Um dieses Problem zu vermeiden, müssen Sie sicherstellen, dass der Anwendungsdomänenname und die Azure AD B2C-Domäne den *gleichen Ursprung* haben. Zur Verwendung des gleichen Ursprungs [aktivieren Sie benutzerdefinierte Domänen](custom-domain.md) für den Azure AD B2C-Mandanten, und konfigurieren Sie dann die Webanwendung mit dem gleichen Ursprung. Beispielsweise hat eine Anwendung, die auf https://app.contoso.com gehostet wird, den gleichen Ursprung wie eine Azure AD B2C-Instanz, die unter https://login.contoso.com ausgeführt wird.

## <a name="prerequisites"></a>Voraussetzungen

* Führen Sie die unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy) beschriebenen Schritte aus.
* [Aktivieren Sie benutzerdefinierte Domänen](custom-domain.md) für Ihre Richtlinien.

## <a name="configure-your-policy"></a>Konfigurieren der Richtlinie

Damit die Azure AD B2C-Benutzeroberfläche in einem iframe-Element eingebettet werden kann, müssen eine Inhaltssicherheitsrichtlinie (`Content-Security-Policy`) und Frameoptionen (`X-Frame-Options`) in den HTTP-Antwortheadern in Azure AD B2C eingefügt werden. Mit diesen Headern kann die Azure AD B2C-Benutzeroberfläche unter Ihrem Anwendungsdomänennamen ausgeführt werden.

Fügen Sie ein **JourneyFraming**-Element innerhalb des [RelyingParty](relyingparty.md)-Elements ein.  Das **UserJourneyBehaviors**-Element muss hinter **DefaultUserJourney** eingefügt werden. Das **UserJourneyBehaviors**-Element sollte wie im folgenden Beispiel aussehen:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

Das **Sources**-Attribut enthält den URI Ihrer Webanwendung. Fügen Sie ein Leerzeichen zwischen den URIs ein. Jeder URI muss die folgenden Anforderungen erfüllen:

- Der URI muss vertrauenswürdig sein und zu Ihrer Anwendung gehören.
- Für den URI muss das HTTPS-Schema verwendet werden.  
- Der vollständige URI der Webanwendung muss angegeben werden. Platzhalter werden nicht unterstützt.

Zusätzlich empfiehlt es sich, dass Sie auch die Einbettung Ihres eigenen Domänennamens in einem iframe-Element blockieren, indem Sie die Header „Content-Security-Policy“ und „X-Frame-Options“ auf Ihren Anwendungsseiten festlegen. Dadurch werden Sicherheitsrisiken bei älteren Browsern hinsichtlich der geschachtelten Einbettung von iframes reduziert.

## <a name="adjust-policy-user-interface"></a>Anpassen der Benutzeroberfläche für Richtlinien

Durch die [Anpassung der Benutzeroberfläche](customize-ui.md) in Azure AD B2C haben Sie fast vollständige Kontrolle über die HTML- und CSS-Inhalte, die Benutzern angezeigt werden. Führen Sie die Schritte zum Anpassen einer HTML-Seite mithilfe von Inhaltsdefinitionen aus. Um die Azure AD B2C-Benutzeroberfläche an die iframe-Größe anzupassen, verwenden Sie eine saubere HTML-Seite ohne Hintergrund und zusätzliche Bereiche.  

Mit dem folgenden CSS-Code werden die HTML-Elemente in Azure AD B2C ausgeblendet und die Größe des Panels an den iframe angepasst.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

In einigen Fällen möchten Sie möglicherweise in der Anwendung angeben, welche Azure AD B2C-Seite derzeit angezeigt wird. Wenn ein Benutzer beispielsweise die Registrierungsoption auswählt, können Sie festlegen, dass in der Anwendung dadurch reagiert wird, dass die Links für die Anmeldung mit einem Social Media-Konto ausgeblendet werden oder die iframe-Größe angepasst wird.

Um in der Anwendung die aktuelle Azure AD B2C-Seite anzugeben, [aktivieren Sie die Richtlinie für JavaScript](./javascript-and-page-layout.md), und verwenden Sie HTML5-POST-Meldungen. Mit dem folgenden JavaScript-Code wird eine POST-Meldung mit `signUp` an die Anwendung gesendet:

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Konfigurieren einer Webanwendung

Wenn ein Benutzer die Anmeldeschaltfläche auswählt, wird in der [Web-App](integrate-with-app-code-samples.md#web-apps-and-apis) eine Autorisierungsanforderung generiert, über die der Benutzer den Azure AD B2C-Anmeldevorgang durchführt. Nach Abschluss der Anmeldung wird in Azure AD B2C ein ID-Token oder ein Autorisierungscode an den konfigurierten Umleitungs-URI in Ihrer Anwendung zurückgegeben.

Zur Unterstützung der eingebetteten Anmeldung verweist die iframe-Eigenschaft **src** auf den Anmeldecontroller (z. B. `/account/SignUpSignIn`), der die Autorisierungsanforderung generiert und den Benutzer zur Azure AD B2C-Richtlinie umleitet.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Nach dem Empfang und der Überprüfung des ID-Tokens in der Anwendung ist der Autorisierungsflow abgeschlossen. Der Benutzer wird in der Anwendung erkannt und als vertrauenswürdig eingestuft. Da der Autorisierungsflow innerhalb des iframe-Elements erfolgt, müssen Sie die Hauptseite neu laden. Nachdem die Seite neu geladen wurde, ändert sich die Anmeldeschaltfläche in „Abmelden“ und der Benutzername wird auf der Benutzeroberfläche angezeigt.  

Im folgenden Beispiel ist dargestellt, wie die Hauptseite durch den Umleitungs-URI der Anmeldung aktualisiert werden kann:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Hinzufügen der Anmeldung mit Social Media-Konten in einer Web-App

Identitätsanbieter für soziale Netzwerke verhindern, dass ihre Anmeldeseiten in Inlineframes dargestellt werden können. Sie können eine separate Richtlinie für Social Media-Konten oder ein und dieselbe Richtlinie für die An- und Abmeldung mit lokalen und Social Media-Konten verwenden. Anschließend können Sie den Abfragezeichenfolgenparameter `domain_hint` verwenden. Mit dem Domänenhinweisparameter wird der Benutzer direkt zur Anmeldeseite des Identitätsanbieters für soziale Netzwerke weitergeleitet.

Fügen Sie in Ihrer Anwendung die Schaltflächen für die Anmeldung mit Social Media-Konten hinzu. Wenn ein Benutzer auf eine der Schaltflächen für Social Media-Konten klickt, muss über das Steuerelement der Richtlinienname geändert oder der Domänenhinweisparameter festgelegt werden.

<!-- TBD: add a diagram -->

Der Umleitungs-URI kann identisch mit dem im iframe verwendeten Umleitungs-URI sein. Das Neuladen der Seite kann übersprungen werden.

## <a name="configure-a-single-page-application"></a>Konfigurieren einer Single-Page-Webanwendung

Für eine Single-Page-Webanwendung müssen Sie auch eine zweite „Anmelde“-HTML-Seite erstellen, die im iframe geladen wird. Diese Anmeldeseite hostet den Authentifizierungsbibliothekscode, über den der Autorisierungscode generiert und das Token zurückgegeben wird.

Wenn das Zugriffstoken in der Single-Page-Webanwendung benötigt wird, verwenden Sie JavaScript-Code, um das Zugriffstoken und das Objekt, in dem es enthalten ist, aus dem iframe abzurufen.

> [!NOTE]
> MSAL 2.0 kann derzeit nicht in einem iframe ausgeführt werden.

Der folgende Beispielcode wird auf der Hauptseite ausgeführt und ruft JavaScript-Code eines iframe-Elements auf:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden verwandten Artikeln:

- [Anpassung der Benutzeroberfläche](customize-ui.md)
- Referenz zum [RelyingParty](relyingparty.md)-Element
- [Aktivieren der Richtlinie für JavaScript](./javascript-and-page-layout.md)
- [Codebeispiele](integrate-with-app-code-samples.md)

::: zone-end
