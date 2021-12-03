---
title: 'Tutorial: Azure AD-SSO-Integration mit IamIP Platform'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und IamIP Platform konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2021
ms.author: jeedes
ms.openlocfilehash: e3264114f9bf8ec4f39225c80862c4b42016ed33
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311173"
---
# <a name="tutorial-azure-ad-sso-integration-with-iamip-platform"></a>Tutorial: Azure AD-SSO-Integration mit IamIP Platform

In diesem Tutorial erfahren Sie, wie Sie IamIP Platform mit Azure Active Directory (Azure AD) integrieren. Die Integration von IamIP Platform mit Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf IamIP Platform hat.
* Ermöglichen Sie es Ihren Benutzer*innen, sich mit ihren Azure AD-Konten automatisch bei IamIP Platform anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein IamIP Platform-Abonnement, für das Einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* IamIP Platform unterstützt das SP- und IDP-initiierte einmalige Anmelden.
* IamIP Platform unterstützt die **Just-In-Time-Benutzerbereitstellung**.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-iamip-platform-from-the-gallery"></a>Hinzufügen von IamIP Platform aus dem Katalog

Zum Konfigurieren der Integration von IamIP Platform in Azure AD müssen Sie IamIP Platform aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **IamIP Platform** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **IamIP Platform** aus, und fügen Sie die App dann hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-iamip-platform"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für IamIP Platform

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit IamIP Platform mithilfe der Testbenutzerin **B. Simon**. Damit das einmalige Anmelden funktioniert, muss eine Verknüpfung zwischen einem*einer Azure AD-Benutzer*in und dem*der entsprechenden Benutzer*in in IamIP Platform eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für IamIP Platform die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für IamIP Platform](#configure-iamip-platform-sso)** : Konfigurieren Sie die Einstellungen für das einmalige Anmelden auf der Anwendungsseite.
    1. **[Erstellen eines*einer IamIP Platform-Testbenutzer*in](#create-iamip-platform-test-user)** : Erstellen Sie ein Pendant von B. Simon in IamIP Platform, das mit der Benutzer*in in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **IamIP Platform** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **SP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** die folgende URL ein: `https://accounts.iamip.com/`.
    
    b. Geben Sie im Textfeld **Antwort-URL** eine der folgenden URLs ein:

    | **Antwort-URL** |
    |---------|
    | `https://accounts.iamip.com/sso-callback` |
    | `https://accounts.iamip.com/sso-login` |
    | `https://accounts.iamip.com/sso-logout` |

    c. Geben Sie im Textfeld **Anmelde-URL** eine der folgenden URLs ein:

    | **Anmelde-URL** |
    |------|
    | `https://accounts.iamip.com/login` |
    | `https://patents.iamip.com/login-user` |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **IamIP Platform einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals den Dienst **Azure Active Directory** aus. Wählen Sie **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie in den **Benutzereigenschaften** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  
   1. Geben Sie im Feld **Benutzername** Folgendes ein: \<username>@\<companydomain>.\<extension>. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf IamIP Platform gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **IamIP Platform** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-iamip-platform-sso"></a>Konfigurieren des einmaligen Anmeldens für IamIP Platform

Zum Konfigurieren des einmaligen Anmeldens in **IamIP Platform** müssen Sie das heruntergeladene **Zertifikat (Base64)** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von IamIP Platform](mailto:info@iamip.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-iamip-platform-test-user"></a>Erstellen eines*einer IamIP Platform-Testbenutzer*in

In diesem Abschnitt wird in IamIP Platform eine Benutzerin mit dem Namen Britta Simon erstellt. IamIP Platform unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein*e Benutzer*in noch nicht in IamIP Platform vorhanden, wird nach der Authentifizierung ein*e neue*r Benutzer*in erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für IamIP Platform weitergeleitet. Dort können Sie den Anmeldeflow initiieren.  

* Navigieren Sie direkt zur Anmelde-URL für IamIP Platform, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Wählen Sie im Azure-Portal **Diese Anwendung testen** aus. Dadurch sollten Sie automatisch bei der IamIP Platform-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „IamIP Platform“ unter „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der IamIP Platform-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von IamIP Platform können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Organisationsdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
