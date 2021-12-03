---
title: 'Tutorial: Azure Active Directory-Integration mit Comeet Recruiting Software | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Comeet Recruiting Software konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: jeedes
ms.openlocfilehash: d5b338446766d3049002711c9b34bd3325e7bf77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311505"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>Tutorial: Azure Active Directory-Integration mit Comeet Recruiting Software

In diesem Tutorial lernen Sie, wie Sie die Comeet Recruiting Software mit Azure Active Directory (Azure AD) integrieren können. Wenn Sie Comeet Recruiting Software mit Azure AD integrieren, können Sie:

* In Azure AD kontrollieren, wer Zugang zur Comeet Recruiting Software hat.
* Die automatische Anmeldung Ihrer Benutzer bei Comeet Recruiting Software mit ihren Azure AD Konten aktivieren.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Comeet Recruiting Software Einmaliges Anmelden (SSO) aktiviertes Abonnement.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Comeet Recruiting Software unterstützt **DSP und IDPB** veranlasste SSO.
* Comeet Recruiting Software unterstützt [Automatisierte Benutzerbereitstellung](comeet-recruiting-software-provisioning-tutorial.md).


## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Hinzufügen von Comeet Recruiting Software aus dem Katalog

Zum Konfigurieren der Integration von Comeet Recruiting Software in Azure AD müssen Sie Comeet Recruiting Software aus dem Katalog der Liste Ihrer verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Bereich **Aus Katalog hinzufügen** **Comeet Recruiting Software in das Suchfeld** ein.
1. Wählen Sie **Comeet Recruiting Software** aus dem Ergebnisbereich und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-comeet-recruiting-software"></a>Konfigurieren und testen Sie Azure AD SSO für Comeet Recruiting Software

Konfigurieren und testen Sie Azure AD SSO mit Comeet Recruiting Software unter Verwendung eines Testbenutzers namens **B Simon**. Damit SSO funktioniert, müssen Sie eine Verknüpfungsbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Comeet Recruiting Software herstellen.

Um Azure AD SSO mit Comeet Recruiting Software zu konfigurieren und zu testen, führen Sie die folgenden Schritte durch:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren von Comeet Recruiting Software SSO](#configure-comeet-recruiting-software-sso)** , um die -Einstellungen für Einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Comeet Recruiting Software-Testbenutzers](#create-comeet-recruiting-software-test-user)** , um eine Entsprechung von Britta Simon in Comeet Recruiting Software zu erhalten, die mit ihrer Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im Azure-Portal auf der Seite Anwendungsintegration von **Comeet Recruiting Software** den Abschnitt **Verwalten** und wählen Sie **Einmaliges Anmelden**.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Kundensupportteam von Comeet Recruiting Software](https://support.comeet.co/knowledgebase/adfs-single-sign-on/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL ein: `https://app.comeet.co`.

5. Ihre Comeet Recruiting Software-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot: Abschnitt „User Attributes“ (Benutzerattribute) mit der Schaltfläche „Bearbeiten“ ausgewählt](common/edit-attribute.png)

6. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus: 

    | Name |  Quellattribut|
    | ---------------| --------------- |
    | nameidentifier | user.mail |
    | comeet_id | user.userprincipalname |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Abschnitt „Benutzeransprüche“ mit den hervorgehobenen Aktionen „Neuen Anspruch hinzufügen“ und „Speichern“](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Comeet Recruiting Software einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt aktivieren Sie B.Simon für die Nutzung des Einmaligen Anmeldens in Azure, indem Sie den Zugriff auf die Comeet Recruiting Software gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Comeet Recruiting Software** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-comeet-recruiting-software-sso"></a>Konfigurieren von Comeet Recruiting Software SSO

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Comeet Recruiting Software** müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Comeet Recruiting Software](https://support.comeet.co/knowledgebase/adfs-single-sign-on/) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-comeet-recruiting-software-test-user"></a>Erstellen eines Comeet Recruiting Software-Testbenutzers

In diesem Abschnitt erstellen Sie in der Comeet Recruiting Software eine Benutzerin namens Britta Simon. Arbeiten Sie mit dem [Comeet Recruiting Software-Supportteam](mailto:support@comeet.co) beim Hinzufügen der Benutzer zur Comeet Recruiting Software-Plattform zusammen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

Comeet Recruiting Software unterstützt auch die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./comeet-recruiting-software-provisioning-tutorial.md).

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

SP-initiiert:

* Klicken Sie im Azure-Portal auf Diese Anwendung testen. Dies leitet Sie zur Comeet Recruiting Software Sign-on URL um, wo Sie den Anmeldevorgang veranlassen können.

* Gehen Sie direkt zur Comeet Recruiting Software Sign-on URL und veranlassen Sie den Anmeldevorgang von dort.

IDP-initiiert:

* Klicken Sie auf Diese Anwendung im Azure-Portal testen und Sie sollten automatisch bei der Comeet Recruiting Software angemeldet sein, für die Sie das SSO eingerichtet haben

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie auf die Kachel Comeet Recruiting Software in Meine Apps klicken: wenn Sie im SP-Modus konfiguriert ist, werden Sie zur Anmeldeseite der Anwendung weitergeleitet, um den Anmeldevorgang zu veranlassen, und wenn Sie im IDP-Modus konfiguriert ist, sollten Sie automatisch bei der Comeet Recruiting Software angemeldet werden, für die Sie das SSO eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Comeet Recruiting Software konfiguriert haben, können Sie die Sitzungskontrolle erzwingen, die vor Exfiltration und Infiltration der sensiblen Daten Ihres Unternehmens in Echtzeit schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
