---
title: 'Tutorial: Konfigurieren von Asana für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD für Asana automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 274810a2-bd74-4500-95f1-c720abf23541
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: thwimmer
ms.openlocfilehash: fca7efc24770b9a920c88082566d0e7509ffe513
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990421"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Asana für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Asana als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer und Gruppen für [Asana](https://www.asana.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Asana
> * Entfernen von Benutzern aus Asana, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Asana
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in Asana
> * [Einmaliges Anmelden](asana-tutorial.md) bei Asana (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* Einen Asana-Mandanten, für den mindestens der [Enterprise](https://www.asana.com/pricing)-Tarif aktiviert ist
* Ein Benutzerkonto in Asana mit Administratorrechten

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf der [Asana-API](https://asana.com/developers/api-reference/users), die für Asana verfügbar ist.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und Asana zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-asana-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Asana für die Unterstützung der Bereitstellung mit Azure AD
 > [!TIP]
 > Um das SAML-basierte einmalige Anmelden für Asana zu aktivieren, befolgen Sie hierzu die Anweisungen im Azure-Portal. Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="generate-secret-token-in-asana"></a>Generieren Sie ein geheimes Token in Asana.

* Melden Sie sich mit Ihrem Administratorkonto bei [Asana](https://app.asana.com/) an.
* Klicken Sie auf der oberen Leiste auf das Profilfoto, und wählen Sie die aktuellen Einstellungen für den Organisationsnamen aus.
* Navigieren Sie zur Registerkarte **Service Accounts** (Dienstkonten).
* Wählen Sie **Add Service Account** (Dienstkonto hinzufügen) aus.
* Aktualisieren Sie nach Bedarf die Abschnitte **Name** und **About** (Infos) sowie das Profilfoto. Kopieren Sie das unter **Token** aufgeführte Token, und wählen Sie „Save Changes“ (Änderungen speichern) aus.

## <a name="step-3-add-asana-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Asana aus dem Azure AD-Anwendungskatalog

Fügen Sie Asana aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Asana zu beginnen. Wenn Sie Asana bereits für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie die gleiche Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden.

* Beim Zuweisen von Benutzern und Gruppen zu Asana müssen Sie eine andere Rolle als den **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-asana"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in Asana 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Asana auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-asana-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Asana in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste den Eintrag **Asana** aus.

    ![Asana-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Mandanten-URL und das geheime Token für Asana ein, die bzw. das Sie von Asana erhalten haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Asana herstellen kann. Wenn die Verbindung nicht hergestellt werden kann, wenden Sie sich an Asana, um die Einrichtung Ihres Kontos zu überprüfen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Asana synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die aus Azure AD mit Asana synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Asana für Updatevorgänge verwendet werden. Wenn Sie sich dafür entscheiden, das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) zu ändern, müssen Sie sicherstellen, dass die Asana-API das Filtern von Benutzern anhand dieses Attributs unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|Erforderlich für Asana|
   |---|---|---|---|
   |userName|String|&check;|&check;|   
   |aktiv|Boolean|||
   |name.formatted|String|||
   |preferredLanguage|String|||
   |title|String|||
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|||


1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Asana synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit Asana synchronisiert werden. Mit den als **übereinstimmende** Eigenschaften ausgewählten Attribute werden die Gruppen in Asana bei Updatevorgängen abgeglichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      |attribute|type|Unterstützung für das Filtern|Erforderlich für Asana|
      |---|---|---|---|
      |displayName|String|&check;|&check;      
      |members|Verweis|||

1. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für Asana zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

1. Legen Sie die Benutzer und Gruppen fest, die in Asana bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die entsprechenden Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

1. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Änderungsprotokoll

* 06.11.2021: Unterstützung für **externalId, name.givenName und name.familyName** entfernt Unterstützung für **preferredLanguage, title und urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division** hinzugefügt. **Gruppenbereitstellung** aktiviert

## <a name="more-resources"></a>Weitere Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)