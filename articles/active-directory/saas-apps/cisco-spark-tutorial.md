---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD in Cisco Webex'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Cisco Webex konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2021
ms.author: jeedes
ms.openlocfilehash: 7dc878c56b52f1a2aea1c6250b3a52d9fba37693
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344968"
---
# <a name="tutorial-azure-ad-sso-integration-with-cisco-webex"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD in Cisco Webex

In diesem Tutorial erfahren Sie, wie Sie Cisco Webex in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Cisco Webex in Azure AD haben Sie folgende Möglichkeiten:

* Sie können in Azure AD steuern, wer auf Cisco Webex Zugriff hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Cisco Webex anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Cisco Webex-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist
*  Dienstanbieter-Metadatendatei von Cisco Webex.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cisco Webex unterstützt **SP-initiiertes** einmaliges Anmelden.
* Cisco Webex unterstützt die [**automatisierte Benutzerbereitstellung**](./cisco-webex-provisioning-tutorial.md).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-cisco-webex-from-the-gallery"></a>Hinzufügen von Cisco Webex aus dem Katalog

Zum Konfigurieren der Integration von Cisco Webex in Azure AD müssen Sie Cisco Webex aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Cisco Webex** in das Suchfeld ein.
1. Wählen Sie **Cisco Webex** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Cisco Webex

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Cisco Webex mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cisco Webex eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Cisco Webex die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren von Cisco Webex für einmaliges Anmelden](#configure-cisco-webex-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Cisco Webex-Testbenutzers](#create-cisco-webex-test-user)** , um ein Pendant zu B. Simon in Cisco Webex zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Cisco Webex** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Laden Sie im Abschnitt **Grundlegende SAML-Konfiguration** die heruntergeladene **Metadatendatei des Dienstanbieters** herunter, und konfigurieren Sie die Anwendung folgendermaßen:

    >[!Note]
    >Die Dienstanbieter-Metadatendatei rufen Sie später im Tutorial im Abschnitt **Konfigurieren von Cisco Webex** ab. 

    a. Klicken Sie auf **Metadatendatei hochladen**.

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    c. Nachdem Sie die Metadatendatei des Dienstanbieters hochgeladen haben, werden im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für **Bezeichner** und **Antwort-URL** automatisch ausgefüllt, wie unten dargestellt:

    d. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://web.ciscospark.com/idb/Consumer/metaAlias/<ID>/sp`
    
    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Kopieren Sie den Literalwert der Antwort-URL, und fügen Sie ihn `https://web.ciscospark.com/` hinzu, um den tatsächlichen Wert für die Anmelde-URL zu formulieren.

1. Die Cisco Webex-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Cisco Webex-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
  
    | Name |  Quellattribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  Der Wert des Quellattributs ist standardmäßig „user.pricipalname“ zugeordnet. Dieser kann in „user.mail“ oder „user.onpremiseuserprincipalname“ oder ein beliebiger anderer Wert gemäß der Einstellung in Webex geändert werden.


1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cisco Webex gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Cisco Webex** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cisco-webex-sso"></a>Konfigurieren des einmaligen Anmeldens für Cisco Webex

1. Melden Sie sich bei Cisco Webex mit Ihren Administratoranmeldeinformationen an.

1. Wählen Sie **Organization Settings** (Organisationseinstellungen) aus, und klicken Sie im Bereich **Authentication** (Authentifizierung) auf **Modify** (Ändern).

    ![Screenshot: Authentifizierungseinstellungen, in denen Sie auf „Modify“ (Ändern) klicken können](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Wählen Sie **Integrate a 3rd-party identity provider. (Advanced)** (Identitätsanbieter eines Drittanbieters integrieren (erweitert)), und klicken Sie auf **Next** (Weiter).

    ![Screenshot: Integration eines Identitätsanbieters eines Drittanbieters](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Klicken Sie auf **Download Metadata File** (Metadatendatei herunterladen), um die **Metadatendatei des Dienstanbieters** herunterzuladen, und sie auf Ihren Computer zu speichern. Klicken Sie anschließend auf **Next** (Weiter).

    ![Screenshot: Metadatendatei des Dienstanbieters](./media/cisco-spark-tutorial/sp-metadata.png)

1. Klicken Sie auf die Option **file browser** (Dateibrowser), und laden Sie die Azure AD-Metadatendatei hoch. Wählen Sie anschließend **Require certificate signed by a certificate authority in Metadata (more secure)** (Signatur einer Zertifizierungsstelle in den Metadaten erzwingen (sicherer)) aus, und klicken Sie auf **Weiter**.

    ![Screenshot: Seite „Import IdP Metadata“ (IdP-Metadaten importieren)](./media/cisco-spark-tutorial/idp-metadata.png)

1. Wählen Sie **Test SSO Connection** (SSO-Verbindung testen) aus, und authentifizieren Sie sich, wenn eine neue Browserregisterkarte geöffnet wird, durch Ihre Anmeldung bei Azure AD.

1. Wechseln Sie zurück zur Browserregisterkarte **Cisco Cloud Collaboration Management**. Wenn der Test erfolgreich war, wählen Sie **This test was successful. Enable Single Sign-On option** (Der Test war erfolgreich. Single Sign-On aktivieren), und klicken Sie auf **Weiter**.

1. Klicken Sie auf **Speichern**.

> [!NOTE]
> Weitere Informationen zum Konfigurieren von Cisco Webex finden Sie auf [dieser Seite](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog).

### <a name="create-cisco-webex-test-user"></a>Erstellen eines Cisco Webex-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen B. Simon in Cisco Webex erstellt. Diese Anwendung unterstützt die automatische Benutzerbereitstellung, die die automatische Bereitstellung und Aufhebung der Bereitstellung basierend auf Ihren Geschäftsregeln ermöglicht.  Microsoft empfiehlt, wann immer möglich, die Verwendung der automatischen Bereitstellung. [Hier](./cisco-webex-provisioning-tutorial.md) finden Sie Informationen zum Aktivieren der automatischen Bereitstellung.

Wenn Sie manuell einen Benutzer erstellen müssen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich bei Cisco Webex mit Ihren Administratoranmeldeinformationen an.

2. Klicken Sie auf **Users** und dann auf **Manage Users**.
   
    ![Screenshot: Seite „Users“ (Benutzer), auf der Sie Benutzer verwalten können](./media/cisco-spark-tutorial/user-1.png) 

3. Wählen Sie im Fenster **Manage User** (Benutzer verwalten) die Option **Manually add or modify users** (Benutzer manuell hinzufügen oder ändern) aus.

    ![Screenshot mit der Seite „Users“ (Benutzer), auf der Sie Benutzer verwalten und Benutzer manuell hinzufügen oder ändern können](./media/cisco-spark-tutorial/user-2.png)

4. Wählen Sie **Names and Email address** (Namen und E-Mail-Adressen) aus. Füllen Sie das Textfeld anschließend wie folgt aus:

    ![Screenshot: Dialogfeld „Manage Users“ (Benutzer verwalten), in dem Sie Benutzer manuell hinzufügen oder ändern können](./media/cisco-spark-tutorial/user-3.png) 

    a. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein (beispielsweise **B**).

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein (beispielsweise **Simon**).

    c. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. b.simon@contoso.com, ein.

5. Klicken Sie auf das Pluszeichen, um B. Simon hinzuzufügen. Klicken Sie auf **Weiter**.

6. Klicken Sie im Fenster **Add Services for Users** (Dienste für Benutzer hinzufügen) auf **Add Users** (Benutzer hinzufügen) und dann auf **Finish** (Fertig stellen).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Cisco Webex weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Cisco Webex, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie auf die Kachel für Cisco Webex unter „Meine Apps“ klicken, werden Sie zur Anmelde-URL von Cisco Webex umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Cisco Webex können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
