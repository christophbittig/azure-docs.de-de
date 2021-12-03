---
title: 'Tutorial: Konfigurieren von GitHub Enterprise Managed User für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD für GitHub Enterprise Managed User automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 6aee39c7-08a1-4110-b936-4c85d129743b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: thwimmer
ms.openlocfilehash: 662d848d9de2f2d219d24bb401c3cb628d6cea06
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989468"
---
# <a name="tutorial-configure-github-enterprise-managed-user-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von GitHub Enterprise Managed User für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in GitHub Enterprise Managed User als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen für GitHub Enterprise Managed User bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in GitHub Enterprise Managed User
> * Entfernen von Benutzern aus GitHub Enterprise Managed User, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und GitHub Enterprise Managed User
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in GitHub Enterprise Managed User
> * Einmaliges Anmelden bei GitHub Enterprise Managed User (empfohlen)

> [!NOTE]
> Dieser Bereitstellungsconnector ist nur für Teilnehmer am Enterprise Managed Users-Betaprogramm aktiviert.


## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator).
* GitHub Enterprise mit aktiviertem Enterprise Managed User, der für die Anmeldung mit SAML-SSO über Ihren Azure AD-Mandanten konfiguriert ist.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Ermitteln Sie, welche Daten [zwischen Azure AD und GitHub Enterprise Managed User zugeordnet](../app-provisioning/customize-application-attributes.md) werden sollen.

## <a name="step-2-configure-github-enterprise-managed-user-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von GitHub Enterprise Managed User zur Unterstützung der Bereitstellung mit Azure AD

1. Die Mandanten-URL lautet `https://api.github.com/scim/v2/enterprises/{enterprise}`. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer GitHub Enterprise Managed User-Anwendung in das Feld „Mandanten-URL“ eingegeben.

2. Melden Sie sich als Administrator von GitHub Enterprise Managed an, und klicken Sie in der oberen rechten Ecke auf Ihr Profilfoto und dann auf **Einstellungen**.

3. Klicken Sie in der linken Seitenleiste auf **Entwicklereinstellungen**.

4. Klicken Sie in der linken Seitenleiste auf **Persönliche Zugriffstoken**.

5. Klicken Sie auf **Neues Token generieren**.

6. Wählen Sie für dieses Token den Bereich **admin:enterprise** aus.

7. Klicken Sie dann auf **Generate Token** (Token generieren).

8. Kopieren und speichern Sie das **geheime Token**. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer GitHub Enterprise Managed User-Anwendung in das Feld „Geheimes Token“ eingegeben.

## <a name="step-3-add-github-enterprise-managed-user-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von GitHub Enterprise Managed User aus dem Azure AD-Anwendungskatalog

Fügen Sie GitHub Enterprise Managed User aus dem Azure AD-Anwendungskatalog hinzu, um mit der Verwaltung der Bereitstellung für GitHub Enterprise Managed User zu beginnen. Wenn Sie GitHub Enterprise Managed User zuvor bereits für das einmalige Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden.

* Beim Zuweisen von Benutzern und Gruppen zu GitHub Enterprise Managed User müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet.

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben.


## <a name="step-5-configure-automatic-user-provisioning-to-github-enterprise-managed-user"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in GitHub Enterprise Managed User

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in ServiceNow auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-github-enterprise-managed-user-in-azure-ad"></a>Führen Sie folgende Schritte aus, um die automatische Benutzerbereitstellung für GitHub Enterprise Managed User in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **GitHub Enterprise Managed User** aus.

    ![Der Link „GitHub Enterprise Managed User“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Mandanten-URL und das geheime Token für Ihren GitHub Enterprise Managed User ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit GitHub Enterprise Managed User herstellen kann. Wenn keine Verbindung hergestellt werden kann, stellen Sie sicher, dass Ihr GitHub Enterprise Managed User-Konto das geheime Token als Unternehmensbesitzer erstellt hat, und versuchen Sie es noch mal.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit GitHub Enterprise Managed User synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit GitHub Enterprise Managed User synchronisiert werden. Als **übereinstimmende** Eigenschaften ausgewählte Attribute werden bei Updatevorgängen für den Abgleich der Benutzerkonten in GitHub Enterprise Managed User verwendet. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern, müssen Sie sicherstellen, dass die GitHub Enterprise Managed User-API das Filtern von Benutzern anhand dieses Attributs unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|
   |---|---|---|
   |externalId|String|&check;|
   |userName|String|
   |aktiv|Boolean|
   |roles|String|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit GitHub Enterprise Managed User synchronisieren** aus.

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit GitHub Enterprise Managed User synchronisiert werden. Als **übereinstimmende** Eigenschaften ausgewählte Attribute werden bei Updatevorgängen für den Abgleich der Gruppen in GitHub Enterprise Managed User verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      |attribute|type|Unterstützung für das Filtern|
      |---|---|---|
      |externalId|String|&check;|
      |displayName|String|
      |members|Verweis|

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für GitHub Enterprise Managed User zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in GitHub Enterprise Managed User bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

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