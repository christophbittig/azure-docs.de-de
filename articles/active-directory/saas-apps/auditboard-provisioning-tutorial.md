---
title: 'Tutorial: Konfigurieren Sie die AuditBoard für die automatische Benutzerbereitstellung mit Azure Active Directory Domain Services | Microsoft Docs'
description: Erfahren Sie, wie Sie Benutzerkonten automatisch aus Azure AD für AuditBoard bereitstellen und die Bereitstellung aufheben können.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e6ab736b-2bb7-4a5a-9f01-67c33f0ff97d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: thwimmer
ms.openlocfilehash: 8050b5352658412aaa156b1e48290810e45a5894
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327438"
---
# <a name="tutorial-configure-auditboard-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren Sie den AuditBoard für die automatische Benutzerbereitstellung

Dieses Tutorial beschreibt die Schritte, die Sie sowohl in AuditBoard als auch in Azure Active Directory (Azure AD) durchführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Wenn konfiguriert, richtet Azure AD über den Azure AD Provisioning Service automatisch Benutzer für den [AuditBoard](https://www.auditboard.com/) ein und hebt die Einrichtung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern im AuditBoard
> * Entfernen Sie Benutzer vom AuditBoard, wenn sie den Zugriff nicht mehr benötigen
> * Halten Sie die Benutzerattribute zwischen Azure AD und AuditBoard synchronisiert
> * [Einmaliges Anmelden](./auditboard-tutorial.md) beim AuditBoard (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Eine AuditBoard-Website (Live).

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Bestimmen Sie, welche Daten [zwischen Azure AD und AuditBoard](../app-provisioning/customize-application-attributes.md) abgebildet werden sollen. 

## <a name="step-2-configure-auditboard-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren Sie AuditBoard für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich bei AuditBoard an. Navigieren Sie zu **Einstellungen** > **Benutzer & Rollen** > **Sicherheit** > **SCIM**.

2. Klicken Sie dann auf **Generate Token** (Token generieren). 

3. Speichern Sie das **Token** und die **SCIM-Basis-URL**. Diese Werte werden in den Feldern Mandanten-URL und Secret Token auf der Bereitstellung-Registerkarte Ihrer AuditBoard-Anwendung im Azure-Portal eingegeben.

   > [!NOTE]
   > Das Erstellen eines neuen Tokens macht das vorherige Token ungültig.

4. Für die AuditBoard-Instanz müssen die folgenden Benutzerberechtigungen für die SCIM-Benutzerrolle festgelegt werden (standardmäßig Systemadministrator). Stellen Sie eine Verbindung mit der AuditBoard-Unterstützung her, um zu bestätigen, dass dies ordnungsgemäß definiert ist`user:action.administer must be set to allow` und `user:action.edit must be set to allow`.


## <a name="step-3-add-auditboard-from-the-azure-ad-application-gallery"></a>Schritt 3: Fügen Sie AuditBoard aus dem Azure AD-Anwendungskatalog hinzu

Fügen Sie AuditBoard aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Auditboard zu beginnen. Wenn Sie zuvor AuditBoard für SSO eingerichtet haben, können Sie die gleiche Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern zu AuditBoard, müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern zur App steuern. Wenn der Bereich auf alle Benutzer festgelegt ist, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-auditboard"></a>Schritt 5: Konfigurieren Sie die automatische Benutzerbereitstellung zu AuditBoard 

Dieser Abschnitt führt Sie durch die Schritte zur Konfiguration des Azure AD-Bereitstellungsdiensts, um Benutzer in TestApp basierend auf Benutzer- und/oder Gruppenzuweisungen in Azure AD zu erstellen, zu aktualisieren und zu deaktivieren.

### <a name="to-configure-automatic-user-provisioning-for-auditboard-in-azure-ad"></a>Um die automatische Benutzerbereitstellung für AuditBoard in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **AuditBoard** aus.

    ![AuditBoard-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** Ihre Mandanten-URL und das geheime Token für AuditBoard ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit AuditBoard herstellen kann. Vergewissern Sie sich im Fall eines Verbindungsfehlers, dass Ihr AuditBoard-Konto über Administratorberechtigungen verfügt und versuchen Sie es erneut.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie unter dem Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit AuditBoard synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributen-Zuordnung** die Benutzerattribute, die von Azure AD zu AuditBoard synchronisiert werden. Die als **übereinstimmende** Attribute ausgewählten Eigenschaften werden verwendet, um die Benutzerkonten in AuditBoard für Aktualisierungsvorgänge abzustimmen. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die AuditBoard-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|Typ|Unterstützung für das Filtern|
   |---|---|---|
   |emails[type eq "work"].value|String|&check;|
   |aktiv|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für AuditBoard zu aktivieren, ändern Sie im Abschnitt **Einstellungen** **den Bereitstellungsstatus** auf **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Definieren Sie die Benutzer, die Sie für AuditBoard bereitstellen möchten, indem Sie im Bereich **Einstellungen** unter **Umfang** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Mit diesem Vorgang wird der erste Synchronisierungszyklus für alle Benutzer gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)