---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit AppNeta Performance Manager'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und AppNeta Performance Manager konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jeedes
ms.openlocfilehash: c9edd0bc2b1abe9aa60ff9d4c6ef08e6894b14d2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288886"
---
# <a name="tutorial-azure-ad-sso-integration-with-appneta-performance-manager"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit AppNeta Performance Manager

In diesem Tutorial erfahren Sie, wie Sie AppNeta Performance Manager in Azure Active Directory (Azure AD) integrieren. Die Integration von AppNeta Performance Manager in Azure AD ermöglicht Folgendes:

- Steuern Sie in Azure AD, wer Zugriff auf AppNeta Performance Manager hat.
- Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei AppNeta Performance Manager anzumelden.
- Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
- AppNeta Performance Manager-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

- AppNeta Performance Manager unterstützt **SP**-initiiertes einmaliges Anmelden.
- AppNeta Performance Manager unterstützt die **Just-In-Time**-Benutzerbereitstellung.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-appneta-performance-manager-from-the-gallery"></a>Hinzufügen von AppNeta Performance Manager aus dem Katalog

Um die Integration von AppNeta Performance Manager in Azure AD zu konfigurieren, müssen Sie AppNeta Performance Manager aus dem Katalog zu Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **AppNeta Performance Manager** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **AppNeta Performance Manager** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-manager"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für AppNeta Performance Manager

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit AppNeta Performance Manager mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in AppNeta Performance Manager eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit AppNeta Performance Manager zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für AppNeta Performance Manager](#configure-appneta-performance-manager-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen eines AppNeta Performance Manager-Testbenutzers](#create-appneta-performance-manager-test-user)** , um in AppNeta Performance Manager ein Pendant zu B. Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **AppNeta Performance Manager** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](./media/appneta-tutorial/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.pm.appneta.com`.

   b. Geben Sie im Feld „Antwort-URL (Assertionsverbraucherdienst-URL)“ Folgendes ein: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

   > [!NOTE]
   > Der oben genannte Wert für die Anmelde-URL ist ein Beispiel. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Diesen Wert erhalten Sie vom [Kundensupportteam für AppNeta Performance Manager](mailto:support@appneta.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die AppNeta Performance Manager-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute:

   ![Screenshot: Standardattribute für ein SAML-Token.](./media/appneta-tutorial/edit-attribute.png)

1. Darüber hinaus wird von der AppNeta Performance Manager-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

   | Name      | Quellattribut       |
   | --------- | ---------------------- |
   | firstName | user.givenname         |
   | lastName  | user.surname           |
   | email     | user.userprincipalname |
   | name      | user.userprincipalname |
   | groups    | user.assignedroles     |
   | phone     | user.telephonenumber   |
   | title     | user.jobtitle          |
   |           |                        |

1. Um Ihre SAML-Gruppenassertionen ordnungsgemäß zu übergeben, müssen Sie App-Rollen konfigurieren und den Wert entsprechend den Rollenzuordnungen festlegen, die in AppNeta Performance Manager festgelegt sind. Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** >  **Alle Anwendungen** die Option **Appneta Performance Manager** aus.

   ![Screenshot: App-Registrierungen mit Appneta Performance Manager am unteren Rand ](./media/appneta-tutorial/app-registrations.png)

1. Klicken Sie im linken Bereich auf **App-Rollen**. Der folgende Bildschirm wird angezeigt:

   ![Screenshot: App-Rollen mit Appneta Performance Manager am unteren Rand ](./media/appneta-tutorial/app-roles.png)

1. Klicken Sie auf **App-Rolle erstellen**.
1. Führen Sie auf dem Bildschirm **App-Rolle erstellen** die folgenden Schritte aus:
   1. Geben Sie im Feld **Anzeigename** einen Namen für die Rolle ein.
   1. Wählen Sie im Feld **Zulässige Membertypen** die Option **Benutzer/Gruppen** aus.
   1. Geben Sie im Feld **Wert** den Wert der Sicherheitsgruppe ein, die in Ihren AppNeta Performance Manager-Rollenzuordnungen festgelegt ist.
   1. Geben Sie im Feld **Beschreibung** eine Beschreibung für die Rolle ein.
   1. Klicken Sie auf **Anwenden**.

   ![Screenshot: Dialogfeld „App-Rolle erstellen“, in dem die Felder wie beschrieben ausgefüllt sind ](./media/appneta-tutorial/create-app-role.png)

1. Nach dem Erstellen der Rollen müssen Sie sie Ihren Benutzern/Gruppen zuordnen. Navigieren Sie zu **Azure Active Directory** > **Unternehmensanwendungen** > **Appneta Performance Manager** > **Benutzer und Gruppen**.
1. Wählen Sie einen Benutzer/eine Gruppe aus, und weisen Sie dann die gewünschte App-Rolle zu (die Sie im vorherigen Schritt erstellt haben).
1. Nachdem Sie die App-Rollen zugeordnet haben, navigieren Sie zu **Azure Active Directory** > **Unternehmensanwendungen** > **Appneta Performance Manager** > **Einmaliges Anmelden**.
1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf AppNeta Performance Manager gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen die Anwendung **AppNeta Performance Manager** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie die Rollen wie oben beschrieben eingerichtet haben, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

   > [!NOTE]
   > In der Praxis fügen Sie der Anwendung Gruppen anstelle einzelner Benutzer hinzu.

## <a name="configure-appneta-performance-manager-sso"></a>Konfigurieren des einmaligen Anmeldens für AppNeta Performance Manager

Um einmaliges Anmelden auf der **AppNeta Performance Manager**-Seite zu konfigurieren, müssen Sie die heruntergeladene **Verbundmetadaten-XML**-Datei an das [Supportteam von AppNeta Performance Manager](mailto:support@appneta.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-appneta-performance-manager-test-user"></a>Erstellen eines AppNeta Performance Manager-Testbenutzers

In diesem Abschnitt wird in AppNeta Performance Manager ein Benutzer namens B. Simon erstellt. AppNeta Performance Manager unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in AppNeta Performance Manager vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Setzen Sie sich mit dem [Supportteam von AppNeta Performance Manager](mailto:support@appneta.com) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

- Wählen Sie im Azure-Portal die Option **Diese Anwendung testen** aus. Dadurch werden Sie zur Anmelde-URL für AppNeta Performance Manager weitergeleitet. Dort können Sie den Anmeldeflow initiieren.

- Rufen Sie direkt die AppNeta Performance Manager-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

- Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie im Portal „Meine Apps“ auf die Kachel „AppNeta Performance Manager“ klicken, werden Sie zur Anmelde-URL für AppNeta Performance Manager weitergeleitet. Weitere Informationen zum Portal „Meine Apps“ finden Sie in dieser [Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von AppNeta Performance Manager können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration Ihrer vertraulichen Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
