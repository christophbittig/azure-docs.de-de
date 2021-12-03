---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 36e41163f2d2c404b54a9dd9f0daf4cc7caf6c74
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299062"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE

In diesem Tutorial erfahren Sie, wie Sie SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE in Azure Active Directory (Azure AD) integrieren. Die Integration von SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Abonnement für SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Hinzufügen von SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE über den Katalog

Um die Integration von SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE in Azure AD zu konfigurieren, müssen Sie SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens von SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Testbenutzers für SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** , um in SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Die Werte müssen durch die tatsächliche Antwort-URL und die tatsächliche Anmelde-URL ersetzt werden. Diese Werte erhalten Sie vom [Clientsupportteam für SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE](mailto:support@ssogen.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Ihre SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE-Anwendung erwartet die Zuordnung von **nameidentifier** zu **user.onpremisessamaccountname**. Klicken Sie daher auf das Symbol **Bearbeiten**, um die Attributzuordnung zu ändern.

    ![image](common/edit-attribute.png)

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>Konfigurieren des einmaligen Anmeldens von SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE

Informationen zum Konfigurieren des einmaligen Anmeldens aufseiten von **SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE** finden Sie in der anwendungsspezifischen Dokumentation zur SSO-Registrierung:

* Oracle EBS: Azure AD-SSO-Integration: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft: Azure AD-SSO-Integration: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards: Azure AD-SSO-Integration: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache: Azure AD-SSO-Integration: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>Erstellen eines Testbenutzers für SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE

Nach erfolgreicher Authentifizierung sendet Azure AD den eindeutigen Benutzerbezeichner (Namens-ID) an die Benutzeranwendung.  Der eindeutige Benutzerbezeichner (Namens-ID) muss dem Benutzerdatensatz in Ihrer Anwendung entsprechen (beispielsweise „FND_USER.USER_NAME“ in Oracle EBS).

Wenden Sie sich ggf. an [info@ssogen.com](mailto:info@ssogen.com) und [support@ssogen.com](mailto:support@ssogen.com), um Unterstützung zu erhalten.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE umgeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Hierdurch sollten Sie automatisch bei der Instanz von SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Instanz von SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SSOGEN – Azure AD-SSO-Gateway für Oracle E-Business Suite – EBS, PeopleSoft und JDE können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
