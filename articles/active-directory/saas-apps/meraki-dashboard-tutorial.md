---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Meraki Dashboard | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Meraki Dashboard konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/28/2021
ms.author: jeedes
ms.openlocfilehash: 32064da608aaf5f522f2d59b98f43e90a194b623
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326112"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Meraki Dashboard

In diesem Tutorial erfahren Sie, wie Sie Meraki Dashboard in Azure Active Directory (Azure AD) integrieren. Die Integration von Meraki Dashboard in Azure AD ermöglicht Folgendes:

- Steuern Sie in Azure AD, wer Zugriff auf Meraki Dashboard hat.
- Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Meraki Dashboard anzumelden.
- Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
- Ein Meraki Dashboard-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

- Meraki Dashboard unterstützt **IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Hinzufügen von Meraki Dashboard aus dem Katalog

Zum Konfigurieren der Integration von Meraki Dashboard in Azure AD müssen Sie Meraki Dashboard aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Meraki Dashboard** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Meraki Dashboard** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Meraki Dashboard

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Meraki Dashboard mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Meraki Dashboard eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Meraki Dashboard die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Meraki Dashboard](#configure-meraki-dashboard-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen einer Meraki Dashboard-Testbenutzerin](#create-meraki-dashboard-admin-roles)** , um eine Entsprechung von B. Simon in Meraki Dashboard zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Meraki Dashboard** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

   Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

   > [!NOTE]
   > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie diesen Wert mit dem Wert der tatsächlichen Antwort-URL. Dies wird später in diesem Tutorial beschrieben.

1. Klicken Sie auf die Schaltfläche **Save** .

1. Die Meraki Dashboard-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

   ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Meraki Dashboard-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

   | Name                                                    | Quellattribut       |
   | ------------------------------------------------------- | ---------------------- |
   | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
   | `https://dashboard.meraki.com/saml/attributes/role`     | user.assignedroles     |

   > [!NOTE]
   > Informationen zum Konfigurieren von Rollen in Azure AD finden Sie [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

   ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

1. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** unter **Fingerabdruck** den Fingerabdruckwert, und speichern Sie ihn auf Ihrem Computer. Dieser Wert muss in einen Wert mit Doppelpunkten konvertiert werden, damit er vom Meraki-Dashboard interpretiert werden kann. Wenn der Fingerabdruck von Azure beispielsweise `C2569F50A4AAEDBB8E` lautet, muss er in `C2:56:9F:50:A4:AA:ED:BB:8E` geändert werden, damit er später auf dem Meraki-Dashboard verwendet werden kann.

   ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

1. Kopieren Sie im Abschnitt **Meraki Dashboard einrichten** den Wert der Abmelde-URL, und speichern Sie ihn auf Ihrem Computer.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Meraki Dashboard gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Meraki Dashboard** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.

   ![Benutzerrolle](./media/meraki-dashboard-tutorial/user-role.png)

   > [!NOTE]
   > Die Option **Rolle auswählen** ist deaktiviert, und die Standardrolle für den ausgewählten Benutzer lautet „BENUTZER“.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-meraki-dashboard-sso"></a>Konfigurieren des einmaligen Anmeldens für Meraki Dashboard

1. Wenn Sie die Konfiguration in Meraki Dashboard automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

   ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Meraki Dashboard einrichten**. Sie gelangen dann direkt zur Meraki Dashboard-Anwendung. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Meraki Dashboard anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

   ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Meraki Dashboard manuell einrichten möchten, müssen Sie sich in einem anderen Webbrowserfenster als Administrator bei der Meraki Dashboard-Unternehmenswebsite anmelden.

4. Navigieren Sie zu **Organization** -> **Settings** (Organisation > Einstellungen).

   ![Meraki Dashboard: Registerkarte „Settings“ (Einstellungen)](./media/meraki-dashboard-tutorial/configure-1.png)

5. Legen Sie unter „Authentication“ (Authentifizierung) für **SAML SSO** (SAML-SSO) die Option **SAML SSO enabled** (SAML-SSO aktiviert) fest.

   ![Meraki Dashboard: Authentication (Authentifizierung)](./media/meraki-dashboard-tutorial/configure-2.png)

6. Klicken Sie auf **Add a SAML IdP** (SAML-IdP hinzufügen).

   ![Meraki Dashboard: Add a SAML IdP (SAML-IdP hinzufügen)](./media/meraki-dashboard-tutorial/configure-3.png)

7. Fügen Sie den konvertierten Wert für **Fingerabdruck**, den Sie aus dem Azure-Portal kopiert und gemäß Schritt 9 des vorherigen Abschnitts in das angegebene Format konvertiert haben, in das Textfeld **X.590 cert SHA1 fingerprint** ein. Klicken Sie anschließend auf **Speichern**. Nach dem Speichern wird die Consumer-URL angezeigt. Kopieren Sie den Wert der Consumer-URL, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

   ![Meraki Dashboard-Konfiguration](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-admin-roles"></a>Erstellen von Meraki Dashboard-Administratorrollen

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Meraki Dashboard-Website als Administrator an.

1. Navigieren Sie zu **Organization** -> **Administrators** (Organisation > Administratoren).

   ![Meraki Dashboard: Administrators (Administratoren)](./media/meraki-dashboard-tutorial/user-1.png)

1. Klicken Sie im Abschnitt „SAML administrator roles“ (SAML-Administratorrollen) auf die Schaltfläche **Add SAML role** (SAML-Rolle hinzufügen).

   ![Meraki Dashboard: Add SAML role (SAML-Rolle hinzufügen)](./media/meraki-dashboard-tutorial/user-2.png)

1. Geben Sie die Rolle **meraki_full_admin** ein, legen Sie für **Organization access** (Organisationszugriff) die Option **Full** (Vollzugriff) fest, und klicken Sie dann auf **Create role** (Rolle erstellen). Wiederholen Sie den Vorgang für **meraki_readonly_admin**, legen Sie dieses Mal jedoch für **Organization access** (Organisationszugriff) die Option **Read-only** (Schreibgeschützt) fest.

   ![Meraki Dashboard: Erstellen eines Benutzers](./media/meraki-dashboard-tutorial/user-3.png)

1. Führen Sie die folgenden Schritte aus, um die Meraki Dashboard-Rollen SAML-Rollen in Azure AD zuzuordnen:

   ![Screenshot der App-Rollen](./media/meraki-dashboard-tutorial/app-role.png)

   a. Klicken Sie im Azure-Portal auf **App-Registrierungen**.

   b. Wählen Sie „Alle Anwendungen“ aus, und klicken Sie auf **Meraki Dashboard**.

   c. Klicken Sie auf **App-Rollen** und dann auf **App-Rolle erstellen**.

   d. Geben Sie den Anzeigenamen ein, z. B. `Meraki Full Admin`.
   
   e. Wählen Sie als zulässige Member `Users/Groups` aus.

   f. Geben Sie als Wert `meraki_full_admin` ein.

   g. Geben Sie als Beschreibung `Meraki Full Admin` ein.

   h. Klicken Sie auf **Speichern**. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

- Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Meraki Dashboard-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

- Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Meraki Dashboard“ klicken, sollten Sie automatisch bei der Meraki Dashboard-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Meraki Dashboard können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
