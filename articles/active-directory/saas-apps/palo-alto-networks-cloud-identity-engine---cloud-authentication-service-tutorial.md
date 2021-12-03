---
title: 'Tutorial: Integration des einmaligen Anmeldens (SSO) von Azure Active Directory mit Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen dem Azure Active Directory und dem Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/14/2021
ms.author: jeedes
ms.openlocfilehash: 79ebe80a0171edb020e3dad9f62106ed25ef1618
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307045"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks-cloud-identity-engine---cloud-authentication-service"></a>Tutorial: Integration des einmaligen Anmeldens (SSO) von dem Azure Active Directory mit dem Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst

In diesem Tutorial erfahren Sie, wie Sie den Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst in Azure Active Directory (Azure AD) integrieren. Die Integration von Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst in Azure AD ermöglicht Ihnen:

* In Azure AD zu steuern, wer Zugriff auf den Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst hat.
* Den Benutzern zu erlauben, sich mit ihren Azure AD-Konten automatisch bei Palo Alto Networks – Cloud Authentifizierungsdienst.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Der Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst unterstützt **SP** initiiertes SSO.

* Der Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst unterstützt **Just-in-Time** Benutzerbereitstellung.

## <a name="add-palo-alto-networks-cloud-identity-engine---cloud-authentication-service-from-the-gallery"></a>Eine Anleitung zu Hinzufügen von Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst vom Katalog

Um die Integration von Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst mit Azure AD zu konfigurieren, müssen Sie den Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst von dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie in dem Bereich **Hinzufügen aus dem Katalog** in das Suchfeld **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** ein.
1. Wählen Sie **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** aus dem Ergebnisbereich aus und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks-cloud-identity-engine---cloud-authentication-service"></a>Konfigurieren und Testen Sie das einmalige Anmelden des Azure AD für Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst

Konfigurieren und Testen Sie das einmalige Anmelden von Azure AD für Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst mithilfe des Testbenutzers mit dem Namen **B. Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst eingerichtet werden.

Konfigurieren und Testen Sie das einmalige Anmelden von Azure AD für Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst, indem Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst](#configure-palo-alto-networks-cloud-identity-engine---cloud-authentication-service-sso)** , um die Einstellungen für das einmalige Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst-Testbenutzer](#create-palo-alto-networks-cloud-identity-engine---cloud-authentication-service-test-user)** , um eine Entsprechung von B. Simon in Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst zu erhalten, die mit der Azure AD Darstellung des Benutzers verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** zum Abschnitt **Verwalten** und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei wird der Wert **Bezeichner** automatisch im Abschnitt „Grundlegende SAML-Konfiguration“ eingefügt.

    d. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<RegionUrl>.paloaltonetworks.com/sp/acs`

    > [!Note]
    > Falls der Wert **Bezeichner** nicht automatisch aufgefüllt wird, geben Sie den erforderlichen Wert entsprechend Ihren Anforderungen manuell ein. Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Nehmen Sie Kontakt zu dem [Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst](mailto:support@paloaltonetworks.com) auf, um diesen Wert zu bekommen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute die benutzerdefinierten Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst-Anwendung, dass in der SAML-Antwort einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name | Quellattribut|
    | ---------------| --------- |
    | Group |  user.groups |
    | username | user.userprincipalname |

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie den Zugriff auf den Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie den **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** von der Anwendungsliste aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-palo-alto-networks-cloud-identity-engine---cloud-authentication-service-sso"></a>Eine Anleitung zum Konfigurieren des einmaligen Anmeldens mit dem Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst

1. Melden Sie sich bei Ihrer Unternehmenswebsite bei dem Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst als Administrator an.

1. Navigieren Sie unter **Authentifizierung** > zu **Identitätsanbieter** und klicken Sie auf **Identitätsanbieter hinzufügen**.

    ![Konto](./media/palo-alto-networks-cloud-identity-engine---cloud-authentication-service-tutorial/add-service.png "Konto") 

1. Führen Sie auf der Seite **SAML-Authentifizierung einrichten** die folgenden Schritte aus.

    ![Authentifizierung](./media/palo-alto-networks-cloud-identity-engine---cloud-authentication-service-tutorial/profile.png "Authentifizierung") 

    a. Als ersten Schritt wählen Sie **Herunterladen der SP Metadaten** aus, um die Metadatendatei herunterzuladen und auf Ihrem Computer zu speichern.

    b. Füllen Sie in Schritt 2 die erforderlichen Felder aus, um Ihr **Identitätsanbieterprofil zu konfigurieren**, das Sie aus dem Azure-Portal kopiert haben. 

    c. Klicken Sie in Schritt 3 auf **SAML-Einrichtung testen**, um die Profilkonfiguration zu überprüfen, und wählen Sie dann **MFA ist auf dem IDP aktiviert** aus.

    ![Testen von SAML](./media/palo-alto-networks-cloud-identity-engine---cloud-authentication-service-tutorial/test.png "Testen von SAML")

    > [!NOTE]
    > Öffnen Sie zum Testen der SSO unter **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** die Konsole **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst**, klicken Sie dann auf die Schlaffläche **Testverbindung** und authentifizieren Sie sich mit dem Testkonto, das Sie im Abschnitt **Erstellen eines Azure AD-Testbenutzers** erstellt haben. 

    d. Geben Sie in Schritt 4 das **BENUTZERNAME-ATTRIBUT** ein und klicken Sie auf **Senden**. 

    ![SAML-Attribute](./media/palo-alto-networks-cloud-identity-engine---cloud-authentication-service-tutorial/attribute.png "SAML-Attribute")

### <a name="create-palo-alto-networks-cloud-identity-engine---cloud-authentication-service-test-user"></a>Eine Anleitung zum Erstellen eines Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst-Testbenutzers

In diesem Abschnitt wird ein Testbenutzer mit dem Namen Britta Simon in dem **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** erstellt. Der **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** unterstützt Just-in-Time Benutzerbereitstellung. Diese Funktion ist standardmäßig aktiviert. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht schon im **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** vorhanden ist, wird nach der Authentifizierung ein neuer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

Öffnen Sie zum Testen der SSO unter **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** die Konsole **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst**, klicken Sie dann auf die Schlaffläche **Testverbindung** und authentifizieren Sie sich mit dem Testkonto, das Sie im Abschnitt **Erstellen eines Azure AD-Testbenutzers** erstellt haben.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren des **Palo Alto Networks Cloud Identity Engine – Cloud-Authentifizierungsdienst** können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
