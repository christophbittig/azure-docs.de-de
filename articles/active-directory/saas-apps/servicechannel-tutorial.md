---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit ServiceChannel | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ServiceChannel konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: c5372588f2bf3dc6b7e720ecf42f1ecf3e50364c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299252"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicechannel"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit ServiceChannel

In diesem Tutorial erfahren Sie, wie Sie ServiceChannel in Azure Active Directory (Azure AD) integrieren. Die Integration von ServiceChannel in Azure AD ermöglicht Folgendes:

- Steuern Sie in Azure AD, wer Zugriff auf ServiceChannel hat.
- Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei ServiceChannel anzumelden.
- Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
- Ein ServiceChannel-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

- ServiceChannel unterstützt **IDP**-initiiertes einmaliges Anmelden.
- ServiceChannel unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-servicechannel-from-the-gallery"></a>Hinzufügen von ServiceChannel aus dem Katalog

Zum Konfigurieren der Integration von ServiceChannel in Azure AD müssen Sie ServiceChannel aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ServiceChannel** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ServiceChannel** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-servicechannel"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ServiceChannel

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ServiceChannel mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ServiceChannel eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ServiceChannel die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für ServiceChannel](#configure-servicechannel-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen eines ServiceChannel-Testbenutzers](#create-servicechannel-test-user)**, um eine Entsprechung von B. Simon in ServiceChannel zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ServiceChannel** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Werte für die folgenden Felder ein:

   a. Geben Sie im Textfeld **Bezeichner** den Wert `http://adfs.<domain>.com/adfs/service/trust` ein.

   b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<customer domain>.servicechannel.com/saml/acs`

   > [!NOTE]
   > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Hier empfehlen wir Ihnen, den eindeutigen Wert der Zeichenfolge im Bezeichner zu verwenden. Wenden Sie sich an das [Supportteam für den ServiceChannel-Client](https://servicechannel.zendesk.com/hc/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Der Rollenanspruch ist vorkonfiguriert und muss nicht von Ihnen konfiguriert werden, Sie müssen ihn aber dennoch in Azure AD erstellen. Informationen dazu finden Sie in [diesem Artikel](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui). Weitere Anleitungen zu Anforderungen finden Sie im ServiceChannel-Handbuch [hier](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example).

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **ServiceChannel einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ServiceChannel gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **ServiceChannel** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie die Rollen wie oben erläutert eingerichtet haben, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-servicechannel-sso"></a>Konfigurieren des einmaligen Anmeldens für ServiceChannel

Um das einmalige Anmelden auf der Seite von **ServiceChannel** zu konfigurieren, müssen Sie das heruntergeladene **Zertifikat (Base64)** und die entsprechenden aus dem Azure-Portal kopierten URLs an das [ServiceChannel-Supportteam](https://servicechannel.zendesk.com/hc/) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-servicechannel-test-user"></a>Erstellen eines ServiceChannel-Testbenutzers

Die Anwendung unterstützt die Just-in-Time-Benutzerbereitstellung. Nach der Authentifizierung werden in der Anwendung automatisch Benutzer erstellt. Informationen zur vollständigen Benutzerbereitstellung erhalten Sie vom [ServiceChannel-Supportteam](https://servicechannel.zendesk.com/hc/).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

- Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der ServiceChannel-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

- Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „ServiceChannel“ klicken, sollten Sie automatisch bei der ServiceChannel-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von ServiceChannel können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
