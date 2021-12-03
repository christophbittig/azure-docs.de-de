---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Boomi | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Boomi konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: be2284e88a8de6f1bf050167fdf15342ef4e1909
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324361"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Boomi

In diesem Tutorial erfahren Sie, wie Sie Boomi in Azure Active Directory (Azure AD) integrieren. Die Integration von Boomi in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Boomi haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Boomi anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Boomi-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Boomi unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="add-boomi-from-the-gallery"></a>Hinzufügen von Boomi über den Katalog

Zum Konfigurieren der Integration von Boomi in Azure AD müssen Sie Boomi aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Boomi** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **Boomi** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-boomi"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Boomi

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Boomi mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Boomi eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Boomi die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Boomi](#configure-boomi-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    * **[Erstellen eines Boomi-Testbenutzers](#create-boomi-test-user)** , um in Boomi eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Boomi** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Metadatendatei des Dienstanbieters** verfügen und die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt „Grundlegende SAML-Konfiguration“ automatisch eingefügt.

    d. Geben Sie die **Anmelde-URL** ein (z. B. `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}`).

    > [!Note]
    > Die **Dienstanbieter-Metadatendatei** rufen Sie später im Tutorial im Abschnitt **Konfigurieren von Boomi** ab. Sollten die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

1. Die Boomi-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![Screenshot: „User Attributes & Claims“ (Benutzerattribute und -ansprüche) mit Standardwerten wie „user.givenname“ als Vorname und „User.mail“ als E-Mail-Adresse](common/default-attributes.png)

1. Darüber hinaus wird von der Boomi-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name |  Quellattribut|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Boomi einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf Boomi gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Boomi** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-boomi-sso"></a>Konfigurieren des einmaligen Anmeldens für Boomi

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Boomi-Unternehmenswebsite als Administrator an.

1. Navigieren Sie zu **Firmenname**, und wechseln Sie zu **Einrichten**.

1. Klicken Sie auf die Registerkarte **SSO-Optionen**, und führen Sie folgende Schritte aus.

    ![Einmaliges Anmelden auf App-Seite konfigurieren](./media/boomi-tutorial/import.png)

    a. Aktivieren Sie das Kontrollkästchen **Enable SAML Single Sign-On**.

    b. Klicken Sie auf **Importieren**, um das heruntergeladene Zertifikat aus Azure AD in das **Identitätsanbieterzertifikat** hochzuladen.

    c. Geben Sie im Textfeld **Anmelde-URL für Identitätsanbieter** den Wert für die **Anmelde-URL** aus dem Konfigurationsfenster der Azure AD-Anwendung ein.

    d. Aktivieren Sie für **Federation Id Location** (Speicherort der Verbund-ID) das Optionsfeld **Federation Id is in FEDERATION_ID Attribute element** (Verbund-ID im Attributelement FEDERATION_ID enthalten).

    e. Kopieren Sie den Wert für **AtomSphere MetaData URL** (AtomSphere-Metadaten-URL), navigieren Sie im Browser Ihrer Wahl zur **Metadaten-URL**, und speichern Sie die Ausgabe in einer Datei. Laden Sie die **Metadaten-URL** im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** hoch.

    f. Klicken Sie auf die Schaltfläche **Save** .

### <a name="create-boomi-test-user"></a>Erstellen eines Boomi-Testbenutzers

Damit sich Azure AD-Benutzer bei Boomi anmelden können, müssen sie in Boomi bereitgestellt werden. Im Fall von Boomi ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:

1. Melden Sie sich bei der Boomi-Unternehmenswebsite als Administrator an.

1. Navigieren Sie nach der Anmeldung zu **Benutzerverwaltung** und wechseln Sie zu **Benutzer**.

    ![Screenshot: Seite „Benutzerverwaltung“ mit ausgewählten Benutzern](./media/boomi-tutorial/user.png "Benutzer")

1. Klicken Sie auf das Symbol **+** . Das Dialogfeld **Benutzerrollen hinzufügen/verwalten** wird geöffnet.

    ![Screenshot: Ausgewähltes Plussymbol](./media/boomi-tutorial/add.png "Benutzer")

    ![Screenshot: Option „Benutzerrollen hinzufügen/verwalten“ zum Konfigurieren eines Benutzers](./media/boomi-tutorial/roles.png "Benutzer")

    a. Geben Sie im Textfeld **E-Mail-Adresse des Benutzers** die E-Mail-Adresse des Benutzers, z.B. B.Simon@contoso.com, ein.

    b. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen des Benutzers ein (beispielsweise „B.“).

    c. Geben Sie im Textfeld **Nachname** den Vornamen des Benutzers ein, z.B. „Simon“.

    d. Geben Sie die **Verbund-ID.** des Benutzers ein. Jeder Benutzer muss über eine Verbund-ID verfügen, die den Benutzer im Konto eindeutig identifiziert.

    e. Weisen Sie dem Benutzer die Rolle **Standardbenutzer** zu. Weisen Sie ihm nicht die Rolle „Administrator“ zu, da er dadurch normalen Zugriff auf Atmosphere sowie Zugriff über einmaliges Anmelden erhalten würde.

    f. Klicken Sie auf **OK**.

    > [!NOTE]
    > Der Benutzer erhält keine Begrüßungs-E-Mail mit einem Kennwort, das zum Anmelden beim AtomSphere-Konto verwendet werden kann, da sein Kennwort über den Identitätsanbieter verwaltet wird. Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Boomi-Benutzerkonten oder mithilfe der von Boomi bereitgestellten APIs erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Boomi-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Boomi“ klicken, sollten Sie automatisch bei der Boomi-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Boomi können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
