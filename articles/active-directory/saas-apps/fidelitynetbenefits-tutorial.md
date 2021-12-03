---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Fidelity NetBenefits'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Fidelity NetBenefits konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: jeedes
ms.openlocfilehash: 304bc6d4d3100478ba4fa542c76cd1d2e4ef4aca
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285615"
---
# <a name="tutorial-azure-ad-sso-integration-with-fidelity-netbenefits"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Fidelity NetBenefits

In diesem Tutorial erfahren Sie, wie Sie Fidelity NetBenefits in Azure Active Directory (Azure AD) integrieren. Die Integration von Fidelity NetBenefits in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Fidelity NetBenefits hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Fidelity NetBenefits anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Fidelity NetBenefits-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Fidelity NetBenefits unterstützt **IDP**-initiiertes einmaliges Anmelden.

* Fidelity NetBenefits unterstützt die **Just-In-Time**-Benutzerbereitstellung.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-fidelity-netbenefits-from-the-gallery"></a>Hinzufügen von Fidelity NetBenefits aus dem Katalog

Zum Konfigurieren der Integration von Fidelity NetBenefits in Azure AD müssen Sie Fidelity NetBenefits aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Fidelity NetBenefits** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Fidelity NetBenefits** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-fidelity-netbenefits"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Fidelity NetBenefits

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Fidelity NetBenefits mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Fidelity NetBenefits eingerichtet werden.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Fidelity NetBenefits die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Fidelity NetBenefits](#configure-fidelity-netbenefits-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Fidelity NetBenefits-Testbenutzers](#create-fidelity-netbenefits-test-user)** , um eine Entsprechung von B. Simon in Fidelity NetBenefits zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Fidelity NetBenefits** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** einen der folgenden Werte ein:

    Für eine Testumgebung: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Für eine Produktionsumgebung: `urn:sp:fidelity:geninbndnbparts20`

    b. Geben Sie im Textfeld **Antwort-URL** die URL ein, die bei der Implementierung von Fidelity bereitgestellt wird, oder wenden Sie sich an Ihren zuständigen Fidelity-Kundenservicemanager.

5. Die Fidelity NetBenefits-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die Fidelity NetBenefits-Anwendung erwartet, dass **nameidentifier** dem Eintrag **employeeid** oder einem anderen Anspruch zugeordnet wird, der als **nameidentifier** für Ihre Organisation fungiert. Bearbeiten Sie daher die Attributzuordnung, indem Sie auf das Symbol **Bearbeiten** klicken und die Attributzuordnung ändern.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelity NetBenefits unterstützt den statischen und dynamischen Verbund. Statisch bedeutet, dass SAML nicht basierend auf der Just-In-Time-Benutzerbereitstellung verwendet wird, und dynamisch bedeutet, dass die Just-In-Time-Benutzerbereitstellung unterstützt wird. Für die Verwendung der JIT-basierten Bereitstellung müssen Kunden einige weitere Ansprüche in Azure AD hinzufügen, z.B. das Geburtsdatum des Benutzers usw. Diese Details werden von Ihrem zuständigen **Fidelity-Kundenservicemanager** bereitgestellt, der auch diesen dynamischen Verbund für Ihre Instanz aktivieren muss.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Fidelity NetBenefits einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Fidelity NetBenefits gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Fidelity NetBenefits** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-fidelity-netbenefits-sso"></a>Konfigurieren des einmaligen Anmeldens für Fidelity NetBenefits

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Fidelity NetBenefits** müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-fidelity-netbenefits-test-user"></a>Erstellen eines Fidelity NetBenefits-Testbenutzers

In diesem Abschnitt erstellen Sie in Fidelity NetBenefits einen Benutzer namens Britta Simon. Wenn Sie einen statischen Verbund erstellen, wenden Sie sich an Ihren zuständigen **Fidelity-Kundenservicemanager**, um Benutzer auf der Fidelity NetBenefits-Plattform zu erstellen. Diese Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

Beim dynamischen Verbund werden die Benutzer während der Just-In-Time-Benutzerbereitstellung erstellt. Für die Verwendung der JIT-basierten Bereitstellung müssen Kunden einige weitere Ansprüche in Azure AD hinzufügen, z.B. das Geburtsdatum des Benutzers usw. Diese Details werden von Ihrem zuständigen **Fidelity-Kundenservicemanager** bereitgestellt, der auch diesen dynamischen Verbund für Ihre Instanz aktivieren muss.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Fidelity NetBenefits-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Fidelity NetBenefits“ klicken, sollten Sie automatisch bei der Fidelity NetBenefits-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Fidelity NetBenefits können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
