---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit EBSCO | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und EBSCO konfigurieren.
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
ms.openlocfilehash: 16757fdab3062ec0fd4b0b685f510e31dabd1824
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348880"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit EBSCO

In diesem Tutorial erfahren Sie, wie Sie EBSCO in Azure Active Directory (Azure AD) integrieren. Die Integration von EBSCO in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf EBSCO hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei EBSCO anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* EBSCO-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* EBSCO unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* EBSCO unterstützt die **Just-in-Time**-Benutzerbereitstellung.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-ebsco-from-the-gallery"></a>Hinzufügen von EBSCO aus dem Katalog

Zum Konfigurieren der Integration von EBSCO in Azure AD müssen Sie EBSCO aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **EBSCO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **EBSCO** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-ebsco"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für EBSCO

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit EBSCO mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in EBSCO eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit EBSCO die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für EBSCO](#configure-ebsco-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines EBSCO-Testbenutzers](#create-ebsco-test-user)**, um eine Entsprechung von B. Simon in EBSCO zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **EBSCO** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Bezeichner** die URL `pingsso.ebscohost.com` ein.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Kundensupportteam von EBSCO](mailto:support@ebsco.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

    o   **Eindeutige Elemente:**  

    o   **Custid** – Geben Sie die eindeutige EBSCO-Kunden-ID ein. 

    o   **Profile** – Clients können den Link, mit dem Benutzer zu einem bestimmten Profil umgeleitet werden (je nachdem, was sie von EBSCO erwerben), anpassen. Sie können eine bestimmte Profil-ID eingeben. Die Haupt-IDs sind eds (EBSCO Discovery Service) und ehost (EBSOCOhost-Datenbanken). Die zugehörigen Anweisungen finden Sie [hier](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. Ihre EBSCO-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

    > [!Note]
    > Das **name**-Attribut ist obligatorisch. Es wird dem Wert **Benutzerbezeichner** in der EBSCO-Anwendung zugeordnet. Es wird standardmäßig hinzugefügt, sodass Sie es nicht manuell hinzufügen müssen.

1. Darüber hinaus wird von der EBSCO-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    | ---------------| --------------- |
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **EBSCO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf EBSCO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **EBSCO** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-ebsco-sso"></a>Konfigurieren des einmaligen Anmeldens für EBSCO

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **EBSCO** müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von EBSCO](mailto:support@ebsco.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-ebsco-test-user"></a>Erstellen eines EBSCO-Testbenutzers

Bei EBSCO erfolgt die Benutzerbereitstellung automatisch.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

Azure AD übermittelt die erforderlichen Daten an die EBSCO-Anwendung. Die Benutzerbereitstellung von EBSCO kann automatisch oder einmalig erfolgen. Dies hängt davon ab, ob der Client über viele bereits vorhandene EBSCOhost-Konten verfügt, in denen persönliche Einstellungen gespeichert sind. Dies kann während der Implementierung mit dem [EBSCO-Supportteam](mailto:support@ebsco.com) erörtert werden. Der Client muss in keinem der beiden Fälle vor dem Testen EBSCOhost-Konten erstellen.

   > [!Note]
   > Sie können die Bereitstellung und Personalisierung von EBSCO-Hostbenutzern automatisieren. Wenden Sie sich mit Fragen zur Just-In-Time-Benutzerbereitstellung an das [EBSCO-Supportteam](mailto:support@ebsco.com).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe von „Meine Apps“.

1. Wenn Sie in „Meine Apps“ auf die Kachel „EBSCO“ klicken, sollten Sie automatisch bei Ihrer EBSCO-Anwendung angemeldet werden.
Weitere Informationen zu „Meine Apps“ finden Sie unter [Einführung in „Meine Apps“](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

1. Klicken Sie nach der Anmeldung bei der Anwendung in der rechten oberen Ecke auf die Schaltfläche **Anmelden**.

    ![EBSCO-Anmeldung in der Anwendungsliste](./media/ebsco-tutorial/application.png)

1. Sie erhalten einmalig eine Aufforderung zum Koppeln der institutionellen/SAML-Anmeldung, die entweder **Link your existing MyEBSCOhost account to your institution account now** (Verknüpfen Sie Ihr vorhandenes MyEBSCOhost-Konto jetzt mit Ihrem Unternehmenskonto) ODER **Create a new MyEBSCOhost account and link it to your institution account** (Erstellen Sie ein neues MyEBSCOhost-Konto, und verknüpfen Sie es mit Ihrem Unternehmenskonto) lautet. Das Konto wird für die Personalisierung in der EBSCOhost-Anwendung verwendet. Wählen Sie die Option **Create a new account** (Neues Konto erstellen) aus. Daraufhin wird das Formular für die Personalisierung wie im folgenden Screenshot gezeigt vorab mit den Werten aus der SAML-Antwort aufgefüllt. Klicken Sie auf **Weiter**, um diese Auswahl zu speichern.
    
     ![Der EBSCO-Benutzer in der Anwendungsliste](./media/ebsco-tutorial/user.png)

1. Löschen Sie nach Abschluss der oben beschriebenen Einrichtung Ihre Cookies und den Cache, und melden Sie sich erneut an. Sie müssen sich nicht erneut manuell anmelden, und die Personalisierungseinstellungen werden gespeichert.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von EBSCO können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
