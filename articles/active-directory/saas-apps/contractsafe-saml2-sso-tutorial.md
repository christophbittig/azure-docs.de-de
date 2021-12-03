---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit ContractSafe Saml2 SSO'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ContractSafe Saml2 SSO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2021
ms.author: jeedes
ms.openlocfilehash: 7e01f3790f14ae2aaf20da465cd9c66336ea5ce6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280505"
---
# <a name="tutorial-integrate-azure-ad-sso-with-contractsafe-saml2-sso"></a>Tutorial: Integrieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit ContractSafe Saml2 SSO

In diesem Tutorial erfahren Sie, wie Sie ContractSafe Saml2 SSO in Azure Active Directory (Azure AD) integrieren. Die Integration von ContractSafe Saml2 SSO in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf ContractSafe Saml2 SSO hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei ContractSafe Saml2 SSO anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein ContractSafe Saml2 SSO-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ContractSafe Saml2 SSO unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Hinzufügen von ContractSafe Saml2 SSO über den Katalog

Um die Integration von ContractSafe Saml2 SSO in Azure AD konfigurieren zu können, müssen Sie ContractSafe Saml2 SSO aus dem Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ContractSafe Saml2 SSO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ContractSafe Saml2 SSO** aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ContractSafe Saml2 SSO

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ContractSafe Saml2 SSO mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ContractSafe Saml2 SSO eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ContractSafe Saml2 SSO die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
   1. [Erstellen Sie einen Azure AD-Testbenutzer](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit dem Konto **B.Simon** zu testen.
   1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um **B.Simon** die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaligen Anmeldens für ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. [Erstellen eines ContractSafe Saml2 SSO-Testbenutzers](#create-a-contractsafe-saml2-sso-test-user), um in ContractSafe Saml2 SSO eine Entsprechung von **B.Simon** zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ContractSafe Saml2 SSO** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

   a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`.

   b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [Supportteam für den ContractSafe Saml2 SSO-Client](mailto:support@contractsafe.com). Sie können sich auch die Formate im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. ContractSafe Saml2 SSO erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![Allgemeine Standardattribute](common/default-attributes.png)

1. Neben diesen Attributen werden von ContractSafe Saml2 SSO noch einige weitere Attribute in der SAML-Antwort erwartet. Diese Attribute werden vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen. Die folgende Liste enthält die zusätzlichen Attribute:

    | Name | Quellattribut|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | email | user.onpremisesuserprincipalname |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**. Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es dann auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **ContractSafe Saml2 SSO einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen **B. Simon**.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory** aus. Wählen Sie **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** eine E-Mail-Adresse in folgendem Format ein: `username@companydomain.extension`. z. B. `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie **B.Simon** die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ContractSafe Saml2 SSO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **ContractSafe Saml2 SSO** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** die Option **B.Simon** aus. Wählen Sie anschließend im unteren Bildschirmbereich die Schaltfläche **Auswählen** aus.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Schaltfläche **Zuweisen**.

## <a name="configure-contractsafe-saml2-sso"></a>Konfigurieren des einmaligen Anmeldens für ContractSafe Saml2 SSO

Um einmaliges Anmelden aufseiten von **ContractSafe Saml2 SSO** zu konfigurieren, müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [ContractSafe Saml2 SSO-Supportteam](mailto:support@contractsafe.com) senden. Das Team sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-a-contractsafe-saml2-sso-test-user"></a>Erstellen eines ContractSafe Saml2 SSO-Testbenutzers

Erstellen Sie in ContractSafe Saml2 SSO einen Benutzer namens B.Simon. Arbeiten Sie mit dem[ContractSafe Saml2 SSO-Supportteam](mailto:support@contractsafe.com) zusammen, um der ContractSafe Saml2 SSO-Plattform Benutzer hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der ContractSafe Saml2 SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „ContractSafe Saml2 SSO“ klicken, sollten Sie automatisch bei der ContractSafe Saml2 SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von ContractSafe Saml2 SSO können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
