---
title: 'Tutorial: Integration des einmaligen Anmeldens (SSO) von Azure Active Directory mit cloudtamer.io | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und cloudtamer.io konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2021
ms.author: jeedes
ms.openlocfilehash: b39616ea0270615db618b8097a28b67f2cff45e5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311551"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudtamerio"></a>Tutorial: Integration des einmaligen Anmeldens (SSO) von Azure Active Directory mit cloudtamer.io

In diesem Tutorial erfahren Sie, wie Sie cloudtamer.io in Azure Active Directory (Azure AD) integrieren. Die Integration von cloudtamer.io in Azure AD ermöglicht Ihnen Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf cloudtamer.io hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei cloudtamer.io anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein cloudtamer.io-Abonnement, für das ein einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* cloudtamer.io unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.
* cloudtamer.io unterstützt die **Just-in-Time**-Benutzerbereitstellung.


## <a name="adding-cloudtamerio-from-the-gallery"></a>Hinzufügen der cloudtamer.io-App aus dem Katalog

Zum Konfigurieren der Integration von cloudtamer.io in Azure-AD müssen Sie Ihrer Liste der verwalteten SaaS-Apps die cloudtamer.io-App aus dem Katalog hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie in dem Abschnitt **Aus dem Katalog hinzufügen** im Suchfeld den Begriff **cloudtamer.io** ein.
1. Wählen Sie in dem Ergebnisbereich **cloudtamer.io** aus und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-cloudtamerio"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für cloudtamer.io

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit cloudtamer.io mithilfe eines Testbenutzers namens **B. Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in cloudtamer.io eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit cloudtamer.io die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für cloudtamer.io](#configure-cloudtamerio-sso)** , um die Einstellungen für das einmalige Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines cloudtamer.io-Testbenutzers](#create-cloudtamerio-test-user)** , um eine Entsprechung von B. Simon in cloudtamer.io zu erhalten, die mit der entsprechenden Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert
1. **[Gruppenassertionen](#group-assertions)** , um Gruppenassertionen für Azure AD und cloudtamer.io festzulegen

### <a name="begin-cloudtamerio-sso-configuration"></a>Eine Anleitung zum Starten der Konfiguration für das einmalige Anmelden mit cloudtamer.io

1. Melden Sie sich bei der cloudtamer.io-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der oberen rechten Ecke auf das **+** Plussymbol und wählen Sie **IDMS** aus.

    ![Ein Screenshot, der das Erstellen einer IDMS-Datei zeigt.](./media/cloudtamer-io-tutorial/idms-creation.png)

1. Wählen Sie **SAML 2.0** als IDMS-Typ aus.

1. Lassen Sie diesen Bildschirm geöffnet, und kopieren Sie die Werte von diesem Bildschirm in die Azure AD Konfiguration.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **cloudtamer.io** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Fügen Sie in das Textfeld **Bezeichner** den Wert für **SERVICE PROVIDER ISSUER (ENTITY ID)** aus cloudtamer.io ein.

    b. Fügen Sie in das Textfeld **Antwort-URL** die **SERVICE PROVIDER ACS URL** aus cloudtamer.io ein.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Fügen Sie in das Textfeld **Anmelde-URL** den Wert für **SERVICE PROVIDER ACS URL** aus cloudtamer.io ein.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Einrichten von cloudtamer.io** die entsprechende/n URL(s) gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie den Zugriff auf cloudtamer.io gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **cloudtamer.io** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cloudtamerio-sso"></a>Konfigurieren der einmaligen Anmeldung mit cloudtamer.io

1. Führen Sie auf der Seite **Hinzufügen einer IDMS-Datei** die folgenden Schritte aus:

    ![Ein Screenshot, der das Hinzufügen einer IDMS-Datei zeigt.](./media/cloudtamer-io-tutorial/configuration.png)

    a. Geben Sie in dem Feld **IDMS-Name** einen Namen ein, den die Benutzer auf dem Anmeldebildschirm erkennen können.

    b. Fügen Sie im Textfeld **IDENTITY PROVIDER ISSUER (ENTITY ID)** den **Bezeichner** ein, den Sie in dem Azure-Portal kopiert haben.

    c. Öffnen Sie die heruntergeladene **Verbundmetadaten-XML** Datei aus dem Azure-Portal im Editor. Kopieren Sie ihren Inhalt und fügen Sie ihn in das Textfeld **IDENTITY PROVIDER METADATA** ein.

    d. Kopieren Sie den Wert für **AUSSTELLER DES DIENSTANBIETERS (ENTITÄTS-ID)** und fügen Sie diesen Wert im Azure-Portal im Abschnitt „Grundlegende SAML-Konfiguration“ in das Textfeld **Bezeichner** ein.

    e. Kopieren Sie den Wert im Feld **DIENSTANBIETER ACS-URL** und fügen Sie diesen Wert im Azure-Portal im Abschnitt „Grundlegende SAML-Konfiguration“ in das Textfeld **Antwort-URL** ein.

    f. Geben Sie unter Assertionszuordnung die folgenden Werte ein:

    | Feld | Wert |
    |-----------|-------|
    | First Name (Vorname) | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | Last Name (Nachname) | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | E-Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
    |  Username | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
    |

1. Klicken Sie auf **Erstellen einer IDMS-Datei**.


### <a name="create-cloudtamerio-test-user"></a>Erstellen eines cloudtamer.io-Testbenutzers

In diesem Abschnitt wird in cloudtamer.io ein Benutzer namens Britta Simon erstellt. cloudtamer.io unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn in cloudtamer.io noch kein Benutzer vorhanden ist, wird nach der Authentifizierung ein neuer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für cloudtamer.io weitergeleitet, wo Sie den Anmeldeablauf initiieren können.  

* Rufen Sie direkt die Anmelde-URL für cloudtamer.io auf und initiieren Sie den Anmeldeablauf.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der cloudtamer.io-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „cloudtamer.io“ in dem Zugriffsbereich „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeablaufs zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der cloudtamer.io-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="group-assertions"></a>Gruppenassertionen

Führen Sie zum einfachen Verwalten der Benutzerberechtigungen für cloudtamer.io mithilfe vorhandener Azure Active Directory-Gruppen die folgenden Schritte aus:

### <a name="azure-ad-configuration"></a>Azure AD-Konfiguration

1. Wechseln Sie im Azure-Portal zu **Azure Active Directory** > **Unternehmensanwendungen**.
1. Wählen Sie in der Liste die Unternehmensanwendung für cloudtamer.io aus.
1. Wählen Sie im Menü auf der linken Seite unter **Übersicht** die Option **Einmaliges Anmelden** aus.
1. Wählen Sie unter **Einmaliges Anmelden** unter **Benutzerattribute und Ansprüche** die Option **Bearbeiten** aus.
1. Wählen Sie **Gruppenanspruch hinzufügen** aus. 
   > [!NOTE]
   > Sie können nur einen Gruppenanspruch haben. Ist diese Option deaktiviert, ist möglicherweise bereits ein Gruppenanspruch definiert.
1. Wählen Sie unter **Gruppenansprüche** die Gruppen aus, die im Anspruch zurückgegeben werden sollen:
   - Wenn Sie jede Gruppe, die Sie in cloudtamer.io verwenden möchten, immer dieser Unternehmensanwendung zuordnen möchten, wählen Sie **Der Anwendung zugewiesene Gruppen** aus.
   - Wenn alle Gruppen angezeigt werden sollen (diese Auswahl kann zu einer großen Anzahl von Gruppenassertionen führen und Beschränkungen unterliegen), wählen Sie **Der Anwendung zugewiesene Gruppen** aus.
1. Übernehmen Sie für **Quellattribut** den Standardwert **Gruppen-ID**.
1. Aktivieren Sie das Kontrollkästchen **Name des Gruppenanspruchs anpassen**.
1. Geben Sie unter **Name** den Namen **memberOf** ein.
1. Wählen Sie **Speichern** aus, um die Konfiguration mit Azure AD abzuschließen.

### <a name="cloudtamerio-configuration"></a>Konfiguration von cloudtamer.io

1. Navigieren Sie in cloudtamer.io zu **Users** > **Identity Management Systems** (Benutzer > Identitätsverwaltungssysteme)
1. Wählen Sie das IDMS aus, das Sie für Azure AD erstellt haben.
1. Wählen Sie auf der Übersichtsseite die Registerkarte **User Group Associations** (Benutzergruppenzuordnungen) aus.
1. Führen Sie für jede gewünschte Benutzergruppenzuordnung die folgenden Schritte aus:
   1. Wählen Sie **Add** > **Add New** (Hinzufügen > Neu hinzufügen) aus.
   1. Gehen Sie im angezeigten Dialogfeld wie folgt vor:
      1. Geben Sie unter **Name** den Namen **memberOf** ein.
      1. Geben Sie unter **RegEx** die Objekt-ID (aus Azure AD) der Gruppe ein, die Sie zuordnen möchten.
      1. Wählen Sie unter **User Group** (Benutzergruppe) die interne cloudtamer.io-Gruppe aus, die Sie der Gruppe unter **RegEx** zuordnen möchten.
      1. Aktivieren Sie das Kontrollkästchen **Update on Login** (Bei Anmeldung aktualisieren).
   1. Wählen Sie **Add** (Hinzufügen) aus, um die Gruppenzuordnung hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von cloudtamer.io können Sie eine Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
