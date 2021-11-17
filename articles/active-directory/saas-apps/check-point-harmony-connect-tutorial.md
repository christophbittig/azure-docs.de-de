---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Check Point Harmony Connect | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Check Point Harmony Connect konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2021
ms.author: jeedes
ms.openlocfilehash: b7ba48cb534ebd0ec2df6038b0b5df49ce2b0e88
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311893"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-harmony-connect"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Check Point Harmony Connect

In diesem Tutorial erfahren Sie, wie Sie Check Point Harmony Connect in Azure Active Directory (Azure AD) integrieren. Die Integration von Check Point Harmony Connect in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Check Point Harmony Connect hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Check Point Harmony Connect anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Check Point Harmony Connect-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Check Point Harmony Connect unterstützt **SP**-initiiertes einmaliges Anmelden.
> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.


## <a name="adding-check-point-harmony-connect-from-the-gallery"></a>Hinzufügen von Check Point Harmony Connect aus dem Katalog

Zum Konfigurieren der Integration von Check Point Harmony Connect in Azure AD müssen Sie Check Point Harmony Connect aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Check Point Harmony Connect** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Check Point Harmony Connect** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-check-point-harmony-connect"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Check Point Harmony Connect

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Check Point Harmony Connect mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Check Point Harmony Connect eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Check Point Harmony Connect die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Check Point Harmony Connect](#configure-check-point-harmony-connect-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Check Point Harmony Connect-Testbenutzers](#create-check-point-harmony-connect-test-user)** , um eine Entsprechung von B. Simon in Check Point Harmony Connect zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Check Point Harmony Connect** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://cloudinfra-gw.portal.checkpoint.com/api/saml/sso`

1. Die Check Point Harmony Connect-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Check Point Harmony Connect-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name |  Quellattribut|
    | ---------------- | --------- |
    | groups | user.groups |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Check Point Harmony Connect einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Check Point Harmony Connect gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Check Point Harmony Connect** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-check-point-harmony-connect-sso"></a>Konfigurieren des einmaligen Anmeldens für Check Point Harmony Connect

1. Melden Sie sich bei Ihrer Check Point Harmony Connect-Website als Administrator an.

1. Klicken Sie auf **SETTINGS** (EINSTELLUNGEN), navigieren Sie zu **Identity Provider** (Identitätsanbieter), und klicken Sie auf **CONNECT NOW** (JETZT VERBINDEN).

    ![Screenshot: Identity Provider (Identitätsanbieter)](./media/check-point-harmony-connect-tutorial/identity-provider.png)

1. Wählen Sie **Microsoft Azure AD** als Identitätsanbieter aus, und klicken Sie auf **NEXT** (WEITER).

    ![Screenshot: Auswählen eines Identitätsanbieters](./media/check-point-harmony-connect-tutorial/select-identity-provider.png)

1. Geben Sie auf der Seite **Verify Domain** (Domäne überprüfen) die Organisationsdomäne und diesen generierten DNS-Eintrag für Ihren DNS-Server als TXT-Eintrag ein, und klicken Sie auf **NEXT** (WEITER).

    ![Screenshot: Domänenwert](./media/check-point-harmony-connect-tutorial/domain.png)

1. Führen Sie auf der Seite „Allow Connectivity“ (Konnektivität zulassen) die folgenden Schritte aus:

    ![Screenshot: Seite „Allow Connectivity“ (Konnektivität zulassen)](./media/check-point-harmony-connect-tutorial/allow-connectivity.png)

    a. Kopieren Sie den Wert für **ENTITY ID** (ENTITÄTS-ID), und fügen Sie diesen Wert im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

    b. Kopieren Sie den Wert für **REPLY URL** (ANTWORT-URL), und fügen Sie diesen Wert im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

    c. Klicken Sie auf **NEXT** (Weiter).

1. Laden Sie auf der Seite **Configure Metadata** (Metadaten konfigurieren) die **Verbundmetadaten-XML** hoch, die Sie aus dem Azure-Portal heruntergeladen haben.

1. Klicken Sie auf der Seite **CONFIRM IDENTITY PROVIDER** (IDENTITÄTSANBIETER BESTÄTIGEN) auf **Add** (Hinzufügen), um die Konfiguration abzuschließen.

### <a name="create-check-point-harmony-connect-test-user"></a>Erstellen eines Check Point Harmony Connect-Testbenutzers

1. Melden Sie sich bei Ihrer Check Point Harmony Connect-Website als Administrator an.

1. Navigieren Sie zu **Policy** -> **Access Control** (Richtlinie > Zugriffssteuerung), erstellen Sie eine **neue Regel**, und klicken Sie auf **(+)** , um über **New User** (Neuer Benutzer) einen neuen Benutzer hinzuzufügen. 

    ![Screenshot: Erstellen eines neuen Benutzers](./media/check-point-harmony-connect-tutorial/add-new-user.png)

1. Geben Sie im Fenster **ADD USER** (BENUTZER HINZUFÜGEN) den Namen und den Benutzernamen in die entsprechenden Textfelder ein, und klicken Sie auf **ADD** (HINZUFÜGEN).

    ![Screenshot: Erstellen eines Benutzers](./media/check-point-harmony-connect-tutorial/add-user.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

Wechseln Sie zum Testen der Check Point Harmony Connect-Instanz zum Authentifizierungsdienst, und authentifizieren Sie sich mithilfe des Testkontos, das Sie im Abschnitt **Erstellen eines Azure AD-Testbenutzers** erstellt haben.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Check Point Harmony Connect können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
