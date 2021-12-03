---
title: 'Tutorial: Konfigurieren von Peripass für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD automatisch in Peripass bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 1d036aa3-4e07-4f48-a6ae-40fc6c066e42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: thwimmer
ms.openlocfilehash: 5372364d5fa7574dcc2005df37d399e451b439c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012698"
---
# <a name="tutorial-configure-peripass-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Peripass für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Peripass als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen für [Peripass](https://www.peripass.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Peripass
> * Entfernen von Benutzern aus Peripass, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Peripass
> * [Einmaliges Anmelden](../manage-apps/add-application-portal-setup-oidc-sso.md) bei Peripass (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein Peripass-Mandant: Wenden Sie sich an [Peripass](https://www.peripass.com/), um Ihren Mandanten einzurichten.
* Ein Peripass-Benutzer mit Berechtigungen für die Konfiguration des Mandanten.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Peripass zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-peripass-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Peripass für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich mit der Anmelde-URL Ihres Mandanten bei Peripass an.

2. Wechseln Sie zur **Konfiguration** Ihres Mandanten.

   ![Screenshot des Hauptmenüs von Peripass](media/peripass-provisioning-tutorial/peripass-main-menu.png)

3. Öffnen Sie die Einstellungen zu **Identitätsanbietern und Bereitstellung**.

   ![Screenshot der Mandantenkonfiguration von Peripass](media/peripass-provisioning-tutorial/peripass-configuration.png)

4. Geben Sie dem Identitätsanbieter, den Sie konfigurieren, einen **Anbieternamen**.

5.   Wählen Sie die **Benutzerrolle** aus, die den bereitgestellten Benutzern zugewiesen werden soll.

6. Notieren Sie sich den **SCIM-Endpunkt** und das **SCIM-Token** Ihres Mandanten (Sie benötigen beide später beim Konfigurieren der Benutzerbereitstellung in den Azure AD Enterprise-Anwendungen, und verwenden Sie sie als **Peripass-Mandanten-URL** und **geheimes Token**).

   ![Screenshot der Einstellungen des Peripass-ID-Anbieters](media/peripass-provisioning-tutorial/peripass-id-provider-settings.png)

7. **Speichern Sie Ihre Änderungen** in der Konfiguration.

   ![Screenshot: Speichern des Anbieters](media/peripass-provisioning-tutorial/peripass-save-changes.png)

## <a name="step-3-add-peripass-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Peripass aus dem Azure AD-Anwendungskatalog

Fügen Sie Peripass aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Peripass zu beginnen. Wenn Sie Peripass zuvor für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden.

* Beim Zuweisen von Benutzern und Gruppen zu Peripass müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen.

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben.


## <a name="step-5-configure-automatic-user-provisioning-to-peripass"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in Peripass

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in ServiceNow auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-peripass-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Peripass in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

   ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Peripass** aus.

   ![Der Peripass-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

   ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

   ![Registerkarte „Bereitstellung“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Mandanten-URL und das geheime Token für Peripass ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Peripass herstellen kann. Vergewissern Sie sich im Fall eines Verbindungsfehlers, dass Ihr Peripass-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch mal.

   ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

   ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Peripass synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Peripass synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Peripass für Updatevorgänge verwendet werden. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die Peripass-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;
   |aktiv|Boolean|
   |displayName|String|
   |externalId|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |emails[type eq "work"].value|String|
   
10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Peripass zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in Peripass bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).



## <a name="more-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
