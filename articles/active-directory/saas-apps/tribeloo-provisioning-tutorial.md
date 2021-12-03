---
title: 'Tutorial: Konfigurieren von Tribeloo für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD für Tribeloo automatisch bereitstellen und die Bereitstellung aufheben.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: d1063ef2-5d39-4480-a1e2-f58ebe7f98c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: thwimmer
ms.openlocfilehash: e01d8584825e1e9a74c82ee34dd3b9c6a2457cf2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045917"
---
# <a name="tutorial-configure-tribeloo-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Tribeloo für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Tribeloo als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen in [Tribeloo](https://www.tribeloo.com/) bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Tribeloo
> * Entfernen von Benutzern aus Tribeloo, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Tribeloo
> * [Einmaliges Anmelden](tribeloo-tutorial.md) bei Tribeloo (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein [Tribeloo](https://www.tribeloo.com/)-Mandant
* Ein Benutzerkonto in Tribeloo mit Administratorberechtigungen


## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und Tribeloo zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-tribeloo-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Tribeloo für die Unterstützung der Bereitstellung mit Azure AD

Navigieren Sie zur [Tribeloo-App](https://app.tribeloo.com/), und melden Sie sich als Benutzer mit Administratorberechtigungen an.
1. Navigieren Sie im seitlichen Menü (1) zu **Admin** (2), und wählen Sie **User management** (Benutzerverwaltung) (3) aus.

    ![Zugreifen auf die Benutzerverwaltung](media/tribeloo-provisioning-tutorial/tribeloo-user-management.png)

1. Wählen Sie die Registerkarte **User provisioning** (Benutzerbereitstellung) (1) aus. Auf dieser Registerkarte haben Sie Zugriff auf Tribeloo-Informationen, die Sie für die Konfiguration der Azure AD-Integration benötigen.
   1. **SCIM base URL** (2)
   1. **SCIM Bearer token** (SCIM-Bearertoken) (3)
1. Kopieren Sie diese Werte in die Zwischenablage, und fügen Sie sie in die entsprechenden Azure AD-Felder ein (siehe Schritt 5). Die AD-Felder heißen **Mandanten-URL** und **Geheimes Token**.

    ![Tribeloo-Bereitstellungsparameter](media/tribeloo-provisioning-tutorial/tribeloo-provisioning-parameters.png)

1. Sie können nun auf der Registerkarte **User Provisioning** (Benutzerbereitstellung) auf die Schaltfläche **Enable User provisioning** (Benutzerbereitstellung aktivieren) (1) klicken, um die Benutzerbereitstellung in Tribeloo zu ermöglichen.

    ![Aktivieren der Bereitstellung in Tribeloo](media/tribeloo-provisioning-tutorial/tribeloo-enable-provisioning.png)

## <a name="step-3-add-tribeloo-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Tribeloo aus dem Azure AD-Anwendungskatalog

Fügen Sie Tribeloo aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Tribeloo zu beginnen. Wenn Sie Tribeloo zuvor für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu Tribeloo müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-tribeloo"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in Tribeloo 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Tribeloo auf der Grundlage von Benutzer- bzw. Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-tribeloo-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Tribeloo in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste den Eintrag **Tribeloo** aus.

    ![Tribeloo-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die **Mandanten-URL** und das **geheime Token** für Tribeloo ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Tribeloo herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Tribeloo-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Tribeloo synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Tribeloo synchronisiert werden. Mit den als **übereinstimmende** Eigenschaften ausgewählten Attributen werden die Benutzerkonten in Tribeloo bei Updatevorgängen abgeglichen. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die Tribeloo-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;
   |emails[type eq "work"].value|String|
   |aktiv|Boolean|   
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |addresses[type eq "work"].formatted|String|

1. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für Tribeloo zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

1. Legen Sie die Benutzer und/oder Gruppen fest, die in Tribeloo bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

1. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md). 

## <a name="change-log"></a>Änderungsprotokoll
* 12.08.2021: Unterstützung für Kernbenutzerattribute vom Typ **emails[type eq "work"].value** und **addresses[type eq "work"].formatted** hinzugefügt.

## <a name="more-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
