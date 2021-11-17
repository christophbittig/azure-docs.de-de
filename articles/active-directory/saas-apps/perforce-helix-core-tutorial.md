---
title: 'Tutorial: Azure AD-SSO-Integration mit Perforce Helix Core – Helix Authentication Service'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Perforce Helix Core - Helix Authentication Service konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 6917af20d95e4c9fcaa3b1f1e3528bd64e4d0736
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287550"
---
# <a name="tutorial-azure-ad-sso-integration-with-perforce-helix-core---helix-authentication-service"></a>Tutorial: Azure AD-SSO-Integration mit Perforce Helix Core – Helix Authentication Service

In diesem Tutorial erfahren Sie, wie Sie Perforce Helix Core - Helix Authentication Service in Azure Active Directory (Azure AD) integrieren. Wenn Sie Perforce Helix Core - Helix Authentication Service in Azure AD integrieren, haben Sie folgende Vorteile:

* Steuern Sie in Azure AD, wer Zugriff auf Perforce Helix Core - Helix Authentication Service hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Perforce Helix Core - Helix Authentication Service anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Abonnement für Perforce Helix Core - Helix Authentication Service, das für das einmalige Anmelden aktiviert ist.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Perforce Helix Core - Helix Authentication Service unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-perforce-helix-core---helix-authentication-service-from-the-gallery"></a>Hinzufügen von Perforce Helix Core - Helix Authentication Service aus dem Katalog

Zum Konfigurieren der Integration von Perforce Helix Core-Helix Authentication Service in Azure AD müssen Sie Perforce Helix Core - Helix Authentication Service aus dem Katalog zu Ihrer Liste mit verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Begriff **Perforce Helix Core - Helix Authentication Service** im Suchfeld ein.
1. Wählen Sie **Perforce Helix Core - Helix Authentication Service** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-perforce-helix-core---helix-authentication-service"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Perforce Helix Core - Helix Authentication Service

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Perforce Helix Core - Helix Authentication Service mithilfe eines Testbenutzers namens **B. Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Perforce Helix Core - Helix Authentication Service eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Perforce Helix Core - Helix Authentication Service die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens von Perforce Helix Core - Helix Authentication Service](#configure-perforce-helix-core---helix-authentication-service-sso)** , um die Einstellungen für einmaliges Anmelden aufseiten der Anwendung zu konfigurieren.
    1. **[Erstellen des Testbenutzers für Perforce Helix Core - Helix Authentication Service](#create-perforce-helix-core---helix-authentication-service-test-user)** , um eine Entsprechung von „B. Simon“ in Perforce Helix Core - Helix Authentication Service zu erhalten, die mit der Azure AD-Darstellung des Benutzers verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Perforce Helix Core - Helix Authentication Service** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/saml`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/saml/sso`

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<HELIX-AUTH-SERVICE>.<CUSTOMER_HOSTNAME>.com/`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL. Wenden Sie sich an das [Clientsupportteam von Perforce Helix Core - Helix Authentication Service](mailto:support@perforce.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

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

In diesem Abschnitt ermöglichen Sie „B. Simon“ die Verwendung des einmaligen Anmeldens von Azure, indem Sie den Zugriff auf Perforce Helix Core - Helix Authentication Service gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie **Perforce Helix Core - Helix Authentication Service** in der Anwendungsliste aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-perforce-helix-core---helix-authentication-service-sso"></a>Konfigurieren des einmaligen Anmeldens von Perforce Helix Core - Helix Authentication Service

Um das einmalige Anmelden aufseiten von **Perforce Helix Core - Helix Authentication Service** zu konfigurieren, müssen Sie die **App-Verbundmetadaten-URL** an das [Supportteam von Perforce Helix Core - Helix Authentication Service](mailto:support@perforce.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-perforce-helix-core---helix-authentication-service-test-user"></a>Erstellen des Testbenutzers in Perforce Helix Core - Helix Authentication Service

In diesem Abschnitt erstellen Sie in Perforce Helix Core - Helix Authentication Service einen Benutzer namens „Britta Simon“. Arbeiten Sie mit dem [Supportteam von Perforce Helix Core - Helix Authentication Service](mailto:support@perforce.com) zusammen, um die Benutzer der Plattform für Perforce Helix Core - Helix Authentication Service hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Perforce Helix Core - Helix Authentication Service weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Anmelde-URL für Perforce Helix Core - Helix Authentication Service auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „Perforce Helix Core - Helix Authentication Service“ klicken, werden Sie zur Anmelde-URL für Perforce Helix Core - Helix Authentication Service weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Perforce Helix Core - Helix Authentication Service können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
