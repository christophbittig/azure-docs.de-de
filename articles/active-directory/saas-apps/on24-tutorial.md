---
title: 'Tutorial: Azure Active Directory-Integration mit ON24 Virtual Environment SAML Connection | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ON24 Virtual Environment SAML Connection konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/14/2021
ms.author: jeedes
ms.openlocfilehash: 654673a394550d396e08a8de5e7914befddb480b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329988"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Tutorial: Azure Active Directory-Integration mit ON24 Virtual Environment SAML Connection

In diesem Tutorial erfahren Sie, wie Sie ON24 Virtual Environment SAML Connection in Azure Active Directory (Azure AD) integrieren. Die Integration von ON24 Virtual Environment SAML Connection in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf ON24 Virtual Environment SAML Connection hat.
* Ermöglichen sie Benutzern, sich mit ihren Azure AD-Konten automatisch bei ON24 Virtual Environment SAML Connection anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* ON24 Virtual Environment SAML Connection-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ON24 Virtual Environment SAML Connection unterstützt **SP**- und **IDP**-iniitiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-on24-virtual-environment-saml-connection-from-the-gallery"></a>Hinzufügen von ON24 Virtual Environment SAML Connection aus dem Katalog

Zum Konfigurieren der Integration von ON24 Virtual Environment SAML Connection in Azure AD müssen Sie ON24 Virtual Environment SAML Connection aus dem Katalog Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ON24 Virtual Environment SAML Connection** in das Suchfeld ein.
1. Wählen Sie **ON24 Virtual Environment SAML Connection** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-on24-virtual-environment-saml-connection"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ON24 Virtual Environment SAML Connection

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ON24 Virtual Environment SAML Connection mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ON24 Virtual Environment SAML Connection eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei ON24 Virtual Environment SAML Connection die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für ON24 Virtual Environment SAML Connection](#configure-on24-virtual-environment-saml-connection-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines ON24 Virtual Environment SAML Connection-Testbenutzers](#create-on24-virtual-environment-saml-connection-test-user)** , um eine Entsprechung von B.Simon in ON24 Virtual Environment SAML Connection zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ON24 Virtual Environment SAML Connection** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** einen der folgenden Werte ein:

    | **URL der Produktionsumgebung** |
    |------|
    | `SAML-VSHOW.on24.com` |
    | `SAML-Gateway.on24.com` |
    | `SAP PROD SAML-EliteAudience.on24.com` |
    |
                
    | **URL der Qualitätssicherungsumgebung** |
    |-----|
    | `SAMLQA-VSHOW.on24.com` |
    | `SAMLQA-Gateway.on24.com` |
    | `SAMLQA-EliteAudience.on24.com` |
    |

    b. Geben Sie im Textfeld **Antwort-URL** eine der folgenden URLs ein:

    | **URL der Produktionsumgebung** |
    |-----|
    | `https://federation.on24.com/sp/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2` |
    | `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2` |
    |

    | **URL der Qualitätssicherungsumgebung** |
    |-------|
    | `https://qafederation.on24.com/sp/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2` |
    | `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` |
    |

    c. Klicken Sie auf **Zusätzliche URLs festlegen**. 

    d. Geben Sie im Textfeld **Relayzustand** eine URL nach folgendem Muster ein: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`.

5.  Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, führen Sie die folgenden Schritte durch:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://vshow.on24.com/vshow/<INSTANCE_NAME>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Relayzustand und die tatsächliche Anmelde-URL. Wenden Sie sich an das [ON24 Virtual Environment SAML Connection-Clientsupportteam](https://www.on24.com/contact-us/) um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **ON24 Virtual Environment SAML Connection einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ON24 Virtual Environment SAML Connection gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen den Eintrag **ON24 Virtual Environment SAML Connection** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-on24-virtual-environment-saml-connection-sso"></a>Konfigurieren des einmaligen Anmeldens für ON24 Virtual Environment SAML Connection

Zum Konfigurieren des einmaligen Anmeldens in **ON24 Virtual Environment SAML Connection** müssen Sie die **Verbundmetadaten-XML** und die entsprechenden URLs, die Sie im Azure-Portal heruntergeladen bzw. kopiert haben, an das [ON24 Virtual Environment SAML Connection-Supportteam](https://www.on24.com/about-us/support/) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Erstellen eines ON24 Virtual Environment SAML Connection-Testbenutzers

In diesem Abschnitt erstellen Sie in ON24 Virtual Environment SAML Connection eine Benutzerin mit dem Namen Britta Simon. Wenden Sie sich an das [ON24 Virtual Environment SAML Connection-Supportteam](https://www.on24.com/about-us/support/), um Benutzer in der ON24 Virtual Environment SAML Connection-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL von ON24 Virtual Environment SAML Connection umgeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL von ON24 Virtual Environment SAML Connection, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Sie sollten automatisch bei der ON24 Virtual Environment SAML Connection-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie unter „Meine Apps“ auf die Kachel „ON24 Virtual Environment SAML Connection“ klicken geschieht Folgendes: Falls Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet, und falls Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der ON24 Virtual Environment SAML Connection-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von ON24 Virtual Environment SAML Connection können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
