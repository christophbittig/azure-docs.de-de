---
title: 'Tutorial: Azure Active Directory-Integration mit Signagelive | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Signagelive konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 4848e395d62629f5e104e275a5430bfaf2c6f3e6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304665"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Tutorial: Azure Active Directory-Integration mit Signagelive

In diesem Tutorial lernen Sie, wie Sie Signagelive in Azure Active Directory (Azure AD) integrieren können. Wenn Sie Signagelive mit Azure AD integrieren, können Sie:

* In Azure AD kontrollieren, wer Zugriff auf Signagelive hat.
* Aktivieren, dass Ihre Benutzer automatisch mit ihren Azure AD-Konten bei Signagelive angemeldet werden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Signagelive-Abonnement mit aktivierten einmaligem Anmelden (SSO).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Signagelive unterstützt SP-initiiertes einmaliges Anmelden.
* Signagelive unterstützt [Automatisierte Benutzerbereitstellung](signagelive-provisioning-tutorial.md).

## <a name="add-signagelive-from-the-gallery"></a>Hinzufügen von Signagelive aus dem Katalog

Zum Konfigurieren der Integration von Signagelive in Azure AD müssen Sie Signagelive aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **aus dem Katalog hinzufügen** in das Suchfeld **Signagelive** ein.
1. Wählen Sie **Signagelive** im Ergebnisbereich aus und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-signagelive"></a>Konfigurieren und testen Sie Azure AD SSO für Signagelive

Konfigurieren und testen Sie Azure AD SSO mit Signagelive unter Verwendung eines Testbenutzers namens **B.Simon**. Damit SSO funktioniert, müssen Sie eine Verknüpfungsbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Signagelive herstellen.

Um Azure AD SSO mit Signagelive zu konfigurieren und zu testen, führen Sie die folgenden Schritte durch:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren Sie Signagelive SSO](#configure-signagelive-sso)** , um die Einstellungen für Einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen Sie Signagelive-Testbenutzer](#create-signagelive-test-user)** , um ein Gegenüber von B.Simon in Signagelive zu haben, das mit der Azure AD-Darstellung des Benutzers verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Im Azure-Portal auf der Seite Anwendungsintegration von **Signagelive** : Suchen Sie den Abschnitt **Verwalten** und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **basic SAML-Konfiguration** den folgenden Schritt aus:

    Geben Sie im Feld **URL für Anmeldung** eine URL mit diesem Muster ein: `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam für den Signagelive-Client](mailto:support@signagelive.com), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende Zertifikat (**Zertifikat (Rohdaten)** ) aus den angegebenen Optionen herunterzuladen. Speichern Sie das Zertifikat anschließend auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificateraw.png)

6. Kopieren Sie im Abschnitt **Signagelive einrichten** die URLs, die Sie benötigen.

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

In diesem Abschnitt aktivieren Sie B.Simon für die Verwendung von Azure Einmaliges Anmelden, indem Sie den Zugriff auf Signagelive gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Signagelive** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-signagelive-sso"></a>Konfigurieren des SSO für Signagelive

Zum Konfigurieren des einmaligen Anmeldens aufseiten von Signagelive müssen Sie das heruntergeladene **Zertifikat (Rohdaten)** und die aus dem Azure-Portal kopierten URLs an das [Supportteam von Signagelive](mailto:support@signagelive.com) senden. Es sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-signagelive-test-user"></a>Erstellen eines Signagelive-Testbenutzers

In diesem Abschnitt erstellen Sie in Signagelive einen Benutzer namens Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer auf der Signagelive-Plattform vom [Signagelive-Supportteam](mailto:support@signagelive.com) unterstützen. Sie müssen Benutzer erstellen und aktivieren, bevor Sie einmaliges Anmelden verwenden.

Signagelive unterstützt auch die automatische Benutzerbereitstellung. Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](./signagelive-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dies wird Sie zur Signagelive Anmelde-URL umleiten, wo Sie den Anmeldevorgang veranlassen können. 

* Gehen Sie direkt zur Signagelive Anmelde-URL und veranlassen Sie den Anmeldevorgang von dort aus.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie auf die Signagelive-Kachel in Meine Apps klicken, wird diese zur Signagelive Anmelde-URL umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Signagelive konfiguriert haben, können Sie die Sitzungskontrolle erzwingen, die vor Exfiltrieren und Infiltrieren der sensiblen Daten Ihres Unternehmens in Echtzeit schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
