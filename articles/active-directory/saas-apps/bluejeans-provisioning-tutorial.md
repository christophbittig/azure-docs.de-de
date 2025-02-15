---
title: 'Tutorial: Konfigurieren von BlueJeans für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in BlueJeans konfigurieren.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: fc1518b169f3da8b34e97c999cd23e201aea59ce
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015673"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von BlueJeans für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in BlueJeans als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD automatisch mithilfe des Azure AD-Bereitstellungsdiensts Benutzer für [BlueJeans](https://www.bluejeans.com) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in BlueJeans
> * Entfernen von Benutzern aus BlueJeans, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und BlueJeans
> * [Einmaliges Anmelden](./bluejeans-tutorial.md) bei BlueJeans (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein BlueJeans-Mandant, für den mindestens der Plan [Mein Unternehmen](https://www.bluejeans.com) aktiviert ist
* Ein Benutzerkonto in BlueJeans mit Administratorrechten
* In BlueJeans Enterprise aktivierte SCIM-Bereitstellung

> [!NOTE]
> Die Integration der Azure AD-Bereitstellung basiert auf der [BlueJeans-API](https://BlueJeans.github.io/developer), die für BlueJeans-Teams mit dem Standard-Tarif oder einem höheren Tarif zur Verfügung steht.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und BlueJeans zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von BlueJeans für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich bei der BlueJeans-Verwaltungskonsole an. Navigieren Sie zu „Group Settings“ > „Security“ (Gruppeneinstellungen > Sicherheit).
2. Wählen Sie **Single Sign On** (Einmaliges Anmelden) und **Configure Now** (Jetzt konfigurieren) aus.

    ![now](./media/bluejeans-provisioning-tutorial/configure.png)

3. Wählen Sie im Fenster **Provision & Integration** (Bereitstellung und Integration) die Option **Create and manage user accounts through IDP** (Benutzerkonten über IDP erstellen und verwalten), und klicken Sie auf **GENERATE TOKEN** (TOKEN GENERIEREN).

    ![generate](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Kopieren und speichern Sie das Token. 
5. Die BlueJeans-Mandanten-URL ist `https://api.bluejeans.com/v2/scim`. Die **Mandanten-URL** und das **geheime Token** aus dem vorherigen Schritt werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer BlueJeans-Anwendung eingegeben.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von BlueJeans aus dem Azure AD-Anwendungskatalog

Fügen Sie BlueJeans aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in BlueJeans zu beginnen. Wenn Sie BlueJeans zuvor für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen des Benutzers festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer zuweisen. Wenn Sie nur anhand der Benutzerattribute auswählen möchten, wer bereitgestellt wird, können Sie den [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern zu BlueJeans müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern zur App steuern. Wenn der Bereich auf alle Benutzer festgelegt ist, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für BlueJeans

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern in Coda anhand von Benutzerzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für BlueJeans in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **BlueJeans** aus.

    ![BlueJeans-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Mandanten-URL und das geheime Token für BlueJeans ein, die Sie in Schritt 2 abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit BlueJeans herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr BlueJeans-Konto über Administratorrechte verfügt, und wiederholen Sie den Vorgang.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)


6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit BlueJeans synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit BlueJeans synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in BlueJeans für Updatevorgänge verwendet. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die BlueJeans-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

|attribute|Typ|Unterstützung für das Filtern|
|---|---|---|
|userName|String|&check;|
|aktiv|Boolean|
|title|String|
|emails[type eq "work"].value|String|
|name.givenName|String|
|name.familyName|String|
|phoneNumbers[type eq "work"].value|String|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für BlueJeans zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer fest, die in BlueJeans bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Connector-Einschränkungen

* In BlueJeans sind Benutzernamen mit mehr als 30 Zeichen nicht zulässig.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
