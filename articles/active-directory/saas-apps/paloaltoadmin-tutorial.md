---
title: 'Tutorial: Azure AD-SSO-Integration in Palo Alto Networks - Admin UI | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Palo Alto Networks - Admin UI konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 7c7650434e6d55125bb4ebc6e53b62b23f280ecf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300197"
---
# <a name="tutorial-azure-ad-sso-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: Azure AD-SSO-Integration in Palo Alto Networks - Admin UI

In diesem Tutorial erfahren Sie, wie Sie Palo Alto Networks - Admin UI in Azure Active Directory (Azure AD) integrieren. Die Integration von Palo Alto Networks - Admin UI in Azure AD ermöglicht Folgendes:

* Steuern in Azure AD, wer Zugriff auf Palo Alto Networks - Admin UI besitzt.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei Palo Alto Networks - Admin UI anmelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement für Palo Alto Networks - Admin UI, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Palo Alto Networks - Admin UI unterstützt **SP**-initiiertes einmaliges Anmelden.
* Palo Alto Networks – Admin UI unterstützt **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Hinzufügen von Palo Alto Networks - Admin UI aus dem Katalog

Zum Konfigurieren der Integration von Palo Alto Networks - Admin UI in Azure AD müssen Sie Palo Alto Networks - Admin UI aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Palo Alto Networks - Admin UI** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Palo Alto Networks - Admin UI** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---admin-ui"></a>Konfigurieren und Testen von Azure AD-SSO für Palo Alto Networks - Admin UI

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Palo Alto Networks - Admin UI anhand eines Testbenutzers mit dem Namen **B. Simon**.
Für einmaliges Anmelden muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Palo Alto Networks - Admin UI eine Linkbeziehung eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Palo Alto Networks - Admin UI die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks - Admin UI](#configure-palo-alto-networks---admin-ui-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Palo Alto Networks - Admin UI-Testbenutzers](#create-palo-alto-networks---admin-ui-test-user)** , um ein Pendant von B. Simon in Palo Alto Networks - Admin UI zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Palo Alto Networks - Admin UI** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<Customer Firewall FQDN>:443/SAML20/SP`.

    b. Geben Sie im Textfeld **Antwort-URL** die Assertionsverbraucherdienst-URL (Assertion Consumer Service, ACS) im folgenden Format ein: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<Customer Firewall FQDN>/php/login.php`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für den Palo Alto Networks - Admin UI-Client](https://support.paloaltonetworks.com/support), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.
    >
    > Port 443 ist für den **Bezeichner** und die **Antwort-URL** erforderlich, da diese Werte in der Palo Alto-Firewall hartcodiert sind. Wenn Sie die Portnummer entfernen, führt dies zu einem Fehler bei der Anmeldung.

    > Port 443 ist für den **Bezeichner** und die **Antwort-URL** erforderlich, da diese Werte in der Palo Alto-Firewall hartcodiert sind. Wenn Sie die Portnummer entfernen, führt dies zu einem Fehler bei der Anmeldung.

1. Die Anwendung Palo Alto Networks – Admin UI erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

   > [!NOTE]
   > Bei den Attributwerten handelt es sich nur um Beispiele. Sie müssen die entsprechenden Werte für *username* und *adminrole* zuordnen. Es ist ein weiteres optionales Attribut *accessdomain* verfügbar, mit dem der Administratorzugriff auf bestimmte virtuelle Systeme in der Firewall beschränkt wird.

1. Darüber hinaus wird von der Anwendung Palo Alto Networks – Admin UI erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name |  Quellattribut|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    > [!NOTE]
    > Der Wert **Name**, der oben als _adminrole_ angezeigt wird, sollte mit dem Wert von _Admin role attribute_ (Administratorrollenattribut) übereinstimmen, der in Schritt 12 des Abschnitts **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks – Admin UI](#configure-palo-alto-networks---admin-ui-sso)** konfiguriert wurde. Der Wert **Quellattribut**, der oben als _customadmin_ angezeigt wird, sollte mit dem Wert von _Admin Role Profile Name_ (Administratorrollenprofilname) übereinstimmen, der in Schritt 9 des Abschnitts **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks – Admin UI](#configure-palo-alto-networks---admin-ui-sso)** konfiguriert wurde.  

    > [!NOTE]
    > Weitere Informationen zu den Attributen finden Sie in den folgenden Artikeln:
    > * [Administrative role profile for Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile) (Administratorrollenprofil für Admin UI – adminrole)
    > * [Device access domain for Admin UI (accessdomain)](https://docs.paloaltonetworks.com/pan-os/8-0/pan-os-web-interface-help/device/device-access-domain.html) (Gerätezugriffsdomäne für Admin UI – accessdomain)

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Palo Alto Networks - Admin UI einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Palo Alto Networks - Admin UI gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Palo Alto Networks - Admin UI** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-palo-alto-networks---admin-ui-sso"></a>Konfigurieren des einmaligen Anmeldens für Palo Alto Networks - Admin UI

1. Öffnen Sie als Administrator in einem neuen Fenster die Administratoroberfläche für die Palo Alto Networks-Firewall.

2. Klicken Sie auf die Registerkarte **Device** (Gerät).

    ![Screenshot: Registerkarte „Geräte“.](./media/paloaltoadmin-tutorial/device.png)

3. Wählen Sie im linken Bereich **SAML Identity Provider** (SAML-Identitätsanbieter) aus, und klicken Sie anschließend auf **Import** (Importieren), um die Metadatendatei zu importieren.

    ![Screenshot: Schaltfläche „Metadatendatei importieren“.](./media/paloaltoadmin-tutorial/admin.png)

4. Führen Sie im Fenster **SAML Identity Provider Server Profile Import** (Import des SAML-Identitätsanbieter-Serverprofils) die folgenden Schritte aus:

    ![Screenshot: Fenster „SAML Identity Provider Server Profile Import“ (Import des SAML-Identitätsanbieter-Serverprofils).](./media/paloaltoadmin-tutorial/profile.png)

    a. Geben Sie im Feld **Profile Name** (Profilname) einen Namen ein (z. B. **AzureAD Admin UI**).

    b. Klicken Sie unter **Identity Provider Metadata** (Identitätsanbieter-Metadaten) auf **Browse** (Durchsuchen), und wählen Sie die Datei „metadata.xml“ aus, die Sie zuvor aus dem Azure-Portal heruntergeladen haben.

    c. Deaktivieren Sie das Kontrollkästchen **Validate Identity Provider Certificate** (Zertifikat des Identitätsanbieters überprüfen).

    d. Klicken Sie auf **OK**.

    e. Committen Sie die Konfigurationen der Firewall, indem Sie auf die Schaltfläche **Commit** (Committen) klicken.

5. Wählen Sie im linken Bereich **SAML Identity Provider** (SAML-Identitätsanbieter) und anschließend das SAML-Identitätsanbieterprofil (z. B. **AzureAD Admin UI**) aus, das Sie im vorherigen Schritt erstellt haben.

    ![Screenshot: SAML-Identitätsanbieterprofil](./media/paloaltoadmin-tutorial/azure.png)

6. Führen Sie im Fenster **SAML Identity Provider Server Profile** (SAML-Identitätsanbieter-Serverprofil) die folgenden Schritte aus:

    ![Screenshot: Fenster „SAML Identity Provider Server Profile“ (SAML-Identitätsanbieter-Serverprofil).](./media/paloaltoadmin-tutorial/server.png)
  
    a. Ersetzen Sie im Feld **Identity Provider SLO URL** (SLO-URL des Identitätsanbieters) die zuvor importierte SLO-URL durch die folgende URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Klicken Sie auf **OK**.

7. Klicken Sie auf der Administratoroberfläche für die Palo Alto Networks-Firewall auf **Device** (Gerät) und dann auf **Admin Roles** (Administratorrollen).

8. Wählen Sie die Schaltfläche **Hinzufügen** aus.

9. Geben Sie im Fenster **Admin Role Profile** (Administratorrollenprofil) einen Namen für die Administratorrolle in das Feld **Name** ein (z. B. **fwadmin**). Der Administratorrollenname sollte mit dem vom Identitätsanbieter gesendeten Attributnamen für die SAML-Administratorrolle identisch sein. Der Name und Wert der Administratorrolle wurden im Abschnitt **Benutzerattribute** im Azure-Portal erstellt.

    ![Konfigurieren Sie die Administratorrolle für Palo Alto Networks.](./media/paloaltoadmin-tutorial/role.png)
  
10. Klicken Sie auf der Administratoroberfläche der Firewall auf **Device** (Gerät) und dann auf **Authentication Profile** (Authentifizierungsprofil).

11. Wählen Sie die Schaltfläche **Hinzufügen** aus.

12. Führen Sie im Fenster **Authentication Profile** (Authentifizierungsprofil) die folgenden Schritte aus: 

    ![Screenshot: Fenster „Authentifizierungsprofil“.](./media/paloaltoadmin-tutorial/authentication.png)

    a. Geben Sie im Feld **Name** einen Namen ein (z. B. **AzureSAML_Admin_AuthProfile**).

    b. Wählen Sie in der Dropdownliste **Type** (Typ) den Eintrag **SAML** aus. 

    c. Wählen Sie in der Dropdownliste **IdP Server Profile** (IdP-Serverprofil) das entsprechende SAML-Identitätsanbieter-Serverprofil aus (z. B. **AzureAD Admin UI**).

    d. Aktivieren Sie das Kontrollkästchen **Enable Single Logout** (Einmaliges Abmelden aktivieren).

    e. Geben Sie im Feld **Admin Role Attribute** (Administratorrollenattribut) den Attributnamen ein (z. B. **adminrole**).

    f. Klicken Sie auf die Registerkarte **Advanced** (Erweitert) und dann unter **Allow List** (Liste „Zulassen) auf **Add** (Hinzufügen).

    ![Screenshot: Schaltfläche „Add“ (Hinzufügen) auf der Registerkarte „Advanced“ (Erweitert).](./media/paloaltoadmin-tutorial/allowlist.png)

    g. Aktivieren Sie das Kontrollkästchen **All** (Alle), oder wählen Sie die Benutzer und Gruppen aus, die sich mit diesem Profil authentifizieren können.  
    Bei der Authentifizierung eines Benutzers gleicht die Firewall den zugewiesenen Benutzernamen bzw. die zugewiesene Gruppe mit den Einträgen in der Liste ab. Wenn Sie keine Einträge hinzufügen, können sich keine Benutzer authentifizieren.

    h. Klicken Sie auf **OK**.

13. Um Administratoren die Verwendung von SAML-SSO mit Azure zu ermöglichen, klicken Sie auf **Device** > **Setup** (Gerät > Einrichten). Klicken Sie im Bereich **Setup** (Einrichten) auf die Registerkarte **Management** (Verwaltung) und dann unter **Authentication Settings** (Authentifizierungseinstellungen) auf die Schaltfläche mit dem Zahnradsymbol (**Einstellungen**).

    ![Screenshot der Schaltfläche für Einstellungen](./media/paloaltoadmin-tutorial/setup.png)

14. Wählen Sie das SAML-Authentifizierungsprofil aus, das Sie im Fenster „Authentifizierungsprofil“ erstellt haben (z.B. **AzureSAML_Admin_AuthProfile**).

    ![Screenshot: Feld „Authentifizierungsprofil“.](./media/paloaltoadmin-tutorial/settings.png)

15. Klicken Sie auf **OK**.

16. Klicken Sie zum Committen der Konfiguration auf **Commit** (Committen).

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Erstellen eines Palo Alto Networks - Admin UI-Testbenutzers

Palo Alto Networks – Admin UI unterstützt die Just-in-Time-Benutzerbereitstellung. Wenn ein Benutzer nicht bereits vorhanden ist, wird er nach der erfolgreichen Authentifizierung automatisch im System erstellt. Zum Erstellen des Benutzers ist keine Aktion Ihrerseits erforderlich.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Palo Alto Networks - Admin UI weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Anmelde-URL für Palo Alto Networks - Admin UI auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Palo Alto Networks - Admin UI“ klicken, sollten Sie automatisch bei der Instanz von Palo Alto Networks - Admin UI angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Palo Alto Networks – Admin UI können Sie eine Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
