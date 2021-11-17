---
title: 'Tutorial: Azure Active Directory-Integration mit Sprinklr | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Sprinklr konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: f7dc7215f812938584438d3f212ce31ddcea23df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329046"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Azure Active Directory-Integration mit Sprinklr

In diesem Tutorial erfahren Sie, wie Sie Sprinklr in Azure Active Directory (Azure AD) integrieren. Die Integration von Sprinklr in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Sprinklr besitzen soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Sprinklr anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Sprinklr-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Sprinklr unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-sprinklr-from-the-gallery"></a>Hinzufügen von Sprinklr aus dem Katalog

Zum Konfigurieren der Integration von Sprinklr in Azure AD müssen Sie Sprinklr über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Sprinklr** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Sprinklr** aus, und fügen Sie die App dann hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sprinklr"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Sprinklr

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Sprinklr mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sprinklr eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Sprinklr die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren von Sprinklr-SSO](#configure-sprinklr-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Sprinklr-Testbenutzers](#create-sprinklr-test-user)** , um eine Entsprechung von B. Simon in Sprinklr zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Sprinklr** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.sprinklr.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.sprinklr.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an den [Sprinklr-Support](https://www.sprinklr.com/contact-us/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Sprinklr einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Sprinklr gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Sprinklr** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sprinklr-sso"></a>Konfigurieren von Sprinklr-SSO

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Sprinklr-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Verwaltung \> Einstellungen**.

    ![Verwaltung](./media/sprinklr-tutorial/settings.png "Verwaltung")

1. Wechseln Sie im linken Fensterbereich zu **Partner verwalten \> Einmaliges Anmelden**.

    ![Partner verwalten](./media/sprinklr-tutorial/users.png "Partner verwalten")

1. Klicken Sie auf **+Einmalige Anmeldungen hinzufügen**.

    ![Screenshot der Schaltfläche „Einmalige Anmeldungen hinzufügen“.](./media/sprinklr-tutorial/add-user.png "Einmaliges Anmelden")

1. Führen Sie im Dialogfeld **Einmaliges Anmelden** die folgenden Schritte aus:

    ![Screenshot der Seite „Einmaliges Anmelden“, auf der Sie die beschriebenen Werte eingeben können.](./media/sprinklr-tutorial/configuration.png "Einmaliges Anmelden")

    a. Geben Sie im Textfeld **Name** einen Namen für Ihre Konfiguration ein (z.B.: **WAADSSOTest**).

    b. Wählen Sie **Aktiviert**.

    c. Wählen Sie **Neues SSO-Zertifikat verwenden**.

    d. Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.

    e. Fügen Sie in das Textfeld **Entity Id** (Entitäts-ID) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    f. Fügen Sie in das Textfeld **Identity Provider Login URL** (Anmelde-URL des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Fügen Sie in das Textfeld **Identity Provider Logout URL** (Abmelde-URL des Identitätsanbieters) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    h. Wählen Sie für **SAML-Benutzer-ID-Typ** die Option **Assertion enthält sprinklr.com-Benutzernamen des Benutzers** aus.

    i. Wählen Sie für **SAML-Benutzer-ID-Speicherort** die Option **Benutzer-ID ist das Namensbezeichnerelement der Ausstellererklärung** aus.

    j. Klicken Sie auf **Speichern**.

    ![SAML](./media/sprinklr-tutorial/save-configuration.png "SAML")

### <a name="create-sprinklr-test-user"></a>Erstellen eines Sprinklr-Testbenutzers

1. Melden Sie sich bei der Sprinklr-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Verwaltung \> Einstellungen**.

    ![Verwaltung](./media/sprinklr-tutorial/settings.png "Verwaltung")

1. Wechseln Sie im linken Fensterbereich zu **Client verwalten \> Benutzer**.

    ![Screenshot der Schaltfläche „Benutzer hinzufügen“ unter „Einstellungen“/„Benutzer“.](./media/sprinklr-tutorial/client.png "Einstellungen")

1. Klicken Sie auf **Benutzer hinzufügen**.

    ![Screenshot des Dialogfelds „Benutzer bearbeiten“, in dem Sie die beschriebenen Werte eingeben können.](./media/sprinklr-tutorial/search-users.png "Einstellungen")

1. Führen Sie im Dialogfeld **Benutzer bearbeiten** die folgenden Schritte aus:

    ![Benutzer bearbeiten](./media/sprinklr-tutorial/update-users.png "Benutzer bearbeiten")

    a. Geben Sie in die Textfelder **E-Mail**, **Vorname** und **Nachname** die Informationen des Azure AD-Benutzerkontos ein, das Sie bereitstellen möchten.

    b. Wählen Sie **Kennwort deaktiviert**.

    c. Wählen Sie **Sprache** aus.

    d. Wählen Sie **Benutzertyp** aus.

    e. Klicken Sie auf **Aktualisieren**.

    > [!IMPORTANT]
    > **Kennwort deaktiviert** muss ausgewählt sein, um einem Benutzer die Anmeldung über einen Identitätsanbieter zu ermöglichen. 

1. Wechseln Sie zu **Rolle**, und führen Sie dann die folgenden Schritte aus:

    ![Partnerrollen](./media/sprinklr-tutorial/role.png "Partnerrollen")

    a. Wählen Sie aus der Liste **Global** die Option **ALL_Permissions** aus.  

    b. Klicken Sie auf **Aktualisieren**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Sprinklr-Benutzerkonten oder mithilfe der von Sprinklr bereitgestellten APIs erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Sprinklr weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Navigieren Sie direkt zur Sprinklr-Anmelde-URL, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“auf die Kachel „Sprinklr“ klicken, werden Sie zur Anmelde-URL für Sprinklr weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Sprinklr können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
