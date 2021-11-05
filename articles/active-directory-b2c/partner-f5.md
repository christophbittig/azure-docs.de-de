---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit F5 BIG-IP
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie Azure AD B2C-Authentifizierung in F5 BIG-IP integrieren, um sicheren Hybridzugriff zu erhalten.
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.service: active-directory
ms.subservice: B2C
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: ca0d912c837a4c3fb218d1bb3fb8b07b43100119
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044625"
---
# <a name="tutorial-extend-azure-active-directory-b2c-to-protect-on-premises-applications-using-f5-big-ip"></a>Tutorial: Erweitern von Azure Active Directory B2C zum Schutz von lokalen Anwendungen mithilfe von F5 BIG-IP

In diesem Beispieltutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) B2C in [F5 BIG-IP Access Policy Manager (APM)](https://www.f5.com/services/resources/white-papers/easily-configure-secure-access-to-all-your-applications-via-azure-active-directory) integrieren. In diesem Tutorial wird gezeigt, wie Legacy-Anwendungen durch BIG-IP-Sicherheit in Kombination mit Azure AD B2C-Vorauthentifizierung, bedingtem Zugriff (Conditional Access, CA) und einmaligem Anmelden (SSO) sicher über das Internet zugänglich gemacht werden können.

F5 Inc. konzentriert sich auf die Bereitstellung, Sicherheit, Leistung und Verfügbarkeit von verbundenen Diensten, einschließlich der Verfügbarkeit von Computing-, Speicher- und Netzwerkressourcen. Die Anwendung bietet Hardware, modularisierte Software und cloudbereite Lösungen für virtuelle Geräte.

Der BIG-IP Application Delivery Controller (ADC) von F5 wird häufig als sicheres Gateway zwischen privaten Netzwerken und dem Internet bereitgestellt.
Er stellt eine Fülle von Funktionen bereit, darunter eine Überprüfung auf Anwendungsebene und vollständig anpassbare Zugriffssteuerungen. Wenn BIG-IP als Reverseproxy bereitgestellt wird, kann die Lösung auch verwendet werden, um sicheren Hybridzugriff auf kritische Geschäftsanwendungen zu ermöglichen, indem Dienste mit einer von APM von F5 verwalteten Verbund-Identitätszugriffsebene als Front-End versehen werden.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Einen [Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Eine vorhandene BIG-IP-Bereitstellung oder Bereitstellen einer [Big-IP Virtual Environment (VE) in Azure](../active-directory/manage-apps/f5-bigip-deployment-guide.md).

- Eine der folgenden F5 BIG-IP-Lizenz-SKUs

  - F5 BIG-IP® Best Bundle

  - Eigenständige Lizenz für F5 BIG-IP Access Policy Manager™.

  - Add-On-Lizenz für F5 BIG-IP Access Policy Manager™ für eine bereits vorhandene Instanz von F5 BIG-IP® Local Traffic Manager™ (LTM).

  - 90 Tage gültige [Testlizenz](https://www.f5.com/trial/big-ip-trial.php) für sämtliche Features von BIG-IP.

- Eine vorhandene headerbasierte Webanwendung oder [Einrichten einer IIS-App](/previous-versions/iis/6.0-sdk/ms525396(v=vs.90)) zum Testen.

- [SSL-Zertifikat](../active-directory/manage-apps/f5-bigip-deployment-guide.md#ssl-profile) zum Veröffentlichen von Diensten über HTTPS oder Verwenden der Standardeinstellung beim Testen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Szenario verwenden wir eine interne Anwendung, deren Zugriff auf den Empfang von HTTP-Autorisierungsheadern von einem Legacy-Brokersystem angewiesen ist, damit die Vertriebsmitarbeiter zu ihren jeweiligen Inhaltsbereichen geleitet werden können.

Der Dienst muss auf eine breitere Verbraucherbasis ausgeweitet werden, sodass für die Anwendung entweder ein Upgrade erforderlich ist, um den Verbrauchern eine Auswahl an Authentifizierungsoptionen zu bieten, oder sie muss ganz durch eine geeignetere Lösung ersetzt werden.

In einer idealen Welt würde die Anwendung aktualisiert, um die direkte Verwaltung und Steuerung über eine moderne Steuerungsebene zu unterstützen. Da es jedoch an jeglicher Form moderner Interoperabilität mangelt, wäre eine Modernisierung mit erheblichem Aufwand verbunden und würde unvermeidliche Kosten und Risiken durch mögliche Ausfallzeiten mit sich bringen.

Stattdessen wird eine BIG-IP Virtual Edition (VE), die zwischen dem öffentlichen Internet und dem internen Azure VNet, mit dem unsere Anwendung verbunden ist, bereitgestellt wird, verwendet, um den Zugriff mit Azure AD B2C mit einer großen Auswahl an Anmelde- und Anmeldemöglichkeiten zu ermöglichen.

Mit einer BIG-IP vor der Anwendung können wir den Dienst mit Azure AD B2C-Vorauthentifizierung und headerbasiertem SSO überlagern und so die Gesamtsicherheit der Anwendung erheblich verbessern, so dass das Unternehmen ohne Unterbrechung weiter wachsen kann.

Die sichere Hybridzugriffslösung für dieses Szenario besteht aus den folgenden Komponenten:

- **Anwendung**: Back-End-Dienst, der durch Azure AD B2C- und sicheren BIG-IP-Hybridzugriff geschützt wird

- **Azure AD B2C**: Der IdP- und OIDC-Autorisierungsserver (Open ID Connect), der für die Überprüfung von Benutzeranmeldeinformationen, Multi-Faktor-Authentifizierung (MFA) und SSO bei BIG-IP APM verantwortlich ist

- **BIG-IP**: Als Reverseproxy für die Anwendung wird der BIG-IP APM auch zum OIDC-Client, der Authentifizierung an den OIDC-Autorisierungsserver delegiert, bevor headerbasiertes SSO mit dem Back-End-Dienst ausgeführt wird.

Die folgende Abbildung veranschaulicht den vom Dienstanbieter initiierten Ablauf für dieses Szenario.

![Screenshot: Der vom Dienstanbieter initiierte Ablauf für dieses Szenario](./media/partner-f5/flow-diagram.png)

|Schritt| BESCHREIBUNG|
|:----|:-------|
| 1. | Der Benutzer stellt eine Verbindung mit dem Anwendungsendpunkt mit BIG-IP als Dienstanbieter her. |
| 2. | Der BIG-IP APM, der den OIDC-Client darstellt, leitet den Benutzer an den Azure AD B2C-Mandantenendpunkt (den OIDC-Autorisierungsserver) um. |
| 3. | Der Azure AD B2C-Mandant authentifiziert den Benutzer vorab und wendet alle erzwungenen Richtlinien für bedingten Zugriff an. |
|4. | Azure AD B2C leitet den Benutzer mit dem Autorisierungscode zurück zum Dienstanbieter. |
| 5. | Der OIDC-Client fordert den Autorisierungsserver auf, Autorisierungscode gegen ein ID-Token zu tauschen. |
| 6. | Der BIG-IP APM gewährt Benutzerzugriff und fügt die HTTP-Header in die Clientanforderung ein, die an die Anwendung weitergeleitet wird. |

Um die Sicherheit zu erhöhen, könnten Organisationen, die dieses Muster verwenden, auch erwägen, den direkten Zugriff auf die Anwendung zu blockieren, sodass ein strikter Pfad über BIG-IP erzwungen wird.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C-Konfiguration

Das Aktivieren von BIG-IP mit Azure AD B2C-Authentifizierung erfordert einen Azure AD B2C-Mandanten mit einem geeigneten Benutzerflow oder einer benutzerdefinierten Richtlinie. [Einrichten eines Azure AD B2C-Benutzerflows](tutorial-create-user-flows.md).

### <a name="create-custom-attributes"></a>Erstellen benutzerdefinierter Attribute

Benutzerdefinierte Attribute können aus verschiedenen Quellen bezogen werden, z. B. direkt aus vorhandenen Azure AD B2C-Benutzerobjekten, von Verbund-IdPs angefordert werden, aus API-Connectors stammen oder während der Anmeldung eines Benutzers erfasst werden. Bei Bedarf können sie in das Token eingebunden werden, das an die Anwendung gesendet wird.

Da Ihre Legacyanwendung bestimmte Attribute erwartet, schließen Sie diese Attribute in Ihren Benutzerflow ein. Sie können diese jedoch durch die Attribute ersetzen, die Ihre Anwendung benötigt. Wenn Sie eine Test-App gemäß den Anweisungen in den Voraussetzungen einrichten, können Sie beliebige Header verwenden, da sie alle angezeigt werden.

1. Melden Sie sich am Portal des Azure AD B2C-Mandanten an.

2. Wählen Sie im linken Bereich **Benutzerattribute** und dann **Hinzufügen** aus, um zwei benutzerdefinierte Attribute zu erstellen.

   - Agent ID: String-**Datentyp**

   - Agent Geo: String-**Datentyp**

### <a name="add-attributes-to-user-flow"></a>Hinzufügen von Attributen zum Benutzerflow

1. Wechseln Sie im linken Bereich zu **Richtlinien** > **Benutzerflows**.

2. Wählen Sie Ihre Richtlinie aus (z.B. **B2C_1_SignupSignin**).

3. Wählen Sie **Benutzerattribute** aus, und fügen Sie beide benutzerdefinierten Attribute sowie das Attribut **Display Name** (Anzeigename) hinzu. Dies sind die Attribute, die während der Benutzeranmeldung erfasst werden.

4. Wählen Sie **Anwendungsansprüche** aus, und fügen Sie beide benutzerdefinierten Attribute sowie das Attribut **Display Name** (Anzeigename) hinzu. Dies sind die Attribute, die an BIG-IP gesendet werden.

Sie können die Funktion [Benutzerflow ausführen](tutorial-create-user-flows.md) im Benutzerflowmenü auf der linken Navigationsleiste verwenden, um zu überprüfen, ob alle definierten Attribute angegeben werden.

### <a name="azure-ad-b2c-federation"></a>Azure AD B2C-Verbund

Damit BIG-IP und Azure AD B2C einander vertrauen können, ist ein Verbund erforderlich, sodass BIG-IP im Azure AD B2C-Mandanten als OIDC-Anwendung registriert werden muss.

1. Wählen Sie im Azure AD B2C-Portal **App-Registrierungen** >  **Neue Registrierung** aus.

2. Geben Sie einen Namen für die Anwendung ein. Beispielsweise **HeaderApp1**.

3. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus.

4. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie den öffentlichen FQDN des zu schützenden Diensts zusammen mit dem Pfad ein.

5. Lassen Sie den Rest unverändert, und wählen Sie **Registrieren** aus.

6. Navigieren Sie zu **Zertifikate und Geheimnisse** >  **+ Neuer geheimer Clientschlüssel**.

7. Geben Sie einen beschreibenden Namen und eine TTL für das Geheimnis an, das von BIG-IP verwendet wird.

8. Notieren Sie sich den geheimen Clientschlüssel, den Sie später zum Konfigurieren von BIG-IP benötigen.

Der Umleitungs-URI ist der BIG-IP-Endpunkt, an den ein Benutzer vom Autorisierungsserver (Azure AD B2C) zurückgeleitet wird, nachdem er sich authentifiziert hat. [Registrieren Sie eine Anwendung](tutorial-register-applications.md) für Azure AD B2C.

## <a name="big-ip-configuration"></a>BIG-IP-Konfiguration

Big-IP bietet mehrere Methoden zum Konfigurieren von sicherem Azure AD-Hybridzugriff, einschließlich einer assistentenbasierten Guided Configuration (geführte Konfiguration), wodurch Zeit und Aufwand für die Implementierung mehrerer gängiger Szenarien minimiert werden. Das workflowgesteuerte Framework bietet eine intuitive Benutzeroberfläche, die auf bestimmte Zugriffstopologien zugeschnitten ist und für die schnelle Veröffentlichung von Webdiensten verwendet wird, die eine minimale Konfiguration für die Veröffentlichung erfordern.

### <a name="version-check"></a>Versionsüberprüfung

Dieses Tutorial basiert auf Guided Configuration v.7/8, kann aber möglicherweise auch für frühere Versionen gelten. Um Ihre Version zu überprüfen, melden Sie sich mit einem Administratorkonto bei der BIG-IP-Webkonfiguration an, und navigieren Sie zu **Access** > **Guided Configuration** (Zugriff > Geführte Konfiguration). Die Version sollte in der oberen rechten Ecke angezeigt werden. Befolgen Sie [diese Anweisungen](https://support.f5.com/csp/article/K85454683), um die Guided Configuration von BIG-IP zu aktualisieren.

### <a name="ssl-profiles"></a>SSL-Profile

Wenn Sie BIG-IP mit einem SSL-Clientprofil konfigurieren, können Sie den clientseitigen Datenverkehr über TLS schützen. Hierzu müssen Sie ein Zertifikat importieren, das mit dem Domänennamen übereinstimmt, der von der öffentlichen URL für Ihre Anwendung verwendet wird. Es wird empfohlen, nach Möglichkeit eine öffentliche Zertifizierungsstelle zu verwenden, aber die integrierten selbstsignierten BIG-IP-Zertifikate können beim Testen ebenfalls verwendet werden.
[Hinzufügen und Verwalten von Zertifikaten](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-ssl-administration-13-0-0.html) in der BIG-IP VE.

## <a name="guided-configuration"></a>Guided Configuration

1. Navigieren Sie in der Webkonfiguration zu **Access** > **Guided Configuration** (Zugriff > Geführte Konfiguration), um den Bereitstellungs-Assistenten zu starten.

2. Wählen Sie **Federation** > **F5 as OAuth Client and Resource Server** (Verbund > F5 als OAuth-Client und Ressourcenserver) aus.

3. Sehen Sie sich die Zusammenfassung des Flows für dieses Szenario an, und klicken Sie dann auf **Next** (Weiter), um den Assistenten zu starten.

### <a name="oauth-properties"></a>OAuth-Eigenschaften

In diesem Abschnitt werden die Eigenschaften definiert, die den Verbund zwischen BIG-IP APM und dem OAuth-Autorisierungsserver (Ihrem Azure AD B2C-Mandanten) ermöglichen. In der gesamten BIG-IP-Konfiguration wird auf OAuth verwiesen, aber die Lösung verwendet tatsächlich OIDC, eine einfache Identitätsebene auf Grundlage des OAuth 2.0-Protokolls, mit der OIDC-Clients die Identität von Benutzern überprüfen und andere Profilinformationen abrufen können.

Achten Sie genau auf die Details, denn jeder Fehler hat Auswirkungen auf Authentifizierung und Zugriff.

#### <a name="configuration-name"></a>Konfigurationsname

Wenn Sie einen Anzeigenamen für die Konfiguration angeben, können Sie zwischen den vielen Bereitstellungskonfigurationen unterscheiden, die möglicherweise in der Guided Configuration vorhanden sein können. Nach dem Festlegen kann der Name nicht mehr geändert werden und ist nur in der Ansicht „Guided Configuration“ sichtbar.

#### <a name="mode"></a>Mode

Der BIG-IP APM dient als OIDC-Client. Wählen Sie daher nur die Option „Client“ aus.

#### <a name="dns-resolver"></a>DNS-Konfliktlöser

Das angegebene Ziel muss in der Lage sein, die öffentlichen IP-Adressen Ihrer Azure AD B2C-Endpunkte aufzulösen. Wählen Sie einen vorhandenen öffentlichen DNS-Konfliktlöser aus, oder erstellen Sie einen neuen Konfliktlöser.

#### <a name="provider-settings"></a>Anbietereinstellungen

Hier konfigurieren wir Azure AD B2C als OAuth2-IdP. Sie werden feststellen, dass die Guided Configuration v8 Azure AD B2C-Vorlagen bietet. Da jedoch mehrere Bereiche fehlen, verwenden wir vorerst einen benutzerdefinierten Typ. F5 plant, die fehlenden Bereiche in einem zukünftigen Update der Guided Configuration einzubinden. Fügen Sie einen neuen Anbieter hinzu, und konfigurieren Sie ihn wie folgt:

- **Allgemeine OAuth-Eigenschaften**

  | Eigenschaften | BESCHREIBUNG |
  |:-------|:---------|
  |OAuth-Anbietertyp | Benutzerdefiniert |
  | Auswählen des OAuth-Anbieters | Neu erstellen (oder einen vorhandenen OAuth-Anbieter verwenden, sofern vorhanden) |
  | Name | Ein eindeutiger Anzeigename für den B2C-IdP. Dieser Name wird Benutzern als Anbieteroption für die Anmeldung angezeigt.|
  | Tokentyp | JSON Web Token |

- **OAuth-Richtlinieneinstellungen**

  | Eigenschaften | BESCHREIBUNG |
  |:-----------|:----------------|
  | `Scope` | Lassen Sie diese Angabe leer. Der OpenID-Bereich zum Anmelden von Benutzern wird automatisch hinzugefügt |
  | Gewährungstyp | Authorization code (Autorisierungscode) |
  | Aktivieren von OpenID Connect | Aktivieren Sie diese Option, um den APM-OAuth-Client in den OIDC-Modus zu versetzen |
  | Attributflusstyp | Authorization code (Autorisierungscode) |

- **OAuth-Anbietereinstellungen**

  Der folgende OpenID-URI bezieht sich auf den Metadatenendpunkt, der von OIDC-Clients verwendet wird, um wichtige IdP-Informationen wie das Rollover von Signaturzertifikaten automatisch zu ermitteln. Suchen Sie den Metadatenendpunkt für Ihren Azure AD B2C-Mandanten, indem Sie zu **App-Registrierungen** > **Endpunkte** navigieren und den Azure AD B2C OpenID Connect-Metadatendokument-URI kopieren. Beispiel: `https://wacketywackb2c .b2clogin.com/<tenantname>.onmicrosoft.com/<policyname>/v2.0/.well-known/openid-configuration`.

  Aktualisieren Sie dann den URI mit Ihren eigenen Eigenschaften: `https://<tenantname>.b2clogin.com/WacketywackB2C.onmicrosoft.com/B2C_1_SignUpIn/v2.0/.well-known/openid-configuration`.

  Fügen Sie diesen URI in den Browser ein, um die OIDC-Metadaten für Ihren Azure AD B2C-Mandanten anzuzeigen.

  | Eigenschaften | BESCHREIBUNG |
  |:----------|:----------|
  | Zielgruppe | Die Client-ID der Anwendung, die BIG-IP in Ihrem Azure AD B2C-Mandanten darstellt. |
  | Authentifizierungs-URI | Der Autorisierungsendpunkt in Ihren B2C-OIDC-Metadaten. |
  | Token-URI | Der Tokenendpunkt in ihren Azure AD B2C-Metadaten. |
  | Userinfo-Anforderungs-URI | Lassen Sie dieses Feld leer. Azure AD B2C unterstützt dieses Feature derzeit nicht. |
  |OpenID-URI | Der OpenID-URI-Metadatenendpunkt, den Sie oben erstellt haben. |
  | Überprüfung abgelaufener Zertifikate ignorieren | Aktivieren Sie dieses Kontrollkästchen nicht. |
  | Selbstsigniertes JWK-Konfigurationszertifikat zulassen | Prüfen |
  | Paket für vertrauenswürdige Zertifizierungsstellen | Wählen Sie „ca-bundle.crt“ aus, um die vertrauenswürdigen F5-Standardzertifizierungsstellen zu verwenden. |
  | Ermittlungsintervall | Geben Sie ein geeignetes Intervall für BIG-IP an, um Ihren Azure AD B2C-Mandanten nach Updates abzufragen. Die minimale Intervallzeit, die von AGC-Version 16.1 0.0.19 final angeboten wird, beträgt 5 Minuten.|

- **OAuth-Servereinstellungen**

  Dieser Abschnitt bezieht sich auf den OIDC-Autorisierungsserver, der Ihr Azure AD B2C-Mandant ist.

  |Eigenschaften | Beschreibungen|
  |:---------|:---------|
  | Client-ID | Die Client-ID der Anwendung, die BIG-IP in Ihrem Azure AD B2C-Mandanten darstellt. |
  | Geheimer Clientschlüssel | Der entsprechende geheime Clientschlüssel der Anwendung. |
  |SSL-Profil des Client-Servers | Durch Festlegen eines SSL-Profils wird sichergestellt, dass der APM mit dem Azure AD B2C-IdP über TLS kommuniziert. Wählen Sie die Standardoption `serverssl` aus. |

- **OAuth-Anforderungseinstellungen**

  Big-IP verfügt interessanterweise über alle erforderlichen Azure AD B2C-Anforderungen im vorkonfigurierten Anforderungssatz. Wir haben jedoch festgestellt, dass für den Build, in dem wir implementiert haben, diese Anforderungen falsch formatiert waren und wichtige Parameter fehlten. Daher haben wir uns dafür entschieden, sie manuell zu erstellen.

- **Tokenanforderung : Aktiviert**

  | Eigenschaften | BESCHREIBUNG |
  |:-----------|:------------|
  | Auswählen der OAuth-Anforderung | Neu erstellen |
  | HTTP-Methode | POST |
  | Aktivieren von Headern| Deaktiviert |
  | Aktivieren von Parametern | Überprüft |

  | Parametertyp | Parametername | Parameterwert|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | nonce | nonce| |
  | redirect-uri | redirect-uri | |
  | scope | scope | |
  | response-type | response-type | |
  | client-secret | client-secret | |
  | custom | grant_type | authorization_code |

- **Authentifizierungsumleitungsanforderung: Aktiviert**

  | Eigenschaften | BESCHREIBUNG |
  |:-----------|:------------|
  | Auswählen der OAuth-Anforderung | Neu erstellen |
  | HTTP-Methode | GET |
  | Art der Eingabeaufforderung | Keine |
  | Aktivieren von Headern | Deaktiviert |
  | Aktivieren von Parametern | Überprüft |

  | Parametertyp | Parametername | Parameterwert|
  |:---------|:---------------|:----------------|
  | client-id | client-id | |
  | redirect-uri | redirect-uri | |
  | response-type |response-type | |
  | scope | scope | |
  | nonce | nonce | |

- **Token refresh request** - **Disabled** (Tokenaktualisierungsanforderung > Deaktiviert): Kann bei Bedarf aktiviert und konfiguriert werden.

- **OpenID UserInfo request** - **Disabled** (OpenID UserInfo-Anforderung > Deaktiviert): Wird derzeit in globalen Azure AD B2C-Mandanten nicht unterstützt.

- **Eigenschaften des virtuellen Servers**

  Ein virtueller BIG-IP-Server muss erstellt werden, um externe Clientanforderungen für den Back-End-Dienst abzufangen, der durch sicheren Hybridzugriff geschützt wird. Dem virtuellen Server muss eine IP-Adresse zugewiesen werden, die dem öffentlichen DNS-Eintrag für den BIG-IP-Dienstendpunkt zugeordnet ist, der die Anwendung darstellt. Verwenden Sie einen vorhandenen virtuellen Server (falls verfügbar), geben Sie andernfalls Folgendes an:

  | Eigenschaften | BESCHREIBUNG |
  |:-----------|:------------|
  | Zieladresse | Private oder öffentliche IP-Adresse, die zum BIG-IP-Dienstendpunkt für die Back-End-Anwendung wird. |
  | Dienstport | HTTPS |
  | Aktivieren des Weiterleitungsports | Aktivieren Sie diese Option, damit Benutzer automatisch von HTTP an HTTPS umgeleitet werden. |
  | Umleitungsport | HTTP |
  | Client-SSL-Profil | Ersetzen Sie das vordefinierte Profil `clientssl` durch das Profil, das Ihr SSL-Zertifikat enthält. Das Testen mit dem Standardprofil ist ebenfalls in Ordnung, verursacht aber wahrscheinlich eine Browserwarnung. |

- **Pooleigenschaften**

  Back-End-Dienste werden in BIG-IP als Pool dargestellt und enthalten mindestens einen Anwendungsserver, an den virtuelle Server eingehenden Datenverkehr weiterleiten. Wählen Sie einen vorhandenen Pool aus, oder erstellen Sie einen neuen Pool.

  | Eigenschaften | BESCHREIBUNG |
  |:-----------|:------------|
  | Lastenausgleichsmethode | Als Roundrobin beibehalten |
  |Poolserver | Interne IP-Adresse der Back-End-Anwendung |
  | Port | Dienstport der Back-End-Anwendung |
  
>[!NOTE]
>Big-IP muss die angegebene Poolserveradresse sehen können.

- **Single sign-on settings (Einstellungen für einmaliges Anmelden)**

  Big-IP unterstützt viele SSO-Optionen, aber im OAuth-Clientmodus ist die Guided Configuration auf Kerberos oder HTTP-Header beschränkt. Aktivieren Sie SSO, und verwenden Sie die folgenden Informationen, damit der APM Eingangsattribute, die Sie zuvor definiert haben, ausgehenden Headern zuordnet.

  | Eigenschaften | BESCHREIBUNG |
  |:-----------|:------------|
  | Headervorgang |`Insert`|
  | Headername | 'name' |
  | Headerwert | `%{session.oauth.client.last.id_token.name}`|
  | Headervorgang | `Insert`|
  |Headername| `agentid`|
  |Headerwert | `%{session.oauth.client.last.id_token.extension_AgentGeo}`|
 
  >[!Note]
  > Bei APM-Sitzungsvariablen, die in geschweiften Klammern definiert sind, wird zwischen Groß-/Kleinschreibung unterschieden. Die Eingabe von „agentid“, wenn der Azure AD B2C-Attributname als „AgentID“ gesendet wird, führt zu einem Attributzuordnungsfehler. Sofern nicht erforderlich, wird empfohlen, alle Attribute in Kleinbuchstaben zu definieren. Im Fall von Azure AD B2C fordert der Benutzerflow den Benutzer zur Eingabe der zusätzlichen Attribute auf, wobei der Name des Attributs verwendet wird, wie er im Portal angezeigt wird, sodass die Verwendung normaler Schreibweise anstelle von Kleinbuchstaben ggf. vorzuziehen ist.

  ![Screenshot: Seite „Single Sign-On Settings“ (SSO-Einstellungen)](./media/partner-f5/single-sign-on.png)

- **Anpassung von Eigenschaften**

  Mit diesen Einstellungen können Sie die Sprache und das Aussehen der Bildschirme anpassen, die Ihren Benutzern angezeigt werden, wenn sie mit dem APM-Zugriffsrichtlinienflow interagieren. Sie können die Bildschirmmeldungen und -eingabeaufforderungen personalisieren, Bildschirmlayouts, Farben, Bilder ändern und Titel, Beschreibungen und Meldungen lokalisieren, die normalerweise in den Zugriffsrichtlinienelementen angepasst werden können.

  Ersetzen Sie die Zeichenfolge „F5 Networks“ im Textfeld „Form Header“ durch den Namen Ihrer eigenen Organisation. Beispiel: „Wacketywack Inc.: Sicherer Hybridzugriff“.

- **Eigenschaften der Sitzungsverwaltung**

  Eine BIG-IP-Sitzungsverwaltungseinstellung wird verwendet, um die Bedingungen zu definieren, unter denen Benutzersitzungen beendet oder fortgesetzt werden dürfen, sowie Grenzwerte für Benutzer und IP-Adressen sowie Fehlerseiten festzulegen. Diese Einstellungen sind optional, aber es wird dringend empfohlen, SLO-Funktionen (Single Log Out, einmaliges Abmelden) zu implementieren, die sicherstellen, dass Sitzungen sicher beendet werden, wenn sie nicht mehr benötigt werden, wodurch das Risiko verringert wird, dass jemand versehentlich nicht autorisierten Zugriff auf veröffentlichte Anwendungen erlangen kann.

## <a name="related-information"></a>Verwandte Informationen

Der letzte Schritt bietet eine Übersicht der Konfigurationen. Wenn Sie auf „Deploy“ (Bereitstellen) klicken, werden Ihre Einstellungen committet und alle erforderlichen BIG-IP- und APM-Objekte erstellt, um sicheren Hybridzugriff auf die Anwendung zu ermöglichen.
Die Anwendung sollte ebenfalls als Zielressource in der Zertifizierungsstelle angezeigt werden. Weitere Informationen finden Sie in der [Anleitung zum Erstellen von Zertifizierungsstellenrichtlinien für Azure AD B2C](conditional-access-identity-protection-overview.md).
Um die Sicherheit zu erhöhen, könnten Organisationen, die dieses Muster verwenden, auch erwägen, den direkten Zugriff auf die Anwendung zu blockieren, damit ein strikter Pfad über BIG-IP erzwungen wird.

## <a name="next-steps"></a>Nächste Schritte

Starten Sie als Benutzer einen Browser, und stellen Sie eine Verbindung mit der externen URL der Anwendung her. Auf der OAuth-Clientanmeldeseite von BIG-IP werden Sie aufgefordert, sich mithilfe von Autorisierungscodegewährung anzumelden. Anweisungen zum Entfernen dieses Schritts finden Sie im Abschnitt zur zusätzlichen Konfiguration.

Sie werden dann umgeleitet, um sich bei Ihrem Azure AD B2C-Mandanten anzumelden und sich zu authentifizieren.

![Screenshot: Benutzeranmeldung](./media/partner-f5/sign-in-message.png)

![Screenshot: Willkommensnachricht nach der Anmeldung](./media/partner-f5/welcome-page.png)

### <a name="supplemental-configurations"></a>Zusätzliche Konfigurationen

**Einmaliges Abmelden (SLO)**

Azure AD B2C unterstützt idP und Anwendungsanmeldung über verschiedene [Mechanismen](session-behavior.md?pivots=b2c-custom-policy#single-sign-out) vollständig.
Wenn die Abmeldefunktion Ihrer Anwendung den Azure AD B2C-Abmeldeendpunkt aufruft, wäre das eine Möglichkeit, SLO umzusetzen. Auf diese Weise können wir gewährleisten, dass Azure AD B2C eine abschließende Weiterleitung an BIG-IP vornimmt, um sicherzustellen, dass die APM-Sitzung zwischen dem Benutzer und der Anwendung ebenfalls beendet wurde.
Eine andere Alternative besteht darin, dass BIG-IP bei der Auswahl der Schaltfläche zum Abmelden von Anwendungen auf die Anforderung lauscht und bei Erkennen der Anforderung gleichzeitig einen Anruf des Azure AD B2C-Abmeldeendpunkts vornimmt. Dieser Ansatz würde verhindern, dass Änderungen an der Anwendung selbst vorgenommen werden müssen, SLO aber trotzdem erreicht wird. Weitere Informationen zum Verwenden von iRules von BIG-IP zum Implementieren dieser Funktion sind [verfügbar](https://support.f5.com/csp/article/K42052145).
In beiden Fällen muss Ihr Azure AD B2C-Mandant den APM-Abmeldeendpunkt kennen. 

1. Navigieren Sie in Ihrem Azure AD B2C-Portal zu **Verwalten** > **Manifest**, und suchen Sie nach der logoutUrl-Eigenschaft. Sie sollte den Wert null aufweisen.

2. Fügen Sie den URI nach der Abmeldung von APM (`https://<mysite.com>/my.logout.php3`) hinzu, wobei `<mysite.com>` der BIG-IP-FQDN für Ihre eigene headerbasierte Anwendung ist.

**Optimierter Anmeldeflow**

Ein optionaler Schritt zur Verbesserung der Benutzeranmeldung wäre das Unterdrücken der OAuth-Anmeldeaufforderung, die Benutzern vor der Azure AD-Vorauthentifizierung angezeigt wird. 

1. Navigieren Sie zu **Access** > **Guided Configuration** (Zugriff > Geführte Konfiguration), und wählen Sie das kleine Vorhängeschlosssymbol ganz rechts in der Zeile für die headerbasierte Anwendung aus, um die strenge Konfiguration zu entsperren.

   ![Screenshot: Optimierter Anmeldeflow](./media/partner-f5/optimized-login-flow.png)

   Das Entsperren der strengen Konfiguration verhindert weitere Änderungen über die Benutzeroberfläche des Assistenten, sodass alle BIG-IP-Objekte, die der veröffentlichten Instanz der Anwendung zugeordnet sind, für direkte Verwaltung geöffnet bleiben.

2. Navigieren Sie zu **Access** > **Profiles/ Policies** > **Access Profiles (Per-session Policies)** (Zugriff > Profile/Richtlinien > Zugriffsprofile (Richtlinien pro Sitzung), und wählen Sie den Bearbeitungslink **Per-Session Policy** (Richtlinie pro Sitzung) für das Richtlinienobjekt der Anwendung aus.

   ![Screenshot: Zugriffsprofile](./media/partner-f5/access-profile.png)

3. Wählen Sie das kleine Kreuz aus, um das Richtlinienobjekt der OAuth-Anmeldeseite zu löschen, und wählen Sie, wenn Sie dazu aufgefordert werden, eine Verbindung mit dem vorherigen Knoten aus.

   ![Screenshot: OAuth-Anmeldeseite](./media/partner-f5/oauth-logon.png)

4. Wählen Sie oben links **Apply Access Policy** (Zugriffsrichtlinie anwenden) aus, und schließen Sie die Registerkarte des visuellen Editors. Der nächste Versuch, eine Verbindung mit der Anwendung herzustellen, sollte Sie direkt zur Azure AD B2C-Anmeldeseite führen.

>[!Note]
>Wenn Sie den strengen Modus erneut aktivieren und eine Konfiguration bereitstellen, werden alle Einstellungen überschrieben, die außerhalb der Benutzeroberfläche der Guided Configuration ausgeführt werden. Daher wird empfohlen, dieses Szenario durch manuelles Erstellen aller Konfigurationsobjekte für Produktionsdienste zu implementieren.

### <a name="troubleshooting"></a>Problembehandlung

Wenn der Zugriff auf die geschützte Anwendung nicht möglich ist, kann das an verschiedenen Faktoren liegen, z. B. an einer falschen Konfiguration.

- BIG-IP-Protokolle sind eine hervorragende Informationsquelle zum Isolieren aller Authentifizierungs- und SSO-Probleme. Bei der Problembehandlung sollten Sie den Ausführlichkeitsgrad des Protokolls erhöhen.

  1. Navigieren Sie zu **Access Policy** > **Overview** > **Event Logs** > **Settings** (Zugriffsrichtlinie > Übersicht > Ereignisprotokolle > Einstellungen).

  2. Wählen Sie die Zeile für Ihre veröffentlichte Anwendung und dann **Edit** > **Access System Logs** (Bearbeiten > Auf Systemprotokolle zugreifen) aus.

  3. Wählen Sie **Debug** (Debuggen) in der SSO-Liste und dann **OK** aus. Sie können ihr Problem jetzt reproduzieren, bevor Sie sich die Protokolle ansehen. Denken Sie jedoch daran, dies nach Abschluss wieder rückgängig zu machen.

- Wenn unmittelbar nach der erfolgreichen Azure AD B2C-Authentifizierung ein mit Big-IP gekennzeichneter Fehler angezeigt wird, ist es möglich, dass sich das Problem auf das einmalige Anmelden aus Azure AD bei BIG-IP bezieht.

  1. Navigieren Sie zu **Access** > **Overview** > **Access reports** (Zugriff > Übersicht > Zugriffsberichte).

  2. Führen Sie den Bericht für die letzte Stunde aus, um zu untersuchen, ob die Protokolle Hinweise liefern. Der Link „View session variables“ (Sitzungsvariablen anzeigen) für Ihre Sitzung hilft auch zu verstehen, ob APM die erwarteten Ansprüche von Azure AD empfängt.

- Wenn keine BIG-IP-Fehlerseite angezeigt wird, hängt das Problem wahrscheinlich eher mit der Back-End-Anforderung oder dem einmaligen Anmelden von BIG-IP bei der Anwendung zusammen.

  1. Navigieren Sie zu **Access Policy** > **Overview** > **Active Sessions** (Zugriffsrichtlinie > Übersicht > Aktive Sitzungen).

  2. Wählen Sie den Link für Ihre aktive Sitzung aus.

- Der Link „View Variables“ (Variablen anzeigen) an dieser Stelle kann auch bei der Ermittlung der Grundursache helfen, insbesondere dann, wenn die der APM von BIG-IP die richtigen Sitzungsattribute nicht abrufen kann.
Die Protokolle Ihrer Anwendung helfen dann zu verstehen, ob sie diese Attribute als Header empfangen hat.

- Beachten Sie bei Verwendung von Guided Configuration v8 ein bekanntes Problem, das nach erfolgreicher Azure AD B2C-Authentifizierung den folgenden BIG-IP-Fehler generiert.  

  ![Screenshot der Fehlermeldung](./media/partner-f5/error-message.png)

  Dies ist ein Richtlinienverstoß, weil BIG-IP die Signatur des Tokens, das von Azure AD B2C ausgestellt wurde, nicht überprüfen kann. Dasselbe Zugriffsprotokoll sollte in der Lage sein, weitere Details zum Problem bereitzustellen.

  ![Screenshot: Zugriffsprotokolle](./media/partner-f5/access-log.png)

  Die genaue Grundursache wird noch vom F5-Engineering untersucht, aber das Problem scheint damit zusammenzuhängen, dass der AGC die Auto JWT-Einstellung während des Bereitstellens nicht aktiviert und dadurch verhindert, dass der APM die aktuellen Tokensignaturschlüssel abruft.

  Bis zur Lösung besteht eine Möglichkeit zum Umgehen des Problems darin, diese Einstellung manuell zu aktivieren. 

  1. Navigieren Sie zu **Access** > **Guided Configuration** (Zugriff > Geführte Konfiguration), und wählen Sie das kleine Vorhängeschlosssymbol ganz rechts in der Zeile für Ihre headerbasierte Anwendung aus.

  2. Navigieren Sie bei entsperrter verwalteter Konfiguration zu **Access** > **Federation** > **OAuth Client/Resource Server** > **Providers** (Zugriff > Verbund > OAuth-Client-/-Ressourcenserver > Anbieter).

  3. Wählen Sie den Anbieter für Ihre Azure AD B2C-Konfiguration aus.

  4. Aktivieren Sie das Kontrollkästchen **Use Auto JWT** (Auto JWT verwenden), und wählen Sie dann **Discover** (Ermitteln) und anschließend **Save** (Speichern) aus.

    Nun sollte das Feld „Key (JWT)“ (Schlüssel (JWT)) mit der Schlüssel-ID (KID) des Tokensignaturzertifikats aufgefüllt werden, das über die OpenID-URI-Metadaten bereitgestellt wird.
  
  5. Wählen Sie schließlich oben links neben dem F5-Logo die gelbe Option **Apply Access Policy** (Zugriffsrichtlinie anwenden) aus. Wenden Sie diese Einstellungen an, und wählen Sie erneut **Apply** (Anwenden) aus, um die Zugriffsprofilliste zu aktualisieren.

Weitere Tipps zur Problembehandlung von [OAuth-Client- und -Ressourcenservern](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-oauth-configuration-14-1-0/apm-oauth-client-and-resource-server.html#GUID-774384BC-CF63-469D-A589-1595D0DDFBA2) finden Sie in den Anleitungen von F5.
