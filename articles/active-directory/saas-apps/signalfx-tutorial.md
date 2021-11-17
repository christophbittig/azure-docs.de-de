---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SignalFx | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SignalFx konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: f97f8710df160608fb01e0b9d708c4a9d1b2373d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304646"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SignalFx

In diesem Tutorial erfahren Sie, wie Sie SignalFx in Azure Active Directory (Azure AD) integrieren. Die Integration von SignalFx in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf SignalFx besitzen soll.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SignalFx anzumelden.
* Sie können Ihre Konten zentral über das Azure-Portal verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SignalFx-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SignalFx unterstützt **IDP**-initiiertes einmaliges Anmelden.
* SignalFx unterstützt **Just-in-Time**-Benutzerbereitstellung.

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Schritt 1: Hinzufügen der SignalFx-Anwendung in Azure

Gehen Sie wie folgt vor, um die SignalFx-Anwendung Ihrer Liste mit den verwalteten SaaS-Apps hinzuzufügen:

1. Melden Sie sich im Azure-Portal an.
1. Wählen Sie im Navigationsfenster auf der linken Seite die Option **Azure Active Directory** aus.
1. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen** aus.
1. Wählen Sie **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SignalFx** in das Suchfeld ein, und wählen Sie die entsprechende Option aus.
     * Es kann unter Umständen einige Minuten dauern, bis die Anwendung Ihrem Mandanten hinzugefügt wird.
1. Lassen Sie das Azure-Portal geöffnet, und öffnen Sie einen neuen Webtab.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Schritt 2: Konfigurieren des einmaligen Anmeldens für SignalFx

Gehen Sie wie folgt vor, um mit dem Konfigurationsprozess für das einmalige Anmelden für SignalFx zu beginnen:

1. Greifen Sie in dem neuen Tab auf die SignalFx-Benutzeroberfläche zu, und melden Sie sich an. 
1. Klicken Sie im oberen Menü auf **Integrations** (Integrationen). 
1. Geben Sie **Azure Active Directory** in das Suchfeld ein, und wählen Sie die entsprechende Option aus.
1. Klicken Sie auf **Create New Integration** (Neue Integration erstellen).
1. Geben Sie unter **Name** einen aussagekräftigen, für Ihre Benutzer nachvollziehbaren Namen ein.
1. Aktivieren Sie **Show on login page** (Auf der Anmeldeseite anzeigen).
    * Dadurch wird auf der Anmeldeseite eine angepasste Schaltfläche angezeigt, auf die Ihre Benutzer klicken können. 
    * Die Schaltfläche wird mit dem Text angezeigt, den Sie unter **Name** eingegeben haben. Geben Sie daher unter **Name** einen aussagekräftigen Namen ein. 
    * Diese Option funktioniert nur, wenn für die SignalFx-Anwendung eine benutzerdefinierte Unterdomäne wie etwa **<Name Ihres Unternehmens>.signalfx.com** verwendet wird. Wenden Sie sich an den SignalFx-Support, um eine benutzerdefinierte Unterdomäne zu erhalten. 
1. Kopieren Sie die Integrations-ID (**Integration ID**). Diese Information wird in einem späteren Schritt benötigt. 
1. Lassen Sie die SignalFx-Benutzeroberfläche geöffnet. 

## <a name="step-3-configure-azure-ad-sso"></a>Schritt 3: Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Kehren Sie zum Azure-Portal zurück, navigieren Sie auf der Anwendungsintegrationsseite für **SignalFx** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus: 

    a. Geben Sie unter **Bezeichner** die URL `https://api.<realm>.signalfx.com/v1/saml/metadata` ein, und ersetzen Sie dabei `<realm>` durch Ihren SignalFx-Bereich. 

    b. Geben Sie unter **Antwort-URL** die URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` ein. Ersetzen Sie dabei `<realm>` durch Ihren SignalFx-Bereich und `<integration ID>` durch die Integrations-ID (**Integration ID**), die Sie zuvor auf der SignalFx-Benutzeroberfläche kopiert haben.

1. Die SignalFx-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. 
    
1. Vergewissern Sie sich, dass die folgenden Ansprüche zu den in Active Directory aufgefüllten Quellattributen passen: 

    | Name |  Quellattribut|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Hierzu muss Active Directory mit mindestens einer überprüften benutzerdefinierten Domäne konfiguriert sein und auf die E-Mail-Konten in dieser Domäne zugreifen können. Sollten Sie unsicher sein oder Unterstützung bei dieser Konfiguration benötigen, wenden Sie sich an den SignalFx-Support.  

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Zertifikat (Base64)** , und wählen Sie **Herunterladen** aus. Laden Sie das Zertifikat herunter, und speichern Sie es auf Ihrem Computer. Kopieren Sie dann den Wert unter **App-Verbundmetadaten-URL**. Er wird später auf der SignalFx-Benutzeroberfläche benötigt. 

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **SignalFx einrichten** den Wert unter **Azure AD-Bezeichner**. Er wird später auf der SignalFx-Benutzeroberfläche benötigt. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Schritt 4: Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Schritt 5: Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SignalFx gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **SignalFx** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Schritt 6: Abschließen der Konfiguration des einmaligen Anmeldens für SignalFx 

1. Öffnen Sie den vorherigen Tab, und kehren Sie zur SignalFx-Benutzeroberfläche zurück, um die aktuelle Azure Active Directory-Integrationsseite anzuzeigen. 
1. Klicken Sie neben **Certificate (Base64)** (Zertifikat (Base64)) auf **Upload File** (Datei hochladen), und navigieren Sie zu der **Base64-codierten Zertifikatdatei**, die Sie zuvor aus Azure-Portal heruntergeladen haben.
1. Fügen Sie neben **Azure AD Identifier** (Azure AD-Bezeichner) den Wert von **Azure AD-Bezeichner** ein, den Sie zuvor aus dem Azure-Portal kopiert haben. 
1. Fügen Sie neben **Federation Metadata URL** (Verbundmetadaten-URL) den Wert von **App-Verbundmetadaten-URL** ein, den Sie zuvor aus dem Azure-Portal kopiert haben. 
1. Klicken Sie auf **Speichern**.

## <a name="step-7-test-sso"></a>Schritt 7: Testen des einmaligen Anmeldens

Machen Sie sich mit der folgenden Anleitung zum Testen des einmaligen Anmeldens sowie mit den Erwartungen im Zusammenhang mit der ersten Anmeldung bei SignalFx vertraut. 

### <a name="test-logins"></a>Testen von Anmeldungen

* Zum Testen der Anmeldung sollten Sie ein privates Fenster bzw. ein Inkognito-Fenster verwenden. Sie können sich aber auch beim Azure-Portal abmelden. Andernfalls wird die erfolgreiche Anmeldung des Testbenutzers durch Cookies für den Benutzer, der die Anwendung konfiguriert hat, gestört und verhindert.

* Bei der ersten Anmeldung eines neuen Testbenutzers wird von Azure eine Kennwortänderung erzwungen. Dadurch wird der SSO-Anmeldevorgang nicht abgeschlossen, und der Testbenutzer wird zum Azure-Portal weitergeleitet. In diesem Fall muss der Testbenutzer sein Kennwort ändern, zur Anmeldeseite von SignalFx oder zu „Meine Apps“ navigieren und es noch mal versuchen.
    * Wenn Sie unter „Meine Apps“ auf die Kachel „SignalFx“ klicken, sollten Sie automatisch bei der SignalFx-Instanz angemeldet werden. 
        * Weitere Informationen zu „Meine Apps“ finden in der [Einführung zu „Meine Apps“](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

* Auf die SignalFx-Anwendung kann über „Meine Apps“ oder über eine benutzerdefinierte, der Organisation zugewiesene Anmeldeseite zugegriffen werden. Der Testbenutzer sollte die Integration von einem dieser Orte aus testen.
    * Dabei kann der Testbenutzer die Anmeldeinformationen verwenden, die zuvor in diesem Prozess für **b.simon\@contoso.com** erstellt wurden.

### <a name="first-time-logins"></a>Erstmalige Anmeldungen

* Wenn sich ein Benutzer zum ersten Mal per SAML-SSO bei SignalFx anmeldet, erhält er eine SignalFx-E-Mail mit einem Link. Der Benutzer muss zur Authentifizierung auf den Link klicken. Diese E-Mail-Überprüfung wird nur bei erstmaligen Benutzern durchgeführt. 

* SignalFx unterstützt die **Just-In-Time**-Benutzererstellung. Das bedeutet: Wenn ein Benutzer in SignalFx nicht vorhanden ist, wird das Konto des Benutzers beim ersten Anmeldeversuch erstellt.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SignalFx können Sie eine Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
