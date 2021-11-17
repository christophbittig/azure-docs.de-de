---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Samsung Knox and Business Services | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Samsung Knox and Business Services konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 444dd939222e402a14940d1934b32044997cf01b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Samsung Knox and Business Services

In diesem Tutorial erfahren Sie, wie Sie Samsung Knox and Business Services in Azure Active Directory (Azure AD) integrieren. Die Integration von Samsung Knox and Business Services in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Samsung Knox and Business Services hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Samsung Knox and Business Services anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Samsung Knox-Konto.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Samsung Knox and Business Services unterstützt **SP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Hinzufügen von Samsung Knox and Business Services aus dem Katalog

Zum Konfigurieren der Integration von Samsung Knox and Business Services in Azure AD müssen Sie Ihrer Liste der verwalteten SaaS-Apps Samsung Knox and Business Services aus dem Katalog hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Begriff **Samsung Knox and Business Services** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich den Eintrag **Samsung Knox and Business Services** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Samsung Knox and Business Services

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Samsung Knox and Business Services mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in [SamsungKnox.com](https://samsungknox.com/) eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Samsung Knox and Business Services die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Samsung Knox and Business Services](#configure-samsung-knox-and-business-services-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Samsung Knox and Business Services-Testbenutzers](#create-samsung-knox-and-business-services-test-user)** , um eine Entsprechung von B. Simon in Samsung Knox and Business Services zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Samsung Knox and Business Services** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    * Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://www2.samsungknox.com/en/sso/login/ad`.
    * Geben Sie im Textfeld **Antwort-URL (Assertionsverbraucherdienst-URL)** die folgende URL ein: `https://central.samsungknox.com/ams/ad/saml/acs`.
    
    ![Grundlegende SAML-Konfigurationswerte](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Samsung Knox and Business Services gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Samsung Knox and Business Services** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-samsung-knox-and-business-services-sso"></a>Konfigurieren des einmaligen Anmeldens für Samsung Knox and Business Services

1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator bei [SamsungKnox.com](https://samsungknox.com/) an.

1. Klicken Sie in der oberen rechten Ecke auf den **Avatar**.

    ![Samsung Knox-Avatar](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Klicken Sie auf der linken Seitenleiste auf **ACTIVE DIRECTORY SETTINGS** (ACTIVE DIRECTORY-EINSTELLUNGEN), und führen Sie die folgenden Schritte aus:

    ![ACTIVE DIRECTORY SETTINGS (ACTIVE DIRECTORY-EINSTELLUNGEN)](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. Geben Sie in das Textfeld **Identifier(entity ID)** (Bezeichner (Entitäts-ID)) den Wert für **Bezeichner** ein, den Sie im Azure-Portal eingegeben haben.

    b. Fügen Sie im Textfeld **App Federation Metadata URL** (App-Verbundmetadaten-URL) den Wert der **App-Verbundmetadaten-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie auf **CONNECT TO AD SSO** (MIT AD SSO VERBINDEN).

### <a name="create-samsung-knox-and-business-services-test-user"></a>Erstellen eines Samsung Knox and Business Services-Testbenutzers

In diesem Abschnitt erstellen Sie in Samsung Knox and Business Services einen Benutzer mit dem Namen Britta Simon. Anweisungen dazu, wie Sie einen Unteradministrator oder Testbenutzer in Ihre Samsung Knox-Organisation einladen, finden Sie in den Administratorhandbüchern [Knox Configure](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm) oder [Knox Mobile Enrollment](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm). Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zu [SamsungKnox.com](https://samsungknox.com/) weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt [SamsungKnox.com](https://samsungknox.com/) auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Samsung Knox and Business Services“ klicken, werden Sie zu [SamsungKnox.com](https://samsungknox.com/) umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Samsung Knox and Business Services können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
