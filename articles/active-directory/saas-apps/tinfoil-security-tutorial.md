---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit TINFOIL SECURITY'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TINFOIL SECURITY konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: 48d46916143fd219c81354a2d3f62ea260c9a2fb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303955"
---
# <a name="tutorial-azure-ad-sso-integration-with-tinfoil-security"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit TINFOIL SECURITY

In diesem Tutorial erfahren Sie, wie Sie TINFOIL SECURITY in Azure Active Directory (Azure AD) integrieren. Die Integration von TINFOIL SECURITY in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf TINFOIL SECURITY hat.
* Ermöglichen Sie es ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei TINFOIL SECURITY anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein TINFOIL SECURITY-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* TINFOIL SECURITY unterstützt **IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-tinfoil-security-from-the-gallery"></a>Hinzufügen von TINFOIL SECURITY aus dem Katalog

Zum Konfigurieren der Integration von TINFOIL SECURITY in Azure AD müssen Sie TINFOIL SECURITY aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **TINFOIL SECURITY** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **TINFOIL SECURITY** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-tinfoil-security"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SECURITY

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit TINFOIL SECURITY mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TINFOIL SECURITY eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit TINFOIL SECURITY die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für TINFOIL SECURITY](#configure-tinfoil-security-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines TINFOIL SECURITY-Testbenutzers](#create-tinfoil-security-test-user)** , um ein Pendant von B. Simon in TINFOIL SECURITY zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **TINFOIL SECURITY** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **SAML-Basiskonfiguration** ist die Anwendung vorkonfiguriert und die notwendigen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche **Speichern** klickt.

1. Die Visitly-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Visitly-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Der Wert „accountid“ wird später in diesem Tutorial erklärt.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

1. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** unter **Fingerabdruck** den Fingerabdruckwert, und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

1. Kopieren Sie im Abschnitt **TINFOIL SECURITY einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TINFOIL SECURITY gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **TINFOIL SECURITY** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-tinfoil-security-sso"></a>Konfigurieren des einmaligen Anmeldens für TINFOIL SECURITY

1. Melden Sie sich in einem anderen Webbrowserfenster bei der TINFOIL SECURITY-Unternehmenswebsite als Administrator an.

1. Klicken Sie oben auf der Symbolleiste auf **Einstellungen**.

    ![Dashboard](./media/tinfoil-security-tutorial/account.png "Dashboard")

1. Klicken Sie auf **Sicherheit**.

    ![Security](./media/tinfoil-security-tutorial/details.png "Sicherheit")

1. Führen Sie auf der Konfigurationsseite **Single Sign-On** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/tinfoil-security-tutorial/certificate.png "Single Sign-On")

    a. Wählen Sie **SAML aktivieren**.

    b. Klicken Sie auf **Manuelle Konfiguration**.

    c. Fügen Sie in das Textfeld **SAML Post URL** (SAML POST-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie im Textfeld **SAML Certificate Fingerprint** (SAML-Zertifikatsfingerabdruck) den Wert von **Fingerabdruck** ein, den Sie aus dem Abschnitt **SAML-Signaturzertifikat** kopiert haben.
  
    e. Kopieren Sie den Wert Ihrer **Konto-ID**, und fügen Sie ihn im Azure-Portal im Abschnitt **Benutzerattribute und Ansprüche** in das Textfeld **Quellattribut** ein.

    f. Klicken Sie auf **Speichern**.

### <a name="create-tinfoil-security-test-user"></a>Erstellen eines TINFOIL SECURITY-Testbenutzers

Damit sich Azure AD-Benutzer bei TINFOIL SECURITY anmelden können, müssen sie in TINFOIL SECURITY bereitgestellt werden. Im Fall von TINFOIL SECURITY ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzers die folgenden Schritte aus:**

1. Wenn der Benutzer einen Teil eines Enterprise-Kontos ist, müssen Sie sich [an das TINFOIL SECURITY-Supportteam wenden](https://www.tinfoilsecurity.com/contact), um das Benutzerkonto anlegen zu lassen.

1. Wenn der Benutzer ein normaler TINFOIL SECURITY-SaaS-Benutzer ist, kann der Benutzer zu allen Websites des Benutzers einen Mitarbeiter hinzufügen. Dies löst einen Prozess aus, bei dem eine Einladung an die angegebene E-Mail-Adresse gesendet wird, um ein neues TINFOIL SECURITY-Benutzerkonto zu erstellen.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von TINFOIL SECURITY-Benutzerkonten oder mithilfe der von TINFOIL SECURITY bereitgestellten APIs erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der TINFOIL SECURITY-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „TINFOIL SECURITY“ klicken, sollten Sie automatisch bei der TINFOIL SECURITY-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von TINFOIL SECURITY können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
