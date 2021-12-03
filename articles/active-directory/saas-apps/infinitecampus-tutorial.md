---
title: 'Tutorial: Integration von Infinite Campus in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Infinite Campus konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: jeedes
ms.openlocfilehash: 817bc6c7b425af9cc02bc339967cb29b1c4b81fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285232"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Tutorial: Integration von Infinite Campus in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Infinite Campus in Azure Active Directory (Azure AD) integrieren. Die Integration von Infinite Campus in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Infinite Campus haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Infinite Campus anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Integration von Infinite Campus in Azure AD konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Infinite Campus-Abonnement mit SSO-Unterstützung
* Sie müssen mindestens ein Azure Active Directory-Administrator sein und die Campus Product-Sicherheitsrolle „Student Information System (SIS)“ ausüben, um die Konfiguration abzuschließen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Infinite Campus unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-infinite-campus-from-the-gallery"></a>Hinzufügen von Infinite Campus aus dem Katalog

Zum Konfigurieren der Integration von Infinite Campus in Azure AD müssen Sie Infinite Campus aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Infinite Campus** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Infinite Campus** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-infinite-campus"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Infinite Campus

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Infinite Campus mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Infinite Campus eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Infinite Campus zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Infinite Campus](#configure-infinite-campus-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Infinite Campus-Testbenutzers](#create-infinite-campus-test-user)** , um in Infinite Campus eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Infinite Campus** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt „Grundlegende SAML-Konfiguration“ die folgenden Schritte aus (beachten Sie, dass die Domäne je nach Hostingmodell variiert, der Wert **FULLY-QUALIFIED-DOMAIN** aber mit Ihrer Infinite Campus-Installation übereinstimmen muss):

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`.

    b. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Infinite Campus gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Infinite Campus** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-infinite-campus-sso"></a>Konfigurieren des einmaligen Anmeldens für Infinite Campus

1. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei Infinite Campus an.

2. Klicken Sie links im Menü auf **System Administration** (Systemverwaltung).

    ![Administrator](./media/infinitecampus-tutorial/admin.png)

3. Navigieren Sie zu **User Security** > **SAML Management** > **SSO Service Provider Configuration** (Benutzersicherheit -> SAML-Verwaltung -> SSO-Dienstanbieterkonfiguration).

    ![SAML](./media/infinitecampus-tutorial/security.png)

4. Führen Sie auf der Seite **SSO Service Provider Configuration** (SSO-Dienstanbieterkonfiguration) die folgenden Schritte aus:

    ![SSO](./media/infinitecampus-tutorial/configuration.png)

    a. Wählen Sie **Enable SAML Single Sign On** (Einmaliges Anmelden per SAML aktivieren) aus.

    b. Bearbeiten Sie den optionalen Attributnamen (**Optional Attribute Name**) so, dass er **name** enthält.

    c. Wählen Sie im Bereich **Select an option to retrieve Identity Provider (IDP) server data** (Option zum Abrufen von IDP-Dienstanbieterdaten auswählen) die Option **Metadata URL** (Metadaten-URL), fügen Sie den aus dem Azure-Portal kopierten Wert für **Verbundmetadaten-URL der App** in das Feld ein, und klicken Sie auf **Sync** (Synchronisieren).

    d. Nach dem Klicken auf **Sync** (Synchronisieren) werden die Werte auf der Seite **SSO Service Provider Configuration** (SSO-Dienstanbieterkonfiguration) automatisch aufgefüllt. Diese Werte können darauf überprüft werden, ob sie den Werten aus Schritt 4 oben entsprechen.

    e. Klicken Sie auf **Speichern**.

### <a name="create-infinite-campus-test-user"></a>Erstellen eines Infinite Campus-Testbenutzers

Infinite Campus besitzt eine demografiezentrierte Architektur. Wenden Sie sich an das [Supportteam von Infinite Campus](mailto:sales@infinitecampus.com), um die Benutzer zur Infinite Campus-Plattform hinzuzufügen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Infinite Campus weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Anmelde-URL für Infinite Campus auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Infinite Campus“ klicken, werden Sie zur Anmelde-URL für Infinite Campus weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Infinite Campus können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
