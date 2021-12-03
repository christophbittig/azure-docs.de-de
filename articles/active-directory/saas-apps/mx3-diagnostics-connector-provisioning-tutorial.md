---
title: 'Tutorial: Konfigurieren von MX3 Diagnostics Connector für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD für MX3 Diagnostics Connector automatisch bereitstellen und die Bereitstellung aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: Thwimmer
manager: beatrizd
ms.assetid: 6d54ea28-0208-45bc-8e29-c6cf9a912f00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2021
ms.author: Thwimmer
ms.openlocfilehash: 2021b7a7af7aee23e118de0b555e86cb7ee20272
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270686"
---
# <a name="tutorial-configure-mx3-diagnostics-connector-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von MX3 Diagnostics Connector für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in MX3 Diagnostics Connector als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer und Gruppen für [MX3 Diagnostics Connector](https://www.mx3diagnostics.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in MX3 Diagnostics Connector
> * Entfernen von Benutzern aus MX3 Diagnostics Connector, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und MX3 Diagnostics Connector.
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in MX3 Diagnostics Connector
> * Einmaliges Anmelden bei MX3 Diagnostics Connector

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein MX3-Konto mit Organisationsfunktion.
* Ein Konto im MX3-Portal mit einmaligem Anmelden (SSO).

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und MX3 Diagnostics Connector zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mx3-diagnostics-connector-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von MX3 Diagnostics Connector zur Unterstützung der Bereitstellung in Azure AD

1. Wenn für Ihr MX3-Konto die Organisationsfunktion nicht aktiviert ist, müssen Sie diese, wie in dieser [Dokumentation](https://www.mx3diagnostics.com/files/files/MX3_PortalGuide_0321.pdf) beschrieben, beantragen.

1. Wenn für Ihr MX3-Konto die Funktion für einmaliges Anmelden (SSO) nicht aktiviert ist, müssen Sie das einmalige Anmelden von Azure AD, wie in dieser Dokumentation beschrieben, einrichten.

1. Melden Sie sich im [MX3-Portal](https://portal.mx3.app) an. Navigieren Sie zur Seite „SSO-Einstellungen“, indem Sie auf „Einstellungen“ und dann auf **Einmaliges Anmelden** klicken.

    ![Screenshot: Einstellungen für einmaliges Anmelden für MX3 Diagnostics Connector.](media/mx3-provisioning/sso-settings.png)


1. Scrollen Sie nach unten, um das Token anzuzeigen. Kopieren und speichern Sie das Token. Sie benötigen diese Angaben in **Schritt 5**.

    ![Screenshot: Geheimes Token von MX3 Diagnostics Connector für Azure AD.](media/mx3-provisioning/sso-settings-token.png)

## <a name="step-3-add-mx3-diagnostics-connector-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von MX3 Diagnostics Connector aus dem Azure AD-Anwendungskatalog

Fügen Sie MX3 Diagnostics Connector aus Azure AD-Anwendungskatalog hinzu, um mit der Verwaltung der Bereitstellung für MX3 Diagnostics Connector zu beginnen. Wenn Sie MX3 Diagnostics Connector zuvor für das einmalige Anmelden (SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu MX3 Diagnostics Connector müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-mx3-diagnostics-connector"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für MX3 Diagnostics Connector 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in MX3 Diagnostics Connector auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-mx3-diagnostics-connector-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für MX3 Diagnostics Connector in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Screenshot: Blatt „Unternehmensanwendungen“.](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **MX3 Diagnostics Connector** aus.

    ![MX3 Diagnostics Connector-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot: Registerkarte „Bereitstellung“ und Pfad im Programm.](common/provisioning.png)

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot: Registerkarte „Bereitstellung“ mit der Option „Automatisch“.](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Werte für die Mandanten-URL von MX3 Diagnostics Connector (https://scim.mx3.app) und das geheime Token ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit MX3 Diagnostics Connector herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr MX3 Diagnostics Connector-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Screenshot: Textfeld zur Eingabe von Token und SCIM-URL](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Screenshot: Textfeld zur Eingabe der E-Mail-Adresse für Benachrichtigungen.](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit MX3 Diagnostics Connector synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit MX3 Diagnostics Connector synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Aktualisierungsvorgängen für den Abgleich der Benutzerkonten in MX3 Diagnostics Connector verwendet. Wenn Sie sich dafür entscheiden, das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) zu ändern, müssen Sie sicherstellen, dass die MX3 Diagnostics Connector-API das Filtern von Benutzern anhand dieses Attributs unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    |attribute|Typ|Unterstützung für das Filtern|
    |---|---|---|
    |userName|String|&check;
    |externalId|String|&check;
    |aktiv|Boolean|
    |name.givenName|String|
    |name.familyName|String|

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit MX3 Diagnostics Connector synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit MX3 Diagnostics Connector synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Aktualisierungsvorgängen für den Abgleich der Gruppen in MX3 Diagnostics Connector verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      |attribute|Typ|Unterstützung für das Filtern|
      |---|---|---|
      |displayName|String|&check;
      |externalId|String|
      |members|Verweis|

1. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für MX3 Diagnostics Connector zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

1. Legen Sie die Benutzer und/oder Gruppen fest, die in MX3 Diagnostics Connector bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

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