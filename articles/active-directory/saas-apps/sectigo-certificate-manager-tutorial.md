---
title: 'Tutorial: Azure Active Directory-Integration in Sectigo Certificate Manager | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Sectigo Certificate Manager konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2021
ms.author: jeedes
ms.openlocfilehash: 4b3283780e611e37ef6cdae1feb6eb4b22f8f255
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306622"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Azure Active Directory-Integration in Sectigo Certificate Manager

In diesem Tutorial erfahren Sie, wie Sie Sectigo Certificate Manager in Azure Active Directory (Azure AD) integrieren. Die Integration von Sectigo Certificate Manager in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Sectigo Certificate Manager hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Sectigo Certificate Manager anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Sectigo Certificate Manager konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure AD-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Sectigo Certificate Manager-Konto

> [!NOTE]
> Sectigo führt mehrere Instanzen von Sectigo Certificate Manager aus. Die Hauptinstanz von Sectigo Certificate Manager ist **https:\//cert-manager.com**. Diese URL wird in diesem Tutorial verwendet.  Wenn sich Ihr Konto in einer anderen Instanz befindet, müssen Sie die URLs entsprechend anpassen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren Sectigo Certificate Manager in Azure AD.

Sectigo Certificate Manager unterstützt die folgenden Features:

* **SP-initiiertes einmaliges Anmelden**
* **IDP-initiiertes einmaliges Anmelden**

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Hinzufügen von Sectigo Certificate Manager im Azure-Portal

Zum Konfigurieren der Integration von Sectigo Certificate Manager in Azure AD müssen Sie Sectigo Certificate Manager aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Sectigo Certificate Manager** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Sectigo Certificate Manager** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sectigo-certificate-manager"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Sectigo Certificate Manager

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Sectigo Certificate Manager mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sectigo Certificate Manager eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Sectigo Certificate Manager zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Sectigo Certificate Manager](#configure-sectigo-certificate-manager-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Sectigo Certificate Manager-Testbenutzers](#create-sectigo-certificate-manager-test-user)** , um eine Entsprechung von B. Simon in Sectigo Certificate Manager zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Sectigo Certificate Manager** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    1. Geben Sie im Feld **Bezeichner (Entitäts-ID)** für die Hauptinstanz von Sectigo Certificate Manager **https:\//cert-manager.com/shibboleth** ein.

    1. Geben Sie im Feld **Antwort-URL** für die Hauptinstanz von Sectigo Certificate Manager **https:\//cert-manager.com/Shibboleth.sso/SAML2/POST** ein.
        
    > [!NOTE]
    > Im Allgemeinen ist die **Anmelde-URL** für den **SP-initiierten Modus** zwar erforderlich, sie wird jedoch nicht für die Anmeldung über Sectigo Certificate Manager benötigt.        

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** zum Konfigurieren des **IDP-initiierten Modus** und zum Ermöglichen von **Tests** die folgenden Schritte aus:

    1. Wählen Sie **Zusätzliche URLs festlegen** aus.

    1. Geben Sie in das Feld **Relayzustand** die kundenspezifische URL für Sectigo Certificate Manager ein. Geben Sie für die Hauptinstanz von Sectigo Certificate Manager **https:\//cert-manager.com/customer/\<customerURI\>/idp** ein.

1. Führen Sie im Abschnitt **Benutzerattribute und Ansprüche** die folgenden Schritte aus:

    1. Löschen Sie alle unter **Zusätzliche Ansprüche** angegebenen Ansprüche.
    
    1. Wählen Sie **Neuen Anspruch hinzufügen** aus, und fügen Sie die folgenden vier Ansprüche hinzu:
    
        | Name | Namespace | `Source` | Quellattribut | BESCHREIBUNG |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | empty | attribute | user.userprincipalname | Muss dem Feld **IdP Person ID** (IdP-Personen-ID) in Sectigo Certificate Manager für Administratoren entsprechen. |
        | mail | empty | attribute | user.mail | Erforderlich |
        | givenName | empty | attribute | user.givenname | Optional |
        | sn | empty | attribute | user.surname | Optional |

       ![Sectigo Certificate Manager: Hinzufügen von vier neuen Ansprüchen](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** neben **Verbundmetadaten-XML** die Option **Herunterladen** aus. Speichern Sie die heruntergeladene XML-Datei auf Ihrem Computer.

    ![Downloadoption für Verbundmetadaten-XML](common/metadataxml.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf Sectigo Certificate Manager gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Sectigo Certificate Manager** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sectigo-certificate-manager-sso"></a>Konfigurieren des einmaligen Anmeldens für Sectigo Certificate Manager

Zum Konfigurieren des einmaligen Anmeldens aufseiten von Sectigo Certificate Manager senden Sie die heruntergeladene Verbundmetadaten-XML-Datei an das [Supportteam von Sectigo Certificate Manager](https://sectigo.com/support). Anhand der von Ihnen gesendeten Informationen stellt das Supportteam von Sectigo Certificate Manager sicher, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet ist.

### <a name="create-sectigo-certificate-manager-test-user"></a>Erstellen eines Sectigo Certificate Manager-Testbenutzers

In diesem Abschnitt erstellen Sie in Sectigo Certificate Manager einen Benutzer mit dem Namen Britta Simon. Wenden Sie sich an das [Supportteam von Sectigo Certificate Manager](https://sectigo.com/support), um den Benutzer der Sectigo Certificate Manager-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für das einmalige Anmelden.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Testen über Sectigo Certificate Manager (SP-initiiertes einmaliges Anmelden)

Navigieren Sie zur kundenspezifischen URL (für die Hauptinstanz von Sectigo Certificate Manager: https:\//cert-manager.com/customer/\<customerURI\>/), und wählen Sie die Schaltfläche unter **Or Sign In With** (Oder anmelden mit) aus.  Bei ordnungsgemäßer Konfiguration werden Sie automatisch bei Sectigo Certificate Manager angemeldet.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Testen über die Konfiguration des einmaligen Anmeldens von Azure (IDP-initiiertes einmaliges Anmelden)

Wählen Sie im Anwendungsintegrationsbereich für **Sectigo Certificate Manager** die Option **Einmaliges Anmelden** und dann **Testen** aus.  Bei ordnungsgemäßer Konfiguration werden Sie automatisch bei Sectigo Certificate Manager angemeldet.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>Testen über das Portal „Meine Apps“ (IDP-initiiertes einmaliges Anmelden)

Wählen Sie im Portal „Meine Apps“ die Option **Sectigo Certificate Manager** aus.  Bei ordnungsgemäßer Konfiguration werden Sie automatisch bei Sectigo Certificate Manager angemeldet. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Sectigo Certificate Manager können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
