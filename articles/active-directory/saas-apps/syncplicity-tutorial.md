---
title: 'Tutorial: Azure Active Directory-Integration mit Syncplicity | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Syncplicity konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2021
ms.author: jeedes
ms.openlocfilehash: 6cbb1a53d2cf32ce5b55d1cb304b3ae675ae43d8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298986"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Tutorial: Integrieren von Syncplicity in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Syncplicity in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Syncplicity in Azure AD haben Sie folgende Möglichkeiten:

* Sie können in Azure AD steuern, wer Zugriff auf Syncplicity haben soll.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Syncplicity anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Syncplicity-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 
* Syncplicity unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-syncplicity-from-the-gallery"></a>Hinzufügen von Syncplicity aus dem Katalog

Zum Konfigurieren der Integration von Syncplicity in Azure AD müssen Sie Syncplicity über den Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Klicken Sie unter **Erstellen** auf **Unternehmensanwendung**.
1. Geben Sie im Abschnitt **Azure AD-Katalog durchsuchen** den Suchbegriff **Syncplicity** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Syncplicity** aus, und klicken Sie dann auf **Erstellen**, um die App hinzuzufügen. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-syncplicity"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Syncplicity

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Syncplicity mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Syncplicity eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Syncplicity die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Syncplicity](#configure-syncplicity-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Syncplicity-Testbenutzers](#create-syncplicity-test-user)**, um eine Entsprechung von B. Simon in Syncplicity zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert
1. **[Aktualisieren des einmaligen Anmeldens](#update-sso)** , um die erforderlichen Änderungen in Syncplicity vorzunehmen, falls Sie die SSO-Einstellungen in Azure AD geändert haben.

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Syncplicity** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<COMPANY_NAME>.syncplicity.com/sp`.

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<COMPANY_NAME>.syncplicity.com`.
    
    c. Geben Sie im Textfeld **Antwort-URL (Assertionsverbraucherdienst-URL)** eine URL in folgendem Format ein: `https://<COMPANY_NAME>.syncplicity.com/Auth/AssertionConsumerService.aspx`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Antwort-URL, Anmelde-URL und Bezeichner. Wenden Sie sich an den [Syncplicity-Support](https://www.syncplicity.com/contact-us), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Bearbeiten**. Klicken Sie dann im Dialogfeld neben dem aktiven Zertifikat auf die Schaltfläche mit Auslassungspunkten, und wählen Sie **Download des PEM-Zertifikats** aus.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

    > [!NOTE]
    > Sie benötigen das PEM-Zertifikat, da Syncplicity keine Zertifikate im CER-Format akzeptiert.

6. Kopieren Sie im Abschnitt **Syncplicity einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Syncplicity gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Syncplicity** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-syncplicity-sso"></a>Konfigurieren des einmaligen Anmeldens für Syncplicity

1. Melden Sie sich bei Ihrem **Syncplicity**-Mandanten an.

1. Klicken Sie im Menü oben auf **Admin** (Verwaltung), wählen Sie **Settings** (Einstellungen) aus, und klicken Sie dann auf **Custom domain and single sign-on** (Benutzerdefinierte Domäne und einmaliges Anmelden).

    ![Syncplicity](./media/syncplicity-tutorial/admin.png "Syncplicity")

1. Führen Sie auf der Dialogseite **Single Sign-On (SSO)** (Einmaliges Anmelden) die folgenden Schritte aus:

    ![Einmaliges Anmelden \(Single Sign-On, SSO\)](./media/syncplicity-tutorial/configuration.png "Single Sign-On \\\(SSO\\\)")

    a. Geben Sie im Textfeld **Custom Domain** (Benutzerdefinierte Domäne) den Namen Ihrer Domäne ein.
  
    b. Wählen Sie **Enabled** (Aktiviert) für **Single Sign-On Status** (Status der einmaligen Anmeldung) aus.

    c. Fügen Sie in das Textfeld **Entitäts-ID** den Wert von **Bezeichner (Entitäts-ID)** ein, den Sie im Azure-Portal in **Grundlegende SAML-Konfiguration** verwendet haben.

    d. Fügen Sie in das Textfeld **Sign-in page URL** (URL für Anmeldeseite) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    e. Fügen Sie in das Textfeld **Logout page URL** (URL der Abmeldeseite) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    f. Klicken Sie unter **Zertifikat des Identitätsanbieters** auf **Datei auswählen**, und laden Sie dann das Zertifikat hoch, das Sie aus dem Azure-Portal heruntergeladen haben.

    g. Klicken Sie auf **ÄNDERUNGEN SPEICHERN**.

### <a name="create-syncplicity-test-user"></a>Erstellen eines Testbenutzers für Syncplicity

Damit sich Azure AD-Benutzer anmelden können, müssen sie in der Syncplicity-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Syncplicity Azure AD-Benutzerkonten erstellt werden.

**Führen Sie zum Bereitstellen von Benutzerkonten in Syncplicity die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **Syncplicity** -Mandanten an (Beispiel: `https://company.Syncplicity.com`).

1. Klicken Sie auf **Admin** (Verwaltung), wählen Sie **User Accounts** (Benutzerkonten) aus, und klicken Sie dann auf **Add a user** (Benutzer hinzufügen).

    ![Verwalten von Benutzern](./media/syncplicity-tutorial/users.png "Benutzer verwalten")

1. Geben Sie die **E-Mail-Adresse** eines Azure AD-Kontos ein, das Sie bereitstellen möchten, und wählen Sie dann für **Role** (Rolle) die Option **User** (Benutzer) aus. Klicken Sie abschließend auf **Next** (Weiter).

    ![Azure-Kontoinformationen](./media/syncplicity-tutorial/roles.png "Azure-Kontoinformationen")

    > [!NOTE]
    > Der Azure AD-Kontoinhaber erhält eine E-Mail mit einem Link zur Bestätigung und Aktivierung des Kontos.

1. Wählen Sie eine Gruppe in Ihrem Unternehmen aus, zu dem der neue Benutzer gehören soll, und klicken Sie dann auf **Next** (Weiter).

    ![Gruppenmitgliedschaft](./media/syncplicity-tutorial/group.png "Gruppenmitgliedschaft")

    > [!NOTE]
    > Wenn keine Gruppen aufgelistet sind, klicken Sie einfach auf **Next** (Weiter).

1. Wählen Sie die Ordner aus, die Sie auf dem Computer des Benutzers von Syncplicity steuern lassen möchten, und klicken Sie dann auf **Next** (Weiter).

    ![Syncplicity-Ordner](./media/syncplicity-tutorial/folder.png "Syncplicity-Ordner")

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Syncplicity-Benutzerkonten oder mithilfe der von Syncplicity bereitgestellten APIs erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Syncplicity weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Syncplicity-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „Syncplicity“ klicken, werden Sie zur Anmelde-URL für Syncplicity weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

### <a name="update-sso"></a>Aktualisieren des einmaligen Anmeldens

Wenn Sie Änderungen am einmaligen Anmelden vornehmen müssen, müssen Sie das verwendete **SAML-Signaturzertifikat** überprüfen. Hat sich das Zertifikat geändert, stellen Sie sicher, dass das neue Zertifikat in Syncplicity hochgeladen wird, wie unter **[Konfigurieren des einmaligen Anmeldens für Syncplicity](#configure-syncplicity-sso)** beschrieben.

Wenden Sie sich bei Verwendung der mobilen Syncplicity-App an den Syncplicity-Kundensupport (support@syncplicity.com), um Unterstützung zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Syncplicity können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
