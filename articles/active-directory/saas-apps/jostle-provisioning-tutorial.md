---
title: 'Tutorial: Konfigurieren von Jostle für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD automatisch in Jostle bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: thwimmer
ms.openlocfilehash: 5fe843fb735aecda1eff51d3e52392a79bf8c1dd
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207348"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Jostle für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Jostle als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen in [Jostle](https://www.jostle.me/) bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Jostle
> * Entfernen von Benutzern aus Jostle, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Jostle
> * [Einmaliges Anmelden](jostle-tutorial.md) bei Jostle (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein [Jostle-Mandant](https://www.jostle.me/)
* Ein Benutzerkonto in Jostle mit Administratorrechten

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung

1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und Jostle zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Jostle zur Unterstützung der Bereitstellung mit Azure AD

### <a name="automation-account"></a>Automation-Konto

Bevor Sie beginnen, müssen Sie in Ihrem Jostle-Intranet einen **Automation-Benutzer** erstellen. Dies ist das Konto, das Sie für die Konfiguration mit Azure verwenden. Automation-Benutzer können unter **Admin Settings > User accounts and data > Manage Automation users** (Administratoreinstellungen > Benutzerkonten und Daten > Automation-Benutzer verwalten) erstellt werden.

Ausführlichere Informationen zu Automation-Benutzern und deren Erstellung finden Sie in [diesem Artikel](https://forum.jostle.us/hc/en-us/articles/360057364073).

Nach der Erstellung muss das Automation-Benutzerkonto **aktiviert** werden (d. h. mindestens einmal bei Ihrem Intranet angemeldet werden), bevor es zum Konfigurieren von Azure verwendet werden kann.

### <a name="manage-user-provisioning"></a>Verwalten der Benutzerbereitstellung

Stellen Sie zunächst sicher, dass Ihr Kontoabonnement **SSO-/Benutzerbereitstellungsfunktionen** enthält. Falls nicht, können Sie sich an den Kundenservicemanager (<success@jostle.me>) wenden, der Sie beim Hinzufügen zu Ihrem Konto unterstützen kann.

Im nächsten Schritt rufen Sie die **API-URL** und den **API-Schlüssel** aus Jostle ab:

1. Wechseln Sie zur Hauptnavigation, und klicken Sie auf **Admin Settings** (Administratoreinstellungen).
1. Klicken Sie unter **User data to/from other systems** (Benutzerdaten in/aus anderen Systemen) auf **Manage user provisioning** (Benutzerbereitstellung verwalten). Wenn „Manage user provisioning“ (Benutzerbereitstellung verwalten) hier nicht angezeigt wird und Sie sichergestellt haben, dass Ihr Konto SSO-/Benutzerbereitstellung beinhaltet, wenden Sie sich an den Support (<support@jostle.me>), damit diese Seite in den Administratoreinstellungen aktiviert wird.
1. Navigieren Sie im Abschnitt **User Provisioning API details** (Details zur Benutzerbereitstellungs-API) zum Feld **Your Base URL** (Ihre Basis-URL), klicken Sie auf die Schaltfläche zum Kopieren, und speichern Sie die URL an einem Speicherort, auf den Sie später einfach zugreifen können.                                                           

   ![Bereitstellung](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Klicken Sie als Nächstes auf die Schaltfläche **Add a new key...** (Neuen Schlüssel hinzufügen...).
1. Wechseln Sie auf dem folgenden Bildschirm zum Feld **Automation User** (Automation-Benutzer), und wählen Sie im Dropdownmenü Ihr Automation-Benutzerkonto aus. 

   ![Integrationskonto](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. Geben Sie im Feld **Provisioning API key description** (Beschreibung des Bereitstellungs-API-Schlüssels) einen Namen für Ihren Schlüssel (d. h. „Azure“) ein, und klicken Sie anschließend auf die Schaltfläche **Add** (Hinzufügen).

1. Nachdem Ihr Schlüssel generiert wurde, **kopieren Sie ihn unbedingt sofort**, und speichern Sie ihn dort, wo Sie auch die URL abgelegt haben. (Dies ist das einzige Mal, dass der Schlüssel angezeigt wird.)                                                               
1. Als Nächstes verwenden Sie die **API-URL** und den **API-Schlüssel** zum Konfigurieren der Integration in Azure.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>Schritt 3 Hinzufügen von Jostle aus dem Azure AD-Anwendungskatalog

Fügen Sie Jostle aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Jostle zu beginnen. Wenn Sie Jostle zuvor für das einmalige Anmelden eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu Jostle müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für Jostle 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und Gruppen in der Jostle-App auf der Grundlage von Benutzer- und Gruppenzuweisungen in Azure AD erläutert.

> [!NOTE]
> Weitere Informationen zur automatischen Benutzerbereitstellung in Jostle finden Sie unter [User Provisioning: Azure Integration](https://forum.jostle.us/hc/en-us/articles/360056368534-User-Provisioning-Azure-Integration) (Benutzerbereitstellung: Azure-Integration).

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Jostle in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **Jostle** aus.

    ![Jostle-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellungen** aus, und klicken Sie auf **Erste Schritte**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

1.  Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Informationen für die Optionen **Mandanten-URL** und **Geheimes Token** für Jostle ein. Um sicherzustellen, dass Azure AD eine Verbindung mit Jostle herstellen kann, wählen Sie **Verbindung testen** aus. Wenn die Verbindung nicht möglich ist, müssen Sie sicherstellen, dass Ihr Jostle-Konto über Administratorberechtigungen verfügt. Versuchen Sie es anschließend noch mal.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**. Jostle sendet jedoch auch Benachrichtigungen zu Bereitstellungsfehlern. Diese Einstellung ist also optional.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Provision Azure Active Directory Users to Jostle** (Azure Active Directory-Benutzer in Jostle bereitstellen) aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die aus Azure AD mit Jostle synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Jostle für Updatevorgänge verwendet werden. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern, müssen Sie sicherstellen, dass die Jostle-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

   |Attribut|Typ|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |aktiv|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "personal"].value|String|
   |emails[type eq "alternate1"].value|String|
   |emails[type eq "alternate2"].value|String|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|String|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |String|  

1. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den im Tutorial [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) bereitgestellten Anleitungen.

1. Um den Azure AD-Bereitstellungsdienst für Jostle zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

1. Definieren Sie die Benutzer oder Gruppen, die in Jostle bereitgestellt werden soll, indem Sie die gewünschten Werte unter **Geltungsbereich** im Abschnitt **Einstellungen** festlegen. Für Jostle sollte der **Geltungsbereich** auf „Nur zugewiesene Benutzer und Gruppen synchronisieren“ festgelegt werden.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

1. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Die Durchführung des ersten Zyklus dauert länger als die Durchführung nachfolgender Zyklen. Dieser erfolgen ungefähr alle 40 Minuten, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung

Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zum Quarantänestatus finden Sie unter [Anwendungsbereitstellung im Quarantänestatus](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)