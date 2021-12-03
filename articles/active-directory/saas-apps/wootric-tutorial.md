---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Wootric | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Wootric konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: ef484784517b93a2fa935d558aed29598d782e42
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316411"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wootric"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Wootric

In diesem Tutorial erfahren Sie, wie Sie Wootric in Azure Active Directory (Azure AD) integrieren. Die Integration von Wootric in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Wootric hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Wootric anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Wootric-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Wootric unterstützt **IDP-initiiertes** einmaliges Anmelden.
* Wootric unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="adding-wootric-from-the-gallery"></a>Hinzufügen von Wootric aus dem Katalog

Zum Konfigurieren der Integration von Wootric in Azure AD müssen Sie Wootric aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Wootric** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Wootric** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-wootric"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Wootric

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Wootric mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Wootric eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Wootric die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Wootric](#configure-wootric-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Wootric-Testbenutzers](#create-wootric-test-user)** , um in Wootric eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Wootric** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **SAML-Basiskonfiguration** ist die Anwendung vorkonfiguriert und die notwendigen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche **Speichern** klickt.


1. Die Wootric-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die Wootric-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name |  Quellattribut |
    | -------------- | --------- |
    | id | user.objectid |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Wootric einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Wootric gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Wootric** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-wootric-sso"></a>Konfigurieren des einmaligen Anmeldens für Wootric

1. Wenn Sie die Konfiguration in Wootric automatisieren möchten, müssen Sie die **Browsererweiterung zur sicheren Anmeldung bei „Meine Apps“** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Wenn Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Wootric einrichten** klicken, werden Sie zur Wootric-Anwendung weitergeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Wootric anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Wootric manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der Wootric-Unternehmenswebsite an.

1. Klicken Sie im oberen Menü auf das **Einstellungssymbol**.

    ![Screenshot des ausgewählten Einstellungssymbols auf der Wootric-Website](./media/wootric-tutorial/configure-1.PNG)

1. Wählen Sie im Menü auf der linken Seite unter **INTEGRATIONS** (INTEGRATIONEN) die Option **Authentication** (Authentifizierung) aus, und klicken Sie auf **Enable Single Sign On with Azure Active Directory** (Einmaliges Anmelden mit Azure Active Directory aktivieren).

    ![Screenshot der verbundenen Option zum Aktivieren des einmaligen Anmeldens mit Azure Active Directory für die Authentifizierung](./media/wootric-tutorial/configure-2.PNG)

1. Führen Sie auf der folgenden Seite die folgenden Schritte aus:

    ![Screenshot der Seite mit Einstellungen zum Eingeben der beschriebenen Werte](./media/wootric-tutorial/configure-3.PNG)

    a. Fügen Sie den Wert der **Anmelde-URL**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **URL für einmaliges Anmelden des Identitätsanbieters** ein.

    b. Fügen Sie in das Textfeld **Identity Provider Issuer** (Aussteller des Identitätsanbieters) die **Entitäts-ID** ein, die Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das aus dem Azure-Portal heruntergeladene **Zertifikat (Base64)** im Editor, und fügen Sie den Inhalt in das Textfeld **X.509 Certificate** (X.509-Zertifikat) ein.

    d. Aktivieren Sie das Kontrollkästchen **Automatically grant access to new users** (Neuen Benutzern automatisch Zugriff gewähren).
    
    e. Klicken Sie auf **Speichern**.

### <a name="create-wootric-test-user"></a>Erstellen eines Wootric-Testbenutzers

In diesem Abschnitt wird in Wootric ein Benutzer namens B. Simon erstellt. Wootric unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Wootric vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Wootric-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Wootric“ klicken, sollten Sie automatisch bei der Wootric-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Wootric können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
