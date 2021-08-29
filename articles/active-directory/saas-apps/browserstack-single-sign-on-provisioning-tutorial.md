---
title: 'Tutorial: Konfigurieren von BrowserStack Single Sign-On für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD für BrowserStack Single Sign-On automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 39999abc-e4a2-4058-81e0-bf88182f8864
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: thwimmer
ms.openlocfilehash: 5e3d33775fbf342f328cb4260607a540930ff22d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327294"
---
# <a name="tutorial-configure-browserstack-single-sign-on-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von BrowserStack Single Sign-On für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in BrowserStack Single Sign-On als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer für [BrowserStack Single Sign-On](https://www.browserstack.com) bereit bzw. hebt die Bereitstellung von Benutzern auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in BrowserStack Single Sign-On
> * Entfernen von Benutzern in BrowserStack Single Sign-On, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und BrowserStack Single Sign-On Single Sign-On
> * [Einmaliges Anmelden](./browserstack-single-sign-on-tutorial.md) bei BrowserStack – einmaliges Anmelden (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z. B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* Ein Benutzerkonto in BrowserStack mit Berechtigungen als **Besitzer**.
* Ein [Enterprise-Plan](https://www.browserstack.com/pricing) mit BrowserStack. 
* Integration des [einmaligen Anmeldens](https://www.browserstack.com/docs/enterprise/single-sign-on/azure-ad) in BrowserStack (obligatorisch).

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und BrowserStack Single Sign-On zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-browserstack-single-sign-on-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von BrowserStack Single Sign-On für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich bei [BrowserStack](https://www.browserstack.com/users/sign_in) als Benutzer mit **Besitzerberechtigungen** an.

2. Navigieren Sie zu **Konto** -> **Einstellungen und Berechtigungen**. Wählen Sie die Registerkarte **Sicherheit** .

3. Klicken Sie unter **Automatische Benutzerbereitstellung** auf **Konfigurieren**.

    ![Einstellungen](media/browserstack-single-sign-on-provisioning-tutorial/configure.png)

4. Wählen Sie die Benutzerattribute aus, die Sie über Azure AD steuern möchten, und klicken Sie auf **Bestätigen**.

    ![Benutzer](media/browserstack-single-sign-on-provisioning-tutorial/attributes.png)

5. Kopieren Sie die Werte für **Mandanten-URL** und **Geheimes Token**. Diese Werte werden in das Feld „Mandanten-URL“ und „Geheimes Token“ auf der Registerkarte „Bereitstellung“ Ihrer BrowserStack Single Sign-On-Anwendung im Azure-Portal eingegeben. Klicken Sie auf **Fertig**.

    ![Autorisierung](media/browserstack-single-sign-on-provisioning-tutorial/credential.png)

6. Ihre Bereitstellungskonfiguration wurde auf BrowserStack gespeichert. **Aktivieren** Sie die Benutzerbereitstellung in BrowserStack, sobald **die Bereitstellungseinrichtung auf Azure AD** abgeschlossen ist, um zu verhindern, dass neue Benutzer aus dem [Konto](https://www.browserstack.com/accounts/manage-users) bei BrowserStack eingeladen werden. 

    ![Konto](media/browserstack-single-sign-on-provisioning-tutorial/enable.png)
    
## <a name="step-3-add-browserstack-single-sign-on-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von BrowserStack Single Sign-On aus dem Azure AD-Anwendungskatalog

Fügen Sie BrowserStack Single Sign-On aus dem Azure AD-Anwendungskatalog hinzu, um die Bereitstellung für BrowserStack Single Sign-On zu verwalten. Wenn Sie zuvor BrowserStack Single Sign-On für SSO eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen des Benutzers festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer zuweisen. Wenn Sie nur anhand der Benutzerattribute auswählen möchten, wer bereitgestellt wird, können Sie den [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern zu BrowserStack Single Sign-On müssen Sie eine andere Rolle als den **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern zur App steuern. Wenn der Bereich auf alle Benutzer festgelegt ist, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-browserstack-single-sign-on"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung für BrowserStack Single Sign-On 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern anhand von Benutzerzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-browserstack-single-sign-on-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für BrowserStack Single Sign-On in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **BrowserStack Single Sign-On** aus.

    ![Der Link für BrowserStack Single Sign-On in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Registerkarte „Bereitstellung“, Bereitstellungsmodus „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administrator-Anmeldeinformationen** die abgerufenen Werte für „Mandanten-URL“ und „Geheimes Token für BrowserStack Single Sign-On ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit BrowserStack Single Sign-On herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Konto bei BrowserStack Single Sign-On über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit BrowserStack Single Sign-On synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die aus Azure AD mit BrowserStack Single Sign-On synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in BrowserStack Single Sign-On für Updatevorgänge verwendet. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern möchten, müssen Sie sicherstellen, dass die BrowserStack Single Sign-On-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|Typ|Unterstützung für das Filtern|
   |---|---|--|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_role|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_team|String|
   |urn:ietf:params:scim:schemas:extension:Bstack:2.0:User:bstack_product|String|


10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für BrowserStack Single Sign-On zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer fest, die in BrowserStack Single Sign-On bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Mit diesem Vorgang wird der erste Synchronisierungszyklus für alle Benutzer gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

- Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
- Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
- Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Connector-Einschränkungen

* BrowserStack Single Sign-On unterstützt keine Gruppenbereitstellung.
* BrowserStack Single Sign-On erfordert, dass **emails[type eq "work"].value** und **userName** den gleichen Quellwert aufweisen.

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

* Schlagen Sie in den Tipps zur Problembehandlung [hier](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#troubleshooting) nach.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren von Attributzuordnungen in BrowserStack Single Sign-On](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad)
* [Einrichten und Aktivieren der automatischen Benutzerbereitstellung in BrowserStack](https://www.browserstack.com/docs/enterprise/auto-user-provisioning/azure-ad#setup-and-enable-auto-user-provisioning)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)