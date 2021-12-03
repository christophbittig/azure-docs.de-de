---
title: 'Tutorial: Azure Active Directory-Integration mit Evidence.com | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Evidence.com konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: jeedes
ms.openlocfilehash: 0eb4ba039deba2cac2059c71deb7cd0d55aa2d9b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285802"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evidencecom"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Evidence.com

In diesem Tutorial erfahren Sie, wie Sie Evidence.com in Azure Active Directory (Azure AD) integrieren. Die Integration von Evidence.com in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Evidence.com hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Evidence.com anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Evidence.com-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Evidence.com unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-evidencecom-from-the-gallery"></a>Hinzufügen von Evidence.com aus dem Katalog

Zum Konfigurieren der Integration von Evidence.com in Azure AD müssen Sie Evidence.com aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Evidence.com** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Evidence.com** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-evidencecom"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Evidence.com

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Evidence.com mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Evidence.com eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Evidence.com die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Evidence.com](#configure-evidencecom-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Evidence.com-Testbenutzers](#create-evidencecom-test-user)** , um eine Entsprechung von B. Simon in Evidence.com zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Evidence.com** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    1. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<yourtenant>.evidence.com`.

    1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<yourtenant>.evidence.com`.

    1. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<your tenant>.evidence.com/?class=UIX&proc=Login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für die Anmelde-URL, den Bezeichner und die Antwort-URL. Wenden Sie sich an das [Clientsupportteam von Evidence.com](https://communities.taser.com/support/SupportContactUs?typ=LE), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Evidence.com einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Evidence.com gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Evidence.com** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-evidencecom-sso"></a>Konfigurieren des einmaligen Anmeldens für Evidence.com

1. Melden Sie sich in einem separaten Webbrowserfenster bei Ihrem Evidence.com-Mandanten als Administrator an, und navigieren Sie zur Registerkarte **Admin**.

2. Klicken Sie auf **Agentur – einmaliges Anmelden**.

3. Wählen Sie **SAML-basiertes einmaliges Anmelden** aus.

4. Kopieren Sie die Werte für **Azure AD-Bezeichner**, **Anmelde-URL** und **Abmelde-URL**, die im Azure-Portal angezeigt werden, in die entsprechenden Felder von Evidence.com.

5. Öffnen Sie die heruntergeladene Datei „Zertifikat (Base64)“ in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Security Certificate** (Sicherheitszertifikat) ein. 

6. Speichern Sie die Konfiguration in Evidence.com.

### <a name="create-evidencecom-test-user"></a>Erstellen eines Evidence.com-Testbenutzers

Damit sich Azure AD-Benutzer anmelden können, müssen sie für den Zugriff innerhalb der Evidence.com-Anwendung bereitgestellt werden. In diesem Abschnitt wird beschrieben, wie in Evidence.com Azure AD-Benutzerkonten erstellt werden.

**So stellen Sie in Evidence.com ein Benutzerkonto bereit**

1. Melden Sie sich in einem Webbrowserfenster bei der Evidence.com-Unternehmenswebsite als Administrator an.

2. Navigieren Sie zur Registerkarte **Admin** .

3. Klicken Sie auf **Benutzer hinzufügen**.

4. Klicken Sie auf die Schaltfläche **Hinzufügen** .

5. Die **E-Mail-Adresse** des hinzugefügten Benutzers muss mit dem Benutzernamen des Benutzers in Azure AD übereinstimmen, dem Sie Zugriff gewähren möchten. Wenn der Benutzername und die E-Mail-Adresse in Ihrem Unternehmen nicht identisch sind, können Sie im Azure-Portal den Abschnitt **Evidence.com > Attribute > Einmaliges Anmelden** verwenden, um das an Evidence.com gesendete nameidenitifier-Element zu ändern und als E-Mail-Adresse zu verwenden.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Evidence.com weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Evidence.com, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Evidence.com“ klicken, werden Sie zur Anmelde-URL für Evidence.com weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Evidence.com können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor Ex- und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
