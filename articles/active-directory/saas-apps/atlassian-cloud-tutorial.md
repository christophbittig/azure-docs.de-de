---
title: 'Tutorial: Azure Active Directory-Integration mit Atlassian Cloud | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Atlassian Cloud konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2021
ms.author: jeedes
ms.openlocfilehash: c5fcd69bb887f089a22f030330cf01ffaf890eb7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321810"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutorial: Integrieren von Atlassian Cloud und Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Atlassian Cloud in Azure Active Directory (Azure AD) integrieren. Die Integration von Atlassian Cloud in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Atlassian Cloud hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Atlassian Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Atlassian Cloud-Abonnement, für das einmaliges Anmelden aktiviert ist
* Um SAML-SSO (Security Assertion Markup Language) für Atlassian Cloud-Produkte zu ermöglichen, müssen Sie Atlassian Access einrichten. Hier finden Sie weitere Informationen zu [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 

* Atlassian Cloud unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.
* Atlassian Cloud unterstützt [automatische Benutzerbereitstellung und -bereitstellungsaufhebung](atlassian-cloud-provisioning-tutorial.md).

## <a name="add-atlassian-cloud-from-the-gallery"></a>Hinzufügen von Atlassian Cloud aus dem Katalog

Zum Konfigurieren der Integration von Atlassian Cloud in Azure AD müssen Sie Atlassian Cloud aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Atlassian Cloud** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Atlassian Cloud** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Atlassian Cloud mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Atlassian Cloud eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Atlassian Cloud die folgenden Schritte aus:

1. **[Konfigurieren von Azure AD mit dem einmaligen Anmelden von Atlassian Cloud](#configure-azure-ad-with-atlassian-cloud-sso)** , um Ihren Benutzern die Verwendung des Azure AD-basierten SAML-SSO mit Atlassian Cloud zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Erstellen eines Atlassian Cloud-Testbenutzers](#create-atlassian-cloud-test-user)** , um eine Entsprechung von B. Simon in Atlassian Cloud zu erhalten, die mit ihrer Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-with-atlassian-cloud-sso"></a>Konfigurieren des einmaligen Anmeldens von Azure AD mit Atlassian Cloud

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Wenn Sie die Konfiguration in Atlassian Cloud automatisieren möchten, müssen Sie die Browsererweiterung **Meine Apps** für die sichere Anmeldung installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Atlassian Cloud einrichten**, um zur Anwendung „Atlassian Cloud“ weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Atlassian Cloud anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie Atlassian Cloud manuell einrichten möchten, melden Sie sich bei der Atlassian Cloud-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

1. Navigieren Sie zunächst zu Ihrer Atlassian-Produktinstanz, und kopieren und speichern Sie die Instanz-URL.
   > [!NOTE]
   > Die URL sollte dem Muster `https://<INSTANCE>.atlassian.com` entsprechen.

   ![Instanzname](./media/atlassian-cloud-tutorial/instance.png)

1. Öffnen Sie das [Atlassian-Verwaltungsportal](https://admin.atlassian.com/), und klicken Sie auf den Namen Ihrer Organisation.

   ![Verwaltungsportal](./media/atlassian-cloud-tutorial/organization.png)

1. Vor dem Konfigurieren des einmaligen Anmeldens müssen Sie Ihre Domäne überprüfen. Weitere Informationen finden Sie im Dokument zur [Atlassian-Domänenüberprüfung](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

1. Navigieren Sie im **ATLASSIAN-Verwaltungsportal** zur Registerkarte **Security** (Sicherheit), wählen Sie **SAML single sign-on** (Einmaliges Anmelden für SAML) aus, und klicken Sie auf **Add SAML configuration** (SAML-Konfiguration hinzufügen).

   ![Sicherheit](./media/atlassian-cloud-tutorial/admin.png)

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Atlassian Cloud** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden einrichten** aus.

   ![Einrichten von SSO](./media/atlassian-cloud-tutorial/set-up.png)

1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.

   ![SAML in Azure](./media/atlassian-cloud-tutorial/azure.png)

1. Scrollen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** nach unten zu **Atlassian Cloud einrichten**
   
   a. Klicken Sie auf **Konfiguration-URLs**.

   ![Single Sign-On](./media/atlassian-cloud-tutorial/configure.png)
   
   b. Kopieren Sie im Azure-Portal den Wert für **Azure AD-Bezeichner**, und fügen Sie ihn in Atlassian in das Textfeld **Identity Provider Entity ID** (Entitäts-ID des Identitätsanbieters) ein.
   
   c. Kopieren Sie im Azure-Portal den Wert für **Anmelde-URL**, und fügen Sie ihn in Atlassian in das Textfeld **Identity Provider SSO URL** (SSO-URL des Identitätsanbieters) ein.

   ![Identity Provider SSO URL (SSO-URL des Identitätsanbieters)](./media/atlassian-cloud-tutorial/configuration-azure.png)

   ![Entitäts-ID](./media/atlassian-cloud-tutorial/login.png)

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen**, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Signaturzertifikat](./media/atlassian-cloud-tutorial/certificate.png)

   ![Zertifikat 1](./media/atlassian-cloud-tutorial/certificate-download.png)

1. Sie müssen die SAML-Konfiguration in Atlassian **hinzufügen** und **speichern**.

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

   a. Kopieren Sie in Atlassian den Wert unter **SP Entity ID** (SP-Entitäts-ID), fügen Sie ihn in Azure in das Feld **Bezeichner (Entitäts-ID)** ein, und legen Sie ihn als Standardwert fest.
   
   b. Kopieren Sie in Atlassian den Wert für **SP Assertion Consumer Service URL** (URL des SP-Assertionsverbraucherdiensts), fügen Sie ihn in Azure in das Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** ein, und legen Sie ihn als Standardwert fest.
   
   c. Kopieren Sie den Wert für die **Instanz-URL**, die Sie in Schritt 4 kopiert haben, und fügen Sie ihn in Azure in das Feld **Relayzustand** ein.

   ![Kopieren der URLs](./media/atlassian-cloud-tutorial/values.png)

   ![Schaltfläche](./media/atlassian-cloud-tutorial/edit-button.png)

   ![Abbildung der URLs](./media/atlassian-cloud-tutorial/image.png)
   
1. Ihre Atlassian Cloud-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Sie können die Attributzuordnung bearbeiten, indem Sie auf das Symbol **Bearbeiten** klicken. 

   ![attributes](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. Attributzuordnung für einen Azure AD-Mandanten mit einer Microsoft 365-Lizenz
      
      a. Klicken Sie auf den Anspruch **Eindeutiger Benutzerbezeichner (Namens-ID)** .

      ![Attribute und Ansprüche](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud erwartet, dass **nameidentifier** (**Eindeutiger Benutzerbezeichner**) der E-Mail des Benutzers (**user.email**) zugeordnet ist. Bearbeiten Sie den Wert unter **Source attribute** (Quellattribut), und ändern Sie ihn in **user.mail**. Speichern Sie die am Anspruch vorgenommenen Änderungen.

      ![Eindeutige Benutzer-ID](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. Die endgültigen Attributzuordnungen sollten wie folgt aussehen:

      ![Abbildung 2](./media/atlassian-cloud-tutorial/attributes.png)
      
   1. Attributzuordnung für einen Azure AD-Mandanten ohne Microsoft 365-Lizenz 

      a. Klicken Sie auf den Anspruch `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

      ![Abbildung 3](./media/atlassian-cloud-tutorial/claims.png)
         
      b. Azure füllt das Attribut **user.mail** für Benutzer, die in Azure AD-Mandanten ohne Microsoft 365-Lizenzen erstellt wurden, nicht auf und speichert die E-Mail für diese Benutzer im Attribut **userprincipalname**. Atlassian Cloud erwartet, dass **nameidentifier** (**Eindeutiger Benutzerbezeichner**) der E-Mail des Benutzers (**user.userprincipalname**) zugeordnet ist.  Bearbeiten Sie den Wert unter **Source attribute** (Quellattribut), und ändern Sie ihn in **user.userprincipalname**. Speichern Sie die am Anspruch vorgenommenen Änderungen.

      ![Festlegen der E-Mail](./media/atlassian-cloud-tutorial/save-claims.png)
         
      c. Die endgültigen Attributzuordnungen sollten wie folgt aussehen:

      ![Abbildung 4](./media/atlassian-cloud-tutorial/final-attributes.png)

1. Führen Sie die folgenden Schritte aus, um einmaliges Anmelden für SAML in einer Authentifizierungsrichtlinie zu erzwingen:

   a.   Wählen Sie im **Atlassian-Verwaltungsportal** die Registerkarte **Security** (Sicherheit) aus, und klicken Sie auf **Authentication policies** (Authentifizierungsrichtlinien).

   b.   Wählen Sie für die Richtlinie, die Sie erzwingen möchten, die Option **Edit** (Bearbeiten) aus. 

   c.   Aktivieren Sie unter **Settings** (Einstellungen) die Option **Enforce single sign-on** (Einmaliges Anmelden erzwingen) für die verwalteten Benutzer, um eine erfolgreiche SAML-Umleitung zu ermöglichen. 

   d.   Klicken Sie auf **Aktualisieren**. 

      ![Authentifizierungsrichtlinien](./media/atlassian-cloud-tutorial/policy.png) 

   > [!NOTE]
   > Die Administratoren können die SAML-Konfiguration testen, indem sie erzwungenes einmaliges Anmelden nur für eine Teilmenge von Benutzern aktivieren – zunächst in einer separaten Authentifizierungsrichtlinie und anschließend für alle Benutzer, wenn keine Probleme auftreten.

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

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden bei Azure, indem Sie Zugriff auf Atlassian Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Atlassian Cloud** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-atlassian-cloud-test-user"></a>Erstellen eines Atlassian Cloud-Testbenutzers

Um Azure AD-Benutzern die Anmeldung bei Atlassian Cloud zu ermöglichen, stellen Sie ihre Benutzerkonten in Atlassian Cloud manuell bereit, indem Sie die folgenden Schritte ausführen:

1. Navigieren Sie zur Registerkarte **Products** (Produkte), wählen Sie **Users** (Benutzer) aus, und klicken Sie auf **Invite users** (Benutzer einladen).

    ![Atlassian Cloud-Benutzerlink](./media/atlassian-cloud-tutorial/users.png)

1. Geben Sie im Feld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers ein, und klicken Sie dann auf **Invite user** (Benutzer einladen).

    ![Erstellen eines Atlassian Cloud-Benutzers](./media/atlassian-cloud-tutorial/invite-users.png)

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Atlassian Cloud weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Atlassian Cloud-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Atlassian Cloud-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie unter „Meine Apps“ auf die Kachel „Atlassian Cloud“ klicken, geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet, und wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Atlassian Cloud-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Atlassian Cloud können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
