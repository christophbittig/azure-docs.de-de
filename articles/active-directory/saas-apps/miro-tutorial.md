---
title: 'Tutorial: Azure Active Directory-Integration mit Miro | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Miro konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/26/2021
ms.author: jeedes
ms.openlocfilehash: 6e2b3c611c5bc06e29fb739f01dc69605509a63a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124795182"
---
# <a name="tutorial-integrate-miro-with-azure-active-directory"></a>Tutorial: Integrieren von Miro in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Miro in Azure Active Directory (Azure AD) integrieren. Eine weitere Version dieses Tutorials finden Sie unter help.miro.com. Die Integration von Miro in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Miro hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Miro anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Miro-Abonnement, für das das einmalige Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 
* Miro unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden sowie die **Just-In-Time**-Benutzerbereitstellung.
* Miro unterstützt die [**Automatisierte** Benutzerbereitstellung und Bereitstellungsaufhebung](miro-provisioning-tutorial.md) (empfohlen).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-miro-from-the-gallery"></a>Hinzufügen von Miro aus dem Katalog

Zum Konfigurieren der Integration von Miro in Azure AD müssen Sie Miro aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Miro** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Miro** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-miro"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Miro

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Miro mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Miro eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Miro die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Miro](#configure-miro-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Miro-Testbenutzers](#create-miro-test-user)** , um eine Entsprechung von B. Simon in Miro zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Miro** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Bezeichner** die URL `https://miro.com/` ein.

5. Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, geben Sie die URL in das Textfeld **Anmelde-URL** ein: `https://miro.com/sso/login/`

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen**, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer. Sie benötigen diese Angabe zum Konfigurieren von SSO auf der Seite von Miro.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png "Downloadlink für das Zertifikat")

1. Kopieren Sie im Abschnitt **Miro einrichten** die Anmelde-URL. Sie benötigen diese Angabe zum Konfigurieren von SSO auf der Seite von Miro.

   ![Kopieren der Anmelde-URL](./media/miro-tutorial/login.png "Kopieren der Anmelde-URL")

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihm Zugriff auf Miro gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Miro** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

* Navigieren Sie alternativ zu den **Eigenschaften** der Anwendung, und deaktivieren Sie **Benutzerzuweisung erforderlich**
![Zuweisungsanforderung deaktivieren](./media/miro-tutorial/properties.png "Deaktivieren der Zuweisungsanforderung").

## <a name="configure-miro-sso"></a>Konfigurieren des einmaligen Anmeldens für Miro

Verwenden Sie zum Konfigurieren von SSO auf der Seite von Miro das zuvor heruntergeladene Zertifikat und die zuvor kopierte Anmelde-URL. Navigieren Sie in den Miro-Kontoeinstellungen zum Abschnitt **Security** (Sicherheit), und aktivieren Sie **Enable SSO/SAML** (SSO/SAML aktivieren). 

1. Fügen Sie die Anmelde-URL in das Feld **SAML-Anmelde-URL** ein.
1. Öffnen Sie die Zertifikatdatei mit einem Text-Editor, und kopieren Sie die Zertifikatsequenz. Fügen Sie die Sequenz in das Feld **Key x509 Certificate** (Schlüssel x509-Zertifikat) ein.
![Miro-Einstellungen](./media/miro-tutorial/security.png "Miro-Einstellungen")

1. Geben Sie im Feld **Domains** (Domänen) Ihre Domänenadresse ein, klicken Sie auf **Add** (Hinzufügen), und führen Sie das Überprüfungsverfahren aus. Wiederholen Sie diesen Vorgang für Ihre anderen Domänenadressen, sofern vorhanden. Das Miro-SSO-Feature funktioniert für Endbenutzer, deren Domänen in der Liste aufgeführt werden. 
![Domäne](./media/miro-tutorial/add-domain.png "Domain")

1. Entscheiden Sie, ob Sie Just-in-Time-Bereitstellung verwenden möchten (pullen Sie Ihre Benutzer während der Registrierung in Miro in Ihr Abonnement), und klicken Sie auf **Save** (Speichern), um die SSO-Konfiguration auf der Miro-Seite abzuschließen.
![Just-in-Time-Bereitstellung](./media/miro-tutorial/save-configuration.png "Just-in-Time-Bereitstellung") 

### <a name="create-miro-test-user"></a>Erstellen eines Miro-Testbenutzers

In diesem Abschnitt wird in Miro ein Benutzer namens B. Simon erstellt. Miro unterstützt Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Miro vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen mithilfe des Testbenutzers B. Simon. 

#### <a name="sp-initiated"></a>SP-initiiert:

* Navigieren Sie direkt zur Anmelde-URL für Miro, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**, und melden Sie sich als B. Simon an. Sie sollten automatisch bei dem Miro-Abonnement angemeldet werden, für das Sie SSO eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Miro“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Miro-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).