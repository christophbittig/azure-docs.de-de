---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Hub Planner'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Hub Planner konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: b08bec1f035a9b4d0c7bc6f1b7f8ac7361ba6ce4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285299"
---
# <a name="tutorial-azure-ad-sso-integration-with-hub-planner"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Hub Planner

In diesem Tutorial erfahren Sie, wie Sie Hub Planner in Azure Active Directory (Azure AD) integrieren. Die Integration von Hub Planner in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Hub Planner haben soll.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Hub Planner anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Hub Planner-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Hub Planner unterstützt **SP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-hub-planner-from-the-gallery"></a>Hinzufügen von Hub Planner aus dem Katalog

Zum Konfigurieren der Integration von Hub Planner in Azure AD müssen Sie Ihrer Liste der verwalteten SaaS-Apps Hub Planner aus dem Katalog hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Begriff **Hub Planner** ein.
1. Wählen Sie im Ergebnisbereich den Eintrag **Hub Planner** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-hub-planner"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Hub Planner

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Hub Planner mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Hub Planner eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Hub Planner die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Hub Planner](#configure-hub-planner-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Hub Planner-Testbenutzers](#create-hub-planner-test-user)** , um eine Entsprechung von B. Simon in Hub Planner zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Hub Planner** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://app.hubplanner.com/sso/metadata`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://app.hubplanner.com/sso/callback`

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.hubplanner.com`

    > [!NOTE]
    > Dies sind die Werte, die Sie verwenden. Die einzige erforderliche Änderung besteht darin, \<SUBDOMAIN\> in der **Anmelde-URL** durch die Unterdomäne zu ersetzen, die Sie bei der Registrierung für Hub Planner erhalten haben. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Hub Planner einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Hub Planner gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Anwendung **Hub Planner** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-hub-planner-sso"></a>Konfigurieren des einmaligen Anmeldens für Hub Planner

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Hub Planner** müssen Sie sich bei Ihrem Hub Planner-Konto anmelden und die unten angegebenen Schritte ausführen. 

### <a name="install-the-extension-in-hub-planner"></a>Installieren der Erweiterung in Hub Planner

Um die SSO-Funktionalität zu aktivieren, müssen Sie zunächst die Erweiterung aktivieren. Führen Sie als Kontobesitzer oder mit den entsprechenden Berechtigungen die folgenden Schritte aus:

1. Wechseln Sie zu **Einstellungen**.
1. Wählen Sie im seitlichen Menü die Option **Erweiterungen verwalten** > **Erweiterungen hinzufügen/entfernen** aus.
1. Suchen Sie nach der Erweiterung für einmaliges Anmelden, und fügen Sie sie hinzu (oder wählen Sie „Kostenlos testen“ aus).
1. Stimmen Sie nach entsprechender Aufforderung den Nutzungsbedingungen zu, und wählen Sie anschließend **Jetzt hinzufügen** aus.

### <a name="enable-sso"></a>Aktivieren von SSO

Nachdem die Erweiterung aktiviert wurde, müssen Sie SSO für Ihr Konto aktivieren. 

1. Wechseln Sie zu **Einstellungen**.
1. Wählen Sie im seitlichen Menü die Option **Authentifizierung** aus.
1. Wählen Sie **SSO (Single Sign-On)** aus.
1. Geben Sie zusätzliche Authentifizierungsinformationen wie in der folgenden Abbildung dargestellt ein, und wählen Sie dann **Speichern** aus.

![Screenshot: SSO-Einstellungen](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Erstellen eines Hub Planner-Testbenutzers

Wenn Sie weitere Benutzer hinzufügen möchten, können Sie dies unter **Einstellungen** > **Ressourcen verwalten** durchführen. Achten Sie darauf, dass Sie die entsprechenden E-Mail-Adressen hinzufügen und die Benutzer einladen. Nach dem Absenden der Einladung erhalten die Benutzer eine E-Mail und können SSO nutzen. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Hub Planner weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Hub Planner-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Hub Planner“ klicken, werden Sie zur Anmelde-URL für Hub Planner umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Hub Planner können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
