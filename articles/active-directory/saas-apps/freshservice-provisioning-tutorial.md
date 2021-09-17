---
title: 'Tutorial: Konfigurieren von Freshservice Provisioning für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie Benutzerkonten aus Azure AD für Freshservice Provisioning automatisch bereitstellen bzw. die Bereitstellung aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: e03ec65a-25ef-4c91-a364-36b2f007443c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: thwimmer
ms.openlocfilehash: 2f5ee2257652bdfa07081a558a8723c334c11ed9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327205"
---
# <a name="tutorial-configure-freshservice-provisioning-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Freshservice Provisioning für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Freshservice Provisioning als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer für [Freshservice Provisioning](https://effy.co.in/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Freshservice Provisioning
> * Löschen von Benutzern in Freshservice Provisioning, wenn sie keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Freshservice Provisioning

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein [Freshservice-Konto](https://www.freshservice.com) mit Berechtigungen als Organisationsadministrator.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) einbezogen werden soll.
3. Bestimmen Sie, welche Daten [zwischen Azure AD und Freshservice Provisioning zugeordnet werden sollen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-freshservice-provisioning-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von Freshservice Provisioning für die Bereitstellung mit Azure AD

1. Installieren Sie in Ihrem Freshservice-Konto die App **Azure Provisioning (SCIM)** aus dem Marketplace, indem Sie zu **Freshservice Admin** > **Apps** > **Get Apps** (Freshservice-Verwaltung> Apps > Apps abrufen) navigieren.
2. Geben Sie auf dem Konfigurationsbildschirm Ihre **Freshservice-Domäne** (z. B. `acme.freshservice.com`) und den **API-Schlüssel für den Organisationsadministrator** an.
3. Klicken Sie auf **Weiter**.
4. Markieren und kopieren Sie das **Bearertoken**. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ für Ihre Freshservice Provisioning-Anwendung in das Feld **Geheimes Token** eingegeben.
5. Klicken Sie auf **Installieren**, um die Installation durchzuführen.
6. Die **Mandanten-URL** lautet `https://scim.freshservice.com/scim/v2`. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ für Ihre Freshservice Provisioning-Anwendung in das Feld **Mandanten-URL** eingegeben.

## <a name="step-3-add-freshservice-provisioning-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von Freshservice Provisioning aus dem Azure AD Anwendungskatalog

Fügen Sie Freshservice Provisioning aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in Freshservice Provisioning zu beginnen. [Hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen des Benutzers festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer zuweisen. Wenn Sie nur anhand der Benutzerattribute auswählen möchten, wer bereitgestellt wird, können Sie den [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern zu Freshservice Provisioning müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern zur App steuern. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-freshservice-provisioning"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für Freshservice Provisioning 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern in der Freshservice Provisioning-App auf der Grundlage von Benutzerzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-freshservice-provisioning-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Freshservice Provisioning in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Freshservice Provisioning** aus.

    ![Freshservice Provisioning-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Mandanten-URL und das geheime Token für Freshservice Provisioning ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Freshservice Provisioning herstellen kann. Vergewissern Sie sich bei einem Verbindungsfehler, dass Ihr Freshservice Provisioning-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Freshservice Provisioning synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Freshservice Provisioning synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Freshservice Provisioning für Updatevorgänge verwendet. Wenn Sie das [übereinstimmende Zielattribut](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) ändern möchten, müssen Sie sicherstellen, dass die Freshservice Provisioning-API das Filtern von Benutzern anhand dieses Attributs unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |aktiv|Boolean|
   |emails[type eq "work"].value|String|
   |displayName|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |addresses[type eq "work"].formatted|String|
   |locale|String|
   |title|String|
   |timezone|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Verweis|
   |urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:isAgent|String|

> [!NOTE]
> Ihrem Schema können benutzerdefinierte Erweiterungsattribute hinzugefügt werden, um die Anforderungen Ihrer Anwendung zu erfüllen, indem Sie die folgenden Schritte ausführen:
> * Wählen Sie unter „Zuordnungen“ **Azure Active Directory-Benutzer bereitstellen** aus.
> * Wählen Sie am unteren Rand der Seite die Option **Erweiterte Optionen anzeigen** aus.
> * Wählen Sie **Attributliste für Freshservice bearbeiten** aus.
> * Geben Sie unten in der Attributliste Informationen zum benutzerdefinierten Attribut in den angezeigten Feldern ein. Der urn-Namespace des benutzerdefinierten Attributs muss dem Muster folgen (siehe folgendes Beispiel). **CustomAttribute** kann den Anforderungen Ihrer Anwendung entsprechend angepasst werden, z. B. urn:ietf:params:scim:schemas:extension:freshservice:2.0:User:**isAgent**.
> * Wählen Sie den entsprechenden Datentyp für das benutzerdefinierte Attribut aus, und klicken Sie auf **Speichern**.
> * Navigieren Sie zurück zum Bildschirm „Standardzuordnungen“, und klicken Sie auf **Neue Zuordnung hinzufügen**. Die benutzerdefinierten Attribute werden in der Dropdownliste **Zielattribut** angezeigt.

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Freshservice Provisioning zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer fest, die in Freshservice Provisioning bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Mit diesem Vorgang wird der erste Synchronisierungszyklus für alle Benutzer gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../manage-apps/check-status-user-account-provisioning.md)
