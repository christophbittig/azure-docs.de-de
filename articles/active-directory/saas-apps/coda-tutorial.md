---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Coda | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Coda konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: 7b1358880660e7ab78d257556f0096a71f8ecc62
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305103"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Coda

In diesem Tutorial erfahren Sie, wie Sie Coda in Azure Active Directory (Azure AD) integrieren. Die Integration von Coda in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Coda hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Coda anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Coda-Abonnement (Enterprise), für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert und die GDrive-Integration deaktiviert ist. Wenden Sie sich an das [Coda-Supportteam](mailto:support@coda.io), um die GDrive-Integration für Ihre Organisation zu deaktivieren, sollte sie derzeit aktiviert sein.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Coda unterstützt **IDP-initiiertes** einmaliges Anmelden.

* Coda unterstützt die **Just-In-Time**-Benutzerbereitstellung.

* Coda unterstützt die [automatisierte Benutzerbereitstellung](coda-provisioning-tutorial.md).

## <a name="add-coda-from-the-gallery"></a>Hinzufügen von Coda aus dem Katalog

Um die Integration von Coda in Azure AD zu konfigurieren, müssen Sie Coda über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Coda** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Coda** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-coda"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Coda

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Coda mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Coda eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Coda die folgenden Schritte aus:

1. **[Beginnen mit der Konfiguration des einmaligen Anmeldens für Coda](#begin-configuration-of-coda-sso)** , um einmaliges Anmelden in Coda zu konfigurieren
1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Coda](#configure-coda-sso)** , um die Konfiguration der Einstellungen für einmaliges Anmelden in Coda abzuschließen
   1. **[Erstellen eines Coda-Testbenutzers](#create-coda-test-user)**, um eine Entsprechung von B. Simon in Coda zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="begin-configuration-of-coda-sso"></a>Beginnen mit der Konfiguration des einmaligen Anmeldens für Coda

Führen Sie zunächst die folgenden Schritte in Coda aus:

1. Öffnen Sie in Coda das Panel **Organization settings** (Organisationseinstellungen).

   ![Öffnen von „Organization settings“ (Organisationseinstellungen)](media/coda-tutorial/settings.png)

1. Vergewissern Sie sich, dass die GDrive-Integration in Ihrer Organisation deaktiviert ist. Wenn sie derzeit aktiviert ist, wenden Sie sich an das [Coda-Supportteam](mailto:support@coda.io), um Unterstützung bei der Migration von GDrive zu einer anderen Lösung zu erhalten.

   ![Deaktivierte GDrive-Integration](media/coda-tutorial/gdrive-off.png)

1. Aktivieren Sie unter **Authenticate with SSO (SAML)** (Authentifizieren mit SSO (SAML)) die Option **Configure SAML** (SAML konfigurieren).

   ![SAML-Einstellungen](media/coda-tutorial/settings-link.png)

1. Notieren Sie sich die Werte für **Entity ID** (Entitäts-ID) und **SAML Response URL** (SAML-Antwort-URL) zur späteren Verwendung.

   ![Entitäts-ID und SAML-Antwort-URL für die Verwendung in Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Coda** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

   a. Geben Sie im Textfeld **Bezeichner** die Entitäts-ID ein, die Sie sich oben notiert haben. Sie muss folgendes Format haben: `https://coda.io/samlId/<CUSTOMID>`.

   b. Geben Sie im Textfeld **Antwort-URL** die SAML-Antwort-URL ein, die Sie sich oben notiert haben. Sie muss folgendes Format haben: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`.

   > [!NOTE]
   > Ihre Werte unterscheiden sich von den obigen Werten. Sie finden Ihre Werte in der Coda-Konsole „Configure SAML“ (SAML konfigurieren). Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Coda einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Coda gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Coda** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-coda-sso"></a>Konfigurieren des einmaligen Anmeldens für Coda

Geben Sie zum Abschließen des Setups Werte von Azure Active Directory im Coda-Panel **Configure Saml** (SAML konfigurieren) ein.

1. Öffnen Sie in Coda das Panel **Organization settings** (Organisationseinstellungen).
1. Aktivieren Sie unter **Authenticate with SSO (SAML)** (Authentifizieren mit SSO (SAML)) die Option **Configure SAML** (SAML konfigurieren).
1. Legen Sie **SAML Provider** (SAML-Anbieter) auf **Azure Active Directory** fest.
1. Fügen Sie unter **Identity Provider Login URL** (Anmelde-URL des Identitätsanbieters) die **Anmelde-URL** aus der Azure-Konsole ein.
1. Fügen Sie unter **Identity Provider Issuer** (Aussteller des Identitätsanbieters) den **Azure AD-Bezeichner** aus der Azure-Konsole ein.
1. Wählen Sie unter **Identity Provider Public Certificate** (Öffentliches Zertifikat des Identitätsanbieters) die Option **Upload Certificate** (Zertifikat hochladen) und dann die zuvor heruntergeladene Zertifikatdatei aus.
1. Wählen Sie **Speichern** aus.

Damit ist das Setup der SAML-SSO-Verbindung abgeschlossen.

### <a name="create-coda-test-user"></a>Erstellen eines Coda-Testbenutzers

In diesem Abschnitt wird in Coda ein Benutzer mit dem Namen Britta Simon erstellt. Coda unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Coda vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

Außerdem unterstützt Coda die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./coda-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Coda-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Coda“ klicken, sollten Sie automatisch bei der Coda-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Coda können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
