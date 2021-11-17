---
title: 'Tutorial: Azure Active Directory-Integration mit LoginRadius | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie das einmalige Anmelden zwischen Azure Active Directory und LoginRadius konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: 1774e5f7099d130fe1692911fbcf1f7794fa7e20
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285113"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Tutorial: Azure Active Directory-Integration mit LoginRadius

In diesem Tutorial erfahren Sie, wie Sie LoginRadius in Azure Active Directory (Azure AD) integrieren. Die Integration von LoginRadius in Azure AD ermöglicht Folgendes:

* In Azure AD steuern, wer Zugriff auf LoginRadius hat.
* Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei LoginRadius anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit LoginRadius konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein LoginRadius-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* LoginRadius unterstützt **SP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-loginradius-from-the-gallery"></a>Hinzufügen von LoginRadius aus dem Katalog

Zum Konfigurieren der Integration von LoginRadius in Azure AD müssen Sie LoginRadius aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** in das Suchfeld **LoginRadius** ein.
1. Wählen Sie im Ergebnisbereich **LoginRadius** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-loginradius"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für LoginRadius

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit LoginRadius mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in LoginRadius eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit LoginRadius die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für LoginRadius](#configure-loginradius-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines LoginRadius-Testbenutzers](#create-loginradius-test-user)** , um eine Entsprechung von B. Simon in LoginRadius zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **LoginRadius** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

   1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die URL `https://lr.hub.loginradius.com/` ein.

   1. Geben Sie in das Textfeld **Antwort-URL (Assertionsverbraucherdienst-URL)** die ACS-URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` von LoginRadius ein. 

   1. Geben Sie in das Textfeld **Anmelde-URL** die URL `https://secure.loginradius.com/login` ein.

5. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **LoginRadius einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf LoginRadius gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **LoginRadius** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-loginradius-sso"></a>Konfigurieren des einmaligen Anmeldens für LoginRadius

In diesem Abschnitt aktivieren Sie das einmalige Anmelden für die LoginRadius-Verwaltungskonsole.

1. Melden Sie sich bei Ihrem Konto der LoginRadius-[Verwaltungskonsole](https://adminconsole.loginradius.com/login) an.

2. Wechseln Sie in der [LoginRadius-Verwaltungskonsole](https://secure.loginradius.com/account/team) zum Abschnitt **Teamverwaltung**.

3. Wählen Sie die Registerkarte **Einmaliges Anmelden** und dann **Azure AD** aus:

   ![Screenshot, der das Menü für einmaliges Anmelden in der LoginRadius-Teamverwaltungskonsole zeigt](./media/loginradius-tutorial/azure-ad.png)

4. Führen Sie auf der Azure AD-Setupseite die folgenden Schritte aus:

   ![Screenshot, der die Azure Active Directory-Konfiguration in der LoginRadius-Teamverwaltungskonsole zeigt](./media/loginradius-tutorial/single-sign-on.png)

    1. Geben Sie als **Speicherort des ID-Anbieters** den ANMELDUNGSENDPUNKT ein, den Sie von Ihrem Azure AD-Konto erhalten.

    1. Geben Sie als **Abmelde-URL des ID-Anbieters** den ABMELDUNGSENDPUNKT ein, den Sie von Ihrem Azure AD-Konto erhalten.
 
    1. Geben Sie als **Zertifikat des ID-Anbieters** das Azure AD-Zertifikat ein, das Sie von Ihrem Azure AD-Konto erhalten. Geben Sie den Zertifikatwert mit Kopf- und Fußzeile ein. Beispiel: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. Geben Sie als **Zertifikat des Dienstanbieters** und **Zertifikatschlüssel des Dienstanbieters** Ihr Zertifikat und Ihren Schlüssel ein. 

       Sie können ein selbstsigniertes Zertifikat erstellen, indem Sie die folgenden Befehle über die Befehlszeile (Linux/Mac) ausführen:

       - Befehl zum Abrufen des Zertifikatschlüssels für SP: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Befehl zum Abrufen des Zertifikats für SP: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Achten Sie darauf, die Zertifikat- und Zertifikatschlüsselwerte mit der Kopf- und Fußzeile einzugeben:
       > - Beispielformat für Zertifikatwert: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Beispielformat für Zertifikatschlüsselwert: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. Wählen Sie im Abschnitt **Datenzuordnung** die Felder (SP-Felder) aus, und geben Sie die entsprechenden Azure AD-Felder (IdP-Felder) ein.

    Im folgenden sind einige Feldnamen für Azure AD aufgeführt.

    | Felder    | Profilschlüssel                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > Die Feldzuordnung **Email** ist erforderlich. Die Feldzuordnungen **FirstName-** und **LastName** sind optional.

### <a name="create-loginradius-test-user"></a>Erstellen eines LoginRadius-Testbenutzers

1. Melden Sie sich bei Ihrem Konto der LoginRadius-[Verwaltungskonsole](https://adminconsole.loginradius.com/login) an.

2. Wechseln Sie in der LoginRadius-Verwaltungskonsole zum Abschnitt „Teamverwaltung“.

   ![Screenshot, der die LoginRadius-Verwaltungskonsole zeigt](./media/loginradius-tutorial/team-management.png)

3. Wählen Sie im seitlichen Menü die Option **Teammitglied hinzufügen** aus, um das Formular zu öffnen. 

4. Auf dem Formular **Teammitglied hinzufügen** erstellen Sie in Ihrer LoginRadius-Site eine Benutzerin namens Britta Simon, indem Sie die Details der Benutzerin angeben und die Berechtigungen zuweisen, die sie haben soll. Weitere Informationen zu den Berechtigungen, die auf Rollen basieren, finden Sie im Abschnitt zu [Rollenzugriffsberechtigungen](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) im Dokument zur [Verwaltung von Teammitgliedern](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) von LoginRadius. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe von MyApps.

1. Rufen Sie in einem Browser https://accounts.loginradius.com/auth.aspx auf, und wählen Sie **Fed SSO log in** (SSO-Verbundanmeldung) aus.
2. Geben Sie den Namen Ihrer LoginRadius-App ein, und wählen Sie dann **Anmelden** aus.
3. Daraufhin sollte ein Popupelement geöffnet werden, in dem Sie aufgefordert werden, sich bei Ihrem Azure AD-Konto anzumelden.
4. Nach der Authentifizierung wird das Popupelement geschlossen, und Sie werden bei der LoginRadius-Verwaltungskonsole angemeldet.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von LoginRadius können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Ex- und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
