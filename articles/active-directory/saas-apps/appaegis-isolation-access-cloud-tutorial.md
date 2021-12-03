---
title: 'Tutorial: Azure AD SSO Integration mit Appaegis Isolation Access Cloud'
description: Lernen Sie, wie man Single Sign-On zwischen Azure Active Directory und Appaegis Isolation Access Cloud konfiguriert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: 358dcd9945751a20332063f14d408b12de151fed
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318398"
---
# <a name="tutorial-azure-ad-sso-integration-with-appaegis-isolation-access-cloud"></a>Tutorial: Azure AD SSO Integration mit Appaegis Isolation Access Cloud

In diesem Tutorial lernen Sie, wie Sie Appaegis Isolation Access Cloud mit Azure Active Directory (Azure AD) integrieren können. Wenn Sie Appaegis Isolation Access Cloud mit Azure AD integrieren, können Sie:

* In Azure AD kontrollieren, wer Zugriff auf Appaegis Isolation Access Cloud hat.
* Ermöglichen Sie Ihren Benutzern, sich automatisch mit ihren Azure AD-Konten bei Appaegis Isolation Access Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Appaegis Isolation Access Cloud Abonnement mit Single Sign-On (SSO).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Appaegis Isolation Access Cloud unterstützt **SP- und IDP**-veranlasstes SSO.
* Appaegis Isolation Access Cloud unterstützt **Just-In-Time**-Benutzerbereitstellung.

## <a name="adding-appaegis-isolation-access-cloud-from-the-gallery"></a>Hinzufügen von Appaegis Isolation Access Cloud aus dem Katalog

Um die Integration von Appaegis Isolation Access Cloud in Azure AD zu konfigurieren, müssen Sie Appaegis Isolation Access Cloud aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Anwendungen hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** **Appaegis Isolation Access Cloud** in das Suchfeld ein.
1. Wählen Sie **Appaegis Isolation Access Cloud** im Ergebnisfeld und fügen Sie die Anwendung hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-appaegis-isolation-access-cloud"></a>Konfigurieren und testen Sie Azure AD SSO für Appaegis Isolation Access Cloud

Konfigurieren und testen Sie Azure AD SSO mit Appaegis Isolation Access Cloud unter Verwendung eines Testbenutzers namens **B.Simon**. Damit SSO funktioniert, müssen Sie eine Verknüpfungsbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Appaegis Isolation Access Cloud herstellen.

Um Azure AD SSO mit Appaegis Isolation Access Cloud zu konfigurieren und zu testen, führen Sie die folgenden Schritte durch:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des Isolation Access Cloud SSO](#configure-appaegis-isolation-access-cloud-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Appaegis Isolation Access Cloud-Testbenutzers](#create-appaegis-isolation-access-cloud-test-user)** , um ein Gegenüber von B. Simon in Appaegis Isolation Access Cloud zu erhalten, das mit der Azure AD Benutzerdarstellung verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Appaegis Isolation Access Cloud** den Abschnitt **Verwalten** und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie die folgenden Schritte aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.appaegis.net`

    b. Geben Sie im Textfeld **Relayzustand** einen Wert nach folgendem Muster ein: `<RelayState>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Anmelde-URL und Relayzustand. Wenden Sie sich an das [Clientsupportteam von Appaegis Isolation Access Cloud](mailto:support@appaegis.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Appaegis Isolation Access Cloud-Anwendung erwartet die SAML-Assertions in einem bestimmten Format, das erfordert, dass Sie Ihrer SAML-Token-Attribut-Konfiguration benutzerdefinierte Attribut-Mappings hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Zusätzlich zu den oben genannten erwartet die Appaegis Isolation Access Cloud-Anwendung, dass einige weitere Attribute in der SAML-Antwort zurückgegeben werden, die unten aufgeführt sind. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name | Quellattribut |
    | ---------| --------- |
    | email | user.userprincipalname |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Appaegis Isolation Access Cloud einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt werden Sie B.Simon die Nutzung von Azure Einmaliges Anmelden ermöglichen, indem Sie den Zugriff auf Appaegis Isolation Access Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Appaegis Isolation Access Cloud**.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-appaegis-isolation-access-cloud-sso"></a>Konfigurieren von Einmaliges Anmelden für Appaegis Isolation Access Cloud

1. Melden Sie sich bei Ihrer Appaegis Isolation Access Cloud-Unternehmensseite als ein Administrator an.

1. Gehen Sie zu **Einstellung** > **Anpassung** und geben Sie eine  **Mandantendomain-URL** in das Textfeld ein und klicken Sie auf die Schaltfläche **+ldP**.

    ![Der Screenshot zeigt die Kontoanpassung.](./media/appaegis-isolation-access-cloud-tutorial/account.png "Kontoanpassung")

1. Führen Sie auf der Seite **Details des Identitätsanbieters** die folgenden Schritte aus.

    ![Der Screenshot zeigt die Details des Identitätsanbieters.](./media/appaegis-isolation-access-cloud-tutorial/details.png "Identitätsanbieter") 

    1. Wählen Sie **Azure AD** aus der Dropdown-Liste in **Identitätsanbieter** aus.

    1. Kopieren Sie den Wert für **ACS-URL**, und fügen Sie diesen Wert im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** im Textfeld **Antwort-URL** ein.

    1. Kopieren Sie den Wert **Entität ID** und fügen Sie diesen Wert in das Textfeld **Bezeichner** im Abschnitt **Basic SAML Konfiguration** im Azure-Portal ein.

    1. Öffnen Sie die heruntergeladene **Verbundmetadaten XML** aus dem Azure-Portal in Notepad und laden Sie die Datei in den **SAML File Upload** hoch.

    1. Aktivieren Sie das Kontrollkästchen **Status** und klicken Sie auf **Speichern**.

### <a name="create-appaegis-isolation-access-cloud-test-user"></a>Erstellen eines Appaegis Isolation Access Cloud Testbenutzers

In diesem Abschnitt wird ein Benutzer namens Britta Simon in Appaegis Isolation Access Cloud erstellt. Appaegis Isolation Access Cloud unterstützt Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn ein Benutzer noch nicht in Appaegis Isolation Access Cloud vorhanden ist, wird nach der Authentifizierung ein neuer Benutzer angelegt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dies leitet Sie zur Appaegis Isolation Access Cloud Sign-on-URL weiter, wo Sie den Anmeldeflow veranlassen können.  

* Gehen Sie direkt zur Appaegis Isolation Access Cloud Sign-on-URL und veranlassen Sie den Anmeldeflow von dort aus.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie auf **Testen Sie diese Anwendung** im Azure-Portal und Sie sollten automatisch bei der Appaegis Isolation Access Cloud angemeldet werden, für die Sie das SSO eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie auf die Kachel Appaegis Isolation Access Cloud in „Meine Apps“ klicken, werden Sie, wenn Sie im SP-Modus konfiguriert sind, auf die Anmeldeseite der Anwendung weitergeleitet, um den Anmeldeflow zu veranlassen, und wenn Sie im IDP-Modus konfiguriert sind, sollten Sie automatisch bei der Appaegis Isolation Access Cloud angemeldet werden, für die Sie das SSO eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Appaegis Isolation Access Cloud konfiguriert haben, können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
