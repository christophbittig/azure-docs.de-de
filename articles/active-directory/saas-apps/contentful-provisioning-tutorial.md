---
title: 'Tutorial: Konfigurieren von Contentful für die automatische Benutzerbereitstellung mit Azure Active Directory'
description: Erfahren Sie, wie Sie Benutzerkonten von Azure Active Directory (Azure AD) automatisch in Contentful bereitstellen und deren Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2020
ms.author: thwimmer
ms.openlocfilehash: 60647cbceed7b7917daa18e8b8c7ed6531ebe0c9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059108"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Contentful für die automatische Benutzerbereitstellung

In diesem Artikel werden die Schritte beschrieben, die Sie sowohl in Contentful als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen in [Contentful](https://www.contentful.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie Antworten bei häufig gestellten Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Unterstützte Funktionen

> [!div class="checklist"]
> * Erstellen von Benutzern in Contentful
> * Entfernen von Benutzern aus Contentful, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Contentful
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in Contentful
> * [Einmaliges Anmelden](contentful-tutorial.md) bei Contentful (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) zum Konfigurieren von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder globaler Administrator). 
* Ein Contentful-Organisationskonto mit einem Abonnement, das die Bereitstellung des Systems für die domänenübergreifende Identitätsverwaltung (Cross-Domain Identity Management, SCIM) unterstützt. Wenn Sie sich an den [Contentful-Support](mailto:support@contentful.com), wenn Sie Fragen zum Abonnement Ihrer Organisation haben.
 
## <a name="plan-your-provisioning-deployment"></a>Planen der Bereitstellung

1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Contentful zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Konfigurieren von Contentful für die Unterstützung der Bereitstellung mit Azure AD

1. Erstellen Sie in Contentful ein Konto vom Typ **Dienstbenutzer**. Alle Bereitstellungsberechtigungen für Azure werden über dieses Konto bereitgestellt. Wir empfehen, **Besitzer** als Organisationsrolle für dieses Konto auszuwählen.

2. Melden Sie sich bei Contentful als **Dienstbenutzer** an.

3. Wählen Sie im Menü auf der linken Seite **Organisationseinstellungen** > **Zugriffstools** > **Benutzerbereitstellung** aus.

   ![Screenshot des Menüs „Organisationseinstellungen“ in Contentful; unter „Zugriffstools“ ist die Option „Benutzerbereitstellung“ hervorgehoben.](media/contentful-provisioning-tutorial/access.png)

4. Kopieren und speichern Sie die **SCIM-URL**. Dieser Wert wird im Azure-Portal auf der Registerkarte **Bereitstellung** Ihrer Contentful-Anwendung eingegeben.

5. Wählen Sie **Persönliches Zugriffstoken generieren** aus.

    ![Screenshot: SCIM-URL zum Generieren eines persönlichen Zugriffstokens](media/contentful-provisioning-tutorial/generate.png)

6. Geben Sie im modalen Fenster einen Namen für das persönliche Zugriffstoken ein, und wählen Sie dann **Generieren** aus.

7. Die SCIM-URL und das geheime Token werden generiert. Kopieren und speichern Sie diese Werte. Diese Werte werden im Azure-Portal auf der Registerkarte **Bereitstellung** Ihrer Contentful-Anwendung eingegeben.

    ![Screenshot des Bereichs „Persönliches Zugriffstoken“ in dem „CFPAT-<token>“ (Tokenplatzhaltername) hervorgehoben ist.](media/contentful-provisioning-tutorial/token.png)


Wenn Sie beim Konfigurieren der Bereitstellung in der Contentful-Verwaltungskonsole Fragen haben, wenden Sie sich an den [Contentful-Support](mailto:support@contentful.com).

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Hinzufügen von Contentful aus dem Azure AD-Anwendungskatalog

Fügen Sie Contentful aus dem Azure AD-Anwendungskatalog hinzu, um die Bereitstellung in Contentful zu verwalten. Wenn Sie Contentful zuvor für einmaliges Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Wir empfehlen jedoch, beim erstmaligen Testen der Integration eine separate App zu erstellen. Unter [Hinzufügen einer Anwendung aus dem Katalog](../manage-apps/add-application-portal.md) finden Sie entsprechende Informationen hierzu. 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen für den Benutzer oder die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. 

Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, führen Sie die entsprechenden Schritte aus, um [der Anwendung Benutzer und Gruppen zuzuweisen](../manage-apps/assign-user-or-group-access-portal.md).

Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen Bereichsfilter verwenden, um [bedingte Regeln für die Bereitstellung von Benutzerkonten zu definieren](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Beim Zuweisen von Benutzern und Gruppen zu Contentful müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ sind von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht wirksam berechtigt“ angegeben. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 
* Fangen Sie klein an. Testen Sie die Bereitstellung mit wenigen Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie den Bereich durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App steuern. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Konfigurieren der automatischen Benutzerbereitstellung in Contentful 

In diesem Abschnitt werden die Schritte zum Einrichten des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in einer Test-App anhand von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Contentful in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen** aus.

   ![Screenshot mit dem Menü „Unternehmensanwendungen“ im Azure-Portal, in dem die Option „Alle Anwendungen“ hervorgehoben ist.](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Contentful** aus.

   ![Screenshot: Die ersten 20 Ergebnisse, die in der Anwendungsliste zurückgegeben wurden.](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

   ![Screenshot des Abschnitts „Verwalten“ im linken Menü, in dem die Registerkarte „Bereitstellung“ hervorgehoben ist.](common/provisioning.png)

4. Legen Sie **Bereitstellungsmodus** auf **Automatisch** fest.

   ![Screenshot der Optionen für „Bereitstellungsmodus“; hervorgehoben ist die Option „Automatisch“.](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** Ihre Werte für die Mandanten-URL und das geheime Token für Contentful ein. Um sicherzustellen, dass Azure AD eine Verbindung mit Contentful herstellen kann, wählen Sie **Verbindung testen** aus. Vergewissern Sie sich im Fall eines Verbindungsfehlers, dass Ihr Contentful-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

   ![Screenshot mit den Textfeldern „Mandanten-URL“ und „Geheimes Token“; die Schaltfläche "Verbindung testen" ist hervorgehoben.](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie dann das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

   ![Screenshot mit dem Textfeld „Benachrichtigungs-E-Mail“.](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Contentful synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Contentful synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Updatevorgängen für den Abgleich der Benutzerkonten in Contentful verwendet. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die Contentful-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Contentful synchronisieren** aus.

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit Contentful synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Updatevorgängen für den Abgleich der Gruppen in Contentful verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    |attribute|type|Unterstützung für das Filtern|
    |---|---|---|
    |displayName|String|&check;|
    |members|Verweis|

12. Führen Sie zum Einrichten von Bereichsfiltern die im Tutorial [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Schritte aus.

13. Um den Azure AD-Bereitstellungsdienst für Contentful zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Screenshot: Umschaltfläche „Ein“ und „Aus“ für den Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Um die Benutzer oder Gruppen zu definieren, die in Contentful bereitgestellt werden sollen, wählen Sie im Abschnitt **Einstellungen** unter **Bereich** die entsprechende Option aus.

    ![Screenshot: Auswählbare Optionen im Fenster „Bereich“](common/provisioning-scope.png)

15. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Screenshot mit den Schaltflächen „Speichern“ und „Abbrechen“](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="monitor-your-deployment"></a>Überwachen der Bereitstellung

Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Anhand der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
