---
title: 'Tutorial: Azure Active Directory-Integration mit SAP HANA | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP HANA konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/02/2021
ms.author: jeedes
ms.openlocfilehash: a122101ee6e42e3314386319c0b1cd15024eb3e3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309070"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Azure Active Directory-Integration mit SAP HANA

In diesem Tutorial erfahren Sie, wie Sie SAP HANA in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP HANA in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP HANA hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAP HANA anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in SAP HANA konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SAP HANA-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist
- Eine in einer öffentlichen IaaS-Umgebung, lokal, auf virtuellen Azure-Computern oder in großen SAP-Instanzen in Azure ausgeführte HANA-Instanz
- Die XSA-Webschnittstelle für die Verwaltung sowie HANA Studio, installiert in der HANA-Instanz

> [!NOTE]
> Es empfiehlt sich nicht, die Schritte in diesem Tutorial in einer SAP HANA-Produktionsumgebung zu testen. Testen Sie die Integration zuerst in der Entwicklungs- oder Stagingumgebung der Anwendung, und verwenden Sie erst danach die Produktionsumgebung.

Beachten Sie beim Testen der Schritte in diesem Tutorial die folgenden Empfehlungen:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* SAP HANA-Abonnement, das für das einmalige Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP HANA unterstützt **IDP**-initiiertes einmaliges Anmelden.
* SAP HANA unterstützt die **Just-in-Time**-Benutzerbereitstellung.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.


## <a name="adding-sap-hana-from-the-gallery"></a>Hinzufügen von SAP HANA aus dem Katalog

Zum Konfigurieren der Integration von SAP HANA in Azure AD müssen Sie SAP HANA aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP HANA** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAP HANA** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAP HANA

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP HANA mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP HANA eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SAP HANA die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für SAP HANA](#configure-sap-hana-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SAP HANA-Testbenutzers](#create-sap-hana-test-user)** , um ein Pendant von Britta Simon in SAP HANA zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP HANA** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`.

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Antwort-URL. Die Werte erhalten Sie vom [Supportteam für den SAP HANA-Client](https://cloudplatform.sap.com/contact.html). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die SAP HANA-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot: Abschnitt „User Attributes“ (Benutzerattribute) mit dem Symbol „Bearbeiten“ ausgewählt](common/edit-attribute.png)

6. Führen Sie im Dialogfeld **Benutzerattribute und Ansprüche** im Abschnitt **Benutzerattribute** die folgenden Schritte aus:
 
    a. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Dialogfeld „Benutzerattribute und Ansprüche“, in dem das Symbol „Bearbeiten“ ausgewählt ist](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Wählen Sie in der Liste **Transformation** die Option **ExtractMailPrefix()** aus.

    c. Wählen Sie in der Liste **Parameter 1** die Option **user.mail** aus.

    d. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAP HANA gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **SAP HANA** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sap-hana-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP HANA

1. Zum Konfigurieren des einmaligen Anmeldens auf der Seite von SAP HANA melden Sie sich bei Ihrer **HANA XSA-Webkonsole** an, indem Sie zum entsprechenden HTTPS-Endpunkt navigieren.

    > [!NOTE]
    > In der Standardkonfiguration leitet die URL die Anforderung an einen Anmeldebildschirm weiter. Dafür sind die Anmeldeinformationen eines authentifizierten SAP HANA-Datenbankbenutzers erforderlich. Der Benutzer, der sich anmeldet, benötigt Berechtigungen zum Ausführen von SAML-Verwaltungsaufgaben.

2. Navigieren Sie in der XSA-Webschnittstelle zu **SAML-Identitätsanbieter**. Klicken Sie dort am unteren Rand des Bildschirms auf die Schaltfläche **+** , um den Bereich **Add Identity Provider Info** (Identitätsanbieterinformationen hinzufügen) anzuzeigen. Führen Sie dann die folgenden Schritte aus:

    ![Identitätsanbieter hinzufügen](./media/saphana-tutorial/sap1.png)

    a. Fügen Sie im Fenster **Add Identity Provider Info** (Identitätsanbieterinformationen hinzufügen) den Inhalt der aus dem Azure-Portal heruntergeladenen XML-Metadatendatei in das Feld **Metadata** (Metadaten) ein.

    ![Screenshot: Bereich „Add Identity Provider Info“ (Identitätsanbieterinformationen hinzufügen), in dem „Metadata“ (Metadaten) und „Name“ hervorgehoben sind](./media/saphana-tutorial/sap2.png)

    b. Wenn der Inhalt des XML-Dokuments gültig ist, extrahiert der Analyseprozess die Informationen, die für die Felder **Subject, Entity ID, and Issuer** (Antragsteller, Entitäts-ID, Zertifikataussteller) im Bildschirmbereich **General data** (Allgemeine Daten) erforderlich sind. Darüber hinaus werden die Informationen für die URL-Felder im Bildschirmbereich **Ziel** extrahiert, z.B. **Basis-URL und SingleSignOn-URL (*)** .

    ![Einstellungen für Identitätsanbieter hinzufügen](./media/saphana-tutorial/sap3.png)

    c. Geben Sie im Bildschirmbereich **General Data** (Allgemeine Daten) im Feld **Name** einen Namen für den neuen SAML-SSO-Identitätsanbieter ein.

    > [!NOTE]
    > Der Name des SAML-IDP ist zwingend erforderlich und muss eindeutig sein. Er wird in der Liste der verfügbaren SAML-IDPs aufgeführt, die angezeigt wird, wenn Sie SAML als Authentifizierungsmethode für SAP HANA-XS-Anwendungen auswählen. Dieser Schritt kann beispielsweise im Bildschirmbereich **Authentication** (Authentifizierung) des XS-Tools für die Artefaktverwaltung ausgeführt werden.

3. Wählen Sie **Save** (Speichern), um die Details des SAML-Identitätsanbieters zu speichern und den neuen SAML-IDP zur Liste der bekannten SAML-Identitätsanbieter hinzuzufügen.

    ![Schaltfläche „Speichern“](./media/saphana-tutorial/sap4.png)

4. Filtern Sie in HANA Studio in den Systemeigenschaften der Registerkarte **Konfiguration** die Einstellungen nach **saml**. Ändern Sie dann den Wert für **assertion_timeout** von **10** Sekunden in **120** Sekunden.

    ![assertion_timeout-Einstellung](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>Erstellen eines SAP HANA-Testbenutzers

Um für Azure AD-Benutzer das Anmelden bei SAP HANA zu aktivieren, müssen Sie sie in SAP HANA bereitstellen.
SAP HANA unterstützt die **Just-in-Time-Bereitstellung**, die standardmäßig aktiviert ist.

Wenn Sie manuell einen Benutzer erstellen müssen, führen Sie die folgenden Schritte aus:

>[!NOTE]
>Sie können die vom Benutzer verwendete externe Authentifizierung ändern. Die Benutzer können sich mit einem externen System wie Kerberos authentifizieren. Um detaillierte Informationen zu externen Identitäten zu erhalten, wenden Sie sich an Ihren [Domänenadministrator](https://cloudplatform.sap.com/contact.html).

1. Öffnen Sie [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) als Administrator, und aktivieren Sie den Datenbankbenutzer für SAML-SSO.

    ![Benutzer erstellen](./media/saphana-tutorial/sap5.png)

2. Aktivieren Sie das unsichtbare Kontrollkästchen links neben **SAML**, und klicken Sie dann auf den Link **Configure** (Konfigurieren).

3. Klicken Sie auf **Add** (Hinzufügen), um den SAML-IDP hinzuzufügen.  Wählen Sie den entsprechenden SAML-IDP aus, und klicken Sie dann auf **OK**.

4. Fügen Sie die **externe Identität** (in diesem Fall „BrittaSimon“) hinzu. Klicken Sie anschließend auf **OK**.

   > [!Note]
   > Sie müssen das Feld **Externe Identität** für den Benutzer auffüllen, das mit dem Feld **NameID** im SAML-Token von Azure AD übereinstimmen muss. Das Kontrollkästchen **Beliebig** sollte nicht aktiviert werden, da diese Option erfordert, dass der IDP die „SPProviderID“-Eigenschaft im Feld „NameID“ sendet, was derzeit von Azure AD nicht unterstützt wird. Ausführlichere Informationen finden Sie in [diesem](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/db6db355bb571014b56eb25057daec5f.html) Dokument.

5. Weisen Sie dem Benutzer zu Testzwecken alle **XS**-Rollen zu.

    ![Zuweisen von Rollen](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Sie sollten nur die Berechtigungen zuweisen, die für Ihre Anwendungsfälle erforderlich sind.

6. Speichern Sie den Benutzer.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der SAP HANA-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „SAP HANA“ klicken, sollten Sie automatisch bei der SAP HANA-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SAP HANA können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
