---
title: 'Tutorial: Azure Active Directory-Integration mit Soloinsight-CloudGate SSO | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Soloinsight-CloudGate SSO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 0fabc06ae9656015296be8c6d96a0f3c3313bc3c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333331"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Tutorial: Integrieren von Soloinsight-CloudGate SSO in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Soloinsight-CloudGate SSO in Azure Active Directory (Azure AD) integrieren. Die Integration von Soloinsight-CloudGate SSO in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Soloinsight-CloudGate SSO hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Soloinsight-CloudGate SSO anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Soloinsight-CloudGate SSO-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Soloinsight-CloudGate SSO unterstützt **SP-initiiertes** einmaliges Anmelden.
* Soloinsight-CloudGate SSO unterstützt die [automatische Benutzerbereitstellung](soloinsight-cloudgate-sso-provisioning-tutorial.md).

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Hinzufügen von Soloinsight-CloudGate SSO aus dem Katalog

Zum Konfigurieren der Integration von Soloinsight-CloudGate SSO in Azure AD müssen Sie Soloinsight-CloudGate SSO aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Soloinsight-CloudGate SSO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Soloinsight-CloudGate SSO** aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-soloinsight-cloudgate-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Soloinsight-CloudGate SSO

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Soloinsight-CloudGate SSO mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Soloinsight-CloudGate SSO eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens mit Soloinsight-CloudGate SSO über Azure AD die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren von Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Soloinsight-CloudGate SSO-Testbenutzers](#create-soloinsight-cloudgate-sso-test-user)** , um eine Entsprechung von B. Simon in Soloinsight-CloudGate SSO zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Soloinsight-CloudGate SSO** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie auf der Seite **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    1. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.sigateway.com/login`.

    1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.sigateway.com/process/sso`.

   > [!NOTE]
   > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit der tatsächlichen Anmelde-URL und dem Bezeichner. Dies ist später in diesem Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für Soloinsight-CloudGate SSO** beschrieben.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen**, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Soloinsight-CloudGate SSO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden von Azure, indem Sie Zugriff auf Soloinsight-CloudGate SSO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Soloinsight-CloudGate SSO**.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurieren von Soloinsight-CloudGate SSO

1. Wenn Sie die Konfiguration in Soloinsight-CloudGate SSO automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Soloinsight-CloudGate SSO einrichten**, um zur Anwendung Soloinsight-CloudGate SSO weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Soloinsight-CloudGate SSO anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 8.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Soloinsight-CloudGate SSO manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Soloinsight-CloudGate SSO-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Melden Sie sich mit Ihren Anmeldeinformationen beim CloudGate-Webportal an, um die Werte abzurufen, die bei der grundlegenden SAML-Konfiguration im Azure-Portal eingefügt werden sollen. Greifen Sie anschließend auf die SSO-Einstellungen zu, die sich unter dem Pfad **Startseite > Verwaltung > Systemeinstellungen > Allgemein** befinden.

    ![CloudGate SSO-Einstellungen](./media/soloinsight-cloudgate-sso-tutorial/main-settings.png)

5. **SAML-Consumer-URL**

    * Kopieren Sie die Links in den Feldern **Saml Consumer URL** (SAML-Consumer-URL) und **Redirect URL** (Umleitungs-URL), und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in die Felder **Bezeichner (Entitäts-ID)** und **Antwort-URL** ein.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/identifier.png)

6. **SAML-Signaturzertifikat**

    * Navigieren Sie zur Quelle der Zertifikatdatei (Base64), die aus den Listen unter „SAML-Signaturzertifikat“ im Azure-Portal heruntergeladen wurden, und klicken Sie mit der rechten Maustaste darauf. Wählen Sie in der Liste die Option **Edit with Notepad++** (Mit Notepad++ bearbeiten). 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopieren Sie den Inhalt der Notepad++-Zertifikatdatei (Base64).

        ![Zertifikatkopie](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Fügen Sie den Inhalt in den SSO-Einstellungen im CloudGate-Webportal in das Feld **Zertifikat** ein, und klicken Sie auf die Schaltfläche „Speichern“.

        ![Zertifikatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Default Group** (Standardgruppe)

    * Wählen Sie im CloudGate-Webportal in der Dropdownliste von **Default Group** (Standardgruppe) die Option **Business Admin** (Unternehmensadministrator).

        ![Standardgruppe](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-Bezeichner und Anmelde-URL**

    * Die kopierte **Anmelde-URL** aus der Konfiguration unter **Soloinsight-CloudGate SSO einrichten** im Azure-Portal muss in den Abschnitt mit den SSO-Einstellungen des CloudGate-Webportals eingegeben werden.

    * Fügen Sie den Link **Anmelde-URL** aus dem Azure-Portal in das Feld **AD Login URL** (AD-Anmelde-URL) im CloudGate-Webportal ein.

    * Fügen Sie den Link **Azure AD-Bezeichner** aus dem Azure-Portal in das Feld **AD Identifier** (AD-Bezeichner) im CloudGate-Webportal ein.

        ![AD-Anmeldung](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Erstellen eines Soloinsight-CloudGate SSO-Testbenutzers

Wählen Sie zum Erstellen eines Testbenutzers im Hauptmenü Ihres CloudGate-Webportals die Option **Mitarbeiter**, und füllen Sie das Formular „Add New employee“ (Neuen Mitarbeiter hinzufügen) aus. Die Autoritätsebene, die dem Testbenutzer zugewiesen werden soll, lautet **Business Admin** (Unternehmensadministrator). Klicken Sie auf **Erstellen**, nachdem alle erforderlichen Felder ausgefüllt wurden.

![Mitarbeitertest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

> [!NOTE]
> Außerdem unterstützt Soloinsight-CloudGate SSO die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./soloinsight-cloudgate-sso-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Soloinsight-CloudGate SSO weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Navigieren Sie direkt zur Anmelde-URL für Soloinsight-CloudGate SSO, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Soloinsight-CloudGate SSO“ klicken, werden Sie an die Anmelde-URL von Soloinsight-CloudGate SSO umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Soloinsight-CloudGate SSO können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
