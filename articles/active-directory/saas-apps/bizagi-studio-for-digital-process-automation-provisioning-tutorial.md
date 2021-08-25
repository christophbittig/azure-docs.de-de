---
title: 'Tutorial: Konfigurieren von Bizagi Studio for Digital Process Automation für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Benutzerkonten aus Azure AD automatisch in Bizagi Studio for Digital Process Automation bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: thwimmer
ms.openlocfilehash: ac1a38662172e12cebfcab952ca076062f03f0ec
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327536"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Bizagi Studio for Digital Process Automation für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in Bizagi Studio for Digital Process Automation als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen für [Bizagi Studio for Digital Process Automation](https://www.bizagi.com/) bereit bzw. hebt deren Bereitstellung auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in Bizagi Studio for Digital Process Automation
> * Entfernen von Benutzern aus Bizagi Studio for Digital Process Automation, wenn der Zugriff nicht mehr erforderlich ist
> * Synchronisieren von Benutzerattributen zwischen Azure AD und Bizagi Studio for Digital Process Automation
> * [Einmaliges Anmelden (SSO)](./bizagi-studio-for-digital-process-automation-tutorial.md) für Bizagi Studio for Digital Process Automation (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) zum Konfigurieren der Bereitstellung, zum Beispiel Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder globaler Administrator. 
* Bizagi Studio for Digital Process Automation Version 11.2.4.2x oder höher.

## <a name="plan-your-provisioning-deployment"></a>Planen der Bereitstellung
Führen Sie für die Planung die folgenden Schritte aus:

1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und Bizagi Studio for Digital Process Automation zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Konfigurieren der Unterstützung der Bereitstellung mit Azure AD
Um Bizagi Studio for Digital Process Automation so zu konfigurieren, dass die Bereitstellung mit Azure AD unterstützt wird, führen Sie die folgenden Schritte aus:

1. Melden Sie sich in Ihrem Arbeitsportal als Benutzer mit **Administratorberechtigungen** an.

2. Navigieren Sie zu **Administrator** > **Sicherheit** > **OAuth 2.0-Anwendungen**.

   ![Screenshot von Bizagi, OAuth 2.0-Anwendungen sind hervorgehoben](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Wählen Sie **Hinzufügen**.
4. Wählen Sie **Bearertoken** als **Gewährungstyp** aus. Wählen Sie **API** und **USER SYNC** als **zulässigen Bereich** aus. Klicken Sie dann auf **Speichern**.

   ![Screenshot der Anwendungsregistrierung, „Gewährungstyp“ und „Zulässiger Bereich“ sind hervorgehoben](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Kopieren und speichern Sie den **geheimen Clientschlüssel**. Der Wert des geheimen Clientschlüssels wird im Azure-Portal für Ihre Bizagi Studio for Digital Process Automation-Anwendung auf der Registerkarte **Bereitstellung** im Feld **Geheimes Token** eingegeben.

   ![Screenshot von OAuth, der geheime Clientschlüssel ist hervorgehoben](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Hinzufügen der Anwendung aus dem Azure AD-Katalog

Um mit der Verwaltung der Bereitstellung für Bizagi Studio for Digital Process Automation zu beginnen, fügen Sie die App aus dem Azure AD-Anwendungskatalog hinzu. Wenn Sie Bizagi Studio for Digital Process Automation bereits für das einmalige Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Für das anfängliche Testen der Integration sollten Sie jedoch eine separate App erstellen. Weitere Informationen finden Sie unter [Quickstart: Hinzufügen einer Anwendung zu Ihrem Azure Active Directory (Azure AD)-Mandanten](../manage-apps/add-application-portal.md). 

## <a name="define-who-is-in-scope-for-provisioning"></a>Definieren des Geltungsbereichs für die Bereitstellung 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen für den Benutzer und/oder die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie den Bereich anhand der Zuweisung festlegen, führen Sie die unter [Zuweisen von Benutzern und Gruppen für eine App und Aufheben ihrer Zuweisung mit der Graph-API](../manage-apps/assign-user-or-group-access-portal.md) beschriebenen Schritte aus, um der Anwendung Benutzer und Gruppen zuzuweisen. Wenn Sie den Bereich ausschließlich anhand der Attribute des Benutzers oder der Gruppe festlegen, können Sie einen Bereichsfilter verwenden. Weitere Informationen finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Beachten Sie bei der Definition von Bereichen die folgenden Punkte:

* Beim Zuweisen von Benutzern und Gruppen zu Bizagi Studio for Digital Process Automation müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht wirklich berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="configure-automatic-user-provisioning"></a>Konfigurieren der automatischen Benutzerbereitstellung 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und Gruppen erläutert. Dieser Vorgang erfolgt in Ihrer Test-App anhand von Benutzer- und Gruppenzuweisungen in Azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Bizagi Studio for Digital Process Automation in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Screenshot des Azure-Portals, die Einträge „Unternehmensanwendungen“ und „Alle Anwendungen“ sind hervorgehoben](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Anwendung **Bizagi Studio for Digital Process Automation** aus.

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Verwaltungsoptionen, die Registerkarte „Bereitstellung“ ist hervorgehoben](common/provisioning.png)

4. Legen Sie **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot des Steuerelements „Bereitstellungsmodus“, „Automatisch“ ist hervorgehoben](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Mandanten-URL und das geheime Token für Bizagi Studio for Digital Process Automation ein. 

      * **Mandanten-URL**: Geben Sie den Bizagi-SCIM-Endpunkt mit folgender Struktur ein: `<Your_Bizagi_Project>/scim/v2/`.
         Beispiel: `https://my-company.bizagi.com/scim/v2/`.

      * **Geheimes Token**: Dieser Wert wird im weiter oben in diesem Artikel beschriebenen Schritt abgerufen.

      Wählen Sie **Verbindung testen** aus, um sicherzustellen, dass Azure AD eine Verbindung mit Bizagi Studio for Digital Process Automation herstellen kann. Wenn keine Verbindung möglich ist, stellen Sie sicher, dass Ihr Bizagi Studio for Digital Process Automation-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

   ![Screenshot mit den Administratoranmeldeinformationen, die Option „Verbindung testen“ ist hervorgehoben](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie unter **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Wählen Sie die Option **Bei Fehler E-Mail-Benachrichtigung senden** aus.

    ![Screenshot der Optionen für „Benachrichtigungs-E-Mail“](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Bizagi Studio for Digital Process Automation synchronisieren** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Bizagi Studio for Digital Process Automation synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Updatevorgängen für den Abgleich der Benutzerkonten in Bizagi Studio for Digital Process Automation verwendet. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern, müssen Sie sicherstellen, dass die Bizagi Studio for Digital Process Automation-API das Filtern von Benutzern anhand dieses Attributs unterstützt. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

   |Attribut|Typ|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |aktiv|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|

   Sie können benutzerdefinierte Erweiterungsattribute hinzufügen, indem Sie zu **Erweiterte Optionen anzeigen > Attributliste für Bizagi bearbeiten** navigieren. Den benutzerdefinierten Erweiterungsattributen muss das Präfix **urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:** vorangestellt sein. Wenn ein benutzerdefiniertes Erweiterungsattribut z. B. **IdentificationNumber** lautet, muss das Attribut in folgender Form hinzugefügt werden: **urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:IdentificationNumber**. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.
   
    ![Bearbeiten Sie die Attributliste.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Weitere Informationen zum Hinzufügen benutzerdefinierter Attribute finden Sie unter [Anpassen von Anwendungsattributen](../app-provisioning/customize-application-attributes.md).

> [!NOTE]
> Es werden nur Basistypeigenschaften unterstützt (z. B. String, Integer, Boolean, DateTime usw.). Mit parametrischen Tabellen oder mehreren Typen verknüpfte Eigenschaften werden noch nicht unterstützt.

10. Anleitungen zum Konfigurieren von Bereichsfiltern finden Sie im [Tutorial: Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Bizagi Studio for Digital Process Automation zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Screenshot des Umschalters für den Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Definieren Sie die Benutzer und Gruppen, die Sie für Bizagi Studio for Digital Process Automation bereitstellen möchten. Wählen Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Screenshot der Bereichsoptionen](common/provisioning-scope.png)

13. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Screenshot des Steuerelements „Speichern“](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

## <a name="monitor-your-deployment"></a>Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

- Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
- Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
- Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand befindet, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen hierzu finden Sie unter [Anwendungsbereitstellung im Quarantänestatus](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)