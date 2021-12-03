---
title: 'Tutorial: Konfigurieren von Gtmhub für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD für Gtmhub automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 10b68d00-a544-480b-9bd6-f6ac291a90d0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/03/2020
ms.author: thwimmer
ms.openlocfilehash: e2efc7199d363533bcaadfbeaeb68734e328cb02
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060286"
---
# <a name="tutorial-configure-gtmhub-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Gtmhub für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Gtmhub als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer und Gruppen für [Gtmhub](https://www.gtmhub.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 

>[!NOTE]
>Wenn die automatische Benutzerbereitstellung konfiguriert ist, wird von Azure AD nur die Bereitstellung von Benutzern und Gruppen für Gtmhub automatisch aufgehoben, und Benutzer werden mithilfe des Azure AD-Bereitstellungsdiensts ihren jeweiligen Teams zugeordnet. Sobald einmaliges Anmelden mit Gtmhub im Jahr 2021 aktiviert wird, werden Benutzer bei der Anmeldung per SSO automatisch bereitgestellt und ihrem jeweiligen Team zugewiesen.


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Entfernen von Benutzern aus Gtmhub, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Gtmhub
> * Automatisches Zuordnen von Benutzern zu Teams und entsprechende Koordinierung

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein Gtmhub-Unternehmenskonto

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Gtmhub zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-gtmhub-to-support-team-mapping-and-user-de-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Gtmhub zur Unterstützung der Teamzuordnung und der Aufhebung der Benutzerbereitstellung mit Azure AD

Damit Sie Ihre Bereitstellungsanwendung mit Ihrem Gtmhub-Konto verbinden können, müssen Sie ein SCIM-Token ausstellen und die Mandanten-URL kompilieren.

### <a name="to-issue-a-new-scim-token"></a>So geben Sie ein neues SCIM-Token aus:

1. Melden Sie sich bei Ihrem **Gtmhub-Konto** an. Navigieren Sie zu **Einstellungen > Konfiguration > API-Token**.

    ![Registerkarte „API-Token“](media/gtmhub-provisioning-tutorial/api-tokens.png)
2. Klicken Sie auf **Token ausstellen**, und wählen Sie **SCIM** aus. Geben Sie einen Namen für das Token ein, und klicken Sie auf die Schaltfläche **API-Token generieren**.

    ![Registerkarte zum Generieren von Token](media/gtmhub-provisioning-tutorial/generate-token.png)
3. Nachdem das Token generiert wurde, können Sie es kopieren und in Ihrer Azure AD-Bereitstellungsanwendung verwenden.

    ![Kopieren des Tokens](media/gtmhub-provisioning-tutorial/token.png)

### <a name="to-compile-the-tenant-url"></a>So kompilieren Sie die Mandanten-URL:

1. Ihre Mandanten-URL muss das folgende Format aufweisen:

    `https://app.gtmhub.com/api/v1/scim/azure/{account_id}`

2. Wenn sich Ihr Gtmhub-Konto im US-Rechenzentrum befindet, müssen Sie das Rechenzentrum ebenfalls der URL hinzufügen:
    
     `https://app.us.gtmhub.com/api/v1/scim/azure/{account_id}`

3. Navigieren Sie zum Abrufen der Konto-ID zu **Einstellungen**, wählen Sie die Registerkarte **API-Token** aus, und kopieren Sie die Konto-ID:  ![Account ID](media/gtmhub-provisioning-tutorial/account-id.png)

## <a name="step-3-add-gtmhub-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Gtmhub aus dem Azure AD-Anwendungskatalog

Fügen Sie Gtmhub aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Gtmhub zu beginnen. Wenn Sie Gtmhub zuvor für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu Gtmhub müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-gtmhub"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in Gtmhub 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in ServiceNow auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-gtmhub-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Gtmhub in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Gtmhub** aus.

    ![Gtmhub-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Mandanten-URL und das geheime Token für Gtmhub ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Gtmhub herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Gtmhub-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Gtmhub synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Gtmhub synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Gtmhub für Updatevorgänge verwendet werden. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die Gtmhub-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|&check;|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Verweis|

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Gtmhub zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Gtmhub bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
