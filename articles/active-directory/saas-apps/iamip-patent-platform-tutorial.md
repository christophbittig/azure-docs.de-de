---
title: 'Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory in IamIP Patent Platform | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und IamIP Patent Platform konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2021
ms.author: jeedes
ms.openlocfilehash: 5de5587e7f517d28207bf041b655591736f84a57
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124833139"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory in IamIP Patent Platform

In diesem Tutorial erfahren Sie, wie Sie IamIP Patent Platform in Azure Active Directory (Azure AD) integrieren. Die Integration von IamIP Patent Platform in Azure AD ermöglicht Folgendes:

* Verwenden Sie Azure AD, um festzulegen, wer auf IamIP Patent Platform zugreifen kann.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei IamIP Patent Platform anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein IamIP Patent Platform-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="tutorial-description"></a>Beschreibung des Tutorials

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* IamIP Patent Platform unterstützt SP- und IDP-initiiertes einmaliges Anmelden.

## <a name="add-iamip-patent-platform-from-the-gallery"></a>Hinzufügen von IamIP Patent Platform aus dem Katalog

Zum Konfigurieren der Integration von IamIP Patent Platform in Azure AD müssen Sie IamIP Patent Platform aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **IamIP Patent Platform** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **IamIP Patent Platform** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für IamIP Patent Platform

Sie konfigurieren und testen das einmalige Anmelden von Azure AD mit IamIP Patent Platform mithilfe eines Testbenutzers mit dem Namen „B. Simon“. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in IamIP Patent Platform eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für IamIP Patent Platform die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
    1. **[Gewähren der Zugriffsberechtigungen für den Testbenutzer](#grant-access-to-the-test-user)** , um dem Benutzer die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.

1. **[Konfigurieren des einmaligen Anmeldens für IamIP Patent Platform](#configure-iamip-patent-platform-sso)** auf Anwendungsseite.
    1. **[Erstellen eines IamIP Patent Platform-Testbenutzers](#create-iamip-patent-platform-test-user)** als Pendant zur Azure AD-Darstellung des Benutzers.

1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **IamIP Patent Platform** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten:

   ![Schaltfläche mit dem Bleistift für die grundlegende SAML-Konfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** diese Schritte aus, wenn Sie über eine Metadatendatei des Dienstanbieters verfügen und die das einmalige Anmelden im IDP-initiierten Modus konfigurieren möchten:

    a. Wählen Sie **Metadatendatei hochladen** aus.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Wählen Sie die Ordnerschaltfläche aus, wählen Sie die Metadatendatei aus, und wählen Sie dann **Hochladen** aus.

    ![Ordnerschaltfläche und Schaltfläche für das Hochladen](common/browse-upload-metadata.png)

    c. Nach dem Hochladen der Metadatendatei werden im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für **Bezeichner** und **Antwort-URL** automatisch eingefügt.

    > [!Note]
    > Sollten die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

1. Wählen Sie **Zusätzliche URLs festlegen**, und führen Sie die folgenden Schritte aus, wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://patents.iamip.com/login-user`.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Herunterladen** für **Zertifikat (Rohdaten)** aus, um das Zertifikat herunterzuladen, und speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadlink für Zertifikat](common/certificateraw.png)

1. Kopieren Sie im Abschnitt **IamIP Patent Platform einrichten** die entsprechende URL bzw. die entsprechenden URLs basierend auf Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals den Dienst **Azure Active Directory** aus. Wählen Sie **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie in den **Benutzereigenschaften** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  
   1. Geben Sie im Feld **Benutzername** Folgendes ein: \<username>@\<companydomain>.\<extension>. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

### <a name="grant-access-to-the-test-user"></a>Gewähren von Zugriff für den Testbenutzer

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf IamIP Patent Platform gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **IamIP Patent Platform** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus:
1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Schaltfläche **Zuweisen**.

## <a name="configure-iamip-patent-platform-sso"></a>Konfigurieren des einmaligen Anmeldens für IamIP Patent Platform

Zum Konfigurieren des einmaligen Anmeldens aufseiten von IamIP Patent Platform müssen Sie das heruntergeladene Zertifikat (Rohdaten) und die entsprechenden aus dem Azure-Portal kopierten URLs an das [Supportteam von IamIP Patent Platform](mailto:info@iamip.com) senden. Die SAML-SSO-Verbindung wird so konfiguriert, dass Sie auf beiden Seiten richtig ist.

### <a name="create-iamip-patent-platform-test-user"></a>Erstellen eines IamIP Patent Platform-Testbenutzers

Wenden Sie sich an das [IamIP Patent Platform-Supportteam](mailto:info@iamip.com), um einen Benutzer namens B.Simon in IamIP Patent Platform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für IamIP Patent Platform weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für IamIP Patent Platform, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der IamIP Patent Platform-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „IamIP Patent Platform“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der IamIP Patent Platform-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von IamIP Patent Platform können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.