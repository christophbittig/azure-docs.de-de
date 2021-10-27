---
title: 'Tutorial: Konfigurieren von G Suite für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Benutzerkonten aus Azure AD für G Suite automatisch bereitstellen und die Bereitstellung wieder aufheben.
services: active-directory
author: twimmers
writer: twimmers
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: thwimmer
ms.openlocfilehash: fca85673ba31b71eda9e9b4242062093ae588aed
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046979"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von G Suite für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie sowohl in G Suite als auch in Azure Active Directory (Azure AD) ausführen müssen, um die automatische Benutzerbereitstellung zu konfigurieren. Nach der Konfiguration stellt Azure AD mithilfe des Azure AD-Bereitstellungsdiensts automatisch Benutzer und Gruppen für [G Suite](https://gsuite.google.com/) bereit bzw. hebt die Bereitstellung von Benutzern und Gruppen auf. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Unterstützte Funktionen
> [!div class="checklist"]
> * Erstellen von Benutzern in G Suite
> * Entfernen von Benutzern aus G Suite, wenn diese keinen Zugriff mehr benötigen
> * Synchronisieren von Benutzerattributen zwischen Azure AD und G Suite
> * Bereitstellen von Gruppen und Gruppenmitgliedschaften in G Suite
> * [Einmaliges Anmelden](./google-apps-tutorial.md) bei G Suite (empfohlen)

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* [Azure AD-Mandant](../develop/quickstart-create-new-tenant.md) 
* Ein Benutzerkonto in Azure AD mit der [Berechtigung](../roles/permissions-reference.md) für die Konfiguration von Bereitstellungen (z.B. Anwendungsadministrator, Cloudanwendungsadministrator, Anwendungsbesitzer oder Globaler Administrator). 
* [Einen G Suite-Mandanten](https://gsuite.google.com/pricing.html)
* Ein Benutzerkonto in G Suite mit Administratorberechtigungen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Schritt 1: Planen der Bereitstellung
1. Erfahren Sie, [wie der Bereitstellungsdienst funktioniert](../app-provisioning/user-provisioning.md).
2. Bestimmen Sie, wer [in den Bereitstellungsbereich](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) einbezogen werden soll.
3. Legen Sie fest, welche Daten [zwischen Azure AD und G Suite zugeordnet werden sollen](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Schritt 2: Konfigurieren von G Suite für die Unterstützung der Bereitstellung mit Azure AD

Bevor Sie G Suite für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in G Suite die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich bei der [G Suite-Verwaltungskonsole](https://admin.google.com/) mit Ihrem Administratorkonto an, und wählen Sie **Sicherheit** aus. Sollte der Link nicht zu sehen sein, befindet er sich möglicherweise unter dem Menü **Weitere Steuerelemente** am unteren Bildschirmrand.

    ![G Suite, Sicherheit](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. Wählen Sie auf der Seite **Sicherheit** die Option **API-Referenz** aus.

    ![G Suite, API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Wählen Sie **API-Zugriff aktivieren** aus.

    ![G Suite, API aktiviert](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Für jeden Benutzer, den Sie für G Suite bereitstellen möchten, **muss** der Benutzername in Azure AD an eine benutzerdefinierte Domäne gebunden werden. Beispielsweise werden Benutzernamen wie bob@contoso.onmicrosoft.com von G Suite nicht akzeptiert. bob@contoso.com ist hingegen zulässig. Sie können die Domäne eines vorhandenen Benutzers ändern, indem Sie die [hier](../fundamentals/add-custom-domain.md) zu findenden Anleitungen befolgen.

4. Nachdem Sie Ihre gewünschten benutzerdefinierten Domänen mit Azure AD hinzugefügt und überprüft haben, müssen Sie sie erneut mit G Suite überprüfen. Zum Überprüfen von Domänen in G Suite verwenden Sie die folgenden Schritte:

    a. Wählen Sie in der [G Suite-Verwaltungskonsole](https://admin.google.com/) die Option **Domänen** aus.

    ![G Suite, Domänen](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Wählen Sie **Domäne oder Domänenalias hinzufügen** aus.

    ![G Suite, Domänen hinzufügen](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Wählen Sie **Andere Domäne hinzufügen** aus, und geben Sie dann den Namen der Domäne ein, die Sie hinzufügen möchten.

    ![G Suite, Andere Domänen hinzufügen](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Wählen Sie **Weiter und Domänenbesitzrecht überprüfen** aus. Führen Sie dann die Schritte aus, mit denen Sie prüfen, ob Sie den Domänennamen besitzen. Umfassende Anweisungen zum Überprüfen Ihrer Domäne mit Google finden Sie unter [Überprüfen des Websitebesitzrechts](https://support.google.com/webmasters/answer/35179).

    e. Wiederholen Sie die obigen Schritte für alle weiteren Domänen, die Sie G Suite hinzufügen möchten.

5. Bestimmen Sie als Nächstes, welches Administratorkonto Sie zum Verwalten der Benutzerbereitstellung in G Suite verwenden möchten. Navigieren Sie zur Registerkarte **Administratorrollen**.

    ![G Suite, Administrator](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. Bearbeiten Sie für die **Administratorrolle** des Kontos die **Berechtigungen** für diese Rolle. Aktivieren Sie alle **Admin-API-Berechtigungen**, damit dieses Konto für die Bereitstellung verwendet werden kann.

    ![G Suite, Administratorberechtigungen](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Schritt 3: Hinzufügen von G Suite aus dem Azure AD-Anwendungskatalog

Fügen Sie G Suite aus dem Azure AD-Anwendungskatalog hinzu, um mit dem Verwalten der Bereitstellung in G Suite zu beginnen. Wenn Sie G Suite zuvor für einmaliges Anmelden (Single Sign-On, SSO) eingerichtet haben, können Sie dieselbe Anwendung verwenden. Es ist jedoch empfehlenswert, beim erstmaligen Testen der Integration eine separate App zu erstellen. [Hier](../manage-apps/add-application-portal.md) erfahren Sie mehr über das Hinzufügen einer Anwendung aus dem Katalog. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Schritt 4. Definieren der Benutzer für den Bereitstellungsbereich 

Mit dem Azure AD-Bereitstellungsdienst können Sie anhand der Zuweisung zur Anwendung oder aufgrund von Attributen für den Benutzer/die Gruppe festlegen, wer in die Bereitstellung einbezogen werden soll. Wenn Sie sich dafür entscheiden, anhand der Zuweisung festzulegen, wer für Ihre App bereitgestellt werden soll, können Sie der Anwendung mithilfe der folgenden [Schritte](../manage-apps/assign-user-or-group-access-portal.md) Benutzer und Gruppen zuweisen. Wenn Sie allein anhand der Attribute des Benutzers oder der Gruppe auswählen möchten, wer bereitgestellt wird, können Sie einen [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) beschriebenen Bereichsfilter verwenden. 

* Beim Zuweisen von Benutzern und Gruppen zu G Suite müssen Sie eine andere Rolle als **Standardzugriff** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen und in den Bereitstellungsprotokollen als „nicht effektiv berechtigt“ gekennzeichnet. Wenn für die Anwendung nur die Rolle „Standardzugriff“ verfügbar ist, können Sie das [Anwendungsmanifest aktualisieren](../develop/howto-add-app-roles-in-azure-ad-apps.md) und weitere Rollen hinzufügen. 

* Fangen Sie klein an. Testen Sie die Bereitstellung mit einer kleinen Gruppe von Benutzern und Gruppen, bevor Sie sie für alle freigeben. Wenn der Bereitstellungsbereich auf zugewiesene Benutzer und Gruppen festgelegt ist, können Sie dies durch Zuweisen von einem oder zwei Benutzern oder Gruppen zur App kontrollieren. Ist der Bereich auf alle Benutzer und Gruppen festgelegt, können Sie einen [attributbasierten Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) angeben. 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Schritt 5: Konfigurieren der automatischen Benutzerbereitstellung in G Suite 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in ServiceNow auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!NOTE]
> Weitere Informationen zum Directory-API-Endpunkt von G Suite finden Sie in der [Referenzdokumentation zur Directory-API](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für G Suite in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**. Benutzer müssen sich unter `portal.azure.com` anmelden und können nicht `aad.portal.azure.com` verwenden.

    ![Blatt „Unternehmensanwendungen“](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Blatt „Alle Anwendungen“](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **G Suite** aus.

    ![G Suite-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**. Klicken Sie auf **Erste Schritte**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

      ![Blatt „Erste Schritte“](./media/g-suite-provisioning-tutorial/get-started.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Klicken Sie im Abschnitt **Administratoranmeldeinformationen** auf **Autorisieren**. In einem neuen Browserfenster werden Sie zum Dialogfeld für die Google-Autorisierung geleitet.

      ![G Suite, Autorisierung](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Bestätigen Sie, dass Sie Azure AD die Berechtigungen erteilen möchten, Änderungen an Ihrem G Suite-Mandanten vorzunehmen. Wählen Sie **Akzeptieren** aus.

     ![G Suite, Mandantenberechtigungen](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit G Suite herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr G Suite-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang. Führen Sie dann erneut den Schritt **Autorisieren** aus.

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer bereitstellen** aus.

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit G Suite synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in G Suite für Updatevorgänge verwendet werden. Wenn Sie sich dafür entscheiden, das [übereinstimmende Zielattribut](../app-provisioning/customize-application-attributes.md) zu ändern, müssen Sie sicherstellen, dass die G Suite-API das Filtern von Benutzern anhand dieses Attributs unterstützt. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |Attribut|type|
   |---|---|
   |primaryEmail|String|
   |relations.[type eq "manager"].value|String|
   |name.familyName|String|
   |name.givenName|String|
   |Angehalten|String|
   |externalIds.[type eq "custom"].value|String|
   |externalIds.[type eq "organization"].value|String|
   |addresses.[type eq "work"].country|String|
   |addresses.[type eq "work"].streetAddress|String|
   |addresses.[type eq "work"].region|String|
   |addresses.[type eq "work"].locality|String|
   |addresses.[type eq "work"].postalCode|String|
   |emails.[type eq "work"].address|String|
   |organizations.[type eq "work"].department|String|
   |organizations.[type eq "work"].title|String|
   |phoneNumbers.[type eq "work"].value|String|
   |phoneNumbers.[type eq "mobile"].value|String|
   |phoneNumbers.[type eq "work_fax"].value|String|
   |emails.[type eq "work"].address|String|
   |organizations.[type eq "work"].department|String|
   |organizations.[type eq "work"].title|String|
   |phoneNumbers.[type eq "work"].value|String|
   |phoneNumbers.[type eq "mobile"].value|String|
   |phoneNumbers.[type eq "work_fax"].value|String|
   |addresses.[type eq "home"].country|String|
   |addresses.[type eq "home"].formatted|String|
   |addresses.[type eq "home"].locality|String|
   |addresses.[type eq "home"].postalCode|String|
   |addresses.[type eq "home"].region|String|
   |addresses.[type eq "home"].streetAddress|String|
   |addresses.[type eq "other"].country|String|
   |addresses.[type eq "other"].formatted|String|
   |addresses.[type eq "other"].locality|String|
   |addresses.[type eq "other"].postalCode|String|
   |addresses.[type eq "other"].region|String|
   |addresses.[type eq "other"].streetAddress|String|
   |addresses.[type eq "work"].formatted|String|
   |changePasswordAtNextLogin|String|
   |emails.[type eq "home"].address|String|
   |emails.[type eq "other"].address|String|
   |externalIds.[type eq "account"].value|String|
   |externalIds.[type eq "custom"].customType|String|
   |externalIds.[type eq "customer"].value|String|
   |externalIds.[type eq "login_id"].value|String|
   |externalIds.[type eq "network"].value|String|
   |gender.type|String|
   |GeneratedImmutableId|String|
   |Bezeichner|String|
   |ims.[type eq "home"].protocol|String|
   |ims.[type eq "other"].protocol|String|
   |ims.[type eq "work"].protocol|String|
   |includeInGlobalAddressList|String|
   |ipWhitelisted|String|
   |organizations.[type eq "school"].costCenter|String|
   |organizations.[type eq "school"].department|String|
   |organizations.[type eq "school"].domain|String|
   |organizations.[type eq "school"].fullTimeEquivalent|String|
   |organizations.[type eq "school"].location|String|
   |organizations.[type eq "school"].name|String|
   |organizations.[type eq "school"].symbol|String|
   |organizations.[type eq "school"].title|String|
   |organizations.[type eq "work"].costCenter|String|
   |organizations.[type eq "work"].domain|String|
   |organizations.[type eq "work"].fullTimeEquivalent|String|
   |organizations.[type eq "work"].location|String|
   |organizations.[type eq "work"].name|String|
   |organizations.[type eq "work"].symbol|String|
   |OrgUnitPath|String|
   |phoneNumbers.[type eq "home"].value|String|
   |phoneNumbers.[type eq "other"].value|String|
   |websites.[type eq "home"].value|String|
   |websites.[type eq "other"].value|String|
   |websites.[type eq "work"].value|String|
   

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen bereitstellen** aus.

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit G Suite synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden verwendet, um die Gruppen in G Suite für Updatevorgänge abzugleichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

      |Attribut|type|
      |---|---|
      |email|String|
      |Members|String|
      |name|String|
      |description|String|

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für G Suite zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in G Suite bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird der erstmalige Synchronisierungszyklus für alle Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der erste Zyklus dauert länger als nachfolgende Zyklen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird.

> [!NOTE]
> Wenn die Benutzer bereits über ein persönliches Konto/Endbenutzerkonto mit der E-Mail-Adresse des Azure AD-Benutzers verfügen, tritt möglicherweise ein Problem auf, das behoben werden kann, indem vor dem Ausführen der Verzeichnissynchronisierung das Google-Übertragungstool verwendet wird.

## <a name="step-6-monitor-your-deployment"></a>Schritt 6: Überwachen der Bereitstellung
Nachdem Sie die Bereitstellung konfiguriert haben, können Sie mit den folgenden Ressourcen die Bereitstellung überwachen:

1. Mithilfe der [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md) können Sie ermitteln, welche Benutzer erfolgreich bzw. nicht erfolgreich bereitgestellt wurden.
2. Anhand der [Fortschrittsleiste](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) können Sie den Status des Bereitstellungszyklus überprüfen und den Fortschritt der Bereitstellung verfolgen.
3. Wenn sich die Bereitstellungskonfiguration in einem fehlerhaften Zustand zu befinden scheint, wird die Anwendung unter Quarantäne gestellt. Weitere Informationen zu den verschiedenen Quarantänestatus finden Sie [hier](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Änderungsprotokoll

* 17.10.2020: Unterstützung für zusätzliche G Suite-Benutzerattribute und -Gruppenattribute hinzugefügt
* 17.10.2020: Die Namen der G Suite-Zielattribute wurden so aktualisiert, dass sie den [hier](https://developers.google.com/admin-sdk/directory) definierten Angaben entsprechen.
* 17.10.2020: Standardattributzuordnungen aktualisiert
* 18.03.2021: Für alle neuen Benutzer wird jetzt die E-Mail-Adresse des Vorgesetzten anstelle der ID synchronisiert. Für alle vorhandenen Benutzer, für die der Vorgesetzte in Form einer ID bereitgestellt wurde, können Sie über [Microsoft Graph](/graph/api/synchronization-synchronizationjob-restart?preserve-view=true&tabs=http&view=graph-rest-beta) einen Neustart mit dem Bereich „vollständig“ durchführen, um sicherzustellen, dass die E-Mail bereitgestellt wird. Diese Änderung wirkt sich nur auf den GSuite-Bereitstellungsauftrag und nicht auf den älteren mit „Goov2OutDelta“ beginnenden Auftrag aus. Beachten Sie, dass die E-Mail-Adresse des Vorgesetzten bei der erstmaligen Erstellung des Benutzers oder bei einer Änderung des Vorgesetzten bereitgestellt wird. Die E-Mail-Adresse des Vorgesetzten wird nicht bereitgestellt, wenn der Vorgesetzte seine E-Mail-Adresse ändert. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)