---
title: Verbund mit einem SAML/WS-Fed-Identitätsanbieter (IdP) für B2B – Azure AD
description: Herstellen eines direkten Verbunds mit einem SAML- oder WS-Verbund-Identitätsanbieter, damit sich Gäste bei Ihren Azure AD-Apps anmelden können
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8a535f7c74e32e59918badbda7747f24a1756f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623986"
---
# <a name="federation-with-samlws-fed-identity-providers-for-guest-users-preview"></a>Verbund mit SAML/WS-Fed-Identitätsanbietern für Gastbenutzer (Vorschau)

> [!NOTE]
>- Ein *Direkter Verbund* in Azure Active Directory wird jetzt als *SAML/WS-Fed-Identitätsanbieter-Verbund (IdP)* bezeichnet.
>- Der SAML/WS-Fed-IdP-Verbund ist eine öffentliche Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dieser Artikel beschreibt, wie Sie einen Verbund mit jedem Unternehmen einrichten können, deren Identitätsanbieter (IdP) das SAML 2.0- oder WS-Fed-Protokoll unterstützt. Wenn Sie einen Verbund mit dem IdP eines Partners einrichten, können neue Gastbenutzer aus dieser Domäne ihr eigenes, vom IdP verwaltetes Unternehmenskonto verwenden, um sich bei Ihrem Azure AD-Mandanten anzumelden und mit Ihnen zusammenzuarbeiten. Es ist nicht erforderlich, dass der Gastbenutzer ein separates Azure AD-Konto erstellt.

> [!IMPORTANT]
> - Wir haben die Einschränkung entfernt, die erforderte, dass die Authentifizierungs-URL-Domäne der Zieldomäne entspricht oder von einem zulässigen IdP stammt. Weitere Informationen finden Sie unter [Schritt 1: Ermitteln, ob der Partner seine DNS-Textdatensätze aktualisieren muss](#step-1-determine-if-the-partner-needs-to-update-their-dns-text-records).
>-  Es wird nun empfohlen, dass der Partner die Zielgruppe des SAML- oder WS-Fed-basierten Identitätsanbieters auf eine mandantenbasierte Zielgruppe festgelegt. Weitere Informationen finden Sie weiter unten in den Abschnitten zu den erforderlichen [SAML 2.0](#required-saml-20-attributes-and-claims) und [WS-Fed](#required-ws-fed-attributes-and-claims)-Attributen und -Ansprüchen.

## <a name="when-is-a-guest-user-authenticated-with-samlws-fed-idp-federation"></a>Wann wird ein Gastbenutzer mit einem SAML/WS-Fed-Identitätsanbieter-Verbund authentifiziert?

Nachdem Sie den Verbund mit dem SAML/WS-Fed-Identitätsprovider eines Unternehmens eingerichtet haben, werden alle neuen Gastbenutzer, die Sie einladen, mithilfe des SAML/WS-Fed-Identitätsproviders authentifiziert. Es ist wichtig zu beachten, dass durch das Einrichten des Verbunds nicht die Authentifizierungsmethode für Gastbenutzer geändert wird, die bereits eine Einladung von Ihnen eingelöst haben. Im Folgenden finden Sie einige Beispiele:

 - Wenn Gastbenutzer bereits Einladungen von Ihnen eingelöst haben und Sie anschließend den Verbund mit deren Unternehmen einrichten, verwenden diese Gastbenutzer weiterhin dieselbe Authentifizierungsmethode, die sie vor dem Einrichten des Verbunds verwendet haben.
 - Wenn Sie den Verbund mit einem SAML/WS-Fed IdP des Unternehmens einrichten und Gastbenutzer einladen und das Partnerunternehmen dann später zu Azure AD wechselt, verwenden die Gastbenutzer, die bereits Einladungen eingelöst haben, weiterhin den SAML/WS-Fed IdP. Und zwar solange, wie die Verbundrichtlinie in Ihrem Mandanten vorhanden ist.
 - Wenn Sie den Verbund mit dem SAML/WS-Fed IdP eines Unternehmens löschen, können sich alle Gastbenutzer, die derzeit den SAML/WS-Fed IdP verwenden, nicht anmelden.

In jedem dieser Szenarien können Sie die Authentifizierungsmethode eines Gastbenutzers aktualisieren, indem Sie [seinen Einlösungsstatus zurücksetzen](reset-redemption-status.md).

Der SAML/WS-Fed IdP-Verbund ist an Domänen-Namespaces, z. B. „contoso.com“ und „fabrikam.com“, gebunden. Wenn Sie einen Verbund mit AD FS oder einem Drittanbieter-IdP einrichten, ordnen Unternehmen diesem IdP einen oder mehrere Domänen-Namespaces zu.

## <a name="end-user-experience"></a>Endbenutzererfahrung 

Mit dem SAML/WS-Fed Identitätsprovider-Verbund melden sich Gastbenutzer mit ihrem eigenen Unternehmenskonto bei Ihrem Azure AD-Mandanten an. Wenn sie auf freigegebene Ressourcen zugreifen und zur Anmeldung aufgefordert werden, werden Benutzer zu ihrem IdP umgeleitet. Nach erfolgreicher Anmeldung werden sie an Azure AD zurückgeleitet, um auf Ressourcen zuzugreifen. Ihre Aktualisierungstoken sind 12 Stunden lang gültig, wobei es sich um die [Standarddauer für Pass-Through-Aktualisierungstoken](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties) in Azure AD handelt. Wenn der Verbundidentitätsanbieter einmaliges Anmelden (SSO) aktiviert hat, erfährt der Benutzer auch SSO, und es wird nach der ersten Authentifizierung keine Anmeldeaufforderung mehr angezeigt.

## <a name="sign-in-endpoints"></a>Endpunkte für die Anmeldung

Beim SAML/WS-Fed IdP-Verbund können sich Gastbenutzer nun mithilfe eines [gemeinsamen Endpunkts](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (d. h. mit einer allgemeinen App-URL, die Ihren Mandantenkontext nicht enthält) bei Ihren mehrmandantenfähigen Anwendungen oder bei Microsoft-Erstanbieter-Apps anmelden. Beim Anmeldevorgang wählt der Gastbenutzer zuerst **Anmeldeoptionen** und dann **Bei einer Organisation anmelden** aus. Der Benutzer gibt dann den Namen Ihres Unternehmens ein und setzt den Vorgang mit seinen eigenen Anmeldeinformationen fort.

Beim SAML/WS-Fed IdP-Verbund können Gastbenutzer auch Anwendungsendpunkte verwenden, die Ihre Mandanteninformationen enthalten, z. B.:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Sie können Gastbenutzern auch einen direkten Link zu einer Anwendung oder Ressource zur Verfügung stellen, indem Sie Ihre Mandanteninformationen einfügen, z. B. `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="can-i-set-up-samlws-fed-idp-federation-with-azure-ad-verified-domains"></a>Kann ich einen SAML/WS-Fed-IdP-Verbund mit überprüften Azure AD-Domänen einrichten?
Nein, wir blockieren den SAML/WS-Fed-IdP-Verbund für überprüfte Azure AD-Domänen zugunsten von nativen von Azure AD verwalteten Domänenfunktionen. Im Azure-Portal oder in PowerShell wird ein Fehler angezeigt, wenn Sie versuchen, einen SAML/WS-Fed-IdP-Verbund mit einer Domäne einzurichten, die in Azure AD DNS-verifiziert ist.

### <a name="can-i-set-up-samlws-fed-idp-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Kann ich einen SAML/WS-Fed IdP-Verbund mit einer Domäne einrichten, für die ein nicht verwalteter (E-Mail verifizierten) Mandant vorhanden ist? 
Ja, Sie können einen SAML/WS-Fed-IdP-Verbund mit Domänen einrichten, die in Azure AD nicht DNS-verifiziert sind, einschließlich nicht verwalteter (per E-Mail verifizierter oder „viraler“) Azure AD-Mandanten. Derartige Mandanten werden erstellt, wenn ein Benutzer eine B2B-Einladung einlöst oder eine Self-Service-Registrierung für Azure AD mit einer Domäne durchführt, die derzeit nicht vorhanden ist. Wenn die Domäne nicht verifiziert wurde und der Mandant keine [Übernahme durch den Administrator](../enterprise-users/domains-admin-takeover.md) durchlaufen hat, können Sie einen Verbund mit dieser Domäne einrichten.

### <a name="how-many-federation-relationships-can-i-create"></a>Wie viele Verbundbeziehungen kann ich erstellen?
Derzeit werden maximal 1.000 Verbundbeziehungen unterstützt. Dieses Limit umfasst sowohl [interne Verbunde](/powershell/module/msonline/set-msoldomainfederationsettings) als auch SAML/WS-Fed IdP-Verbunde.

### <a name="can-i-set-up-federation-with-multiple-domains-from-the-same-tenant"></a>Kann ich einen Verbund mit mehreren Domänen desselben Mandanten einrichten?
Der SAML/WS-Fed IdP-Verbund mit mehreren Domänen desselben Mandanten wird derzeit nicht unterstützt.

### <a name="do-i-need-to-renew-the-signing-certificate-when-it-expires"></a>Muss ich das Signaturzertifikat erneuern, wenn es abläuft?
Wenn Sie die Metadaten-URL in den IdP-Einstellungen angeben, verlängert Azure AD das Signaturzertifikat automatisch, wenn es abläuft. Wenn das Zertifikat jedoch aus irgendeinem Grund vor der Ablaufzeit rotiert wird, oder wenn Sie keine Metadaten-URL bereitstellen, kann Azure AD es nicht verlängern. In diesem Fall müssen Sie das Signaturzertifikat manuell aktualisieren.

### <a name="if-samlws-fed-idp-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Welche Methode hat Vorrang, wenn die Authentifizierung mittels SAML/WS-Fed IdP-Verbund und mit der E-Mail-Einmalkennung aktiviert ist?
Wenn ein SAML/WS-Fed IdP-Verbund mit einem Partnerunternehmen eingerichtet ist, hat dieser Vorrang vor der Authentifizierung per E-Mail-Einmalkennung für neue Gastbenutzer dieses Unternehmens. Wenn ein Gastbenutzer eine Einladung mit Authentifizierung mittels Einmalkennung eingelöst hat, bevor Sie den SAML/WS-Fed IdP-Verbund einrichten, wird er die Authentifizierung mit Einmalkennung weiterhin verwenden.

### <a name="does-samlws-fed-idp-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Behandelt der SAML/WS-Fed IdP-Verbund Anmeldeprobleme aufgrund eines teilweise synchronisierten Mandanten?
Nein, in diesem Szenario sollte die Funktion [E-Mail-Einmalkennung](one-time-passcode.md) verwendet werden. Ein „teilweise synchronisierter Mandant“ bezieht sich auf einen Azure AD-Partnermandanten, bei dem lokale Benutzeridentitäten nicht vollständig mit der Cloud synchronisiert sind. Ein Gast, dessen Identität noch nicht in der Cloud vorhanden ist, der aber versucht, Ihre B2B-Einladung einzulösen, kann sich dann nicht anmelden. Die Einmalkennungsfunktion gestattet diesem Gast die Anmeldung. Die SAML/WS-Fed IdP-Verbundfunktion behandelt Szenarien, in denen der Gast über ein eigenes vom IdP verwaltetes Unternehmenskonto verfügt, das Unternehmen aber über gar keine Azure AD-Präsenz verfügt.

### <a name="once-samlws-fed-idp-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Wenn der SAML/WS-Fed IdP-Verbund mit einem Unternehmen konfiguriert ist, muss dann jedem Gast eine individuelle Einladung gesendet werden, die dieser dann einlösen muss?
Durch das Einrichten des SAML/WS-Fed IdP-Verbunds wird nicht die Authentifizierungsmethode für Gastbenutzer geändert, die bereits eine Einladung von Ihnen eingelöst haben. Sie können die Authentifizierungsmethode eines Gastbenutzers aktualisieren, indem Sie [seinen Einlösungsstatus zurücksetzen](reset-redemption-status.md).

### <a name="is-there-a-way-to-send-a-signed-request-to-the-saml-identity-provider"></a>Gibt es eine Möglichkeit, eine signierte Anforderung an den SAML-Identitätsanbieter zu senden?
Derzeit unterstützt das Azure AD-SAML/WS-Fed-Verbundfeature das Senden eines signierten Authentifizierungstokens an den SAML-Identitätsanbieter nicht.

## <a name="step-1-determine-if-the-partner-needs-to-update-their-dns-text-records"></a>Schritt 1: Ermitteln, ob der Partner seine DNS-Textdatensätze aktualisieren muss

Je nach seinem IdP muss der Partner möglicherweise seine DNS-Einträge aktualisieren, um einen Verbund mit Ihnen zu aktivieren. Verwenden Sie die folgenden Schritte, um zu ermitteln, ob DNS-Updates erforderlich sind.

1. Wenn der IdP des Partners einer der folgenden zulässigen IdP ist, sind keine DNS-Änderungen erforderlich (Änderungen an der Liste sind vorbehalten):

     - accounts.google.com
     - pingidentity.com
     - login.pingone.com
     - okta.com
     - oktapreview.com
     - okta-emea.com
     - my.salesforce.com
     - federation.exostar.com
     - federation.exostartest.com
     - idaptive.app
     - idaptive.qa

2. Wenn der IdP nicht zu den oben aufgeführten zulässigen Anbietern gehört, überprüfen Sie die IdP-Authentifizierungs-URL des Partners, um festzustellen, ob die Domäne mit der Zieldomäne oder einem Host innerhalb der Zieldomäne übereinstimmt. Anders ausgedrückt: Beim Einrichten eines Verbunds für `fabrikam.com` gilt Folgendes:

     - Wenn die Authentifizierungs-URL `https://fabrikam.com` oder `https://sts.fabrikam.com/adfs` (ein Host in derselben Domäne) lautet, sind keine DNS-Änderungen erforderlich.
     - Wenn die Authentifizierungs-URL `https://fabrikamconglomerate.com/adfs`  oder  `https://fabrikam.com.uk/adfs` lautet, stimmt die Domäne nicht mit der fabrikam.com-Domäne überein. Der Partner muss seiner DNS-Konfiguration somit einen TXT-Eintrag für die Authentifizierungs-URL hinzufügen.

    > [!IMPORTANT]
    > Es gibt ein bekanntes Problem mit dem folgenden Schritt. Derzeit wird die Blockierung der Authentifizierung nicht aufgehoben, wenn der Domäne des Verbund-IdP ein DNS-TXT-Eintrag hinzugefügt wird. Wir arbeiten aktiv daran, dieses Problem zu beheben.

3. Wenn gemäß dem vorherigen Schritt DNS-Änderungen erforderlich sind, bitten Sie den Partner, den DNS-Einträgen seiner Domäne einen TXT-Eintrag hinzuzufügen, wie im folgenden Beispiel gezeigt:

   `fabrikam.com.  IN   TXT   DirectFedAuthUrl=https://fabrikamconglomerate.com/adfs`

## <a name="step-2-configure-the-partner-organizations-idp"></a>Schritt 2: Konfigurieren des IdPs des Partnerunternehmens

Als Nächstes muss Ihr Partnerunternehmen ihren IdP mit den erforderlichen Ansprüchen und Vertrauensstellungen der vertrauenden Seite konfigurieren.

> [!NOTE]
> Wir verwenden als Beispiel Active Directory-Verbunddienste (AD FS), um zu veranschaulichen, wie SAML/WS-Fed IdP für einen Verbund konfiguriert wird. Beispiele zum Konfigurieren von AD FS als SAML 2.0 oder WS-Fed-IdP in der Vorbereitung für einen Verbunds finden Sie unter [Konfigurieren eines SAML/WS-Fed IdP-Verbunds mit AD FS](direct-federation-adfs.md).

### <a name="saml-20-configuration"></a>SAML 2.0-Konfiguration

Azure AD B2B kann so konfiguriert werden, dass es einen Verbund mit Identitätsanbietern bildet, die das SAML-Protokoll mit den unten aufgeführten bestimmten Anforderungen verwenden. Weitere Informationen zum Einrichten einer Vertrauensstellung zwischen Ihrem SAML-IdP und Azure AD, finden Sie unter [Verwenden eines SAML 2.0-Identitätsanbieters (IdP) für das einmalige Anmelden](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> Die Zieldomäne für den SAML/WS-Fed-IdP-Verbund darf nicht in Azure AD DNS-verifiziert sein. Weitere Informationen finden Sie im Abschnitt [Häufig gestellte Fragen](#frequently-asked-questions).

#### <a name="required-saml-20-attributes-and-claims"></a>Erforderliche SAML 2.0-Attribute und -Ansprüche
In den folgenden Tabellen sind die Anforderungen für bestimmte Attribute und Ansprüche aufgeführt, die bei dem Drittanbieter-IdP konfiguriert werden müssen. Die folgenden Attribute müssen in der SAML 2.0-Antwort vom Identitätsanbieter empfangen werden, um einen Verbund einzurichten. Diese Attribute können durch Verlinkung mit der XML-Datei des Online-Sicherheitstokendiensts oder durch manuelle Eingabe konfiguriert werden.

Erforderliche Attribute für die SAML 2.0-Antwort des Identitätsanbieters:

|attribute  |Wert  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Zielgruppe     |`https://login.microsoftonline.com/<tenant ID>/` (Empfohlene Mandanten-Benutzergruppe.) Ersetzen Sie `<tenant ID>` durch die Mandanten-ID des Azure AD-Mandanten, mit dem Sie einen Verbund einrichten.<br><br>`urn:federation:MicrosoftOnline` (Dieser Wert ist veraltet.)          |
|Issuer (Aussteller)     |Der Aussteller-URI des Partneridentitätsanbieters, z. B. `http://www.example.com/exk10l6w90DHM0yi...`         |


Erforderliche Ansprüche für das vom Identitätsanbieter ausgegebene SAML 2.0-Token:

|attribute  |Wert  |
|---------|---------|
|NameID-Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Verbund-Konfiguration

Azure AD B2B kann so konfiguriert werden, dass es einen Verbund mit Identitätsanbietern bildet, die das WS-Fed-Protokoll mit den unten aufgeführten bestimmten Anforderungen verwenden. Derzeit sind die beiden WS-Verbund-Anbieter auf Kompatibilität mit Azure AD getestet, einschließlich AD FS und Shibboleth. Weitere Informationen zum Aufbau einer Vertrauensstellung der vertrauenden Seite zwischen einem WS-Verbund-kompatiblen Anbieter mit Azure AD finden Sie in dem „Artikel zur STS-Integration unter Verwendung von WS-Protokollen“, der in den [Dokumenten zur Kompatibilität von Azure AD-Identitätsanbietern](https://www.microsoft.com/download/details.aspx?id=56843) verfügbar ist.

> [!NOTE]
> Die Zieldomäne für den Verbund darf nicht in Azure AD DNS-verifiziert sein. Weitere Informationen finden Sie im Abschnitt [Häufig gestellte Fragen](#frequently-asked-questions).

#### <a name="required-ws-fed-attributes-and-claims"></a>Erforderliche WS-Verbund-Attribute und -Ansprüche

In den folgenden Tabellen sind die Anforderungen für bestimmte Attribute und Ansprüche aufgeführt, die bei dem Drittanbieter WS-Fed-IdP konfiguriert werden müssen. Die folgenden Attribute müssen in der WS-Fed-Nachricht vom Identitätsanbieter empfangen werden, um einen Verbund einzurichten. Diese Attribute können durch Verlinkung mit der XML-Datei des Online-Sicherheitstokendiensts oder durch manuelle Eingabe konfiguriert werden.

Erforderliche Attribute in der WS-Verbund-Nachricht vom Identitätsanbieter:
 
|attribute  |Wert  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Zielgruppe     |`https://login.microsoftonline.com/<tenant ID>/` (Empfohlene Mandanten-Benutzergruppe.) Ersetzen Sie `<tenant ID>` durch die Mandanten-ID des Azure AD-Mandanten, mit dem Sie einen Verbund einrichten.<br><br>`urn:federation:MicrosoftOnline` (Dieser Wert ist veraltet.)          |
|Issuer (Aussteller)     |Der Aussteller-URI des Partneridentitätsanbieters, z. B. `http://www.example.com/exk10l6w90DHM0yi...`         |

Erforderliche Ansprüche für das vom Identitätsanbieter ausgegebene WS-Verbund-Token:

|attribute  |Wert  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-3-configure-samlws-fed-idp-federation-in-azure-ad"></a>Schritt 3: Konfigurieren des SAML/WS-Fed-IdP-Verbunds in Azure AD

Als Nächstes konfigurieren Sie den Verbund mit dem in Schritt 1 in Azure AD konfigurierten Identitätsanbieter. Sie können das Azure AD-Portal oder PowerShell verwenden. Es kann 5–10 Minuten dauern, bis die Verbundrichtlinie wirksam wird. Versuchen Sie während dieser Zeit nicht, eine Einladung für die Verbunddomäne einzulösen. Die folgenden Attribute sind erforderlich:

- Aussteller-URI des Partneridentitätsanbieters
- Passiver Authentifizierungsendpunkt des Partneridentitätsanbieters (nur HTTPS wird unterstützt)
- Zertifikat

### <a name="to-configure-federation-in-the-azure-ad-portal"></a>So konfigurieren Sie den Verbund im Azure AD-Portal

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** > **Alle Identitätsanbieter** aus.
3. Wählen Sie **Neuer SAML-/WS-Fed-IdP** aus.

    ![Screenshot der Schaltfläche zum Hinzufügen eines neuen SAML- oder WS-Verbund-Identitätsanbieters](media/direct-federation/new-saml-wsfed-idp.png)

4. Wählen Sie auf der Seite **Neuer SAML-/WS-Fed-IdP** unter **Identitätsanbieterprotokoll** die Option **SAML-** oder **WS-FED** aus.

    ![Screenshot der Schaltfläche „Analysieren“ auf der SAML- oder WS-Fed IdP-Seite](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Geben Sie den Domänennamen Ihres Partnerunternehmens ein, der der Zieldomänenname für den Verbund ist.
6. Sie können eine Metadatendatei hochladen, um Metadatendetails aufzufüllen. Wenn Sie sich entschließen, Metadaten manuell einzugeben, geben Sie die folgenden Informationen ein:
   - Domänenname des Partneridentitätsanbieters
   - Entitäts-ID des Partneridentitätsanbieters
   - Passiver Requestorendpunkt des Partneridentitätsanbieters
   - Zertifikat
   > [!NOTE]
   > Metadaten-URL ist optional, wird aber dringend empfohlen. Wenn Sie die Metadaten-URL angeben, kann Azure AD das Signaturzertifikat automatisch verlängern, wenn es abläuft. Wenn das Zertifikat aus irgendeinem Grund vor der Ablaufzeit rotiert wird, oder wenn Sie keine Metadaten-URL bereitstellen, kann Azure AD es nicht verlängern. In diesem Fall müssen Sie das Signaturzertifikat manuell aktualisieren.

7. Wählen Sie **Speichern** aus. 

### <a name="to-configure-samlws-fed-idp-federation-in-azure-ad-using-powershell"></a>So konfigurieren Sie SAML/WS-Fed IdP-Verbund in Azure AD mit PowerShell

1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). Wenn Sie detaillierte Schritte benötigen, enthält der Schnellstart die Anleitung [PowerShell-Modul](b2b-quickstart-invite-powershell.md#prerequisites).
2. Führen Sie den folgenden Befehl aus:

   ```powershell
   Connect-AzureAD
   ```

3. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an.
4. Führen Sie die folgenden Befehle aus, und ersetzen Sie dabei die Werte aus der Verbundmetadatendatei. Für AD FS-Server und Okta lautet die Verbunddatei „federationmetadata.xml“, z. B.: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-4-test-samlws-fed-idp-federation-in-azure-ad"></a>Schritt 4: Testen des SAML/WS-Fed-IdP-Verbunds in Azure AD
Testen Sie nun Ihre Einrichtung des Verbunds, indem Sie einen neuen B2B-Gastbenutzer einladen. Detailinformationen finden Sie unter [Hinzufügen von Azure AD B2B-Zusammenarbeitsbenutzern im Azure-Portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-samlws-fed-idp-federation-relationship"></a>Gewusst wie – eine SAML/WS-Fed IdP-Verbundbeziehung bearbeiten

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** aus.
3. Wählen Sie **Alle Identitätsanbieter** aus.
4. Wählen Sie unter **SAML-/WS-Fed-IdP** den Anbieter aus.
5. Aktualisieren Sie die Werte im Bereich mit den Identitätsanbieterdetails.
6. Wählen Sie **Speichern** aus.


## <a name="how-do-i-remove-federation"></a>Gewusst wie – einen Verbund entfernen

Sie können Ihre Verbundeinrichtung entfernen. Wenn Sie das tun, können sich Verbundgastbenutzer, die ihre Einladungen bereits eingelöst haben, nicht mehr anmelden. Sie können ihnen jedoch den Zugriff auf ihre Ressourcen zurückgeben, indem Sie [ihren Einlösungsstatus zurücksetzen](reset-redemption-status.md). So entfernen Sie einen Verbund mit einem Identitätsanbieter im Azure AD-Portal:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Wählen Sie im linken Bereich **Azure Active Directory** aus.
2. Wählen Sie **Externe Identitäten** aus.
3. Wählen Sie **Alle Identitätsanbieter** aus.
4. Wählen Sie den Identitätsanbieter und dann **Löschen** aus.
5. Wählen Sie **Ja** aus, um den Löschvorgang zu bestätigen. 

So entfernen Sie den Verbund mit einem Identitätsanbieter mit PowerShell:

1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Führen Sie den folgenden Befehl aus:

   ```powershell
   Connect-AzureAD
   ```

3. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an.
4. Geben Sie den folgenden Befehl ein:

   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Umgebung zum Einlösen von Einladungen](redemption-experience.md), wenn sich externe Benutzer mit verschiedenen Identitätsanbietern anmelden.
