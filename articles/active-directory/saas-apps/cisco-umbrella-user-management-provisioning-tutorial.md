---
title: 'Tutorial: Konfigurieren von Cisco Umbrella User Management für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten automatisch aus Azure AD in Cisco Umbrella User Management bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 1aa20f40-19ec-4213-9a3b-5eb2bcdd9bbd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: thwimmer
ms.openlocfilehash: 38b4c7cc04e5a9cc0d3dd35875ed94af804ca383
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327619"
---
# <a name="tutorial-configure-cisco-umbrella-user-management-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Cisco Umbrella User Management für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in Cisco Umbrella User Management und Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen für [Cisco Umbrella User Management](https://umbrella.cisco.com/) bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Cisco Umbrella User Management
> * Entfernen von Benutzern aus Cisco Umbrella User Management, wenn diese keinen Zugriff mehr benötigen
> * Wichtige Benutzerattribute zwischen Azure AD und der Cisco Umbrella-Benutzerverwaltung synchronisiert
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in Cisco Umbrella User Management

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z.B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein [Cisco Umbrella-Abonnement](https://signup.umbrella.com).
* Ein Benutzerkonto in Cisco Umbrella mit vollständigen Administratorberechtigungen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Cisco Umbrella User Management zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-import-objectguid-attribute-via-azure-ad-connect-optional"></a>Schritt 2: Importieren des ObjectGUID-Attributs über Azure AD Connect (optional)
Wenn Sie zuvor Benutzer- und Gruppenidentitäten aus dem lokalen AD für Cisco Umbrella bereitgestellt haben und nun die gleichen Benutzer und Gruppen aus Azure AD bereitstellen möchten, müssen Sie das ObjectGUID-Attribut synchronisieren, damit zuvor bereitgestellte Identitäten in der Umbrella-Richtlinie beibehalten werden.

> [!NOTE]
> Der lokale Umbrella AD Connector sollte vor dem Importieren des ObjectGUID-Attributs deaktiviert werden.
  
Wenn Sie Microsoft Azure AD Connect verwenden, wird das ObjectGUID-Attribut von Benutzern und Gruppen nicht standardmäßig aus dem lokalen AD mit Azure AD synchronisiert. Aktivieren Sie zum Synchronisieren dieses Attributs die optionale **Verzeichniserweiterungs-Attributsynchronisierung,** und wählen Sie die objectGUID-Attribute für Gruppen und Benutzer aus.  

   ![Seite „Optionale Features“ des Azure Active Directory Connect-Assistenten](./media/cisco-umbrella-user-management-provisioning-tutorial/active-directory-connect-directory-extension-attribute-sync.png)

> [!NOTE]
> Bei der Suche im Feld **Verfügbare Attribute** wird die Groß-/Kleinschreibung berücksichtigt.

   ![Screenshot der Auswahlseite „Verzeichniserweiterungen“](./media/cisco-umbrella-user-management-provisioning-tutorial/active-directory-connect-directory-extensions.png)

## <a name="step-3-configure-cisco-umbrella-user-management-to-support-provisioning-with-azure-ad"></a>Schritt 3: Konfigurieren von Cisco Umbrella User Management für die Unterstützung der Bereitstellung in Azure AD

1. Melden Sie sich beim [Cisco Umbrella-Dashboard](https://login.umbrella.com ) an. Navigieren Sie zu **Deployments** > **Core Identities** > **Users and Groups (Core Identities-Benutzer und -Gruppen für Bereitstellungen)** .

2. Wenn der Importmechanismus auf Manueller Import festgelegt ist, klicken Sie auf **Import from IdP** (Aus IdP importieren), um den Importmechanismus zu wechseln. 
  
3. Erweitern Sie die Azure Active Directory Karte, und klicken Sie auf die **Seite API-Schlüssel**.

   ![API](./media/cisco-umbrella-user-management-provisioning-tutorial/keys.png)

4. Erweitern Sie die Azure Active Directory Karte auf der Seite API-Schlüssel und klicken Sie auf **Generate Token** (Token generieren).

   ![Generate](./media/cisco-umbrella-user-management-provisioning-tutorial/token.png)

5. Das generierte Token wird nur einmal angezeigt. Kopieren und speichern Sie die URL und das Token. Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ der Cisco Umbrella User Management-Anwendung in das Feld **Tenant URL** (Mandanten-URL) bzw. **Secret Token** (Geheimes Token) eingegeben.  


## <a name="step-4-add-cisco-umbrella-user-management-from-the-azure-ad-application-gallery"></a>Schritt 4. Hinzufügen von Cisco Umbrella User Management aus dem Azure AD-Anwendungskatalog

Fügen Sie Cisco Umbrella User Management aus dem Azure AD-Anwendungskatalog hinzu, um mit der Verwaltung der Bereitstellung in Cisco Umbrella User Management zu beginnen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-5-define-who-will-be-in-scope-for-provisioning"></a>Schritt 5: Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu Cisco Umbrella User Management müssen Sie eine andere Rolle als **Default Access** (Standardzugriff) auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 

## <a name="step-6-configure-automatic-user-provisioning-to-cisco-umbrella-user-management"></a>Schritt 6: Konfigurieren der automatischen Benutzerbereitstellung für Cisco Umbrella User Management 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdienstes zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Cisco Umbrella User Management auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-cisco-umbrella-user-management-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Cisco Umbrella User Management in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Cisco Umbrella User Management** aus.

    ![Der Link zu Cisco Umbrella User Management in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Admin Credentials** (Administratoranmeldeinformationen) die Werte für „Mandanten-URL“ und „Geheimes Token“ für Cisco Umbrealla User Management ein. Klicken Sie auf **Test Connection** (Verbindung testen), um sicherzustellen, dass Azure AD eine Verbindung mit Cisco Umbrella User Management herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Cisco Umbrella User Management-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Cisco Umbrella User Management** (Azure Active Directory-Benutzer mit Cisco Umbrella User Management synchronisieren).

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Cisco Umbrella User Management synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Cisco Umbrella User Management für Aktualisierungsvorgänge verwendet. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die Cisco Umbrella User Management-API das Filtern von Benutzern basierend auf diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|Typ|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |aktiv|Boolean|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId|String|

> [!NOTE]
> Wenn Sie das objectGUID-Attribut für Benutzer über Azure AD Connect importiert haben (siehe Schritt 2), fügen Sie eine Zuordnung von objectGUID zu urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId hinzu.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to Cisco Umbrella User Management** (Azure Active Directory-Gruppen mit Cisco Umbrella User Management synchronisieren).

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Cisco Umbrella User Management synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Cisco Umbrella User Management für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      |attribute|Typ|Unterstützung für das Filtern|
      |---|---|---|
      |displayName|String|&check;|
      |externalId|String|
      |members|Verweis|
      |urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:Group:nativeObjectId|String|

> [!NOTE]
> Wenn Sie das objectGUID-Attribut für Gruppen über Azure AD Connect importiert haben (siehe Schritt 2), fügen Sie eine Zuordnung von objectGUID zu urn:ietf:params:scim:schemas:extension:ciscoumbrella:2.0:User:nativeObjectId hinzu.

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Cisco Umbrella User Management zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **On** (Ein).

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Cisco Umbrella User Management bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-7-monitor-your-deployment"></a>Schritt 7. Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Connector-Einschränkungen
* Cisco Umbrella User Management unterstützt die Bereitstellung von maximal 200 Gruppen. Gruppen, die diesen Bereich sprengen, können nicht für Cisco Umbrella bereitgestellt werden. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)