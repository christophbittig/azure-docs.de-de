---
title: F5 BIG-IP APM und Azure AD SSO für formularbasierte Authentifizierungsanwendungen
description: Erfahren Sie, wie Sie den BIG-IP-Zugriffsrichtlinienverwalter (APM) von F5 integrieren und Azure Active Directory einen sicheren Hybridzugriff auf formularbasierte Anwendungen ermöglichen.
author: gargi-sinha
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8306c9f0035eababdcf5feb115786982d78f0d52
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137172"
---
# <a name="tutorial-integrate-azure-active-directory-with-f5-big-ip-for-forms-based-authentication-single-sign-on"></a>Tutorial: Integration von Azure Active Directory mit F5 BIG-IP für formularbasierte Authentifizierung mit einmaligem Anmelden

In diesem Tutorial erfahren Sie, wie Sie den BIG-IP-Zugriffsrichtlinienverwalter (APM) von F5 integrieren und Azure Active Directory (Azure AD) einen sicheren Hybridzugriff auf formularbasierte Anwendungen ermöglichen.

Die Integration von veröffentlichten BIG-IP-Anwendungen in Azure AD bietet viele Vorteile, darunter:

- Verbesserte Zero Trust-Governance durch die Vorauthentifizierung und Autorisierung durch Azure AD

- Vollständiges einmaliges Anmelden (Single Sign-On, SSO) zwischen Azure AD und veröffentlichten Big-IP-Diensten

- Verwalten der Identitäten und des Zugriffs über eine zentrale Steuerungsebene: das [Azure-Portal](https://portal.azure.com)

Weitere Informationen zu allen Vorteilen finden Sie im Artikel zur [F5 BIG-IP und Azure AD Integration.](f5-aad-integration.md) und [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../active-directory-appssoaccess-whatis.md).

## <a name="scenario-description"></a>Beschreibung des Szenarios

Für dieses Szenario verfügen wir über eine interne Legacy-Anwendung, die für die formularbasierte Authentifizierung (FBA) konfiguriert ist.

Das ideale Szenario wäre, die Anwendung direkt über Azure AD zu verwalten und zu steuern. Da es jedoch an jeglicher Form moderner Protokollinteroperabilität mangelt, würde die Modernisierung erhebliche Anstrengungen und Zeit in Anspruch nehmen und unvermeidliche Kosten und Risiken potenzieller Downtimes mit sich bringen.

Stattdessen wird eine BIG-IP Virtual Edition (VE) zwischen dem Internet und dem internen Azure VNet, mit dem die Anwendung verbunden ist, eingesetzt, um den eingehenden Zugriff zu steuern, wobei Azure AD für seine umfangreichen Authentifizierungs- und Autorisierungsfunktionen genutzt wird.

Mit einer BIG-IP vor der Anwendung können wir den Dienst mit Azure AD-Vorauthentifizierung und formularbasiertem SSO überlagern und so die Gesamtsicherheit der Anwendung erheblich verbessern, so dass das Unternehmen ohne Unterbrechung weiter tätig sein kann.

Die vom BIG-IP APM zwischengespeicherten Benutzeranmeldeinformationen sind dann für SSO mit anderen formularbasierten Authentifizierungsanwendungen verfügbar.

## <a name="scenario-architecture"></a>Szenario-Architektur

Die sichere Hybridzugriffslösung für dieses Szenario besteht aus den folgenden Komponenten:

**Anwendung**: Back-End-Dienst, der durch Azure AD - und sicheren BIG-IP-Hybridzugriff geschützt wird Diese spezielle Anwendung validiert Benutzeranmeldeinformationen anhand von Active Directory (AD), aber dies kann jedes beliebige Verzeichnis sein, einschließlich LDS (AD Lightweight Directory Services), Open Source usw.

**Azure AD**: Der SAML-Identitätsanbieter (IDP), der für die Überprüfung der Benutzeranmeldeinformationen, des bedingten Zugriffs (Conditional Access, CA) und des einmaligen Anmeldens bei big-IP-APM verantwortlich ist.

**BIG-IP**: Reverse Proxy und SAML Service Provider (SP) für die Anwendung, wobei die Authentifizierung an den SAML IdP delegiert wird, bevor ein formularbasiertes einmaliges Anmelden für die Backend-Anwendung durchgeführt wird.

![Screenshot des Flussdiagramms](./media/f5-big-ip-forms-advanced/flow-diagram.png)

| Schritte | Beschreibung|
|:-------|:----------|
| 1. | Der Benutzer stellt eine Verbindung mit dem SAML SP-Endpunkt der Anwendung (BIG-IP APM) her.|
| 2. | BIG-IP leitet den Benutzer zur Vorabauthentifizierung an den SAML-Identitätsanbieter (Azure AD) weiter.|
| 3. | Azure AD authentifiziert die Benutzer und wendet alle erzwungenen Richtlinien für bedingten Zugriff an.|
| 4. | Der Benutzer wird mit dem ausgestellten Token und den Ansprüchen zum SAML SP zurückgeleitet. |
| 5. | BIG-IP fordert den Benutzer zur Eingabe des Anwendungskennworts auf und speichert es im Cache. |
| 6. | BIG-IP sendet eine Anforderung an die Anwendung und erhält ein Anmeldeformular.|
| 7. | APM-Scripting reagiert automatisch auf die Eingabe von Benutzername und Passwort vor dem Absenden des Formulars.|
| 8. | Die Anwendungsnutzlast wird vom Webserver bedient und an den Client gesendet. Optional erkennt APM eine erfolgreiche Anmeldung, indem der Antwortheader untersucht und nach einer Cookie- oder Umleitungs-URI gesucht wird.|

## <a name="prerequisites"></a>Voraussetzungen

Eine vorherige BIG-IP-Erfahrung ist nicht erforderlich, aber Sie benötigen:

- Eine kostenloses Abonnement (oder höher) von Azure AD

- Eine vorhandene BIG-IP-Adresse oder [Bereitstellen einer BIG-IP Virtual Edition (VE) in Azure](f5-bigip-deployment-guide.md)

- Eine der folgenden F5 BIG-IP-Lizenz-SKUs

  - F5 BIG-IP&reg;-Paket „Best“

  - Eigenständige Lizenz für F5 BIG-IP Access Policy Manager&trade; (APM)

  - Add-On-Lizenz für F5 BIG-IP Access Policy Manager&trade; (APM) für eine bereits vorhandene Instanz von F5 BIG-IP&reg; Local Traffic Manager&trade; (LTM)

  - 90 Tage gültige [Testlizenz](https://www.f5.com/trial/big-ip-trial.php) für sämtliche Features von BIG-IP.

- Benutzeridentitäten   aus dem lokalen Verzeichnis mit Azure AD [synchronisiert](../hybrid/how-to-connect-sync-whatis.md)

- Ein Konto mit den [Berechtigungen](../roles/permissions-reference.md#application-administrator) eines Azure AD-Anwendungsadministrators

- [SSL-Zertifikat](f5-bigip-deployment-guide.md#ssl-profile) zum Veröffentlichen von Diensten über HTTPS oder Verwenden von Standardzertifikaten beim Testen

- Eine vorhandene formularbasierte Authentifizierungsanwendung oder [Einrichtung einer IIS-FBA-App](/troubleshoot/aspnet/forms-based-authentication) zu Testzwecken

## <a name="deployment-modes"></a>Bereitstellungsmodi

Es gibt mehrere Methoden zur Konfiguration eines BIG-IP für dieses Szenario. In diesem Tutorial wird der erweiterte Ansatz behandelt, der einen flexibleren Ansatz für die Implementierung eines sicheren Hybridzugriffs bietet, indem alle BIG-IP-Konfigurationsobjekte manuell erstellt werden. Dieser Ansatz eignet sich für Szenarien, die nicht von der vorlagenbasierten Guided Configuration abgedeckt werden.

>[!NOTE]
>Alle Beispielzeichenfolgen und -werte in diesem Artikel sollten durch die tatsächlichen Werte für Ihre Umgebung ersetzt werden.

## <a name="adding-f5-big-ip-from-the-azure-ad-gallery"></a>Hinzufügen von F5 BIG-IP aus dem Azure AD-Anwendungskatalog

Das Einrichten einer SAML-Verbundvertrauensstellung zwischen BIG-IP APM und Azure AD ist einer der ersten Schritte bei der Implementierung des sicheren Hybridzugriffs. Es legt die Integration fest, die für BIG-IP erforderlich ist, um die Vorauthentifizierung und die [Zertifizierungsstelle](../conditional-access/overview.md) an Azure AD, bevor zugriff auf den veröffentlichten Dienst gewährt wird.

1. Melden Sie sich beim **Azure-Portal** mit einem Konto mit Administratorrechten für die Anwendung an

2. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.

3. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie auf dem oberen Menüband **+ Neue Anwendung** aus.

4. Suchen Sie im Katalog nach **F5**, und wählen Sie die **Azure AD-Integration für F5 BIG-IP APM** aus.

5. Geben Sie einen Namen für die Anwendung an, und wählen Sie dann **Hinzufügen/Erstellen** aus, um sie Ihrem Mandanten hinzuzufügen. Der Name sollte den jeweiligen Dienst widerspiegeln,

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

1. Navigieren Sie in der Ansicht der Eigenschaften der neuen **F5**-Anwendung zu **Manage** >  **Single sign-on** (Verwalten > Einmaliges Anmelden).

2. Wählen Sie auf der Seite **Methode für einmaliges Anmelden auswählen** die Option **SAML** aus, und überspringen Sie die Aufforderung zum Speichern der Einstellungen für einmaliges Anmelden, indem Sie **Nein, ich speichere es später** auswählen.

3. Wählen Sie im Menü **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die folgenden Informationen anzugeben:

   a. Ersetzen Sie die vordefinierte **Bezeichner-URL** durch die URL für Ihren veröffentlichten BIG-IP-Dienst. Zum Beispiel, `https://myvacation.contoso.com`

   b. Gehen Sie mit der **Antwort-URL** genauso vor, aber geben Sie den Pfad für den SAML-Endpunkt von APM an. Zum Beispiel, `https://myvacation.contoso.com/saml/sp/profile/post/acs`

      >[!NOTE]
      >In dieser Konfiguration würde der SAML-Fluss im IdP-initiierten Modus arbeiten, in dem Azure AD dem Benutzer eine SAML-Assertion ausstellt, bevor er an den BIG-IP-Serviceendpunkt für die Anwendung weitergeleitet wird. Big-IP APM unterstützt sowohl den IdP- als auch den SP-initiierten Modus.

   c. Geben Sie für `Logout URI` die Abmelde-URL den SLO-Endpunkt (Single logout, einmaliges Abmelden) vom BIG-IP APM ein, dem der Hostheader des veröffentlichten Diensts vorangestellt ist. `Providing an SLO URI` stellt sicher, dass die BIG-IP-APM-Sitzung des Benutzers auch nach der Abmeldung von Azure AD beendet wird. Zum Beispiel, `https://myvacation.contoso.com/saml/sp/profile/redirect/slr`

     ![Screenshot der bearbeiteten SAML-Konfiguration](./media/f5-big-ip-forms-advanced/basic-saml-configuration.png)

     >[!Note]
     > In TMOS v16 wurde der SAML-SLO-Endpunkt in „/saml/sp/profile/redirect/slo“ geändert.

4. Wählen Sie vor dem Beenden des SAML-Konfigurationsmenüs **Speichern** aus, und überspringen Sie die SSO-Testaufforderung.

   Beachten Sie die Eigenschaften im Abschnitt **Benutzerattribute und Ansprüche**, da Azure AD diese den Benutzern für die BIG-IP APM-Authentifizierung und SSO für die Backend-Anwendung ausstellt.

5. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** die Schaltfläche **Download** aus, um die Datei **Verbund-XML-Metadaten** auf Ihrem Computer zu speichern.

   ![Screenshot des Links zum Herunterladen von Verbundmetadaten](./media/f5-big-ip-forms-advanced/saml-certificate.png)

   Von Azure AD erstellte SAML-Signaturzertifikate haben eine Lebensdauer von 3 Jahren und sollten mithilfe des veröffentlichten [Leitfadens](manage-certificates-for-federated-single-sign-on.md) verwaltet werden.

### <a name="azure-ad-authorization"></a>Azure AD-Autorisierung

Standardmäßig gibt Azure AD nur Token für Benutzer aus, denen der Zugriff auf eine Anwendung gewährt wurde.

1. Wählen Sie in der Konfigurationsansicht der Anwendung die Option **Benutzer und Gruppen** aus.

2. Wählen Sie **+** **Benutzer hinzufügen** und anschließend im Menü **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

3. Fügen Sie im Dialogfeld **Benutzer und Gruppen** die Gruppen von Benutzern hinzu, die für den Zugriff auf die interne Anwendung autorisiert sind. Wählen Sie danach **Auswählen** \> **Zuweisen** aus.

Damit ist der Teil der SAML-Verbundvertrauensstellung in Azure AD abgeschlossen. Der BIG-IP APM kann nun so eingerichtet werden, dass die interne Webanwendung veröffentlicht und mit den entsprechenden Eigenschaften konfiguriert wird, um die SAML-Vorauthentifizierung und SSO abzuschließen.

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

### <a name="service-provider"></a>Dienstanbieter

Diese Einstellungen definieren die SAML SP-Eigenschaften, die APM zum Überlagern der Legacy-Anwendung mit der SAML-Vorauthentifizierung verwendet.

1. Wählen Sie **Access** > **Federation** > **SAML Service Provider** > **Local SP Services** > **Create** aus.

   ![Screenshot der F5-Formularkonfiguration](./media/f5-big-ip-forms-advanced/f5-forms-configuration.png)

2. **Geben Sie einen Namen** und genau dieselbe **Entitäts-ID** an, die in Azure AD zuvor definiert wurde.

   ![Sceenshot eines neuen saml sp-Diensts](./media/f5-big-ip-forms-advanced/saml-sp-service.png)

    **Die Einstellungen für den SP-Namen** sind nur erforderlich, wenn die Entitäts-ID nicht exakt mit dem Hostnamensteil der veröffentlichten URL übereinstimmt oder wenn kein reguläres Format für die Hostnamen-URL verwendet wird. Geben Sie das externe Schema und den Hostnamen der zu veröffentlichenden Anwendung an, wenn die Entitäts-ID `urn:myvacation:contosoonline` ist.

### <a name="external-idp-connector"></a>Externer IdP-Connector

Ein SAML IdP-Connector definiert die Einstellungen, die erforderlich sind, damit der BIG-IP-APM Azure AD ALS SAML-IDP akzeptiert. Diese Einstellungen ordnen den SAML SP einem SAML-IdP zu und richten die Verbundvertrauensstellung zwischen APM und Azure AD ein.

1. Scrollen Sie nach unten zum neuen SAML-SP-Objekt, und wählen Sie **Bind/UnBind IDP Connectors** (IDP-Connectors binden/trennen) aus.

   ![Sceenshot lokaler SP-Dienste](./media/f5-big-ip-forms-advanced/local-services.png)

2. Wählen Sie **Create New IDP Connector** (Neuen IDP-Connector erstellen) und dann im Dropdownmenü den Eintrag **From Metadata** (Aus Metadaten) aus.

   ![Sceenshot der Dropdownliste „Metadaten“](./media/f5-big-ip-forms-advanced/from-metadata.png)
  
3. Navigieren Sie zur XML-Datei mit den Verbundmetadaten, die Sie zuvor heruntergeladen haben, und geben Sie für das APM-Objekt, das den externen SAML-IDP darstellt, einen **Identity Provider Name** (Identitätsanbietername) an. Zum Beispiel, `MyVacation\_AzureAD`

   ![Sceenshot des neuen idp saml-Connectors](./media/f5-big-ip-forms-advanced/new-idp-saml-connector.png)

4. Wählen Sie **Neue Zeile hinzufügen** aus, um den neuen **SAML IdP-Connector** und dann **Update** auszuwählen.
  
   ![Sceenshot: Hinzufügen einer neuen Zeile](./media/f5-big-ip-forms-advanced/add-new-row.png)

5. Wählen Sie auf **OK aus, um die Einstellungen zu speichern**.

   ![Sceenshot der Bearbeitung der saml idp, die diesen Sp verwendet](./media/f5-big-ip-forms-advanced/edit-saml-idp-using-sp.png)

### <a name="forms-based-sso"></a>Formularbasiertes einmaliges Anmelden (SSO)

Das SSO für den FBA kann entweder in dem von dem Client initiierten Modus oder von BIG-IP selbst ausgeführt werden. Beide Methoden emulieren die Benutzeranmeldung, indem sie vor dem automatischen Absenden des Formulars Anmeldeinformationen in die Tags für den Benutzernamen und das Kennwort einfügen. Der Flow ist fast transparent, mit der Ausnahme, dass Benutzer beim Zugriff auf eine FBA-Anwendung ihr Kennwort einmal angeben müssen. Das Kennwort wird dann für die Wiederverwendung in anderen FBA-Anwendungen zwischengespeichert.

Dies deckt den APM-Ansatz ab, bei dem SSO direkt für die Back-End-Anwendung verarbeitet wird.

Wählen Sie **Access** > **Single Sign-on** > **Forms Based** > **Create** aus und geben Sie Folgendes an:

|Eigenschaft | BESCHREIBUNG |
|:------|:---------|
| Name | Ein SSO-APM-Objekt kann von anderen veröffentlichten Anwendungen wiederverwendet werden. Verwenden Sie daher einen beschreibenden Namen für die Konfiguration. Zum Beispiel,|
| Verwenden der SSO-Vorlage | Keine |
| Username Source (Quelle des Benutzernamens) | Die bevorzugte Benutzernamequelle zum Vorabfüllen des Formulars für die Kennwortsammlung. Jede APM-Sitzungsvariable kann verwendet werden, aber die Standardvariable `session.sso.token.last.username` funktioniert in der Regel am besten, da sie den Azure AD UPN des angemeldeten Benutzers enthält |
| Kennwortquelle | Belassen Sie den Standardwert `session.sso.token.last.password`, da dies die APM-Variable ist, die die BIG-IP zum Zwischenspeichern des von Benutzern bereitgestellten Kennworts verwendet. |

![Sceenshot einer neuen SSO-Konfiguration](./media/f5-big-ip-forms-advanced/new-sso-configuration.png)

|Eigenschaft | BESCHREIBUNG |
|:------|:---------|
| Start-URI | Der Anmelde-URI Ihrer FBA-Anwendung. Die formularbasierte APM-Authentifizierung führt SSO aus, wenn der Anforderungs-URI diesem URI-Wert entspricht.|
| Formularaktionen | Lassen Sie das Feld leer, damit die ursprüngliche Anforderungs-URL für SSO verwendet wird |
| Formularparameter für Benutzername | Der Elementname des Benutzernamenfelds Ihrer Anmeldeformulare. Verwenden Sie die Entwicklungstools Ihres Browsers, um dieen zu ermitteln.| 
| Formularparameter für Kennwort | Der Elementname des Benutzernamenfelds Ihrer Anmeldeformulare. Verwenden Sie auch hier Entwicklungstools.|

![Sceenshot einer SSO-Methodenkonfiguration](./media/f5-big-ip-forms-advanced/sso-method-configuration.png)

![Sceenshot der Anmeldeseite von contoso my vacation](./media/f5-big-ip-forms-advanced/contoso-example.png)

Weitere Informationen zum Konfigurieren eines APM für FBA SSO finden Sie [hier](https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-access-policy-manager-single-sign-on-concepts-configuration-14-1-0/single-sign-on-methods.html#GUID-F8588DF4-F395-4E44-881B-8D16EED91449).

### <a name="access-profile-configuration"></a>Konfigurieren von Zugriffsprofilen

Ein Zugriffsprofil bindet viele APM-Elemente, die den Zugriff auf virtuelle BIG-IP-Server verwalten, einschließlich Zugriffsrichtlinien, SSO-Konfiguration und Benutzeroberflächeneinstellungen.

1. Navigieren Sie zu **Access** > **Profiles/Policies** > **Access Profiles (Per-Session Policies)**  > **Erstellen** aus, um die folgenden Informationen anzugeben:

   | Eigenschaft | Beschreibung |
   |:-----|:-------|
   | Name | Zum Beispiel, `MyVacation` |
   |Profiltyp | Alle |
   | SSO Configuration | Das soeben erstellte FBA-SSO-Konfigurationsobjekt|
   |Akzeptierte Sprache | Fügen Sie mindestens eine Sprache hinzu|

   ![Sceenshot zeigt das Erstellen eines neuen Zugriffsprofils](./media/f5-big-ip-forms-advanced/create-new-access-profile.png)

2. Ändern Sie die Sitzungsrichtlinie, um eine Anmeldeseite mit dem bereits ausgefüllten Benutzernamen anzuzeigen. Wählen Sie den Link **Bearbeiten** für das Profil pro Sitzung aus, das Sie gerade erstellt haben, um den Visual Policy Editor (VPE) von APM zu starten.

   ![Sceenshot zeigt die Richtlinie „Pro Sitzung bearbeiten“](./media/f5-big-ip-forms-advanced/edit-per-session-policy.png)

3. Wählen Sie nach dem Start der VPE das **+** Vorzeichen neben dem Fallback aus.

   ![Sceenshot zeigt + neben dem Fallback an, sobald vpe gestartet wurde.](./media/f5-big-ip-forms-advanced/vpe-launched.png)

4. Wählen Sie im Popup **Authentifizierung** > **SAML-Authentifizierung** > **Add Item** (Element hinzufügen) aus.

   ![Sceenshot zeigt die Schaltfläche saml auth and add items (Saml-Authentifizierung und Elemente hinzufügen)](./media/f5-big-ip-forms-advanced/saml-auth-add-item.png)

5. Ändern Sie in der **SAML-Authentifizierungs-SP** den Namen in **Azure AD Authentifizierung,** und legen Sie den **AAA-Server** so fest, dass er das zuvor erstellte SAML SP-Objekt verwendet.

   ![Sceenshot zeigt die Einstellungen des Azure AD-Authentifizierungsservers](./media/f5-big-ip-forms-advanced/azure-ad-auth-server.png)

6. Wählen Sie das **+** Anmeldezeichen für den Branch **Erfolgreich** aus.

7. Wählen Sie im Popup **Authentifizierung** > **Anmeldungsseite** > **Add Item** (Element hinzufügen) aus.

   ![Sceenshot zeigt Einstellungen der Anmeldeseite](./media/f5-big-ip-forms-advanced/logon-page.png)

8. Ändern Sie die Option **Schreibgeschützt** für das Feld Benutzername in **Ja**.

   ![Sceenshot zeigt, dass schreibgeschützt auf „Ja“ festgelegt ist](./media/f5-big-ip-forms-advanced/set-read-only-as-yes.png)

9. Fügen Sie ein SSO Credential Mapping-Objekt hinzu, indem Sie das Vorzeichen für **+** das Fallback der Anmeldeseite auswählen.

10. Wählen Sie im Popupfenster die **Zuweisung** > **SSO-Anmeldeinformationen** > **Element hinzufügen** aus.

    ![Sceenshot zeigt SSO-Anmeldeinformationszuordnungsinformationen](./media/f5-big-ip-forms-advanced/sso-credential-mapping.png)

11. Lassen Sie die Standardeinstellungen im Popupfenster angezeigt, und fahren Sie fort.

    ![Sceenshot zeigt SSO-Anmeldeinformationszuordnungsinformationen](./media/f5-big-ip-forms-advanced/save-sso-credential-mapping.png)

12. Wählen Sie den oberen Link **Deny** (Verweigern) aus, um die Branch **Erfolgreich** auf **Allow** > **Save** (Zulassen und Speichern) zu ändern.

**Attributzuordnung**

Das Hinzufügen einer LogonID_Mapping-Konfiguration ist zwar optional, ermöglicht aber, dass in der BIG-IP-Liste der aktiven Sitzungen der UPN des angemeldeten Benutzers anstelle einer Sitzungsnummer angezeigt wird Dies ist nützlich für die Analyse von Protokollen oder die Problembehandlung.

1. Wählen Sie das **+** Symbol für den Branch SAML Auth Successful (SAML-Authentifizierung **Erfolgreich** aus.

2. Wählen Sie im Popupfenster **Assignment** > **Variable Assign** > **Add Item** (Arbeitsauftrag, Variable zu weisen, Element hinzufügen) aus.

   ![Sceenshot zeigt Informationen zum Zuweisen von Variablen](./media/f5-big-ip-forms-advanced/variable-assign.png)

3. Geben Sie einen beschreibenden Namen an, und wählen Sie im Abschnitt **Variablenzuweisung** die Option **Neue Eintragsänderung** > **ändern** aus. Beispiel: `LogonID_Mapping`.

   ![Sceenshot zeigt Hinzufügen eines neuen Eingabefelds](./media/f5-big-ip-forms-advanced/add-new-entry.png)

4. Legen Sie beide Variablen so fest, dass Folgendes verwendet wird:

   | Eigenschaft | BESCHREIBUNG |
   |:-----|:-------|
   | Benutzerdefinierte Variable | `session.logon.last.username` |
   | Sitzungsvariable | `session.saml.last.identity`|

   Wählen Sie anschließend **Fertig** > **Speichern** aus.

5. Committen Sie diese Einstellungen, indem Sie **Apply Access Policy** (Zugriffsrichtlinie anwenden) in der oberen linken Ecke auswählen, und schließen Sie dann den visuellen Richtlinien-Editor.

   ![Sceenshot zeigt die Anwendung der Zugriffsrichtlinie](./media/f5-big-ip-forms-advanced/apply-access-policy.png)

### <a name="backend-pool-configuration"></a>Konfiguration des Back-End-Pools

Damit BIG-IP weiß, wohin Der Clientdatenverkehr weitergeleitet werden soll, müssen Sie ein BIG-IP-Knotenobjekt erstellen, das den Back-End-Server darstellt, der Ihre Anwendung hosten soll. Platzieren Sie diesen Knoten anschließend in einem BIG-IP-Serverpool.

1. Wählen Sie **Local Traffic** > **Pools** > **Pool List** > **Create** aus, und geben Sie einen Namen für ein Serverpoolobjekt an. Beispiel: `MyApps_VMs`.

   ![Sceenshot zeigt Poolliste an](./media/f5-big-ip-forms-advanced/pool-list.png)

2. Fügen Sie ein Poolmitgliedsobjekt mit den folgenden Angaben hinzu:

   | Eigenschaft | BESCHREIBUNG |
   |:-----|:-------|
   | Node Name (Knotenname) | Optionaler Anzeigename für den Server, der die Back-End-Webanwendung hostet |
   | Adresse | IP-Adresse des Servers, der die Anwendung hostet |
   | Service Port (Dienstport) | Der HTTP/S-Port, den die Anwendung abhört |

   ![Sceenshot zeigt Poolliste](./media/f5-big-ip-forms-advanced/pool-member.png)

>[!NOTE]
>Integritätsmonitore erfordern eine zusätzliche [Konfiguration](https://support.f5.com/csp/article/K13397), die in diesem Tutorial nicht behandelt wird.

## <a name="virtual-server-configuration"></a>Konfiguration des virtuellen Servers

Ein virtueller Server ist ein BIG-IP-Datenebenenobjekt, das durch eine virtuelle IP-Adresse repräsentiert wird und auf Client-Anfragen an die Anwendung wartet. Jeder empfangene Datenverkehr wird verarbeitet und anhand des APM-Zugriffsprofils ausgewertet, das dem virtuellen Server zugeordnet ist, bevor er gemäß den Richtlinienergebnissen und -einstellungen weitergeleitet wird.

1. Wählen Sie **Local Traffic** > **Virtual Servers** > **Virtual Server List** > **Create** (Lokaler Datenverkehr > Virtuelle Server > Liste virtueller Server Erstellen) aus.

2. Geben Sie dem virtuellen Server einen **Namen**,eine nicht verwendete IP-Adresse IPv4/IPv6, die big-IP zugewiesen werden kann, um Clientdatenverkehr zu empfangen, und legen Sie den **Dienstport** auf 443 fest.

   ![Sceenshot zeigt virtuellen Server](./media/f5-big-ip-forms-advanced/virtual-server.png)

3. **HTTP-Profil**: Legen Sie dieses auf http fest.

4. **SSL-Profil (Client)** : Aktiviert Transport Layer Security (TLS) und ermöglicht die Veröffentlichung von Diensten über HTTPS. Wählen Sie das Client-SSL-Profil aus, das Sie als Teil der Vorbedingungen erstellt haben, oder belassen Sie die Standardeinstellung, wenn Sie testen.

   ![Sceenshot zeigt SSL-Profil](./media/f5-big-ip-forms-advanced/ssl-profile.png)

5. Ändern Sie die **Quelladressenübersetzung** in **Automatische Zuordnung**.

   ![Sceenshot zeigt automatische Zuordnung](./media/f5-big-ip-forms-advanced/auto-map.png)

6. Legen Sie unter **Zugriffsrichtlinie** das zuvor erstellte **Zugriffsprofil** fest. Dadurch werden das Azure AD SAML-Vorauthentifizierungsprofil und die FBA-SSO-Richtlinie an den virtuellen Server gebunden.

   ![Sceenshot zeigt die Anwendung der Zugriffsrichtlinie](./media/f5-big-ip-forms-advanced/access-policy.png)

7. Legen Sie abschließend den **Standardpool** auf das Back-End-Poolobjekt fest, das sie im vorherigen Abschnitt erstellt haben.

   ![Sceenshot zeigt Standardpool](./media/f5-big-ip-forms-advanced/default-pool.png)

## <a name="session-management"></a>Sitzungsverwaltung

Mit den Einstellungen für die Sitzungsverwaltung von BIG-IPs werden die Bedingungen festgelegt, unter denen Benutzersitzungen beendet oder fortgesetzt werden dürfen, sowie Grenzen für Benutzer und IP-Adressen und Fehlerseiten. Sie können Ihre eigene Richtlinie erstellen, indem Sie auf **Zugriffsrichtlinie** > **Zugriffsprofile** klicken und Ihre Anwendung aus der Liste auswählen.

Im Hinblick auf die SLO-Funktionalität wird durch die Definition einer Single Log-Out URI in Azure AD sichergestellt, dass eine vom IdP initiierte Abmeldung vom MyApps-Portal auch die Sitzung zwischen dem Client und dem BIG-IP APM beendet.

Nach dem Import der Verbund-Metadaten der Anwendung stellt xml dem APM den Azure AD SAML SLO-Endpunkt für vom SP initiierte Sign-outs zur Verfügung. Damit dies wirklich effektiv ist, muss der APM genau wissen, wann sich ein Benutzer abmeldet.

Nehmen wir ein Szenario, in dem ein BIG-IP-Webportal nicht verwendet wird und der Benutzer keine Möglichkeit hat, den APM anzuweisen, sich abzumelden. Selbst wenn sich der Benutzer von der Anwendung selbst abmeldet, ist dies für BIG-IP technisch nicht erkennbar, sodass die Anwendungssitzung problemlos über SSO wiederhergestellt werden kann. Aus diesem Grund muss die SP-initiierte Abmeldesitzung sorgfältig überdingt werden, um sicherzustellen, dass Sitzungen sicher beendet werden, wenn sie nicht mehr benötigt werden.

Eine Möglichkeit, dies zu erreichen, ist das Hinzufügen einer SLO-Funktion zu Ihrer Abmeldeschaltfläche für Anwendungen, damit ihr Client an den Azure AD SAML-Abmeldeendpunkt umgeleitet werden kann. Den SAML-Abmeldeendpunkt für Ihren Mandanten finden Sie unter **App Registrations** > **Endpoints**.

Wenn eine Änderung der Anwendung nicht möglich ist, sollte die BIG-IP auf den Abmeldeaufruf der Anwendung warten und bei Erkennung der Anforderung SLO auslösen. Weitere Informationen zur Verwendung von BIG-IP iRules finden Sie im Artikel [K42052145](https://support.f5.com/csp/article/K42052145) und im [Artikel K12056](https://support.f5.com/csp/article/K12056) von F5.

## <a name="summary"></a>Zusammenfassung

Ihre Anwendung sollte nun veröffentlicht und über einen sicheren hybriden Zugang zugänglich sein, entweder direkt über ihre URL oder über die Anwendungsportale von Microsoft.

Die Anwendung sollte ebenfalls als Zielressource in der Zertifizierungsstelle angezeigt werden. Weitere Informationen finden Sie in dem [Leitfaden](../conditional-access/concept-conditional-access-policies.md) zum Erstellen von Zertifizierungsstellenrichtlinien.

Um die Sicherheit zu erhöhen, könnten Organisationen, die dieses Muster verwenden, auch erwägen, den direkten Zugriff auf die Anwendung zu blockieren, damit ein strikter Pfad über BIG-IP erzwungen wird.

## <a name="next-steps"></a>Nächste Schritte

Verbinden Sie sich über einen Browser mit der externen URL der Anwendung oder wählen Sie das Symbol der Anwendung im MyApps-Portal aus. Nach der Authentifizierung bei Azure AD werden Sie an den BIG-IP-Endpunkt für die Anwendung weitergeleitet und zur Eingabe eines Kennworts aufgefordert. Beachten Sie, wie der APM den Benutzernamen mit dem UPN aus Azure AD vorausfüllt. Der vom APM vorausgefüllte Benutzername ist schreibgeschützt, um die Sitzungskonsistenz zwischen Azure AD und der Backend-Anwendung sicherzustellen. Dieses Feld kann bei Bedarf durch zusätzliche Konfiguration ausgeblendet werden.

![Sceenshot zeigt gesichertes SSO](./media/f5-big-ip-forms-advanced/secured-sso.png)

Nach dem Absenden sollte der Benutzer automatisch bei der Anwendung angemeldet werden.

![Sceenshot zeigt Begrüßungsnachricht](./media/f5-big-ip-forms-advanced/welcome-message.png)

## <a name="troubleshoot"></a>Problembehandlung

Wenn der Zugriff auf die geschützte Anwendung nicht möglich ist, kann das an verschiedenen Faktoren liegen, z. B. an einer falschen Konfiguration. Bei der Problembehandlung sind einige Dinge zu beachten:

- Das FBA-SSO wird ausgeführt, indem big-IP das Anmeldeformular am angegebenen URI auswertet und nach den in Ihrer Konfiguration definierten Benutzernamen- und Kennwortelementtags sucht.

- Elementtags müssen konsistent sein, da SSO nicht möglich ist. Komplexere Formulare, die dynamisch generiert werden, erfordern möglicherweise eine genauere Analyse mithilfe von Entwicklungstools, um das Make-Up des Anmeldeformulars zu verstehen.

- Der vom Client initiierte Ansatz eignet sich möglicherweise besser für Anmeldeseiten, die mehrere Formulare enthalten, da er die Angabe eines Formularnamens und sogar das Anpassen der JavaScript-Formularhandlerlogik ermöglicht.

- Beide FBA-SSO-Methoden optimieren die Benutzerfreundlichkeit und Sicherheit, indem alle Formularinteraktionen verborgen werden. In einigen Fällen kann es jedoch hilfreich sein, zu überprüfen, ob Anmeldeinformationen tatsächlich eingefügt werden. Dies ist im vom Client initiierten Modus möglich, indem sie das automatische Senden von Formularen in Ihrem SSO-Profil deaktivieren und dann dev tools verwenden, um die beiden Stileigenschaften zu deaktivieren, die verhindern, dass die Anmeldeseite angezeigt wird.

  ![Sceenshot zeigt die Eigenschaften](./media/f5-big-ip-forms-advanced/properties.png)

BIG-IP-Protokolle sind eine hervorragende Informationsquelle zum Isolieren aller Authentifizierungs- und SSO-Probleme. Bei der Problembehandlung sollten Sie den Ausführlichkeitsgrad des Protokolls erhöhen.

1. Navigieren Sie zu **Access Policy** > **Overview** > **Event Logs** > **Settings** (Zugriffsrichtlinie > Übersicht > Ereignisprotokolle > Einstellungen).

2. Wählen Sie die Zeile für Ihre veröffentlichte Anwendung und dann **Edit** > **Access System Logs** (Bearbeiten > Auf Systemprotokolle zugreifen) aus.

3. Wählen Sie **Debug** (Debuggen) in der SSO-Liste und dann **OK** aus. Sie können ihr Problem dann reproduzieren, bevor Sie sich die Protokolle ansehen. Denken Sie jedoch daran, dies nach Abschluss wieder rückgängig zu machen.

Wenn unmittelbar nach der erfolgreichen Azure AD -Vorabauthentifizierung ein mit Big-IP gekennzeichneter Fehler angezeigt wird, ist es möglich, dass sich das Problem auf das einmalige Anmelden aus Azure AD bei BIG-IP bezieht.

Navigieren Sie zu **Access** > **Overview** > **Access reports** (Zugriff, Übersicht, Zugriffsberichte) aus und führen Sie den Bericht für die letzte Stunde aus, um Protokolle mit hinweisenden Informationen zu sehen. Der Link **View session variables** (Sitzungsvariablen anzeigen) für Ihre Sitzung hilft auch zu verstehen, ob APM die erwarteten Ansprüche von Azure AD empfängt.

Wenn keine BIG-IP-Fehlerseite angezeigt wird, hängt das Problem wahrscheinlich eher mit der Back-End-Anforderung oder dem einmaligen Anmelden von BIG-IP bei der Anwendung zusammen. Navigieren Sie in diesem Fall zu **Access Policy** > **Overview** > **Active Sessions** (Zugriffsrichtlinie, Übersicht, aktive Sitzungen) und wählen Sie den Link für Ihre aktive Sitzung aus.

Der Link **View Variables** (Variablen anzeigen) an dieser Stelle kann auch bei der Ermittlung der Grundursache helfen, insbesondere dann, wenn die der APM die richtige Benutzer-ID und das richtige Kennwort nicht abrufen kann.

Weitere Informationen finden Sie in der [Referenz zu Sitzungsvariablen](https://techdocs.f5.com/en-us/bigip-15-0-0/big-ip-access-policy-manager-visual-policy-editor/session-variables.html) von F5 von BIG-IP.
