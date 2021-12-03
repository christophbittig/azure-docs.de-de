---
title: 'Tutorial: Azure Active Directory-Integration mit Amazon Business | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Amazon Business konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: 00d40649effd5aec98233e81e937c96f0d23e284
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336485"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Tutorial: Integrieren von Amazon Business in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Amazon Business in Azure Active Directory (Azure AD) integrieren. Die Integration von Amazon Business in Azure AD hat folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Amazon Business haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Amazon Business anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein für einmaliges Anmelden (Single Sign-On, SSO) geeignetes Amazon Business-Abonnement. Navigieren Sie zur Seite [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp), um ein Amazon Business-Konto zu erstellen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einem bereits vorhandenen Amazon Business-Konto.

* Amazon Business unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Amazon Business unterstützt die **Just-In-Time**-Benutzerbereitstellung.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-amazon-business-from-the-gallery"></a>Hinzufügen von Amazon Business über den Katalog

Um die Integration von Amazon Business in Azure AD zu konfigurieren, müssen Sie Amazon Business über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Amazon Business** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **Amazon Business** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-amazon-business"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Amazon Business

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Amazon Business mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Amazon Business eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Amazon Business zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Amazon Business](#configure-amazon-business-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Testbenutzers für Amazon Business](#create-amazon-business-test-user)** , um in Amazon Business ein Pendant von B.Simon zu erhalten, das mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Amazon Business** den Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine der folgenden URLs ein:

       | URL | Region |
       |-|-|
       | `https://www.amazon.com`| Nordamerika |
       | `https://www.amazon.co.jp`| Asien, Osten |
       | `https://www.amazon.de`| Europa |

    1. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:

       | URL | Region |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Nordamerika |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Asien, Osten |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Europa |

       > [!NOTE]
       > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Antwort-URL. Den Wert `<idpid>` finden Sie im SSO-Konfigurationsabschnitt von Amazon Business (wird später in diesem Tutorial erläutert). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Wenn Sie die Anwendung im **Dienstanbieter**-initiierten Modus konfigurieren möchten, müssen Sie die vollständige URL, die in der Amazon Business-Konfiguration bereitgestellt wird der **Anmelde-URL** im Abschnitt **Zusätzliche URLs festlegen** hinzufügen.

1. Der folgende Screenshot zeigt die Liste der Standardattribute. Bearbeiten Sie die Attribute, indem Sie im Abschnitt **Benutzerattribute und Ansprüche** auf das **Stift** symbol klicken.

    ![Screenshot: „Benutzerattribute und -ansprüche“ mit Standardwerten wie „user.givenname“ als Vorname und „user.mail“ als E-Mail-Adresse](media/amazon-business-tutorial/map-attribute.png)

1. Bearbeiten Sie Attribute, und kopieren Sie den **Namespacewert** dieser Attribute in den Editor.

    ![Screenshot von „Benutzerattribute und Ansprüche“ mit Spalten für Anspruchsnamen und -wert](media/amazon-business-tutorial/attribute.png)

1. Darüber hinaus wird von der Amazon Business-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Gruppenansprüche** im Abschnitt **Benutzerattribute und Ansprüche** die folgenden Schritte aus:

    1. Klicken Sie auf den **Stift** neben **Im Anspruch zurückgegebene Gruppen**.

        ![Screenshot von „Benutzerattribute und -ansprüche“ mit ausgewähltem Symbol „Im Anspruch zurückgegebene Gruppen“](./media/amazon-business-tutorial/claim.png)

        ![Screenshot der Gruppenansprüche mit in diesem Verfahren beschriebenen Werten](./media/amazon-business-tutorial/group-claim.png)

    1. Wählen Sie **Alle Gruppen** aus der Optionsfeldliste aus.

    1. Wählen Sie unter **Quellattribut** die Option **Gruppen-ID** aus.

    1. Aktivieren Sie das Kontrollkästchen **Name des Gruppenanspruchs anpassen**, und geben Sie den Gruppennamen gemäß den Anforderungen Ihrer Organisation ein.

    1. Klicken Sie auf **Speichern**.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Metadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Amazon Business einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

> [!NOTE]
> Administratoren müssen die Testbenutzer bei Bedarf in ihrem Mandanten erstellen. Die folgenden Schritte zeigen die Erstellung eines Testbenutzers.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Erstellen einer Azure AD-Sicherheitsgruppe im Azure-Portal

1. Klicken Sie auf **Azure Active Directory > Alle Gruppen**.

    ![Screenshot des Menüs im Azure-Portal, in dem „Azure Active Directory“ und im Bereich „Gruppen“ die Option „Alle Gruppen“ ausgewählt sind](./media/amazon-business-tutorial/all-groups-tab.png)

1. Klicken Sie auf **Neue Gruppe**:

    ![Screenshot der Schaltfläche „Neue Gruppe“](./media/amazon-business-tutorial/new-group-tab.png)

1. Geben Sie **Gruppentyp**, **Gruppenname**, **Gruppenbeschreibung** und **Mitgliedschaftstyp** an. Klicken Sie auf den Pfeil, um Mitglieder auszuwählen, und suchen Sie nach dem Mitglied, das Sie der Gruppe hinzufügen möchten, oder klicken Sie auf das gewünschte Mitglied. Klicken Sie auf **Auswählen**, um die ausgewählten Mitglieder hinzuzufügen, und klicken Sie anschließend auf **Erstellen**.

    ![Screenshot des Bereichs „Gruppe“ mit Optionen zum Auswählen von Mitgliedern und zum Einladen externer Benutzer](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf Amazon Business gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Amazon Business** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

    >[!NOTE]
    > Wenn Sie die Benutzer in Azure AD nicht zuweisen, tritt der folgende Fehler auf:

    ![Screenshot mit einer Fehlermeldung, die besagt, dass Sie nicht angemeldet werden können](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Zuweisen der Azure AD-Sicherheitsgruppe im Azure-Portal

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Amazon Business** aus.
2. Geben Sie in der Anwendungsliste **Amazon Business** ein, und wählen Sie die entsprechende Option aus.
3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.
4. Klicken Sie auf **Benutzer hinzufügen**.
5. Suchen Sie nach der gewünschten Sicherheitsgruppe, und klicken Sie auf die Gruppe, um sie dem Abschnitt „Mitglieder auswählen“ hinzuzufügen. Klicken Sie auf **Auswählen** und anschließend auf **Zuweisen**.

    ![Suchen nach der Sicherheitsgruppe](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Vergewissern Sie sich anhand der Benachrichtigungen auf der Menüleiste, dass die Gruppe erfolgreich der Enterprise-Anwendung im Azure-Portal zugewiesen wurde.

## <a name="configure-amazon-business-sso"></a>Konfigurieren des einmaligen Anmeldens von Amazon Business

1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der Amazon Business-Unternehmenswebsite an.

1. Klicken Sie auf **das Benutzer** Profil, **und wählen** Sie Geschäfts Einstellungen aus.

    ![Benutzerprofil](media/amazon-business-tutorial/user-profile.png)

1. Wählen Sie im Assistentenfür **Systemintegrationen** die Option für einmaliges Anmelden **(Single Sign-On, SSO)** aus.

    ![Einmaliges Anmelden (Single Sign-On, SSO)](media/amazon-business-tutorial/sso-settings.png)

1. Wählen Sie im **Einrichtungs-Assistenten** für einmaliges Anmelden den Anbieter gemäß den Anforderungen Ihrer Organisation aus, und klicken Sie auf **Next** (Weiter).

    ![Screenshot des S S O-Setups, in dem „Microsoft Azure A D“ und „Weiter“ ausgewählt sind](media/amazon-business-tutorial/default-group.png)

    > [!NOTE]
    > Obwohl es sich bei Microsoft ADFS um eine aufgelistete Option handelt, funktioniert es nicht mit Azure AD SSO.

1. Wählen Sie **im** Assistenten für neue Benutzerkonten Standard die **Standardgruppe** aus, und **wählen Sie dann** standardmäßige Kauf Rolle gemäß der Benutzerrolle in Ihrer Organisation aus, und klicken Sie auf **Als** nächstes.

    ![Screenshot der Standardeinstellungen für ein neues Benutzerkonto, in denen Microsoft-S S O, Anforderer und „Weiter“ ausgewählt sind](media/amazon-business-tutorial/group.png)

1. Klicken Sie im Assistentenzum **Hochladen Ihrer Metadatendatei** auf **Browse** (Durchsuchen), um die **Metadaten-XML-Datei** hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben, und klicken Sie anschließend auf **Upload** (Hochladen).

    ![Screenshot des Assistenten zum Hochladen der Metadatendatei, in dem Sie eine X M L-Datei auswählen und hochladen können](media/amazon-business-tutorial/connection-data.png)

1. Nachdem Sie die heruntergeladene Metadatendatei hochgeladen haben ,**werden** die Felder im Abschnitt "Verbindungsdaten" automatisch aufgefüllt. Klicken Sie danach auf **Next** (Weiter).

    ![Screenshot der Verbindungsdaten, in denen Sie einen Azure A D-Bezeichner, eine Anmelde-U R L und ein S A M L-Signaturzertifikat angeben können](media/amazon-business-tutorial/connection.png)

1. Klicken Sie im Assistentenzum **Hochladen Ihrer Attributanweisung** auf **Skip** (Überspringen).

    ![Screenshot von „Hochladen Ihrer Attributanweisung“, wo Sie eine Attributanweisung auswählen können, in diesem Fall jedoch „Überspringen“ auswählen](media/amazon-business-tutorial/upload-attribute.png)

1. Fügen Sie im Assistenten für die **Attributzuordnung** die Anforderungsfelder hinzu, indem Sie auf die Option **+ Add a field** (+ Feld hinzufügen) klicken. Fügen Sie die Attributwerte (einschließlich des Namespace, den Sie aus dem Abschnitt **Benutzerattribute und Ansprüche** des Azure-Portals kopiert haben) dem Feld **SAML AttributeName** (SAML-Attributname) hinzu, und klicken Sie auf **Next** (Weiter).

    ![Screenshot der Attributzuordnung, in der Sie die Namen Ihrer SAML-Attribute für Amazon-Daten bearbeiten können.](media/amazon-business-tutorial/attribute-mapping.png)

1. Klicken Sie im Assistenten für **Amazon-Verbindungsdaten** auf **Next** (Weiter).

    ![Screenshot der Amazon-Verbindungsdaten, wo Sie zum Fortfahren auf „Weiter“ klicken](media/amazon-business-tutorial/amazon-connect.png)

1. Überprüfen Sie **den** Status der Schritte, die konfiguriert wurden, und **klicken Sie** auf Test starten.

    ![Screenshot der S S O-Verbindungsdetails mit der Option zum Starten des Tests](media/amazon-business-tutorial/status.png)

1. Klicken Sie im Assistenten zum **Testen der SSO-Verbindung** auf **Test** (Testen).

    ![Screenshot von „Testen der S S O-Verbindung“ mit der Schaltfläche „Testen“](media/amazon-business-tutorial/test.png)

1. Kopieren Sie im Assistenten für die **IDP-initiierte URL** vor dem Klicken auf **Activate** (Aktivieren) den Wert, der **idpid** zugewiesen ist, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** unter **Antwort-URL** in den Parameter **idpid** ein.

    ![Screenshot von „I D P-initiierte U R L“, wo Sie eine U R L zum Testen abrufen können und dann „Aktivieren“ auswählen](media/amazon-business-tutorial/activate.png)

1. Aktivieren Sie im Assistenten für die **SSO-Aktivierung das Kontrollkästchen** **I have fully tested SSO and am ready to go live** (Ich habe SSO vollständig getestet und bin bereit zur Aktivierung), und klicken Sie anschließend auf **Switch to active** (Aktivieren).

    ![Screenshot der Bestätigung im Assistenten für die S S O-Aktivierung, in der Sie „Aktivieren“ auswählen können](media/amazon-business-tutorial/switch-active.png)

1. Schließlich wird im **Abschnitt SSO-** Verbindungsdetails **der** Status als **aktiv** angezeigt.

    ![Screenshot der S S O-Verbindungsdetails mit dem Status „Aktiv“](media/amazon-business-tutorial/details.png)

    > [!NOTE]
    > Wenn Sie die Anwendung im **Dienstanbieter**-initiierten Modus konfigurieren möchten, führen Sie den folgenden Schritt aus, und fügen Sie die Anmelde-URL aus dem obigen Screenshot in das Textfeld **Anmelde-URL** im Abschnitt **Zusätzliche URLs festlegen** des Azure-Portals ein. Verwenden Sie das folgende Format:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<UNIQUE_ID>`

### <a name="create-amazon-business-test-user"></a>Erstellen eines Testbenutzers für Amazon Business

In diesem Abschnitt wird in Amazon Business ein Benutzer namens B.Simon erstellt. Amazon Business unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Amazon Business vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Amazon Business weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Amazon Business-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Amazon Business-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Amazon Business“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Amazon Business-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Amazon Business können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
