---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Check Point CloudGuard Posture Management | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Check Point CloudGuard Posture Management konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/13/2021
ms.author: jeedes
ms.openlocfilehash: db7e60255369c853ec3986d8446d47466048d1b9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288025"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-posture-management"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Check Point CloudGuard Posture Management

In diesem Tutorial erfahren Sie, wie Sie Check Point CloudGuard Posture Management in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Check Point CloudGuard Posture Management in Azure AD haben Sie folgende Möglichkeiten:

- Sie können in Azure AD steuern, wer Zugriff auf Check Point CloudGuard Posture Management haben soll.
- Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Check Point CloudGuard Posture Management anzumelden.
- Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Check Point CloudGuard Posture Management-Abonnement mit SSO-Unterstützung

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Check Point CloudGuard Posture Management unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-check-point-cloudguard-posture-management-from-the-gallery"></a>Hinzufügen von Check Point CloudGuard Posture Management aus dem Katalog

Zum Konfigurieren der Integration von Check Point CloudGuard Posture Management in Azure AD müssen Sie Check Point CloudGuard Posture Management aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Check Point CloudGuard Posture Management** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Check Point CloudGuard Posture Management** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-check-point-cloudguard-posture-management"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Check Point CloudGuard Posture Management

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Check Point CloudGuard Posture Management mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Check Point CloudGuard Posture Management eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Check Point CloudGuard Posture Management die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)**, um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. **[Konfigurieren des einmaligen Anmeldens für Check Point CloudGuard Posture Management](#configure-check-point-cloudguard-posture-management-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen eines Check Point CloudGuard Posture Management-Testbenutzers](#create-check-point-cloudguard-posture-management-test-user)** , um in Check Point CloudGuard Posture Management eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Check Point CloudGuard Posture Management** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

   Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://secure.dome9.com/sso/saml/<YOURCOMPANYNAME>`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

   Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://secure.dome9.com/sso/saml/<YOURCOMPANYNAME>`

   > [!NOTE]
   > Hierbei handelt es sich um Beispielwerte. Die Werte müssen durch die tatsächliche Antwort-URL und die tatsächliche Anmelde-URL ersetzt werden. Den Wert `<company name>` erhalten Sie im Abschnitt **Konfigurieren des einmaligen Anmeldens für Check Point CloudGuard Posture Management**. Die Vorgehensweise wird später in diesem Tutorial erläutert. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Check Point CloudGuard Posture Management-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

   ![image](common/edit-attribute.png)

1. Darüber hinaus wird von der Check Point CloudGuard Posture Management-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

   | Name     | Quellattribut   |
   | -------- | ------------------ |
   | memberof | user.assignedroles |

   > [!NOTE]
   > Klicken Sie [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui), um zu erfahren, wie Sie Rollen in Azure AD erstellen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Check Point CloudGuard Posture Management einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Check Point CloudGuard Posture Management gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Check Point CloudGuard Posture Management** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie die Rollen wie oben erläutert eingerichtet haben, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-check-point-cloudguard-posture-management-sso"></a>Konfigurieren des einmaligen Anmeldens für Check Point CloudGuard Posture Management

1. Wenn Sie die Konfiguration in Check Point CloudGuard Posture Management automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

   ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Check Point CloudGuard Posture Management einrichten**. Daraufhin werden Sie zur Check Point CloudGuard Posture Management-Anwendung weitergeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Check Point CloudGuard Posture Management anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

   ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Check Point CloudGuard Posture Management manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der Check Point CloudGuard Posture Management-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie in der oberen rechten Ecke auf **Profileinstellungen** und dann auf **Kontoeinstellungen**.

   ![Screenshot: Menü „Profileinstellungen“ mit ausgewählter Option „Kontoeinstellungen“](./media/dome9arc-tutorial/account-settings.png)

5. Navigieren Sie zu **SSO**, und klicken Sie dann auf **AKTIVIEREN**.

   ![Screenshot: Registerkarte „SSO“ (Einmaliges Anmelden) mit ausgewählter Option „Aktivieren“](./media/dome9arc-tutorial/enable.png)

6. Führen Sie im Abschnitt für die SSO-Konfiguration die folgenden Schritte aus:

   ![Konfiguration von Check Point CloudGuard Posture Management](./media/dome9arc-tutorial/configuration.png)

   a. Geben Sie den Unternehmensnamen in das Textfeld **Konto-ID** ein. Dieser Wert wird für die **Antwort-URL** und die **Anmelde-URL** verwendet, die im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** angegeben sind.

   b. Fügen Sie im Textfeld **Issuer** (Aussteller) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

   c. Fügen Sie in das Textfeld **Idp endpoint url** (IdP-Endpunkt-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

   d. Öffnen Sie das heruntergeladene Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.

   e. Klicken Sie auf **Speichern**.

### <a name="create-check-point-cloudguard-posture-management-test-user"></a>Erstellen eines Check Point CloudGuard Posture Management-Testbenutzers

Damit sich Azure AD-Benutzer bei Check Point CloudGuard Posture Management anmelden können, müssen sie in der Anwendung bereitgestellt werden. Check Point CloudGuard Posture Management unterstützt die Just-In-Time-Bereitstellung. Damit diese funktioniert, muss der Benutzer jedoch eine bestimmte **Rolle** auswählen und sie dem Benutzer zuweisen.

> [!Note]
> Wenn Sie Informationen zum Erstellen der **Rolle** oder andere Details benötigen, wenden Sie sich an das [Supportteam für den Check Point CloudGuard Posture Management-Client](mailto:Dome9@checkpoint.com).

**Führen Sie zum manuellen Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Check Point CloudGuard Posture Management-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Benutzer und Rollen** und dann auf **Benutzer**.

   ![Screenshot: „Benutzer und Rollen“ mit ausgewählter Aktion „Benutzer“](./media/dome9arc-tutorial/users.png)

3. Klicken Sie auf **BENUTZER HINZUFÜGEN**.

   ![Screenshot: „Benutzer und Rollen“ mit ausgewählter Schaltfläche „BENUTZER HINZUFÜGEN“](./media/dome9arc-tutorial/add-user.png)

4. Führen Sie im Abschnitt **Benutzer erstellen** die folgenden Schritte aus:

   ![Mitarbeiter hinzufügen](./media/dome9arc-tutorial/create-user.png)

   a. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers ein, z.B. B.Simon@contoso.com.

   b. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein, z. B. „B.“.

   c. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein, z.B. Simon.

   d. Legen Sie **SSO User** (SSO-Benutzer) auf **Ein** fest.

   e. Klicken Sie auf **ERSTELLEN**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Check Point CloudGuard Posture Management weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Navigieren Sie direkt zur Anmelde-URL für Check Point CloudGuard Posture Management, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Check Point CloudGuard Posture Management-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie unter „Meine Apps“ auf die Kachel „Check Point CloudGuard Posture Management“ klicken, geschieht Folgendes: Falls Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Falls Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Check Point CloudGuard Posture Management-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Check Point CloudGuard Posture Management können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
