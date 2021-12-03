---
title: 'Tutorial: Konfigurieren von BIC Cloud Design für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD für BIC Cloud Design automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: Thwimmer
manager: beatrizd
ms.assetid: 1aace746-6f6d-4ac4-ad2c-7ba65bb86a72
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2021
ms.author: Thwimmer
ms.openlocfilehash: 12bdfb49c490593ecf348688477797045ed9b8f7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495349"
---
# <a name="tutorial-configure-bic-cloud-design-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von BIC Cloud Design für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in BIC Cloud Design als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer und Gruppen für [BIC Cloud Design](https://www.gbtec.de/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in BIC Cloud Design.
> * Entfernen von Benutzern aus BIC Cloud Design, wenn diese keinen Zugriff mehr benötigen.
> * Synchronisieren von Benutzerattributen zwischen Azure AD und BIC Cloud Design.
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in BIC Cloud Design.
> * [Einmaliges Anmelden](bic-cloud-design-tutorial.md) bei BIC Cloud Design.

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 


## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und BIC Cloud Design zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-bic-cloud-design-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von BIC Cloud Design für die Unterstützung der Bereitstellung mit Azure AD

Wenn Sie BIC Cloud Design für die Unterstützung der Bereitstellung mit Azure AD konfigurieren möchten, schreiben Sie eine E-Mail an das [BIC Cloud Design-Supportteam](mailto:bicsupport@gbtec.de).

## <a name="step-3-add-contoso-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Contoso aus dem Azure AD-Anwendungskatalog

Fügen Sie BIC Cloud Design aus dem Azure AD-Anwendungskatalog hinzu, um mit der Verwaltung der Bereitstellung in BIC Cloud Design zu beginnen. Wenn Sie BIC Cloud Design zuvor für das einmalige Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu BIC Cloud Design müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-bic-cloud-design"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in BIC Cloud Design 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in BIC Cloud Design auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-bic-cloud-design-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für BIC Cloud Design in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **BIC Cloud Design** aus.

    ![Contoso-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Mandanten-URL und das geheime Token für BIC Cloud Design ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit BIC Cloud Design herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr BIC Cloud Design-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Mappings** (Zuordnungen) die Option **Synchronize Azure Active Directory Users to BIC Cloud Design** (Azure Active Directory-Benutzer mit BIC Cloud Design synchronisieren) aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit BIC Cloud Design synchronisiert werden. Beachten Sie, dass die als **Übereinstimmung** ausgewählten Attribute für den Abgleich der Benutzerkonten in BIC Cloud Design für Updatevorgänge verwendet werden. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die BIC Cloud Design-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|Erforderlich für Real Links|
   |---|---|---|---|
    |userName|String|&check;|&check;
    |emails[type eq "work"].value|String|&check;|&check;
    |aktiv|Boolean||&check;
    |roles[primary eq "True"].value|String||&check;
    |displayName|String||&check;
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String||&check;

1. Wählen Sie im Abschnitt **Mappings** (Zuordnungen) die Option **Synchronize Azure Active Directory Groups to BIC Cloud Design** (Azure Active Directory-Gruppen mit BIC Cloud Design synchronisieren) aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit BIC Cloud Design synchronisiert werden. Beachten Sie, dass die als **Übereinstimmung** ausgewählten Attribute für den Abgleich der Gruppen in BIC Cloud Design für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|Erforderlich für Real Links|
   |---|---|---|---|
      |displayName|String|&check;|&check;
      |externalId|String||&check;
      |members|Verweis|

1. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für BIC Cloud Design zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

1. Legen Sie die Benutzer bzw. Gruppen fest, die in BIC Cloud Design bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

1. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="more-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)