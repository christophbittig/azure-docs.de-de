---
title: 'Tutorial: Azure Active Directory-Integration mit Learning Pool LMS | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Learning Pool LMS konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: jeedes
ms.openlocfilehash: ab0ada258b8df3ccca7086f2d8c84a2b4b1ee336
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313469"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-pool-lms"></a>Tutorial: Azure Active Directory-Integration mit Learning Pool LMS

In diesem Tutorial erfahren Sie, wie Sie Learning Pool LMS in Azure Active Directory (Azure AD) integrieren. Die Integration von Learning Pool LMS in Azure AD ermöglicht Ihnen Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Learning Pool LMS hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Learning Pool LMS anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Learning Pool LMS-Abonnement, für das das einmaligem Anmelden (SSO) aktiviert ist.

> [!NOTE]
> Wenn Sie ein Projekt für das einmalige Anmelden starten, führt Sie ein Mitglied des Learning Pool LMS Delivery-Teams durch diesen Prozess. Wenn Sie keinen Kontakt mit einem Mitglied des Learning Pool LMS Delivery-Teams haben, wenden Sie sich an Ihren Learning Pool LMS-Konto-Manager.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Learning Pool LMS unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-learning-pool-lms-from-the-gallery"></a>Hinzufügen von Learning Pool LMS aus dem Katalog

Zum Konfigurieren der Integration von Learning Pool LMS in Azure AD müssen Sie die Learning Pool LMS-App aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** den Suchbegriff **Learning Pool LMS** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Learning Pool LMS** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-learning-pool-lms"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Learning Pool LMS

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Learning Pool LMS mit einem vorhandenen Azure-Benutzer. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Learning Pool LMS eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Learning Pool LMS die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
1. **[Zuweisen eines Azure AD-Benutzers](#assign-an-azure-ad-user)** , um dem Benutzer die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Learning Pool LMS](#configure-learning-pool-lms-sso)** , um die Einstellungen für das einmalige Anmelden auf der Anwendungsseite zu konfigurieren.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Learning Pool LMS** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei wird der Wert **Bezeichner** automatisch im Abschnitt „Grundlegende SAML-Konfiguration“ eingefügt.

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://parliament.preview.Learningpool.com/auth/shibboleth/index.php`.

    > [!Note]
    > Falls der Wert **Bezeichner** nicht automatisch aufgefüllt wird, geben Sie den erforderlichen Wert manuell ein.

5. Sie müssen mindestens ein Attribut senden, das verwendet wird, um Ihre Azure-Benutzer mit den Benutzern in Learning Pool LMS abzugleichen. Normalerweise sind die Standardattribute ausreichend, aber in einigen Fällen müssen Sie möglicherweise einige benutzerdefinierte Attribute senden. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld „Benutzerattribute“ zu öffnen falls das erforderlich ist.

    ![Screenshot: Benutzerattribute mit ausgewähltem Bearbeitungssymbol](common/edit-attribute.png)

6. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus: 

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Benutzeransprüche mit Option zum Hinzufügen eines neuen Anspruchs](common/new-save-attribute.png)

    ![Screenshot des Dialogfelds „Benutzeransprüche verwalten“, in dem Sie die hier beschriebenen Werte eingeben können](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche zum Kopieren bei der **App-Verbundmetadaten-URL**, und geben Sie diese URL an das Learning Pool Delivery-Team weiter.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="assign-an-azure-ad-user"></a>Zuweisen eines Azure AD-Benutzers

In diesem Abschnitt ermöglichen Sie einem vorhandenen Azure AD Benutzer die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer den Zugriff auf Learning Pool LMS gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Learning Pool LMS** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ einen geeigneten Benutzer aus und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-learning-pool-lms-sso"></a>Konfigurieren des einmaligen Anmeldens für Learning Pool LMS

Das Learning Pool Delivery-Team verwendet die **App-Verbundmetadaten-URL** um das LMS so zu konfigurieren, dass SAML2-Verbindungen akzeptiert werden. Sie werden aufgefordert, einige Testschritte auszuführen, um zu überprüfen, ob die Verbindung ordnungsgemäß konfiguriert ist, und das Learning Pool Delivery-Team führt Sie durch diesen Prozess.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Sie werden von dem Learning Pool Delivery-Team durch den Testprozess geführt.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Learning Pool LMS können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
