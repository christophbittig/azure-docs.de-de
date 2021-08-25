---
title: 'Tutorial: Konfigurieren von getAbstract für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory-Benutzerkonten automatisch in getAbstract bereitstellen und deren Bereitstellung wieder aufheben.
services: active-directory
documentationcenter: ''
author: twimmers
writer: twimmers
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: thwimmer
ms.openlocfilehash: 1c7278b0d8a0e9ed3d94e69d1ad5da34ab0bc854
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327198"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von getAbstract für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in getAbstract als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Bei der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen für [getAbstract](https://www.getabstract.com) bereit bzw. hebt deren Bereitstellung auf. Wichtige Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Was ist die automatisierte Bereitstellung von SaaS-App-Benutzern in Azure AD?](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Unterstützte Funktionen

> [!div class="checklist"]
> * Erstellen von Benutzern in getAbstract
> * Entfernen von Benutzern aus getAbstract, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und getAbstract
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in getAbstract
> * Aktivieren des [einmaligen Anmeldens (SSO)](getabstract-tutorial.md) für getAbstract (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Einen Azure AD-Mandanten](../develop/quickstart-create-new-tenant.md)
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) zum Konfigurieren der Bereitstellung, beispielsweise „Anwendungsadministrator“, „Cloudanwendungsadministrator“, „Anwendungsbesitzer“ oder „Globaler Administrator“
* Ein getAbstract-Mandant (getAbstract-Unternehmenslizenz)
* Einmaliges Anmelden (SSO), das für den Azure AD Mandanten und den getAbstract-Mandanten aktiviert ist.
* Genehmigung und Aktivierung von System for Cross-Domain Identity Management (SCIM) für getAbstract (E-Mail an b2b.itsupport@getabstract.com senden)

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung

1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
1. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
1. Legen Sie fest, welche Daten [zwischen Azure AD und getAbstract zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von getAbstract für die Unterstützung der Bereitstellung mit Azure AD

1. Melden Sie sich bei getAbstract an.
1. Klicken Sie oben rechts auf das Symbol „Einstellungen“ und dann auf die Option **My Central Admin** (Meine zentrale Verwaltung).

    ![Screenshot: getAbstract-Option „My Central Admin“ (Meine zentrale Verwaltung)](media/getabstract-provisioning-tutorial/my-account.png)

1. Suchen Sie die Option **SCIM Admin** (SCIM-Administrator), und wählen Sie sie aus.

    ![Screenshot: getAbstract-Option „SCIM Admin“ (SCIM-Administrator)](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Klicken Sie auf **Starten**.

    ![Screenshot: SCIM-Client-ID für getAbstract](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Wählen Sie **Generate new token**.

    ![Screenshot: SCIM-Token 1 für getAbstract](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Wenn Sie sich sicher sind, klicken Sie auf **Neues Token generieren**. Wählen Sie andernfalls **Abbrechen** aus.

    ![Screenshot: SCIM-Token 2 für getAbstract](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Klicken Sie auf das Symbol „In Zwischenablage kopieren“, oder wählen Sie das gesamte Token aus, und kopieren Sie es. Notieren Sie sich außerdem die Mandanten- bzw. Basis-URL `https://www.getabstract.com/api/scim/v2`. Diese Werte werden im Azure-Portal auf der Registerkarte **Bereitstellung** für Ihre getAbstract-Anwendung in den Dialogfeldern **Geheimes Token** und **Mandanten-URL** eingegeben.

    ![Screenshot: SCIM-Token 3 für getAbstract](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von getAbstract aus dem Azure AD-Anwendungskatalog

Fügen Sie getAbstract aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in getAbstract zu beginnen. Wenn Sie getAbstract zuvor für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es wird empfohlen, beim erstmaligen Testen der Integration eine separate App zu erstellen. Weitere Informationen zum Hinzufügen einer Anwendung aus der Galerie finden Sie in [dieser Schnellstartanleitung](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder anhand von Attributen für den Benutzer oder die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Sie können wie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschrieben einen Bereichsfilter verwenden, wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt werden soll.

* Wenn Sie getAbstract Benutzer und Gruppen zuweisen, müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht wirklich berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen.

* Fangen Sie klein an. Testen Sie die Bereitstellung mit wenigen Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie diese Option kontrollieren, indem Sie der App ein oder zwei Benutzer bzw. eine oder zwei Gruppen zuweisen. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben.

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in getAbstract

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Clarizen One anhand von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für getAbstract in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Screenshot: Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Liste der Anwendungen **getAbstract** aus.

    ![Screenshot: getAbstract-Link in der Liste der Anwendungen](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot: Registerkarte „Bereitstellung“](common/provisioning.png)

1. Legen Sie **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot mit auf „Automatisch“ festgelegtem Bereitstellungsmodus](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Informationen für die Optionen **Mandanten-URL** und **Geheimes Token** für getAbstract ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit getAbstract herstellen kann. Vergewissern Sie sich bei einem Verbindungsfehler, dass Ihr getAbstract-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Screenshot: Dialogfelder „Mandanten-URL“ und „Geheimes Token“](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Screenshot: Feld „Benachrichtigungs-E-Mail“](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Klicken Sie im Abschnitt **Zuordnungen** auf **Synchronize Azure Active Directory Users to getAbstract** (Azure Active Directory-Benutzer mit getAbstract synchronisieren).

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit getAbstract synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählte Attribute werden für den Abgleich der Benutzerkonten in getAbstract für Updatevorgänge verwendet werden. Wenn Sie das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) ändern, müssen Sie sicherstellen, dass die getAbstract-API das Filtern von Benutzern nach diesem Attribut unterstützt. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

   |Attribut|Typ|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |aktiv|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |preferredLanguage|String|

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit getAbstract synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit getAbstract synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden verwendet, um die Gruppen in getAbstract für Updatevorgänge abzugleichen. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    |Attribut|Typ|Unterstützung für das Filtern|
    |---|---|---|
    |displayName|String|&check;|
    |externalId|String|
    |members|Verweis|

1. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den im Tutorial [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) bereitgestellten Anleitungen.

1. Ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**, um den Azure AD-Bereitstellungsdienst für getAbstract zu aktivieren.

    ![Screenshot: Bereitstellungsstatus „Ein“](common/provisioning-toggle-on.png)

1. Definieren Sie die Benutzer oder Gruppen, die in getAbstract bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Screenshot: Bereitstellungsbereich](common/provisioning-scope.png)

1. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Screenshot der Schaltfläche „Speichern“](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung

Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

* Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
* Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
* Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zum Quarantänestatus finden Sie unter [Anwendungsbereitstellung im Quarantänestatus](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
