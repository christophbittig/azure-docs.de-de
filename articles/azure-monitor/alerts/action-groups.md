---
title: Erstellen und Verwalten von Aktionsgruppen im Azure-Portal
description: Erfahren Sie, wie Sie Aktionsgruppen im Azure-Portal erstellen und verwalten.
author: dkamstra
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: dukek
ms.openlocfilehash: af5742cc4b6c6fc218b1fd5597d5ff2c1e5ef3fd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440068"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Erstellen und Verwalten von Aktionsgruppen im Azure-Portal
Eine Aktionsgruppe ist eine Sammlung von Benachrichtigungseinstellungen, die vom Besitzer eines Azure-Abonnements definiert wurden. Azure Monitor-, Service Health- und Azure Advisor-Warnungen verwenden Aktionsgruppen, um Benutzer über das Auslösen einer Warnung zu informieren. Verschiedene Warnungen können je nach den Bedürfnissen des Benutzers die gleiche Aktionsgruppe oder verschiedene Aktionsgruppen verwenden. 

In diesem Artikel wird beschrieben, wie Sie Aktionsgruppen im Azure-Portal erstellen und verwalten.

Jede Aktion besteht aus den folgenden Eigenschaften:

* **Typ:** Die ausgeführte Benachrichtigung oder Aktion. Beispiele sind das Senden eines Sprachanrufs, SMS, E-Mail oder das Auslösen verschiedener Arten von automatisierten Aktionen. Siehe „Typen“ weiter unten in diesem Artikel.
* **Name**: Ein eindeutiger Bezeichner innerhalb der Aktionsgruppe.
* **Details**: Die entsprechenden Details. Diese können je nach *Typ* variieren.

Weitere Informationen zum Verwenden von Azure Resource Manager-Vorlagen zur Konfigurierung von Aktionsgruppen finden Sie unter [Aktionsgruppen-Resource Manager-Vorlagen](./action-groups-create-resource-manager-template.md).

Die Aktionsgruppe ist ein **globaler** Dienst, der daher keine Abhängigkeit von einer bestimmten Azure-Region aufweist. Anforderungen vom Client können vom Aktionsgruppendienst in einer beliebigen Region verarbeitet werden. Dies bedeutet, dass der Datenverkehr automatisch von anderen Regionen geroutet und verarbeitet wird, wenn eine Region nicht verfügbar ist. Da es sich um einen *globalen Dienst* handelt, muss sich der Client nicht um die **Notfallwiederherstellung** kümmern. 

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Erstellen einer Aktionsgruppe mit dem Azure-Portal

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach dem Eintrag **Überwachen**, und wählen Sie ihn aus. Im Bereich **Überwachen** sind alle Ihre Überwachungseinstellungen und -daten an einem zentralen Ort zusammengefasst.

1. Wählen Sie **Warnungen** und dann **Aktionen verwalten** aus.

    ![Schaltfläche „Aktionen verwalten“](./media/action-groups/manage-action-groups.png)
    
1. Wählen Sie **Aktionsgruppe hinzufügen** aus, und füllen Sie die relevanten Felder im Assistenten aus.

    ![Der Befehl „Aktionsgruppe hinzufügen“](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Konfigurieren grundlegender Aktionsgruppeneinstellungen

Gehen Sie unter **Projektdetails** wie folgt vor:

Wählen Sie das **Abonnement** und die **Ressourcengruppe** aus, in der die Aktionsgruppe gespeichert ist.

Gehen Sie unter **Instanzendetails** wie folgt vor:

1. Geben Sie einen **Aktionsgruppennamen** ein.

1. Geben Sie einen **Anzeigenamen** ein. Der Anzeigename wird anstelle eines vollständigen Aktionsgruppennamens verwendet, wenn Benachrichtigungen unter Verwendung dieser Gruppe gesendet werden.

      ![Das Dialogfeld "Aktionsgruppe hinzufügen](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Konfigurieren von Benachrichtigungen

1. Klicken Sie auf die Schaltfläche **Weiter: Benachrichtigungen >** , um zur Registerkarte **Benachrichtigungen** zu wechseln, oder wählen Sie die Registerkarte **Benachrichtigungen** am oberen Rand des Bildschirms aus.

1. Definieren Sie eine Liste von Benachrichtigungen, die bei Auslösung einer Warnung gesendet werden. Geben Sie für jede Benachrichtigung Folgendes an:

    a. **Benachrichtigungstyp**: Wählen Sie den Typ der zu sendenden Benachrichtigung aus. Verfügbare Optionen:
      * E-Mail an Azure Resource Manager-Rolle: Sendet eine E-Mail an Benutzer, die bestimmten ARM-Rollen auf Abonnementebene zugewiesen sind.
      * E-Mail/SMS/Push/Sprachanruf: Sendet diese Benachrichtigungstypen an bestimmte Empfänger.
    
    b. **Name**: Geben Sie einen eindeutigen Namen für die Benachrichtigung ein.

    c. **Details**: Geben Sie je nach ausgewähltem Benachrichtigungstyp eine E-Mail-Adresse, eine Telefonnummer usw. ein.
    
    d. **Allgemeines Warnungsschema**: Sie können auch das [allgemeine Warnungsschema](./alerts-common-schema.md) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet.

    ![Die Registerkarte „Benachrichtigungen“](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Konfigurieren von Aktionen

1. Klicken Sie auf die Schaltfläche **Weiter: Aktionen >** , um zur Registerkarte **Aktionen** zu wechseln, oder wählen Sie die Registerkarte **Aktionen** am oberen Rand des Bildschirms aus.

1. Definieren Sie eine Liste von Aktionen, die bei Auslösung einer Warnung gestartet werden. Geben Sie für jede Aktion Folgendes an:

    a. **Aktionstyp:** Wählen Sie „Automation-Runbook“, „Azure-Funktion“, „ITSM“, „Logik-App“, „Sicherer Webhook“ oder „Webhook“ aus.
    
    b. **Name**: Geben Sie einen eindeutigen Namen für die Aktion ein.

    c. **Details**: Geben Sie je nach Aktionstyp einen Webhook-URI, eine Azure-App, eine ITSM-Verbindung oder ein Automation-Runbook ein. Legen Sie für die ITSM-Aktion darüber hinaus **Arbeitselement** und andere Felder fest, die Ihr ITSM-Tool benötigt.
    
    d. **Allgemeines Warnungsschema**: Sie können auch das [allgemeine Warnungsschema](./alerts-common-schema.md) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet.
    
    ![Die Registerkarte „Aktionen“](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Erstellen der Aktionsgruppe

1. Bei Interesse können Sie sich die Einstellungen für **Tags** genauer ansehen. Auf diese Weise können Sie der Aktionsgruppe Schlüssel-Wert-Paare für die Kategorisierung zuordnen. Dieses Feature ist für jede Azure-Ressource verfügbar.

    ![Die Registerkarte „Tags“](./media/action-groups/action-group-4-tags.png)
    
1. Klicken Sie auf **Überprüfen + erstellen**, um die Einstellungen zu überprüfen. Dadurch wird eine schnelle Überprüfung Ihrer Eingaben vorgenommen, um sicherzustellen, dass alle erforderlichen Felder ausgewählt sind. Wenn Probleme vorliegen, werden diese hier angezeigt. Nachdem Sie die Einstellungen überprüft haben, klicken Sie auf **Erstellen**, um die Aktionsgruppe bereitzustellen.
    
    ![Die Registerkarte „Überprüfen + erstellen“](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Wenn Sie eine Aktion konfigurieren, um eine Person per E-Mail oder SMS zu benachrichtigen, erhält diese Person eine Bestätigung mit dem Hinweis, dass sie der Aktionsgruppe hinzugefügt wurde.

## <a name="manage-your-action-groups"></a>Verwalten von Aktionsgruppen

Nachdem Sie eine Aktionsgruppe erstellt haben, können Sie **Aktionsgruppen** anzeigen, indem Sie auf der Landing Page **Warnungen** im Bereich **Monitor** auf **Aktionen verwalten** klicken. Wählen Sie die Aktionsgruppe, die Sie verwalten möchten, um Folgendes zu tun:

* Fügen Sie Aktionen hinzu, bearbeiten oder entfernen Sie diese.
* Löschen der Aktionsgruppe.

## <a name="action-specific-information"></a>Aktionsspezifische Informationen

> [!NOTE]
> Informationen zu den numerischen Grenzwerten für die unten angegebenen Elemente finden Sie unter [Grenzwerte für Monitor](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits).  

### <a name="automation-runbook"></a>Automation Runbook
Informationen zu den Einschränkungen für Runbook-Nutzlasten finden Sie unter [Azure-Abonnementdienstgrenzen](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Runbook-Aktionen verfügen. 

### <a name="azure-app-push-notifications"></a>Pushbenachrichtigungen der Azure-App
Aktivieren Sie Pushbenachrichtigungen an die [mobile Azure-App](https://azure.microsoft.com/features/azure-portal/mobile-app/), indem Sie die E-Mail-Adresse angeben, die Sie beim Konfigurieren der mobilen Azure-App als Konto-ID verwenden.

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Azure-App-Aktionen verfügen.

### <a name="email"></a>Email
E-Mails werden von den folgenden E-Mail-Adressen gesendet. Achten Sie darauf, dass Ihre E-Mail-Filterung ordnungsgemäß konfiguriert ist.
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von E-Mail-Aktionen verfügen. Weitere Informationen finden Sie im Artikel [Ratenlimits für Sprache, SMS-Nachrichten, E-Mail-Nachrichten, Azure App-Pushbenachrichtigungen und Webhookbeiträge](./alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>E-Mail an Azure Resource Manager-Rolle
Senden Sie eine E-Mail an die Mitglieder dieser Rolle im Abonnement. Die E-Mail wird nur an Mitglieder der Rolle gesendet, die **Azure AD-Benutzer** sind. E-Mail wird nicht an Azure AD-Gruppen oder Dienstprinzipale gesendet.

Eine Benachrichtigungs-E-Mail wird nur an die *primäre E-Mail-Adresse* gesendet.

Wenn Sie keine Benachrichtigungen an der *primären E-Mail-Adresse* erhalten, können Sie die folgenden Schritte ausführen:

1. Navigieren Sie im Azure-Portal zu *Active Directory*.
2. Klicken Sie auf „Alle Benutzer“ (im linken Bereich). Daraufhin wird eine Liste der Benutzer angezeigt (im rechten Bereich).
3. Wählen Sie den Benutzer aus, für den Sie die Angaben zur *primären E-Mail-Adresse* überprüfen möchten.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Beispiel für das Überprüfen eines Benutzerprofils" border="true":::

4. Wenn im Benutzerprofil unter „Kontaktinformationen“ der Bereich „E-Mail“ leer ist, klicken Sie oben auf die Schaltfläche *Bearbeiten*, fügen Sie die *primäre E-Mail-Adresse* hinzu, und klicken Sie dann oben auf die Schaltfläche *Speichern*.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Beispiel für das Hinzufügen einer primären E-Mail-Adresse" border="true":::

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von E-Mail-Aktionen verfügen. Weitere Informationen finden Sie im Artikel [Ratenlimits für Sprache, SMS-Nachrichten, E-Mail-Nachrichten, Azure App-Pushbenachrichtigungen und Webhookbeiträge](./alerts-rate-limiting.md).

Beim Einrichten der *E-Mail-ARM-Rolle* müssen Sie sicherstellen, dass die drei folgenden Bedingungen erfüllt sind:

1. Der Typ der Entität, die der Rolle zugewiesen wird, muss **Benutzer** lauten.
2. Die Zuweisung muss auf **Abonnementebene** erfolgen.
3. Der Benutzer muss eine E-Mail im **AAD-Profil** konfiguriert haben. 

> [!NOTE]
> Es kann bis zu **24** Stunden dauern, bis ein Kunde Benachrichtigungen empfängt, nachdem er seinem Abonnement eine neue ARM-Rolle hinzugefügt hat.

### <a name="event-hub-preview"></a>Event Hub (Vorschau)
> [!NOTE]
> Der Event Hub-Aktionstyp befindet sich derzeit in *Vorschau*. Während der Vorschau kann es zu Fehlern und Unterbrechungen bei der Verfügbarkeit der Funktionen kommen.

Eine Event Hub-Aktion veröffentlicht Benachrichtigungen an einen [Azure Event Hub](~/articles/event-hubs/event-hubs-about.md). Sie können dann den Benachrichtigungsstrom über Ihren Ereignisverbraucher abonnieren.

Derzeit kann die Event Hub-Aktion nur mit einer [Azure Resource Manager-Vorlage](./action-groups-create-resource-manager-template.md) definiert werden.

### <a name="function"></a>Funktion
Diese Aktion ruft einen vorhandenen HTTP-Triggerendpunkt in [Azure Functions](../../azure-functions/functions-get-started.md) auf. Ihr Endpunkt muss das HTTP POST-Verb verarbeiten, um eine Anforderung zu verarbeiten.

Beim Definieren der Funktionsaktion werden der httptrigger-Endpunkt und der Zugriffsschlüssel der Funktion in der Aktionsdefinition gespeichert. Beispiel: `https://azfunctionurl.azurewebsites.net/api/httptrigger?code=this_is_access_key`. Wenn Sie den Zugriffsschlüssel für die Funktion ändern, müssen Sie die Funktionsaktion in der Aktionsgruppe löschen und neu erstellen.

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Functions-Aktionen verfügen.

### <a name="itsm"></a>ITSM
Für eine ITSM-Aktion muss eine ITSM-Verbindung hergestellt werden. Informieren Sie sich, wie Sie [eine ITSM-Verbindung erstellen](./itsmc-overview.md).

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von ITSM-Aktionen verfügen. 

### <a name="logic-app"></a>Logik-App
Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Logik-App-Aktionen verfügen.

### <a name="secure-webhook"></a>Sicherer Webhook
Mithilfe der Aktion für sichere Aktionsgruppen-Webhooks können Sie Azure Active Directory nutzen, um die Verbindung zwischen Ihrer Aktionsgruppe und Ihrer geschützten Web-API (Webhookendpunkt) zu schützen. Der gesamte Workflow für das Nutzen dieser Funktionalität wird unten beschrieben. Eine Übersicht über Azure AD-Anwendungen und -Dienstprinzipale finden Sie unter [Microsoft Identity Platform (v2.0): Übersicht](../../active-directory/develop/v2-overview.md).

> [!NOTE]
> Zur Verwendung der Webhookaktion ist es erforderlich, dass der Webhookendpunkt des Ziels entweder keine Details der Warnung zum erfolgreichen Funktionieren benötigt oder die Warnungskontextinformationen, die als Teil des POST-Vorgangs bereitgestellt werden, analysieren kann. Wenn der Webhookendpunkt die Warnungskontextinformationen nicht selbst verarbeiten kann, können Sie eine Lösung wie eine [Logik-App-Aktion](./action-groups-logic-app.md) zur benutzerdefinierten Bearbeitung der Warnungskontextinformationen verwenden, um sie an das erwartete Datenformat des Webhooks anzupassen.

1. Erstellen Sie eine Azure AD-Anwendung für ihre geschützte Web-API. Weitere Informationen finden Sie unter [Geschützte Web-API: App-Registrierung](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Konfigurieren Sie Ihre geschützte API so, dass Sie von einer [Daemon-App aufgerufen wird](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Aktivieren Sie Aktionsgruppen, um Ihre Azure AD-Anwendung zu verwenden.

    > [!NOTE]
    > Sie müssen Mitglied der [Rolle „Azure AD-Anwendungsadministrator“](../../active-directory/roles/permissions-reference.md#all-roles) sein, um dieses Skript ausführen zu können.
    
    - Ändern Sie den Connect-AzureAD-Befehl des PowerShell-Skripts so, dass Ihre Azure AD-Mandanten-ID verwendet wird.
    - Ändern Sie die Variable „$myAzureADApplicationObjectId“ des PowerShell-Skripts, um die Objekt-ID Ihrer Azure AD-Anwendung zu verwenden.
    - Führen Sie das geänderte Skript aus.
    
3. Konfigurieren Sie die Aktion für den sicheren Aktionsgruppen-Webhook.
    - Kopieren Sie den Wert von $MyApp.ObjectID aus dem Skript, und geben Sie ihn in das Feld Anwendungsobjekt-ID in der Webhookaktionsdefinition ein.
    
    ![Sichere Webhookaktion](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>PowerShell-Skript für sicheren Webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
Weitere wichtige Informationen finden Sie in den Artikeln zu [Ratenlimits](./alerts-rate-limiting.md) und zum [Verhalten von SMS-Benachrichtigungen](./alerts-sms-behavior.md). 

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von SMS-Aktionen verfügen.

> [!NOTE]
> Wenn Sie auf der Benutzeroberfläche für die Aktionsgruppe im Azure-Portal Ihren Länder-/Regionscode nicht auswählen können, wird SMS für Ihr Land bzw. Ihre Region nicht unterstützt.  Wenn Ihr Länder-/Regionscode nicht verfügbar ist, können Sie über das [Benutzerfeedback](https://feedback.azure.com/d365community/idea/e527eaa6-2025-ec11-b6e6-000d3a4f09d0) dafür stimmen, dass Ihr Land bzw. Ihre Region hinzugefügt wird. In der Zwischenzeit besteht eine Umgehungslösung darin, dass Ihre Aktionsgruppe einen Webhook bei einem anderen SMS-Anbieter mit Unterstützung in Ihrem Land bzw. Ihrer Region anruft.  

Die Preise für unterstützte Länder/Regionen sind auf der [Seite mit der Preisübersicht für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) aufgeführt.

**Liste der Länder, in denen SMS-Benachrichtigungen unterstützt werden**

| Landesvorwahl | Name des Lands |
|:---|:---|
| 61 | Australien |
| 43 | Österreich |
| 32 | Belgien |
| 55 | Brasilien |
| 1 |Canada |
| 56 | Chile |
| 86 | China |
| 420 | Tschechische Republik |
| 45 | Dänemark |
| 372 | Estland |
| 358 | Finnland |
| 33 | Frankreich |
| 49 | Deutschland |
| 852 | Hongkong |
| 91 | Indien |
| 353 | Irland |
| 972 | Israel |
| 39 | Italien |
| 81 | Japan |
| 352 | Luxemburg |
| 60 | Malaysia |
| 52 | Mexiko |
| 31 | Niederlande |
| 64 | Neuseeland |
| 47 | Norwegen |
| 351 | Portugal |
| 1 | Puerto Rico |
| 40 | Rumänien |
| 7  | Russland  |
| 65 | Singapur |
| 27 | Südafrika |
| 82 | Südkorea |
| 34 | Spanien |
| 41 | Schweiz |
| 886 | Taiwan |
| 971 | Vereinigte Arabische Emirate    |
| 44 | United Kingdom |
| 1 | USA |

### <a name="voice"></a>Sprache
Weitere wichtige Informationen finden Sie im Artikeln zu [Ratenlimits](./alerts-rate-limiting.md).

Es kann sein, dass Sie in einer Aktionsgruppe über eine begrenzte Anzahl von Sprachaktionen verfügen.

> [!NOTE]
> Wenn Sie auf der Benutzeroberfläche für die Aktionsgruppe im Azure-Portal Ihren Länder-/Regionscode nicht auswählen können, werden Sprachanrufe für Ihr Land bzw. Ihre Region nicht unterstützt. Wenn Ihr Länder-/Regionscode nicht verfügbar ist, können Sie über das [Benutzerfeedback](https://feedback.azure.com/d365community/idea/e527eaa6-2025-ec11-b6e6-000d3a4f09d0) dafür stimmen, dass Ihr Land bzw. Ihre Region hinzugefügt wird.  In der Zwischenzeit besteht eine Umgehungslösung darin, dass Ihre Aktionsgruppe einen Webhook bei einem anderen Anbieter von Sprachanrufen mit Unterstützung in Ihrem Land bzw. Ihrer Region anruft.  
> Die einzige derzeit in der Aktionsgruppe für Sprachbenachrichtigungen im Azure-Portal unterstützte Landeskennzahl ist „+1“ (USA). 

Die Preise für unterstützte Länder/Regionen sind auf der [Seite mit der Preisübersicht für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) aufgeführt.

### <a name="webhook"></a>Webhook

> [!NOTE]
> Zur Verwendung der Webhookaktion ist es erforderlich, dass der Webhookendpunkt des Ziels entweder keine Details der Warnung zum erfolgreichen Funktionieren benötigt oder die Warnungskontextinformationen, die als Teil des POST-Vorgangs bereitgestellt werden, analysieren kann. Wenn der Webhookendpunkt die Warnungskontextinformationen nicht selbst verarbeiten kann, können Sie eine Lösung wie eine [Logik-App-Aktion](./action-groups-logic-app.md) zur benutzerdefinierten Bearbeitung der Warnungskontextinformationen verwenden, um sie an das erwartete Datenformat des Webhooks anzupassen.

Webhooks werden mithilfe der folgenden Regeln verarbeitet:
- Ein Webhookaufruf wird maximal dreimal versucht.
- Der Aufruf wird wiederholt, wenn innerhalb des Timeoutzeitraums keine Antwort empfangen wird oder einer der folgenden HTTP-Statuscodes zurückgegeben wird: 408, 429, 503 oder 504
- Der erste Aufruf wartet 10 Sekunden auf eine Antwort.
- Der zweite und der dritte Versuch warten 30 Sekunden auf eine Antwort.
- Nachdem bei allen drei Versuchen, den Webhook aufzurufen, ein Fehler aufgetreten ist, ruft keine der Aktionsgruppen den Endpunkt während der nächsten 15 Minuten auf.

Unter [IP-Adressen von Aktionsgruppen](../app/ip-addresses.md) finden Sie Quell-IP-Adressbereiche.


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [SMS-Warnungsverhalten in Aktionsgruppen](./alerts-sms-behavior.md).  
* Erweitern Sie Ihr [Verständnis des Webhookschemas für Aktivitätsprotokollwarnungen](./activity-log-alerts-webhook.md).  
* Weitere Informationen zum ITSM-Connector finden Sie [hier](./itsmc-overview.md).
* Weitere Informationen zu [Ratenlimits](./alerts-rate-limiting.md) für Warnungen.
* Verschaffen Sie sich eine [Übersicht über Aktivitätsprotokollwarnungen](./alerts-overview.md), und erfahren Sie, wie Sie Warnungen empfangen können.  
* Erfahren Sie, wie Sie [Warnungen konfigurieren, wenn eine Dienstintegritätsbenachrichtigung gesendet wird](../../service-health/alerts-activity-log-service-notifications-portal.md).
