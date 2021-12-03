---
title: 'Tutorial: Azure AD SSO-Integration mit Dagster Cloud'
description: Lernen Sie, wie man Einmaliges Anmelden zwischen Azure Active Directory und Dagster Cloud konfiguriert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 8acba59a2e67a23fb2921ab0e776e37010d77669
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314832"
---
# <a name="tutorial-azure-ad-sso-integration-with-dagster-cloud"></a>Tutorial: Azure AD SSO-Integration mit Dagster Cloud

In diesem Tutorial lernen Sie, wie Sie Dagster Cloud mit Azure Active Directory (Azure AD) integrieren können. Wenn Sie Dagster Cloud mit Azure AD integrieren, können Sie:

* In Azure AD kontrollieren, wer Zugriff auf Dagster Cloud hat.
* Es Ihren Nutzern ermöglichen, sich automatisch mit ihren Azure-AD-Konten bei Dagster Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Dagster-Cloud-Abonnement mit Einmaligem Anmelden (SSO).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Dagster Cloud unterstützt **SP und IDP** veranlasste SSO.
* Dagster Cloud unterstützt **Just-In-Time** Benutzerbereitstellung.

## <a name="add-dagster-cloud-from-the-gallery"></a>Dagster Cloud aus dem Katalog hinzufügen

Um die Integration von Dagster Cloud in Azure AD zu konfigurieren, müssen Sie Dagster Cloud aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Anwendungen hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** **Dagster Cloud Q** in das Suchfeld ein.
1. Wählen Sie **Dagster Cloud** im Ergebnisfeld und fügen Sie die Anwendung hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-dagster-cloud"></a>Azure AD SSO für Dagster Cloud konfigurieren und testen

Konfigurieren und testen Sie Azure AD SSO mit Dagster Cloud unter Verwendung eines Testbenutzers namens **B.Simon**. Damit SSO funktioniert, müssen Sie eine Link-Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Dagster Cloud herstellen.

Um Azure AD SSO mit Dagster Cloud zu konfigurieren und testen, führen Sie die folgenden Schritte durch:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Dagster Cloud SSO](#configure-dagster-cloud-sso)** konfigurieren, um die Einstellungen für Einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Dagster Cloud-Testbenutzer anlegen](#create-dagster-cloud-test-user)** , um ein Gegenüber von B.Simon in Dagster Cloud zu haben, das mit der Azure AD-Repräsentation des Benutzers verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im Azure-Portal auf der Seite Anwendungsintegration von **Dagster Cloud** den Abschnitt **Manage** und wählen Sie **Einmaliges Anmelden**.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<CustomerName>.dagster.cloud/auth/saml/consume`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<CustomerName>.dagster.cloud/auth/saml/consume`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<CustomerName>.dagster.cloud/auth/saml/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das Supportteam für den [Dagster Cloud-Client](mailto:support@elementl.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Dagster-Cloud-Anwendung erwartet die SAML-Assertions in einem bestimmten Format, das es erfordert, dass Sie benutzerdefinierte Attribut-Zuordnungen zu Ihrer SAML-Token-Attribut-Konfiguration hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Zusätzlich zu den oben genannten erwarte die Dagster Cloud-Anwendung, dass einige weitere Attribute in der SAML-Antwort zurückgegeben werden, die unten aufgeführt sind. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name | Quellattribut|
    | ---------| --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.userprincipalname |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Dagster Cloud einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt aktivieren Sie B.Simon für die Nutzung von Azure Einmaliges Anmelden durch Gewährung des Zugangs zur Dagster Cloud.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Dagster Cloud** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-dagster-cloud-sso"></a>Konfigurieren von Dagster Cloud SSO

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Dagster Cloud** müssen Sie die **heruntergeladene Verbundmetadaten-XML** und die kopierten URLs aus Azure-Portal an das Supportteam von [Dagster Cloud](mailto:support@elementl.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-dagster-cloud-test-user"></a>Erstellen von einem Dagster Cloud Testbenutzer

In diesem Abschnitt wird ein Benutzer namens Britta Simon in Dagster Cloud erstellt. Dagster Cloud unterstützt Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn ein Benutzer noch nicht in der Dagster Cloud existiert, wird nach der Authentifizierung ein neuer Benutzer angelegt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dies leitet Sie zur Dagster Cloud Sign-on URL um, wo Sie den Anmeldevorgang veranlassen können.  

* Gehen Sie direkt zur Dagster Cloud Sign-on URL und veranlassen Sie den Anmeldevorgang von dort aus.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie auf **diese Anwendung testen** im Azure-Portal und Sie sollten automatisch in der Dagster Cloud angemeldet werden, für die Sie das SSO eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie auf die Dagster-Cloud-Kachel in Meine Apps klicken, werden Sie, wenn Sie im SP-Modus konfiguriert sind, zur Anmeldeseite der Anwendung weitergeleitet, um den Anmeldevorgang zu veranlassen, und wenn Sie im IDP-Modus konfiguriert sind, sollten Sie automatisch bei der Dagster-Cloud angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Dagster Cloud konfiguriert haben, können Sie die Sitzungskontrolle erzwingen, die vor Exfiltration und Infiltration der sensiblen Daten Ihres Unternehmens in Echtzeit schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
