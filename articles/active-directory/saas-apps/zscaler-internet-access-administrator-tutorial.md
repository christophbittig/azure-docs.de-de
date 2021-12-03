---
title: 'Tutorial: Azure Active Directory-Integration mit Zscaler Internet Access Administrator | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler Internet Access Administrator konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 03ca5536ed373183f48769158e7cdf3686dc2722
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302386"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Tutorial: Azure Active Directory-Integration mit Zscaler Internet Access Administrator

In diesem Tutorial erfahren Sie, wie Sie Zscaler Internet Access Administrator in Azure Active Directory (Azure AD) integrieren. Die Integration von Zscaler Internet Access Administrator in Azure AD ermöglicht Folgendes:

- Steuern Sie in Azure AD, wer Zugriff auf Zscaler Internet Access Administrator hat.
- Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Zscaler Internet Access Administrator anzumelden.
- Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
- Ein Abonnement für Zscaler Internet Access Administrator, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

- Zscaler Internet Access Administrator unterstützt **IDP**-initiiertes SSO.

## <a name="add-zscaler-internet-access-administrator-from-the-gallery"></a>Hinzufügen von Zscaler Internet Access Administrator aus dem Katalog

Zum Konfigurieren der Integration von Zscaler Internet Access Administrator in Azure AD müssen Sie Zscaler Internet Access Administrator aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zscaler Internet Access Administrator** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zscaler Internet Access Administrator** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zscaler Internet Access Administrator

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler Internet Access Administrator mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler Internet Access Administrator eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zscaler Internet Access Administrator die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für Zscaler Internet Access Administrator](#configure-zscaler-internet-access-administrator-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen eines Testbenutzers für Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)** , um eine Entsprechung von Britta Simon in Zscaler Internet Access Administrator zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zscaler Internet Access Administrator** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   a. Geben Sie im Textfeld **Bezeichner** eine der folgenden URLs gemäß Ihren Anforderungen ein:

   | Bezeichner                       |
   | -------------------------------- |
   | `https://admin.zscaler.net`      |
   | `https://admin.zscalerone.net`   |
   | `https://admin.zscalertwo.net`   |
   | `https://admin.zscalerthree.net` |
   | `https://admin.zscloud.net`      |
   | `https://admin.zscalerbeta.net`  |

   b. Geben Sie im Textfeld **Antwort-URL** eine der folgenden URLs gemäß Ihren Anforderungen ein:

   | Antwort-URL                                    |
   | -------------------------------------------- |
   | `https://admin.zscaler.net/adminsso.do`      |
   | `https://admin.zscalerone.net/adminsso.do`   |
   | `https://admin.zscalertwo.net/adminsso.do`   |
   | `https://admin.zscalerthree.net/adminsso.do` |
   | `https://admin.zscloud.net/adminsso.do`      |
   | `https://admin.zscalerbeta.net/adminsso.do`  |

1. Die Zscaler Internet Access Administrator-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute und Ansprüche** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute und Ansprüche** zu öffnen.

   ![Attributlink](./media/zscaler-internet-access-administrator-tutorial/attributes.png)

1. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

   | Name | Quellattribut   |
   | ---- | ------------------ |
   | Role | user.assignedroles |

   a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

   b. Wählen Sie in der Liste **Quellattribut** den Attributwert aus.

   c. Klicken Sie auf **OK**.

   d. Klicken Sie auf **Speichern**.

   > [!NOTE]
   > Klicken Sie [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui), um herauszufinden, wie Sie die Rolle in Azure AD konfigurieren.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt für die **Einrichtung von Zscaler Internet Access Administrator** die URLs, die Ihre Anforderungen erfüllen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler Internet Access Administrator gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Zscaler Internet Access Administrator** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie die Rollen wie oben erläutert eingerichtet haben, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-zscaler-internet-access-administrator-sso"></a>Konfigurieren des einmaligen Anmeldens für Zscaler Internet Access Administrator

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrer Zscaler Internet Access Administrator-Benutzeroberfläche an.

2. Navigieren Sie zu **Administration > Administrator Management** (Administration > Administratorverwaltung), und führen Sie die folgenden Schritte aus. Klicken Sie anschließend auf „Speichern“:

   ![Screenshot der Administratorverwaltung mit Optionen zum Aktivieren der SAML-Authentifizierung, Hochladen des SSL-Zertifikats und Angeben eines Ausstellers](./media/zscaler-internet-access-administrator-tutorial/management.png "Verwaltung")

   a. Aktivieren Sie die Option **SAML-Authentifizierung aktivieren**.

   b. Klicken Sie auf **Upload** (Hochladen), um das Azure-SAML-Signaturzertifikat hochzuladen, das Sie aus dem Azure-Portal unter **Public SSL Certificate** (Öffentliches SSL-Zertifikat) heruntergeladen haben.

   c. Fügen Sie zur Erhöhung der Sicherheit optional die Details zum **Aussteller** hinzu, um den Aussteller der SAML-Antwort zu überprüfen.

3. Führen Sie auf der Administratorbenutzeroberfläche die folgenden Schritte aus:

   ![Screenshot der Administratorbenutzeroberfläche zum Ausführen der Schritte](./media/zscaler-internet-access-administrator-tutorial/activation.png)

   a. Bewegen Sie den Mauszeiger unten links auf das Menü **Aktivierung**.

   b. Klicken Sie auf **Aktivieren**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Erstellen des Zscaler Internet Access Administrator-Testbenutzers

Das Ziel dieses Abschnitts besteht darin, eine Benutzerin mit dem Namen Britta Simon in Zscaler Internet Access Administrator zu erstellen. Für Zscaler Internet Access wird die Just-In-Time-Bereitstellung für einmaliges Anmelden für Administratoren nicht unterstützt. Sie müssen manuell ein Administratorkonto erstellen.
Die Schritte zum Erstellen eines Administratorkontos finden Sie in der Zscaler-Dokumentation:

https://help.zscaler.com/zia/adding-admins

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

- Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Zscaler Internet Access Administrator-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

- Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel für Zscaler Internet Access Administrator klicken, sollten Sie automatisch bei der Zscaler Internet Access Administrator-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Zscaler Internet Access Administrator können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
