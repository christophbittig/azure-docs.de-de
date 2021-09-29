---
title: 'Tutorial: Azure Active Directory-Integration mit Bime | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Bime konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: d444a62b7bb6be680c359d6b853a629a1e2dc5a9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124830801"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Tutorial: Azure Active Directory-Integration mit Bime

In diesem Tutorial erfahren Sie, wie Sie Bime in Azure Active Directory (Azure AD) integrieren.
Die Integration von Bime in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Bime hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Bime angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit Bime benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Bime-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Bime unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-bime-from-the-gallery"></a>Hinzufügen von Bime aus dem Katalog

Zum Konfigurieren der Integration von Bime in Azure AD müssen Sie Bime aus dem Katalog Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Bime aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Bime** ein, wählen Sie im Ergebnisbereich **Bime** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Bime in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Bime basierend auf einem Testbenutzer namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Bime eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei Bime müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Bime](#configure-bime-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Bime-Testbenutzers](#create-bime-test-user)** , um in Bime ein Pendant von Britta Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Bime die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Bime** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Bime](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenant-name>.Bimeapp.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<tenant-name>.Bimeapp.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Bime](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

6. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** den **FINGERABDRUCK**, und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

7. Kopieren Sie im Abschnitt **Bime einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-bime-single-sign-on&quot;></a>Konfigurieren des einmaligen Anmeldens für Bime

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Bime-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der Symbolleiste auf **Administration** und dann auf **Konto**.

    ![Screenshot, in dem „Administration“ und „Konto“ ausgewählt sind](./media/bime-tutorial/ic775558.png &quot;Admin")

3. Führen Sie auf der Kontenkonfigurationsseite die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/bime-tutorial/ic775559.png "Einmaliges Anmelden konfigurieren")

    a. Aktivieren Sie **Enable SAML authentication**.

    b. Fügen Sie in das Textfeld **Remote Login URL** (Remoteanmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Certificate Fingerprint** (Fingerabdruck des Zertifikats) den Wert **Fingerabdruck** des Zertifikats ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Bime gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Bime** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **Bime** aus.

    ![Bime-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-bime-test-user&quot;></a>Erstellen eines Bime-Testbenutzers

Damit sich Azure AD-Benutzer bei Bime anmelden können, müssen sie in Bime bereitgestellt werden. Im Fall von Bime ist die Bereitstellung eine manuelle Aufgabe.

**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich bei Ihrem **Bime** -Mandanten an.

2. Klicken Sie auf der Symbolleiste auf **Administration** und dann auf **Benutzer**.

    ![Screenshot, in dem „Administration“ und „Benutzer“ ausgewählt sind](./media/bime-tutorial/ic775561.png &quot;Admin")

3. Klicken Sie unter **Benutzerliste** auf **Neuen Benutzer hinzufügen** („+“).

    ![Benutzer](./media/bime-tutorial/ic775562.png "Benutzer")

4. Führen Sie auf der Dialogfeldseite **User Details** die folgenden Schritte aus:

    ![User Details (Benutzerdetails)](./media/bime-tutorial/ic775563.png "User Details")

    a. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein (z.B. **Britta**).

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein (z.B. **Simon**).

    c. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com**.

    d. Klicken Sie auf **Speichern**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Bime-Benutzerkonten oder mit den APIs von Bime bereitstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Bime“ klicken, sollten Sie automatisch bei der Bime-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)