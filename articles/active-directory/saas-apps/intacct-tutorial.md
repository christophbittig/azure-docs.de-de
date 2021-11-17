---
title: 'Tutorial: Azure Active Directory-Integration von Sage Intacct | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Sage Intacct konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d490177056ccc2ff09c41b1700887fc3e70e3a58
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346608"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Tutorial: Integrieren von Sage Intacct in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Sage Intacct in Azure Active Directory (Azure AD) integrieren. Die Integration von Sage Intacct in Azure AD ermöglicht Ihnen Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Sage Intacct hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Sage Intacct anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Sage Intacct-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Sage Intacct unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-sage-intacct-from-the-gallery"></a>Hinzufügen von Sage Intacct aus dem Katalog

Zum Konfigurieren der Integration von Sage Intacct in Azure AD müssen Sie Ihrer Liste der verwalteten SaaS-Apps Sage Intacct aus dem Katalog hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Namen **Sage Intacct** ein.
1. Wählen Sie im Ergebnisbereich die App **Sage Intacct** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Sage Intacct

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Sage Intacct mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sage Intacct eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Sage Intacct die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
2. **[Konfigurieren des einmaligen Anmeldens für Sage Intacct](#configure-sage-intacct-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Sage Intacct-Testbenutzers](#create-sage-intacct-test-user)** , um eine Entsprechung von B. Simon in Sage Intacct zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Sage Intacct** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Antwort-URL** die folgenden URLs ein:  
    `https://www.intacct.com/ia/acct/sso_response.phtml` (Wählen Sie diese Einstellung als Standard aus.)  
    `https://www.p-02.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-03.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-04.intacct.com/ia/acct/sso_response.phtml`  
    `https://www.p-05.intacct.com/ia/acct/sso_response.phtml`  

1. Die Sage Intacct-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld „Benutzerattribute“ zu öffnen.

    ![image](common/edit-attribute.png)

1. Darüber hinaus erwartet die Sage Intacct-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt „Ansprüche“ die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | Attributname  |  Quellattribut|
    | ---------------| --------------- |
    | Name des Unternehmens | **Sage Intacct-Unternehmens-ID** |
    | name | Der Wert muss der Sage Intacct-**Benutzer-ID** entsprechen, die Sie später in diesem Tutorial im Abschnitt **Erstellen eines Sage Intacct-Testbenutzers** eingeben. |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein, oder wählen Sie ihn aus.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Sage Intacct einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Sage Intacct gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Sage Intacct** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sage-intacct-sso"></a>Konfigurieren des einmaligen Anmeldens für Sage Intacct

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Sage Intacct-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf die Registerkarte **Unternehmen** und dann auf **Unternehmensinformationen**.

    ![Company](./media/intacct-tutorial/ic790037.png "Company")

1. Klicken Sie auf die Registerkarte **Sicherheit** und dann auf **Bearbeiten**.

    ![Security](./media/intacct-tutorial/ic790038.png "Sicherheit")

1. Führen Sie im Abschnitt **Einmaliges Anmelden (SSO)** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/intacct-tutorial/ic790039.png "Einmaliges Anmelden")

    a. Wählen Sie **Einmaliges Anmelden aktivieren** aus.

    b. Wählen Sie für **Identitätsanbietertyp** die Option **SAML 2.0** aus.

    c. Fügen Sie in das Textfeld **Issuer URL** (Aussteller-URL) den Wert des **Azure AD-Bezeichners** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in das Textfeld **Login URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Öffnen Sie das **Base-64**-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Feld **Zertifikat** ein.

    f. Klicken Sie auf **Speichern**.

### <a name="create-sage-intacct-test-user"></a>Erstellen eines Sage Intacct-Testbenutzers

Um Azure AD-Benutzer so einzurichten, dass sie sich bei Sage Intacct anmelden können, müssen sie in Sage Intacct bereitgestellt werden. Bei Sage Intacct ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen von Benutzerkonten die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **Sage Intacct**-Mandanten an.

1. Klicken Sie auf die Registerkarte **Unternehmen** und dann auf **Benutzer**.

    ![Benutzer](./media/intacct-tutorial/ic790041.png "Benutzer")

1. Klicken Sie auf die Registerkarte **Hinzufügen**.

    ![Add (Hinzufügen)](./media/intacct-tutorial/ic790042.png "Hinzufügen")

1. Führen Sie im Abschnitt **Benutzerinformationen** die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Benutzerinformationen“, in dem Sie die Informationen für diesen Schritt eingeben können](./media/intacct-tutorial/ic790043.png "Benutzerinformationen")

    a. Geben Sie im Abschnitt **Benutzerinformationen** die **Benutzer-ID**, den **Nachnamen**, den **Vornamen**, die **E-Mail-Adresse**, den **Titel** und die **Telefonnummer** eines Azure AD-Kontos ein, das Sie bereitstellen möchten.

    > [!NOTE]
    > Stellen Sie sicher, dass die **Benutzer-ID** im obigen Screenshot dem Wert von **Quellattribut** entspricht, der im Azure-Portal im Abschnitt **Benutzerattribute** dem Attribut **Name** zugeordnet ist.

    b. Wählen Sie die **Administratorrechte** eines Azure AD-Kontos aus, das Sie bereitstellen möchten.

    c. Klicken Sie auf **Speichern**. 
    
    d. Der Besitzer des Azure AD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

1. Klicken Sie auf die Registerkarte **Einmaliges Anmelden**, und stellen Sie sicher, dass die **Verbund-SSO-Benutzer-ID** im folgenden Screenshot dem Wert von **Quellattribut** entspricht, der im Azure-Portal im Abschnitt **Benutzerattribute** dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` zugeordnet ist.

    ![Screenshot: Abschnitt „Benutzerinformationen“, in dem Sie die Verbund-SSO-Benutzer-ID eingeben können](./media/intacct-tutorial/ic790044.png "Benutzerinformationen")

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Sage Intacct-Benutzerkonten oder mithilfe der von Sage Intacct bereitgestellten APIs bereitstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Sage Intacct-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Sage Intacct“ klicken, sollten Sie automatisch bei der Sage Intacct-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Sage Intacct können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
