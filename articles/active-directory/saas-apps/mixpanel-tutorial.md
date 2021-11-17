---
title: 'Tutorial: Azure Active Directory-Integration mit Mixpanel | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Mixpanel konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: c7b005521c1743cd0099deb3dea071ffb0269f4b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300502"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Tutorial: Azure Active Directory-Integration mit Mixpanel

In diesem Tutorial erfahren Sie, wie Sie Mixpanel in Azure Active Directory (Azure AD) integrieren. Die Integration von Mixpanel in Azure AD ermöglicht Ihnen Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Mixpanel hat.
* Sie können Ihren Benutzern, ermöglichen sich mit ihren Azure AD-Konten automatisch bei Mixpanel anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Mixpanel-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Mixpanel unterstützt **SP-initiiertes** einmaliges Anmelden.

* Mixpanel unterstützt die [Automatisierte Benutzerbereitstellung](mixpanel-provisioning-tutorial.md).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-mixpanel-from-the-gallery"></a>Hinzufügen von Mixpanel aus dem Katalog

Zum Konfigurieren der Integration von Mixpanel in Azure AD müssen Sie Mixpanel aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** den Suchbegriff **Mixpanel** in das Suchfeld ein.
1. Wählen Sie die Option **Mixpanel** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-mixpanel"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Mixpanel

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Mixpanel mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Mixpanel eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Mixpanel die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Mixpanel](#configure-mixpanel-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Mixpanel-Testbenutzers](#create-mixpanel-test-user)** , um in Mixpanel die Entsprechung von B. Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Mixpanel** zum Abschnitt **Verwalten** und wählen Sie **einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** den folgenden Schritt aus:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://mixpanel.com/login/`.

    > [!NOTE]
    > Registrieren Sie sich unter [https://mixpanel.com/register/](https://mixpanel.com/register/), um Ihre Anmeldeinformationen einzurichten, und wenden Sie sich zur Aktivierung der SSO-Einstellungen für Ihren Mandanten an das [Mixpanel-Supportteam](mailto:support@mixpanel.com). Gegebenenfalls können Sie den Wert für die Anmelde-URL auch vom Mixpanel-Supportteam erhalten. 

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Mixpanel einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon durch gewähren des Zugriffs auf Mixpanel die Verwendung des einmaligen Anmeldens von Azure.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Mixpanel** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-mixpanel-sso"></a>Konfigurieren des SSO für Mixpanel

1. Melden Sie sich in einem anderen Browserfenster in Ihrer Mixpanel-Anwendung als Administrator an.

2. Klicken in der linken unteren Ecke der Seite auf das kleine **Zahnradsymbol** . 
   
    ![Einmaliges Anmelden bei Mixpanel](./media/mixpanel-tutorial/gear-icon.png) 

3. Klicken Sie auf die Registerkarte **Access security** (Zugriffssicherheit) und dann auf **Change settings** (Einstellungen ändern).
   
    ![Screenshot: Registerkarte „Access security“ (Zugriffssicherheit), auf der Sie Einstellungen ändern können](./media/mixpanel-tutorial/settings.png) 

4. Klicken Sie auf der Dialogfeldseite **Change your certificate** (Zertifikat ändern) auf **Choose file** (Datei auswählen), um das heruntergeladene Zertifikat hochzuladen, und klicken Sie dann auf **NEXT** (WEITER).
   
    ![Screenshot: Dialogfeld „Change your certificate“ (Zertifikat ändern), in dem Sie eine Zertifikatdatei auswählen können](./media/mixpanel-tutorial/certificate.png) 

5.  Fügen Sie auf der Dialogfeldseite **Change your authentication URL** (Authentifizierungs-URL ändern) im Textfeld für die Authentifizierungs-URL den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben, und klicken Sie anschließend auf **NEXT** (WEITER).
   
    ![Screenshot: Bereich „Change your authentication URL“ (Authentifizierungs-URL ändern), in dem Sie Ihre Anmelde-URL kopieren können](./media/mixpanel-tutorial/authentication.png) 

6. Klicken Sie auf **Fertig**.

### <a name="create-mixpanel-test-user"></a>Erstellen eines Mixpanel-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Mixpanel. 

1. Melden Sie sich bei Ihrer Mixpanel-Unternehmenswebsite als Administrator an.

2. Klicken Sie in der linken unteren Ecke der Seite auf die kleine Zahnrad-Schaltfläche, um das Fenster **Einstellungen** zu öffnen.

3. Klicken Sie auf die Registerkarte **Team** .

4. Geben Sie im Textfeld für **Team Member** die in Azure hinterlegte E-Mail-Adresse von Britta Simon ein.
   
    ![Screenshot: Registerkarte „Team“, auf der Sie eine Adresse für eine Einladung hinzufügen können](./media/mixpanel-tutorial/member.png) 

5. Klicken Sie auf **Einladen**. 

> [!Note]
> Der Benutzer erhält eine E-Mail für die Profileinrichtung.

> [!NOTE]
> Außerdem unterstützt Mixpanel die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./mixpanel-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Mixpanel weitergeleitet, wo Sie den Anmeldeabfluss initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Mixpanel, und initiieren Sie dort den Anmeldeabfluss.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Mixpanel“ klicken, werden Sie zur Anmelde-URL für Mixpanel weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Mixpanel können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
