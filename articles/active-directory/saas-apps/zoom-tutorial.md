---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Zoom | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zoom konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: b3474c926a0b172ab4665431c5897fdf333be8df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303680"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Zoom

In diesem Tutorial erfahren Sie, wie Sie Zoom in Azure Active Directory (Azure AD) integrieren. Die Integration von Zoom in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Zoom hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Zoom anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Zoom-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zoom unterstützt **SP-initiiertes** einmaliges Anmelden. 
* Zoom unterstützt die [**automatisierte** Benutzerbereitstellung](./zoom-provisioning-tutorial.md).

## <a name="adding-zoom-from-the-gallery"></a>Hinzufügen von Zoom aus dem Katalog

Zum Konfigurieren der Integration von Zoom in Azure AD müssen Sie Zoom aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zoom** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zoom** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-zoom"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zoom

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zoom mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zoom eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Zoom die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Zoom](#configure-zoom-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Zoom-Testbenutzers](#create-zoom-test-user)** , um eine Entsprechung von B. Simon in Zoom zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zoom** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.zoom.us`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `<companyname>.zoom.us`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<companyname>.zoom.us`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Zoom](https://support.zoom.us/hc/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Zoom einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

> [!NOTE]
> Informationen zum Konfigurieren einer Rolle in Azure AD finden Sie unter [Gewusst wie: Konfigurieren von im SAML-Token ausgestellten Rollenansprüchen für Unternehmensanwendungen](../develop/active-directory-enterprise-app-role-management.md).

> [!NOTE]
> Zoom erwartet in der SAML-Nutzlast unter Umständen einen Gruppenanspruch. Falls Sie Gruppen erstellt haben, wenden Sie sich mit den Gruppeninformationen an das [Supportteam für den Zoom-Client](https://support.zoom.us/hc/), damit es die Gruppeninformationen auf ihrer Seite konfigurieren kann. Geben Sie außerdem die Objekt-ID an das [Supportteam für den Zoom-Client](https://support.zoom.us/hc/) weiter, damit es diese bei sich konfigurieren kann. Informationen zum Ermitteln der Objekt-ID finden Sie im Artikel zum [Konfigurieren von Zoom mit Azure](https://support.zoom.us/hc/articles/115005887566).

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zoom gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Zoom** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-zoom-sso"></a>Konfigurieren des einmaligen Anmeldens für Zoom

1. Wenn Sie die Konfiguration in Zoom automatisieren möchten, müssen Sie die **Browsererweiterung zur sicheren Anmeldung bei „Meine Apps“** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Zoom einrichten**. Sie werden dann zur Anwendung Zoom weitergeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Zoom anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Zoom manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der Zoom-Unternehmenswebsite an.

2. Klicken Sie auf die Registerkarte **Einmaliges Anmelden**.

    ![Registerkarte „Einmaliges Anmelden“](./media/zoom-tutorial/zoom-sso1.png "Einmaliges Anmelden")

3. Klicken Sie auf die Registerkarte **Sicherheitskontrolle**, und öffnen Sie anschließend die Einstellungen für **Einmaliges Anmelden**.

4. Führen Sie im Abschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Abschnitt „Einmaliges Anmelden“](./media/zoom-tutorial/zoom-sso2.png "Einmaliges Anmelden")

    a. Fügen Sie in das Textfeld **Anmelde-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Für die **URL der Abmeldeseite** müssen Sie im Azure-Portal auf der linken Seite auf **Azure Active Directory** klicken und anschließend zu **App-Registrierungen** navigieren.

    ![Schaltfläche „Azure Active Directory“](./media/zoom-tutorial/appreg.png)

    c. Klicken Sie auf **Endpunkte**.

    ![Endpunkt-Schaltfläche](./media/zoom-tutorial/endpoint.png)

    d. Kopieren Sie den **SAML-P-ABMELDUNGSENDPUNKT**, und fügen Sie ihn in das Textfeld **URL der Abmeldeseite** ein.

    ![Schaltfläche zum Kopieren des Endpunkts](./media/zoom-tutorial/endpoint1.png)

    e. Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie den Inhalt in das Textfeld **Identity provider certificate** ein.

    f. Fügen Sie im Textfeld **Aussteller** den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben. 

    g. Klicken Sie auf **Änderungen speichern**.

    > [!NOTE]
    > Weitere Informationen finden Sie in der Dokumentation zum Zoomen [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566).

### <a name="create-zoom-test-user"></a>Erstellen eines Zoom-Testbenutzers

In diesem Abschnitt wird in Zoom ein Benutzer namens B. Simon erstellt. Zoom unterstützt die automatische Benutzerbereitstellung (standardmäßig aktiviert). Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](./zoom-provisioning-tutorial.md).

> [!NOTE]
> Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [Supportteam für den Zoom-Client](https://support.zoom.us/hc/).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Daraufhin werden Sie zur Anmelde-URL für Zoom weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Navigieren Sie direkt zur Anmelde-URL für Zoom, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „Zoom“ klicken, werden Sie zur Anmelde-URL für Zoom weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Azure AD Zoom können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
