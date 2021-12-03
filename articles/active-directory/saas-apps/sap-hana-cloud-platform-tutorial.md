---
title: 'Tutorial: Azure AD-SSO-Integration in SAP Cloud Platform'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Cloud Platform konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 4d7628b1431cebed1136aa2cb1b09d92e7fbb30b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320791"
---
# <a name="tutorial-azure-ad-sso-integration-with-sap-cloud-platform"></a>Tutorial: Azure AD-SSO-Integration in SAP Cloud Platform

In diesem Tutorial erfahren Sie, wie Sie SAP Cloud Platform in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP Cloud Platform in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP Cloud Platform hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAP Cloud Platform anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SAP Cloud Platform-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

>[!IMPORTANT]
>Sie müssen eine eigene Anwendung bereitstellen oder eine Anwendung unter Ihrem SAP Cloud Platform-Konto abonnieren, um das einmalige Anmelden testen zu können. In diesem Tutorial wird eine Anwendung auf dem Konto bereitgestellt.
> 

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Cloud Platform unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-sap-cloud-platform-from-the-gallery"></a>Hinzufügen von SAP Cloud Platform aus dem Katalog

Zum Konfigurieren der Integration von SAP Cloud Platform in Azure AD müssen Sie SAP Cloud Platform aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP Cloud Platform** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAP Cloud Platform** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAP Cloud Platform

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP Cloud Platform mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Cloud Platform eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SAP Cloud Platform die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für SAP Cloud Platform](#configure-sap-cloud-platform-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SAP Cloud Platform-Testbenutzers](#create-sap-cloud-platform-test-user)** , um ein Pendant von Britta Simon in SAP Cloud Platform zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP Cloud Platform** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Bezeichner** die URL von SAP Cloud Platform in einem der folgenden Formate an: 

    | **Identifier** |
    |-----------|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:

    | **Antwort-URL** |
    |----------|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    c. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die von den Benutzern zur Anmeldung bei der **SAP Cloud Platform**-Anwendung verwendet wird. Dies ist die kontospezifische URL einer geschützten Ressource in der SAP Cloud Platform-Anwendung. Die URL basiert auf dem folgenden Muster: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Dies ist die URL in Ihrer SAP Cloud Platform-Anwendung, die die Authentifizierung des Benutzers erfordert.
    > 

    | **Anmelde-URL** |
    |------|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und Anmelde-URL aktualisieren. Anmelde-URL und Bezeichner erhalten Sie vom [Supportteam für den SAP Cloud Plattform-Client](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html). Die Antwort-URL finden Sie im Abschnitt für die Verwaltung der Vertrauensstellung. Dies wird weiter unten im Tutorial erläutert.
    > 
4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

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

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf SAP Cloud Platform gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **SAP Cloud Platform** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sap-cloud-platform-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP Cloud Platform

1. Melden Sie sich in einem anderen Webbrowserfenster unter `https://account.<landscape host>.ondemand.com/cockpit` (Beispiel: https://account.hanatrial.ondemand.com/cockpit) ) beim SAP Cloud Platform-Cockpit an.

2. Klicken Sie auf die Registerkarte **Vertrauen** .
   
    ![Trust (Vertrauen)](./media/sap-hana-cloud-platform-tutorial/account.png "Vertrauen")

3. Führen Sie im Abschnitt für die Verwaltung der Vertrauensstellung unter **Local Service Provider** (Lokaler Dienstanbieter) die folgenden Schritte aus:

    ![Screenshot: Abschnitt für die Verwaltung der Vertrauensstellung, in dem die Registerkarte „Local Service Provider“ (Lokaler Dienstanbieter) ausgewählt ist und alle Textfelder hervorgehoben sind](./media/sap-hana-cloud-platform-tutorial/service.png "Verwaltung von Vertrauensstellungen")
   
    a. Klicken Sie auf **Bearbeiten**.

    b. Wählen Sie als **Konfigurationstyp** die Option **Benutzerdefiniert**.

    c. Belassen Sie als **Name des lokalen Dienstanbieters** den Standardwert. Kopieren Sie diesen Wert, und fügen Sie ihn in der Azure AD-Konfiguration für SAP Cloud Plattform in das Feld **Bezeichner** ein.

    d. Klicken Sie zum Generieren eines **Signaturschlüssels** und eines **Signaturzertifikat**-Schlüsselpaars auf **Schlüsselpaar generieren**.

    e. Wählen Sie als **Prinzipalweitergabe** die Option **Deaktiviert**.

    f. Wählen Sie unter **Zwangsauthentifizierung** die Option **Deaktiviert**.

    g. Klicken Sie auf **Speichern**.

4. Führen Sie nach dem Speichern der Einstellungen für **Local Service Provider** (Lokaler Dienstanbieter) die folgenden Schritte aus, um die Antwort-URL zu erhalten:
   
    ![Abrufen von Metadaten](./media/sap-hana-cloud-platform-tutorial/certificate.png "Metadaten abrufen")

    a. Klicken Sie zum Herunterladen der SAP Cloud Platform-Metadatendatei auf **Metadaten abrufen**.

    b. Öffnen Sie die heruntergeladene SAP Cloud Platform-Metadatendatei (XML-Datei), und navigieren Sie zum Tag **Ns3:AssertionConsumerService**.
 
    c. Kopieren Sie den Wert des Attributs **Speicherort**, und fügen Sie ihn in der Azure AD-Konfiguration für SAP Cloud Plattform in das Textfeld **Antwort-URL** ein.

5. Klicken Sie auf die Registerkarte **Vertrauenswürdiger Identitätsanbieter**, und klicken Sie dann auf **Vertrauenswürdigen Identitätsanbieter hinzufügen**.
   
    ![Screenshot: Seite für die Verwaltung der Vertrauensstellung, auf der die Registerkarte „Trusted Identity Provider“ (Vertrauenswürdiger Identitätsanbieter) ausgewählt ist](./media/sap-hana-cloud-platform-tutorial/add-service.png "Verwaltung von Vertrauensstellungen")
   
    >[!NOTE]
    >Um die Liste der vertrauenswürdigen Identitätsanbieter verwalten zu können, müssen Sie den Konfigurationstyp „Benutzerdefinierte“ im Abschnitt „Lokale Dienstanbieter“ ausgewählt haben. Beim Standard-Konfigurationstyp haben Sie ein nicht-bearbeitbares und implizites Vertrauen gegenüber dem SAP ID-Dienst. Bei der Option „Keine“ haben Sie keine Vertrauenseinstellungen.
    > 
    > 

6. Klicken Sie auf die Registerkarte **Allgemein** und dann auf **Durchsuchen**, um die heruntergeladene Metadatendatei hochzuladen.
    
    ![Verwaltung von Vertrauensstellungen](./media/sap-hana-cloud-platform-tutorial/general.png "Verwaltung von Vertrauensstellungen")
    
    >[!NOTE]
    >Nach dem Hochladen der Metadatendatei werden die Werte für **URL für einmaliges Anmelden**, **URL für einmaliges Abmelden** und **Signaturzertifikat** automatisch ausgefüllt.
    > 
     
7. Klicken Sie auf die Registerkarte **Attribute** .

8. Führen Sie auf der Registerkarte **Attribute** die folgenden Schritte aus:
    
    ![Attribute](./media/sap-hana-cloud-platform-tutorial/principal-attribute.png "Attributes") 

    a. Klicken Sie auf **Assertion-Attribut hinzufügen**, und für Sie dann die folgenden Assertion-basierten Attribute hinzu:
       
    | Assertion-Attribut | Prinzipal-Attribut |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
    >[!NOTE]
    >Die Konfiguration der Attribute hängt davon ab, wie die Anwendung(en) für SPC entwickelt wurden – also welche Attribute sie in der SAML-Antwort erwarten und unter welchem Namen (Prinzipal-Attribut) sie auf dieses Attribut im Code zugreifen.
    > 
    
    b. Das **Standard-Attribut** im Screenshot dient nur der Veranschaulichung. Es ist nicht erforderlich, damit das Szenario funktioniert.  
 
    c. Die Namen und Werte für das im Screenshot gezeigte **Prinzipal Attribut** ist abhängig davon, wie die Anwendung entwickelt wird. Es ist möglich, dass die Anwendung andere Zuordnungen erfordert.

### <a name="assertion-based-groups"></a>Assertion-Gruppen

Als optionalen Schritt können Sie Assertion-Gruppen für Ihren Azure Active Directory-Identitätsanbieter konfigurieren.

Mithilfe von Gruppen für SAP Cloud Platform können Sie einzelne oder mehrere Benutzer dynamisch einzelnen oder mehreren Rollen in Ihren SAP Cloud Platform-Anwendungen zuweisen – definiert durch Werte von Attributen in der SAML 2.0-Assertion. 

Beispiel: Wenn die Assertion das Attribut „*contract=temporary*“ enthält, kann es sein, dass Sie alle betroffenen Benutzer der Gruppe „*TEMPORÄR*“ hinzufügen möchten. Die Gruppe „*TEMPORÄR*“ enthält unter Umständen Rollen aus mindestens einer Anwendung, die unter Ihrem SAP Cloud Platform-Konto bereitgestellt wurde.
 
Verwenden Sie Assertion-basierte Gruppen, wenn Sie Anwendungsrollen in Ihrem SAP Cloud Platform-Konto gleichzeitig viele Benutzer zuweisen möchten. Wenn Sie nur einen einzelnen Benutzer oder eine geringe Anzahl von Benutzern bestimmten Rollen zuweisen möchten, empfiehlt es sich, diese direkt im SAP Cloud Platform-Cockpit auf der Registerkarte **Autorisierungen** zuzuweisen.

### <a name="create-sap-cloud-platform-test-user"></a>Erstellen eines SAP Cloud Platform-Testbenutzers

Um Azure AD-Benutzern die Anmeldung bei SAP Cloud Platform zu ermöglichen, müssen Sie ihnen Rollen in SAP Cloud Platform zuweisen.

**Führen Sie die folgenden Schritte aus, um einem Benutzer eine Rolle zuzuweisen:**

1. Melden Sie sich bei Ihrem **SAP Cloud Platform**-Cockpit an.

2. Führen Sie Folgendes aus:
   
    ![Autorisierungen](./media/sap-hana-cloud-platform-tutorial/roles.png "Authorizations")
   
    a. Klicken Sie auf **Autorisierung**.

    b. Klicken Sie auf die Registerkarte **Benutzer** .

    c. Geben Sie im Textfeld **Benutzer** die E-Mail-Adresse des Benutzers ein.

    d. Klicken Sie auf **Zuweisen** , um den Benutzer einer Rolle zuzuweisen.

    e. Klicken Sie auf **Speichern**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SAP Cloud Platform weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die SAP Cloud Platform-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „SAP Cloud Platform“ klicken, sollten Sie automatisch bei der SAP Cloud Platform-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SAP Cloud Platform können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
