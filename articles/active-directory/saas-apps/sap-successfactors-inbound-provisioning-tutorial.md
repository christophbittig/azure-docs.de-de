---
title: 'Tutorial: Konfigurieren der eingehenden Bereitstellung von SuccessFactors in AD and Azure AD'
description: Erfahren Sie, wie Sie die eingehende Bereitstellung von SuccessFactors konfigurieren.
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: d4a1350999258b47db4a5188fa9eab92aa56e567
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604944"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Tutorial: Konfigurieren der Benutzerbereitstellung von SAP SuccessFactors in Active Directory 
In diesem Tutorial werden die Schritte vorgestellt, die Sie zum Bereitstellen von Benutzern aus SuccessFactors Employee Central in sowohl Active Directory (AD) als auch Azure AD ausführen müssen, wobei einige E-Mail-Adressen optional in SuccessFactors zurückgeschrieben werden. 

>[!NOTE]
>Verwenden Sie dieses Tutorial, wenn die Benutzer, die Sie aus SuccessFactors bereitstellen möchten, ein lokales AD-Konto und optional ein Azure AD-Konto benötigen. Wenn die Benutzer aus SuccessFactors nur Azure AD-Konten benötigen (reine Cloudbenutzer), informieren Sie sich im Tutorial zum [Konfigurieren der Benutzerbereitstellung von SAP SuccessFactors in Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md). 


## <a name="overview"></a>Übersicht

Der [Azure Active Directory-Benutzerbereitstellungsdienst](../app-provisioning/user-provisioning.md) ist mit [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) integriert, um den Identitätslebenszyklus von Benutzern zu verwalten. 

Die vom Azure AD-Benutzerbereitstellungsdienst unterstützten SuccessFactors-Workflows zur Benutzerbereitstellung ermöglichen die Automatisierung der folgenden Szenarien im Personalwesen und bei der Verwaltung des Identity Lifecycle Managements:

* **Einstellung neuer Mitarbeiter:** Wenn in SuccessFactors ein neuer Mitarbeiter hinzugefügt wird, wird in Active Directory, in Azure Active Directory und optional in Microsoft 365 sowie in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch ein Benutzerkonto erstellt, und die E-Mail-Adresse wird in SuccessFactors zurückgeschrieben.

* **Aktualisierung von Mitarbeiterattributen und -profilen:** Wenn in SuccessFactors ein Mitarbeiterdatensatz aktualisiert wird (beispielsweise der Name, Titel oder Vorgesetzte), wird das entsprechende Benutzerkonto in Active Directory, in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch aktualisiert.

* **Kündigung von Mitarbeitern:** Wenn einem Mitarbeiter in SuccessFactors gekündigt wird, wird das entsprechende Benutzerkonto in Active Directory, in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) automatisch deaktiviert.

* **Wiedereinstellung von Mitarbeitern:** Wenn ein Mitarbeiter in SuccessFactors erneut eingestellt wird, kann sein altes Konto in Active Directory, in Azure Active Directory und optional in Microsoft 365 und in [anderen, von Azure AD unterstützten SaaS-Anwendungen](../app-provisioning/user-provisioning.md) je nach Präferenz automatisch reaktiviert oder erneut bereitgestellt werden.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Für wen ist diese Benutzerbereitstellungslösung am besten geeignet?

Diese Lösung zur Benutzerbereitstellung von SuccessFactors in Active Directory eignet sich ideal für:

* Organisationen, die eine vordefinierte, cloudbasierte Lösung für die SuccessFactors-Benutzerbereitstellung verwenden möchten

* Organisationen, bei denen Benutzer direkt aus SuccessFactors in Active Directory bereitgestellt werden müssen

* Organisationen, bei denen Benutzer mithilfe von Daten bereitgestellt werden müssen, die aus [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) abgerufen werden

* Organisationen, bei denen Benutzer beim Einstellen, Verschieben und Verlassen nur auf Grundlage von Änderungsinformationen, die in [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) erkannt werden, mit einer oder mehreren Active Directory-Gesamtstrukturen, -Domänen und -Organisationseinheiten synchronisiert werden müssen

* Organisationen, die Microsoft 365 für E-Mails verwenden

## <a name="solution-architecture"></a>Lösungsarchitektur

In diesem Abschnitt wird die Lösungsarchitektur der End-to-End-Benutzerbereitstellung für häufige Hybridumgebungen beschrieben. Es gibt zwei zugehörige Flows:

* **Autoritativer Personaldatenfluss – von SuccessFactors in ein lokales Active Directory-Verzeichnis:** In diesem Flow treten mitarbeiterbezogene Ereignisse (z. B. Neueinstellungen, Wechsel, Kündigungen) zuerst in der Cloud in SuccessFactors Employee Central auf und werden dann über Azure AD und den Bereitstellungs-Agent in ein lokales Active Directory-Verzeichnis übertragen. Abhängig vom Ereignis kann dies dann in Active Directory zu Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgängen führen.
* **E-Mail-Rückschreibeflow – vom lokalen Active Directory-Verzeichnis zu SuccessFactors:** Nach Abschluss der Kontoerstellung in Active Directory wird das Konto über die Azure AD Connect-Synchronisierung mit Azure AD synchronisiert. Anschließend können die Attribute für die E-Mail-Adresse und den Benutzernamen in SuccessFactors zurückgeschrieben werden.

  ![Übersicht](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>End-to-End-Benutzerdatenfluss

1. Das Team der Personalabteilung führt Mitarbeitertransaktionen (Einstellungen/Wechsel/Kündigungen) in SuccessFactors Employee Central aus.
2. Der Azure AD-Bereitstellungsdienst führt geplante Synchronisierungen von Identitäten aus SuccessFactors EC aus und ermittelt Änderungen, die für eine Synchronisierung mit dem lokalen Active Directory verarbeitet werden müssen.
3. Der Azure AD-Bereitstellungsdienst ruft den lokalen Azure AD Connect-Bereitstellungs-Agent mit einer Anforderungsnutzlast auf, die die Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgänge für das AD-Konto enthält.
4. Der Azure AD Connect-Bereitstellungs-Agent verwendet ein Dienstkonto zum Hinzufügen/Aktualisieren von AD-Kontodaten.
5. Die Synchronisierungs-Engine von Azure AD Connect führt eine Deltasynchronisierung aus, um Updates in Active Directory zu pullen.
6. Die Active Directory-Updates werden mit Azure Active Directory synchronisiert.
7. Wenn die [SuccessFactors-Rückschreibe-App](sap-successfactors-writeback-tutorial.md) konfiguriert ist, schreibt sie das E-Mail-Attribut und den Benutzernamen zurück in SuccessFactors, sofern das verwendete Attribut übereinstimmt.

## <a name="planning-your-deployment"></a>Planen der Bereitstellung

Das Konfigurieren einer cloudbasierten Benutzerbereitstellung für die Personalabteilung aus SuccessFactors in Active Directory erfordert eine ausführliche Planung, die unter anderem die folgenden Aspekte abdecken sollte:
* Einrichtung des Azure AD Connect-Bereitstellungs-Agents 
* Anzahl der bereitzustellenden SuccessFactors-Apps für die Benutzerbereitstellung in AD
* Filter für Übereinstimmungs-ID, Attributzuordnung, Transformation und Bereich

Ausführliche Anleitungen zu diesen Themen finden Sie unter [Planen der HR-Cloudbereitstellung](../app-provisioning/plan-cloud-hr-provision.md). Weitere Informationen zu den unterstützten Entitäten, Verarbeitungsdetails und zum Anpassen der Integration für verschiedene HR-Szenarien finden Sie im [Referenzhandbuch zur Integration von SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md). 

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurieren von SuccessFactors für die Integration

Eine allgemeine Anforderung für alle SuccessFactors-Bereitstellungsconnectors besteht darin, dass sie Anmeldeinformationen eines SuccessFactors-Kontos mit den erforderlichen Berechtigungen zum Aufrufen der OData-APIs von SuccessFactors benötigen. In diesem Abschnitt werden die Schritte zum Erstellen des Dienstkontos in SuccessFactors und zum Erteilen der benötigten Berechtigungen beschrieben. 

* [Erstellen/Ermitteln des API-Benutzerkontos in SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Erstellen einer API-Berechtigungsrolle](#create-an-api-permissions-role)
* [Erstellen einer Berechtigungsgruppe für den API-Benutzer](#create-a-permission-group-for-the-api-user)
* [Erteilen der Berechtigungsrolle für die Berechtigungsgruppe](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Erstellen/Ermitteln des API-Benutzerkontos in SuccessFactors
Wenden Sie sich an das Administratorteam von SuccessFactors oder Ihren Implementierungspartner, um ein Benutzerkonto in SuccessFactors zu erstellen oder zu bestimmen, das zum Aufrufen der OData-APIs verwendet wird. Die Anmeldeinformationen (Benutzername und Kennwort) dieses Kontos sind für das Konfigurieren der Bereitstellungs-Apps in Azure AD erforderlich. 

### <a name="create-an-api-permissions-role"></a>Erstellen einer API-Berechtigungsrolle

1. Melden Sie sich bei SAP SuccessFactors mit einem Benutzerkonto an, das Zugriff auf das Admin Center hat.
1. Suchen Sie nach *Manage Permission Roles* (Berechtigungsrollen verwalten), und wählen Sie dann in den Suchergebnissen **Manage Permission Roles** (Berechtigungsrollen verwalten) aus.
  ![Verwalten von Berechtigungsrollen](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
1. Klicken Sie in der Liste „Permission Role“ (Berechtigungsrolle) auf **Create New** (Neue erstellen).
    > [!div class="mx-imgBorder"]
    > ![Erstellen einer neuen Berechtigungsrolle](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
1. Fügen Sie einen **Rollennamen** und eine **Beschreibung** für die neue Berechtigungsrolle hinzu. Der Name und die Beschreibung sollten darauf hindeuten, dass die Rolle für Berechtigungen zur API-Verwendung vorgesehen ist.
    > [!div class="mx-imgBorder"]
    > ![Details zur Berechtigungsrolle](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
1. Klicken Sie unter den Berechtigungseinstellungen auf **Permission...** (Berechtigung), scrollen Sie in der Berechtigungsliste nach unten, und klicken Sie auf **Manage Integration Tools** (Integrationstools verwalten). Aktivieren Sie das Kontrollkästchen für **Allow Admin to Access to OData API through Basic Authentication** (Administratoren den Zugriff auf OData-API über die Standardauthentifizierung erlauben).
    > [!div class="mx-imgBorder"]
    > ![Verwalten von Integrationstools](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
1. Scrollen Sie in demselben Feld nach unten, und wählen Sie **Employee Central API** aus. Fügen Sie wie unten gezeigt Berechtigungen für das Lesen und Bearbeiten über die OData-API hinzu. Wählen Sie die Bearbeitungsoption aus, wenn Sie dasselbe Konto für das Rückschreiben in ein SuccessFactors-Szenario verwenden möchten. 
    > [!div class="mx-imgBorder"]
    > ![Lese-/Schreibberechtigungen](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Navigieren Sie in demselben Feld für die Berechtigungen zu **Benutzerberechtigungen > Mitarbeiterdaten**, und überprüfen Sie die Attribute, die das Dienstkonto aus dem SuccessFactors-Mandanten lesen kann. Wenn Sie beispielsweise das Attribut *Username* aus SuccessFactors abrufen möchten, sollten Sie sich vergewissern, dass für dieses Attribut die Anzeigeberechtigung erteilt wurde. Überprüfen Sie analog dazu jedes Attribut auf die Anzeigeberechtigung. 

    > [!div class="mx-imgBorder"]
    > ![Berechtigungen für Mitarbeiterdaten](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >Eine vollständige Liste der Attribute, die von dieser Bereitstellungs-App abgerufen werden, finden Sie in der [SuccessFactors-Attributreferenz](../app-provisioning/sap-successfactors-attribute-reference.md).

1. Klicken Sie auf **Done** (Fertig). Klicken Sie auf **Änderungen speichern**.

### <a name="create-a-permission-group-for-the-api-user"></a>Erstellen einer Berechtigungsgruppe für den API-Benutzer

1. Suchen Sie im SuccessFactors Admin Center nach *Manage Permission Groups* (Berechtigungsgruppen verwalten), und wählen Sie in den Suchergebnissen dann **Manage Permission Groups** (Berechtigungsgruppen verwalten) aus.
    > [!div class="mx-imgBorder"]
    > ![Verwalten von Berechtigungsgruppen](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
1. Klicken Sie im Fenster „Manage Permission Groups“ (Berechtigungsgruppen verwalten) auf **Create New** (Neue erstellen).
    > [!div class="mx-imgBorder"]
    > ![Hinzufügen einer neuen Gruppe](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
1. Fügen Sie einen Gruppennamen für die neue Gruppe hinzu. Im Gruppennamen sollte angegeben werden, dass die Gruppe für API-Benutzer vorgesehen ist.
    > [!div class="mx-imgBorder"]
    > ![Name der Berechtigungsgruppe](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
1. Fügen Sie der Gruppe Mitglieder hinzu. Beispielsweise können Sie im Dropdownmenü „People Pool“ (Personenpool) die Option **Username** (Benutzername) auswählen und dann den Benutzernamen des API-Kontos eingeben, das für die Integration verwendet werden soll. 
    > [!div class="mx-imgBorder"]
    > ![Add group members](./media/sap-successfactors-inbound-provisioning/add-group-members.png) (Hinzufügen von Gruppenmitgliedern)
1. Klicken Sie auf **Done** (Fertig), um die Erstellung der Berechtigungsgruppe abzuschließen.

### <a name="grant-permission-role-to-the-permission-group"></a>Erteilen der Berechtigungsrolle für die Berechtigungsgruppe

1. Suchen Sie im SuccessFactors Admin Center nach *Manage Permission Roles* (Berechtigungsrollen verwalten), und wählen Sie in den Suchergebnissen dann **Manage Permission Roles** (Berechtigungsrollen verwalten) aus.
1. Wählen Sie in der Liste **Permission Role** (Berechtigungsrolle) die Rolle aus, die Sie für die Berechtigungen zur API-Verwendung erstellt haben.
1. Klicken Sie unter **Grant this role to...** (Rolle erteilen für) auf die Schaltfläche **Add...** (Hinzufügen).
1. Wählen Sie im Dropdownmenü die Option **Permission Group...** (Berechtigungsgruppe) aus, und klicken Sie dann auf **Select...** (Auswählen), um das Fenster „Groups“ (Gruppen) zu öffnen. Darin können Sie die oben erstellte Gruppe suchen und auswählen. 
    > [!div class="mx-imgBorder"]
    > ![Hinzufügen der Berechtigungsgruppe](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
1. Überprüfen Sie die erteilte Berechtigungsrolle für die Berechtigungsgruppe. 
    > [!div class="mx-imgBorder"]
    > ![Details zu Berechtigungsrolle und -gruppe](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
1. Klicken Sie auf **Änderungen speichern**.

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Konfigurieren der Benutzerbereitstellung aus SuccessFactors in Active Directory

Dieser Abschnitt enthält die Schritte zum Konfigurieren der Bereitstellung von Benutzerkonten aus SuccessFactors in jeder Active Directory-Domäne im Geltungsbereich Ihrer Integration.

* [Hinzufügen der Bereitstellungsconnector-App und Herunterladen des Bereitstellungs-Agents](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Installieren und Konfigurieren der lokalen Bereitstellungs-Agents](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurieren der Konnektivität zwischen SuccessFactors und Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Konfigurieren von Attributzuordnungen](#part-4-configure-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Teil 1: Hinzufügen der Bereitstellungsconnector-App und Herunterladen des Bereitstellungs-Agents

**So konfigurieren Sie die Bereitstellung aus SuccessFactors in Active Directory**

1. Besuchen Sie <https://portal.azure.com>.

2. Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie die Kategorie **Alle** aus.

5. Suchen Sie nach **SuccessFactors to Active Directory User Provisioning**, und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Ändern des **Modus** **Bereitstellung** in **Automatisch**

8. Klicken Sie auf das angezeigte Informationsbanner, um den Bereitstellungs-Agent herunterzuladen. 
   >[!div class="mx-imgBorder"]
   >![Herunterladen des Agents](./media/workday-inbound-tutorial/pa-download-agent.png "Bildschirm für das Herunterladen des Agents")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Teil 2: Installieren und Konfigurieren der lokalen Bereitstellungs-Agents

Um Active Directory lokal bereitzustellen, muss der Bereitstellungs-Agent auf einem in die Domäne eingebundenen Server installiert werden, der über Netzwerkzugriff auf die gewünschten Active Directory-Domänen verfügt.

Übertragen Sie das heruntergeladene Installationsprogramm für den Agent auf den Serverhost, und führen Sie die Schritte im [Abschnitt zur Installation des Agents](../cloud-sync/how-to-install.md) aus, um die Konfiguration des Agents durchzuführen.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Teil 3: Konfigurieren der Konnektivität zwischen SuccessFactors und Active Directory in der Bereitstellungs-App
In diesem Schritt stellen Sie im Azure-Portal Konnektivität zwischen SuccessFactors und Active Directory her. 

1. Wechseln Sie im Azure-Portal zurück zu der in [Teil 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) erstellten App zur Benutzerbereitstellung von SuccessFactors zu Active Directory.

1. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Benutzerkontos für die SuccessFactors-API ein, und fügen Sie die Unternehmens-ID an. Verwenden Sie das Format **Benutzername\@Unternehmens-ID**.

   * **Administratorkennwort**: Geben Sie das Kennwort des Benutzerkontos für die SuccessFactors-API ein. 

   * **Mandanten-URL**: Geben Sie den Namen des Dienstendpunkts für die SuccessFactors-OData-API ein. Geben Sie nur den Hostnamen des Servers ohne „http“ oder „https“ ein. Dieser Wert sollte das folgende Format aufweisen: **\<api-server-name\>.successfactors.com**.

   * **Active Directory-Gesamtstruktur:** Der „Name“ Ihrer Active Directory-Domäne, mit dem diese beim Agent registriert wurde. Wählen Sie über die Dropdownliste die Zieldomäne für die Bereitstellung aus. Dieser Wert ist meist eine Zeichenfolge wie: *contoso.com*

   * **Active Directory-Container:** Geben Sie den DN des Containers an, in dem der Agent Benutzerkonten standardmäßig erstellen soll.
        Beispiel: *OU=Users,DC=contoso,DC=com*
        > [!NOTE]
        > Diese Einstellung wird nur für die Benutzerkontoerstellung verwendet, wenn das Attribut *parentDistinguishedName* nicht in den Attributzuordnungen konfiguriert ist. Diese Einstellung wird nicht zum Suchen von Benutzern oder für Updatevorgänge verwendet. Der Suchvorgang schließt die gesamte Domänenteilstruktur ein.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.
     > [!NOTE]
     > Der Azure AD-Bereitstellungsdienst sendet eine E-Mail-Benachrichtigung, wenn der Bereitstellungsauftrag in den Zustand [Quarantäne](../app-provisioning/application-provisioning-quarantine-status.md) wechselt.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Überprüfen Sie bei einem Fehler, ob die SuccessFactors-Anmeldeinformationen und die AD-Anmeldeinformationen, die beim Einrichten des Agents angegeben wurden, gültig sind.
     >[!div class="mx-imgBorder"]
     >![Azure portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Nachdem die Anmeldeinformationen erfolgreich gespeichert wurden, wird im Abschnitt **Zuordnungen** die Standardzuordnung **SuccessFactors-Benutzer mit lokalem Active Directory synchronisieren** angezeigt.

### <a name="part-4-configure-attribute-mappings"></a>Teil 4: Konfigurieren von Attributzuordnungen

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus SuccessFactors in Active Directory.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **SuccessFactors-Benutzer in lokalem Active Directory synchronisieren**.

1. Im Feld **Quellobjektbereich** können Sie die Benutzergruppen in SuccessFactors für die Bereitstellung in Active Directory auswählen, indem Sie verschiedene attributbasierte Filter definieren. Der Standardbereich ist „Alle Benutzer in SuccessFactors“. Beispielfilter:

   * Beispiel: Auswählen der Benutzer mit personIdExternal von 1000000 bis 1999999

      * Attribut: personIdExternal

      * Operator: REGEX Match

      * Wert: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Beispiel: Nur Mitarbeiter und keine vorübergehend Beschäftigten

      * Attribut: EmployeeID

      * Operator: IS NOT NULL

   > [!TIP]
   > Wenn Sie die Bereitstellungs-App zum ersten Mal konfigurieren, müssen Sie Ihre Attributzuordnungen und Ausdrücke testen und überprüfen, um sicherzustellen, dass sie damit das gewünschte Ergebnis erzielen. Microsoft empfiehlt, die Bereichsfilter unter **Quellobjektbereich** zu verwenden, um Ihre Zuordnungen mit einigen Testbenutzern von SuccessFactors zu testen. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

   > [!CAUTION] 
   > Beim Standardverhalten des Bereitstellungsmoduls werden Benutzer deaktiviert/gelöscht, die sich außerhalb des gültigen Bereichs befinden. Dies ist bei Ihrer Integration von SuccessFactors mit AD möglicherweise nicht wünschenswert. Informationen zum Außerkraftsetzen dieses Standardverhaltens finden Sie im Artikel [Überspringen des Löschens von Benutzerkonten außerhalb des gültigen Bereichs](../app-provisioning/skip-out-of-scope-deletions.md).
  
1. Im Feld **Zielobjektaktionen** können Sie global filtern, welche Aktionen auf Active Directory angewendet werden. **Erstellen** und **Aktualisieren** erfolgen am häufigsten.

1. Im Abschnitt **Attributzuordnungen** können Sie definieren, wie einzelne SuccessFactors-Attribute Active Directory-Attributen zugeordnet werden.

     >[!NOTE]
     >Eine vollständige Liste der SuccessFactors-Attribute, die von der Anwendung unterstützt werden, finden Sie in der [SuccessFactors-Attributreferenz](../app-provisioning/sap-successfactors-attribute-reference.md).

1. Klicken Sie auf eine vorhandene Attributzuordnung, um sie zu aktualisieren. Oder klicken Sie am unteren Bildschirmrand auf **Neue Zuordnung hinzufügen**, um neue Zuordnungen hinzuzufügen. Eine einzelne Attributzuordnung unterstützt die folgenden Eigenschaften:

      * **Zuordnungstyp**

         * **Direkt:** schreibt den Wert des SuccessFactors-Attributs unverändert in das AD-Attribut.

         * **Konstant**: Schreibt einen statischen, konstanten Zeichenfolgenwert in das AD-Attribut.

         * **Ausdruck:** ermöglicht das Schreiben eines benutzerdefinierten Werts basierend auf einem oder mehreren SuccessFactors-Attributen in das AD-Attribut. [Weitere Informationen finden Sie im Artikel zu Ausdrücken](../app-provisioning/functions-for-customizing-application-data.md).

      * **Quellattribut:** das Benutzerattribut aus SuccessFactors

      * **Standardwert**: Optional. Wenn das Quellattribut einen leeren Wert aufweist, wird von der Zuordnung stattdessen dieser Wert geschrieben.
            Die meisten Konfigurationen sehen vor, dieses Feld leer zu lassen.

      * **Zielattribut**: Das Benutzerattribut in Active Directory.

      * **Objekte mit diesem Attribut abgleichen:** gibt an, ob diese Zuordnung zum eindeutigen Bestimmen von Benutzern zwischen SuccessFactors und Active Directory verwendet werden soll. Dieser Wert wird meist im Feld „Worker-ID“ für SuccessFactors festgelegt, das in der Regel einem der Mitarbeiter-ID-Attribute in Active Directory zugeordnet ist.

      * **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

      * **Diese Zuordnung anwenden**

         * **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.

         * **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

1. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.

Nachdem die Konfiguration Ihrer Attributzuordnung abgeschlossen ist, können Sie die Bereitstellung für einen einzelnen Benutzer testen, indem Sie die [bedarfsgesteuerte Bereitstellung](../app-provisioning/provision-on-demand.md) verwenden und dann den [Dienst für die Benutzerbereitstellung aktivieren und starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivieren und Starten der Benutzerbereitstellung

Nachdem Sie die Konfiguration der SuccessFactors-Bereitstellungs-App abgeschlossen haben und die Bereitstellung für einen einzelnen Benutzer per [bedarfsgesteuerter Bereitstellung](../app-provisioning/provision-on-demand.md) überprüft haben, können Sie den Bereitstellungsdienst im Azure-Portal aktivieren.

> [!TIP]
> Beim Aktivieren des Bereitstellungsdiensts werden Bereitstellungsvorgänge für alle Benutzer im Bereich initiiert. Wenn Fehler bei der Zuordnung oder SuccessFactors-Datenprobleme auftreten, kann der Bereitstellungsauftrag fehlschlagen und in den Quarantänezustand versetzt werden. Um dies zu vermeiden, empfehlen wir Ihnen, den Filter **Quellobjektbereich** zu konfigurieren und Ihre Attributzuordnungen mit einigen Testbenutzern per [bedarfsgesteuerter Bereitstellung](../app-provisioning/provision-on-demand.md) zu testen, bevor Sie die vollständige Synchronisierung für alle Benutzer starten. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren und Sie die gewünschten Ergebnisse erhalten, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Wechseln Sie zum Blatt **Bereitstellung**, und klicken Sie auf **Bereitstellung beginnen**.

1. Dieser Vorgang startet die erste Synchronisierung, die abhängig von der Anzahl von Benutzern im SuccessFactors-Mandanten mehrere Stunden dauern kann. Sie können die Statusanzeige überprüfen, um den Fortschritt des Synchronisierungszyklus zu verfolgen. 

1. Im Azure-Portal können Sie sich auf der Registerkarte **Überwachungsprotokolle** jederzeit ansehen, welche Aktionen der Bereitstellungsdienst ausgeführt hat. Die Überwachungsprotokolle enthalten alle einzelnen Synchronisierungsereignisse des Bereitstellungsdiensts, beispielsweise welche Benutzer in SuccessFactors gelesen und anschließend zu Active Directory hinzugefügt oder darin aktualisiert wurden. 

1. Nach Abschluss der ersten Synchronisierung wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:

   > [!div class="mx-imgBorder"]
   > ![Statusanzeige für die Bereitstellung](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu unterstützten SuccessFactors-Attributen für die eingehende Bereitstellung](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Weitere Informationen zum Konfigurieren des E-Mail-Rückschreibens an SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
* [Weitere Informationen zum Konfigurieren des einmaligen Anmeldens zwischen SuccessFactors und Azure Active Directory](successfactors-tutorial.md)
* [Erfahren Sie, wie Sie andere SaaS-Anwendungen in Azure Active Directory integrieren.](tutorial-list.md)
* [Weitere Informationen zum Exportieren und Importieren von Bereitstellungskonfigurationen](../app-provisioning/export-import-provisioning-configuration.md)