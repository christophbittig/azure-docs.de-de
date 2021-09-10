---
title: Skalieren von Sitzungshosts mit Azure Automation – Azure
description: So skalieren Sie Azure Virtual Desktop-Sitzungshosts automatisch mit Azure Automation.
author: Heidilohr
ms.topic: how-to
ms.date: 03/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ac3caf51337b87125f10cc7a6cd1378d7bfdb923
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005310"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Skalieren von Sitzungshosts mit Azure Automation

Sie können die Gesamtkosten für die Bereitstellung von Azure Virtual Desktop verringern, indem Sie Ihre virtuellen Computer (VMs) skalieren. Das bedeutet, dass Sie die Sitzungshost-VMs außerhalb der Spitzenzeiten herunterfahren und ihre Zuordnung aufheben und sie dann während der Spitzenzeiten wieder einschalten und erneut zuordnen.

In diesem Artikel erfahren Sie mehr über das mit dem Azure Automation-Konto und Azure Logic Apps erstellte Skalierungstool, mit dem die Sitzungshost-VMs in Ihrer Azure Virtual Desktop-Umgebung automatisch skaliert werden können. Um zu erfahren, wie Sie das Skalierungstool verwenden, fahren Sie mit dem Abschnitt [Voraussetzungen](#prerequisites) fort.

## <a name="how-the-scaling-tool-works"></a>Funktionsweise des Skalierungstools

Das Skalierungstool bietet eine kostengünstige Automatisierungsoption für Kunden, die ihre Kosten für die Sitzungshost-VMs optimieren möchten.

Sie können das Skalierungstool für Folgendes verwenden:

- Planen des Startens und Beendens von VMs basierend auf Spitzenzeiten und ruhigeren Geschäftszeiten
- Aufskalieren von VMs basierend auf der Anzahl von Sitzungen pro CPU-Kern
- Abskalieren von VMs außerhalb von Spitzenzeiten unter Beibehaltung einer Mindestanzahl von Sitzungshost-VMs

Das Skalierungstool nutzt für seine Funktion eine Kombination aus einem Azure Automation-Konto, einem PowerShell-Runbook, einem Webhook und Azure Logic Apps. Wenn das Tool ausgeführt wird, ruft Azure Logic Apps einen Webhook auf, um das Azure Automation-Runbook zu starten. Das Runbook erstellt dann einen Auftrag.

Während der Spitzenauslastung überprüft der Auftrag die aktuelle Anzahl von Sitzungen und die VM-Kapazität der derzeit ausgeführten Sitzungshosts für jeden Hostpool. Es berechnet anhand dieser Informationen, ob die ausgeführten Sitzungshost-VMs die vorhandenen Sitzungen ausreichend unterstützen können. Die Berechnung basiert auf dem Parameter *SessionThresholdPerCPU*, der in der Datei **CreateOrUpdateAzLogicApp.ps1** definiert ist. Wenn die Sitzungshost-VMs die aktiven Sitzungen nicht unterstützen können, startet der Auftrag zusätzliche Sitzungshost-VMs im Hostpool.

>[!NOTE]
>*SessionThresholdPerCPU* schränkt die Anzahl der Sitzungen auf dem virtuellen Computer nicht ein. Dieser Parameter legt nur fest, wann neue VMs gestartet werden müssen, um einen Lastenausgleich für die Verbindungen auszuführen. Wenn Sie die Anzahl der Sitzungen einschränken möchten, befolgen Sie die Anweisungen zu [Update-AzWvdHostPool](configure-host-pool-load-balancing.md#configure-breadth-first-load-balancing), um den Parameter *MaxSessionLimit* entsprechend zu konfigurieren.

Außerhalb der Spitzenauslastungszeiten ermittelt der Auftrag anhand des Parameters *MinimumNumberOfRDSH*, wie viele Sitzungshost-VMs heruntergefahren werden sollten. Wenn Sie den Parameter *LimitSecondsToForceLogOffUser* auf einen positiven Wert über null festlegen, legt der Auftrag die Sitzungshost-VMs auf den Ausgleichsmodus fest, um zu verhindern, dass neue Sitzungen Verbindungen mit den Hosts herstellen. Der Auftrag fordert alle derzeit angemeldeten Benutzer über eine Benachrichtigung auf, ihre Änderungen zu speichern, wartet den konfigurierten Zeitraum ab und erzwingt dann das Abmelden der Benutzer. Nachdem alle Benutzersitzungen einer Sitzungshost-VM abgemeldet wurden, wird die VM über den Auftrag heruntergefahren. Nachdem die VM heruntergefahren wurde, setzt der Auftrag seinen Ausgleichsmodus für den Sitzungshost zurück.

>[!NOTE]
>Wenn Sie die Sitzungshost-VM manuell auf den Ausgleichsmodus festlegen, verwaltet der Auftrag die Sitzungshost-VM nicht. Wenn die Sitzungshost-VM ausgeführt wird und auf den Ausgleichsmodus festgelegt ist, wird sie als nicht verfügbar behandelt. Der Auftrag startet dadurch zusätzliche VMs, um die Last zu bewältigen. Es wird empfohlen, alle Azure-VMs mit Tags zu versehen, bevor Sie sie manuell in den Ausgleichsmodus versetzen. Wenn Sie später den Azure Logic Apps-Scheduler erstellen, können Sie den Namen des Tags mit dem Parameter *MaintenanceTagName* festlegen. Mithilfe von Tags können Sie diese VMs von den Computern unterscheiden, die vom Skalierungstool verwaltet werden. Durch Festlegen des Wartungstags wird außerdem verhindert, dass das Skalierungstool Änderungen an der VM vornimmt, bis Sie das Tag entfernen.

Wenn Sie den Parameter *LimitSecondsToForceLogOffUser* auf null festlegen, wird die Abmeldung von Benutzersitzungen durch den Auftrag gemäß der Einstellung für die Sitzungskonfiguration in den angegebenen Gruppenrichtlinien verwaltet. Um diese Gruppenrichtlinien anzuzeigen, wechseln Sie zu **Computerkonfiguration** > **Richtlinien** > **Administrative Vorlagen** > **Windows-Komponenten** > **Remotedesktopdienste** > **Remotedesktop-Sitzungshost** > **Sitzungszeitlimits**. Wenn aktive Sitzungen auf einer Sitzungshost-VM ausgeführt werden, wird die Sitzungshost-VM vom Auftrag weiterhin ausgeführt. Wenn keine aktiven Sitzungen vorhanden sind, wird die Sitzungshost-VM über den Auftrag heruntergefahren.

Der Auftrag berücksichtigt immer auch die Einstellung *MaxSessionLimit* des Hostpools bei der Ermittlung, ob die aktuelle Anzahl der Sitzungen 90 % der maximalen Kapazität überschreitet. In diesem Fall startet der Auftrag weitere Sitzungshost-VMs.

Der Auftrag wird in regelmäßigen Abständen basierend auf einem festgelegten Wiederholungsintervall ausgeführt. Sie können dieses Intervall basierend auf der Größe Ihrer Azure Virtual Desktop-Umgebung ändern. Beachten Sie jedoch, dass das Starten und Herunterfahren von VMs einige Zeit in Anspruch nehmen kann, und planen Sie eine entsprechende Verzögerung ein. Es wird empfohlen, das Wiederholungsintervall auf 15 Minuten festzulegen.

Für das Tool gelten allerdings folgende Einschränkungen:

- Diese Lösung gilt nur für in einem Pool zusammengefasste Multisession-Sitzungshost-VMs.
- Mit dieser Lösung können VMs in allen Regionen verwaltet werden, sie kann jedoch nur unter demselben Abonnement wie Ihr Azure Automation-Konto und Azure Logic Apps verwendet werden.
- Die maximale Laufzeit eines Auftrags im Runbook beträgt 3 Stunden. Wenn das Starten oder Beenden der VMs im Hostpool länger dauert, tritt bei dem Auftrag ein Fehler auf. Weitere Informationen finden Sie unter [Freigegebene Ressourcen](../automation/automation-runbook-execution.md#fair-share).
- Mindestens eine VM oder ein Sitzungshost muss aktiviert sein, damit der Skalierungsalgorithmus ordnungsgemäß funktioniert.
- Das Skalierungstool unterstützt keine Skalierung, die auf CPU oder Arbeitsspeicher basiert.
- Die Skalierung funktioniert nur mit vorhandenen Hosts im Hostpool. Das Skalierungstool unterstützt die Skalierung neuer Sitzungshosts nicht.

>[!NOTE]
>Das Skalierungstool steuert den Lastenausgleichsmodus des Hostpools, den es zurzeit skaliert. Es nutzt den breitenorientierten Lastenausgleichsmodus sowohl zu Spitzenzeiten als auch außerhalb der Spitzenzeiten.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der Einrichtung des Skalierungstools beginnen, sollten Sie Folgendes vorbereiten:

- Einen [Azure Virtual Desktop-Hostpool](create-host-pools-azure-marketplace.md)
- Sitzungshostpool-VMs, die konfiguriert und für den Azure Virtual Desktop-Dienst registriert sind
- Einen Benutzer mit dem Zugriff [Mitwirkender](../role-based-access-control/role-assignments-portal.md) für das Azure-Abonnement

Der Computer, den Sie zum Bereitstellen des Tools verwenden, muss Folgendes aufweisen:

- Windows PowerShell 5.1 oder höher
- Das Microsoft PowerShell-Modul „Az“

Wenn alles bereit ist, können Sie anfangen.

## <a name="create-or-update-an-azure-automation-account"></a>Erstellen oder Aktualisieren eines Azure Automation-Kontos

>[!NOTE]
>Wenn Sie bereits über ein Azure Automation-Konto mit einem Runbook verfügen, in dem eine ältere Version des Skalierungsskripts ausgeführt wird, müssen Sie lediglich die nachstehende Anleitung befolgen, um sicherzustellen, dass es auf dem neuesten Stand ist.

Zunächst benötigen Sie ein Azure Automation-Konto zum Ausführen des PowerShell-Runbooks. Das in diesem Abschnitt beschriebene Verfahren funktioniert auch dann, wenn Sie bereits über ein Azure Automation-Konto verfügen, mit dem Sie das PowerShell-Runbook einrichten möchten. Die Einrichtung erfolgt folgendermaßen:

1. Öffnen Sie Windows PowerShell.

2. Führen Sie das folgende Cmdlet aus, um sich bei Ihrem Azure-Konto anzumelden.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >Ihr Konto muss über die Berechtigung „Mitwirkender“ für das Azure-Abonnement verfügen, in dem Sie das Skalierungstool bereitstellen möchten.

3. Führen Sie das folgende Cmdlet aus, um das Skript zum Erstellen des Azure Automation-Kontos herunterzuladen:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Führen Sie das folgende Cmdlet aus, um das Skript auszuführen und das Azure Automation-Konto zu erstellen. Sie können Werte für die Parameter eingeben oder sie auskommentieren, um die Standardwerte zu verwenden.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "UseARMAPI"             = $true
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. Die Ausgabe des Cmdlets enthält einen Webhook-URI. Notieren Sie sich den URI unbedingt, da Sie ihn als Parameter verwenden, wenn Sie den Ausführungszeitplan für Azure Logic Apps einrichten.

6. Wenn Sie den Parameter **WorkspaceName** für Log Analytics angegeben haben, enthält die Cmdlet-Ausgabe auch die Log Analytics-Arbeitsbereichs-ID und den zugehörigen Primärschlüssel. Notieren Sie sich unbedingt den URI, da Sie ihn später wieder als Parameter verwenden, wenn Sie den Ausführungszeitplan für die Azure-Logik-App einrichten.

7. Nachdem Sie Ihr Azure Automation-Konto eingerichtet haben, melden Sie sich bei Ihrem Azure-Abonnement an, und überprüfen Sie, ob Ihr Azure Automation-Konto und das zugehörige Runbook in der angegebenen Ressourcengruppe angezeigt werden, wie in der folgenden Abbildung dargestellt:

    >[!div class="mx-imgBorder"]
    >![Abbildung der Azure-Übersicht mit dem neu erstellten Azure Automation-Konto und den Runbooks](media/automation-account.png)

    Wählen Sie den Namen Ihres Runbooks aus, um zu überprüfen, ob sich Ihr Webhook dort befindet, wo er sein sollte. Navigieren Sie als nächstes zum Abschnitt „Ressourcen“ des Runbooks, und wählen Sie **Webhooks** aus.

## <a name="create-an-azure-automation-run-as-account"></a>Erstellen eines ausführenden Azure Automation-Kontos

Nachdem Sie nun über ein Azure Automation-Konto verfügen, müssen Sie auch ein ausführendes Azure Automation-Konto erstellen, sofern Sie noch nicht über eines verfügen. Dieses Konto ermöglicht dem Tool den Zugriff auf Ihre Azure-Ressourcen.

Ein [ausführendes Azure Automation-Konto](../automation/manage-runas-account.md) ermöglicht die Authentifizierung für die Verwaltung von Ressourcen in Azure mit Azure-Cmdlets. Beim Erstellen eines ausführenden Kontos wird ein neuer Dienstprinzipalbenutzer in Azure Active Directory erstellt, und dem Dienstprinzipalbenutzer wird auf Abonnementebene die Rolle „Mitwirkender“ zugewiesen. Ein ausführendes Azure-Konto eignet sich hervorragend für die sichere Authentifizierung mit Zertifikaten und einem Dienstprinzipalnamen, ohne einen Benutzernamen und ein Kennwort in einem Anmeldeinformationsobjekt speichern zu müssen. Weitere Informationen zur Authentifizierung mit ausführenden Konten finden Sie unter [Einschränken der Berechtigungen von ausführenden Konten](../automation/manage-runas-account.md#limit-run-as-account-permissions).

Alle Benutzer, die Mitglied der Rolle „Abonnement-Administratoren“ und Co-Administrator des Abonnements sind, können ein ausführendes Konto erstellen.

So erstellen Sie ein ausführendes Konto in Ihrem Azure Automation-Konto

1. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie in der Liste der Ressourcen **Automation-Konten** ein, und wählen Sie diese Option aus.

2. Wählen Sie auf der Seite **Automation-Konten** den Namen Ihres Azure Automation-Kontos aus.

3. Wählen Sie im linken Bereich des Fensters im Abschnitt **Kontoeinstellungen** die Option **Ausführendes Konto** aus.

4. Wählen Sie **Ausführendes Azure-Konto** aus. Wenn der Bereich **Ausführendes Azure-Konto hinzufügen** angezeigt wird, überprüfen Sie die Übersichtsinformationen, und wählen Sie dann **Erstellen** aus, um den Kontoerstellungsprozess zu starten.

5. Warten Sie einige Minuten, bis Azure das ausführende Konto erstellt hat. Sie können den Erstellungsprozess im Menü unter den Benachrichtigungen nachverfolgen.

6. Zum Abschluss erstellt der Prozess die Ressource **AzureRunAsConnection** im angegebenen Azure Automation-Konto. Wählen Sie **Ausführendes Azure-Konto** aus. Die Verbindungsressource enthält die Anwendungs-ID, die Mandanten-ID, die Abonnement-ID und den Zertifikatfingerabdruck. Diese Informationen finden Sie auch auf der Seite **Verbindungen**. Um zu dieser Seite zu wechseln, wählen Sie im Bereich links im Fenster im Abschnitt **Freigegebene Ressourcen** die Option **Verbindungen** aus, und klicken Sie dann auf das Verbindungsobjekt mit dem Namen **AzureRunAsConnection**.

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Erstellen der Azure-Logik-App und des Ausführungszeitplans

Abschließend müssen Sie die Azure-Logik-App erstellen und einen Ausführungszeitplan für das neue Skalierungstool einrichten. Laden Sie ggf. zunächst das [PowerShell-Modul für die Desktopvirtualisierung (DesktopVirtualization)](powershell-module.md) herunter, und importieren Sie es, um es in Ihrer PowerShell-Sitzung verwenden zu können.

1. Öffnen Sie Windows PowerShell.

2. Führen Sie das folgende Cmdlet aus, um sich bei Ihrem Azure-Konto anzumelden.

    ```powershell
    Login-AzAccount
    ```

3. Führen Sie das folgende Cmdlet aus, um das Skript zum Erstellen der Azure-Logik-App herunterzuladen.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. Führen Sie das folgende PowerShell-Skript aus, um die Azure-Logik-App und den Ausführungszeitplan für Ihren Hostpool zu erstellen:

    >[!NOTE]
    >Sie müssen dieses Skript für jeden Hostpool ausführen, den Sie automatisch skalieren möchten, Sie benötigen jedoch nur ein Azure Automation-Konto.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $WVDHostPool = Get-AzResource -ResourceType "Microsoft.DesktopVirtualization/hostpools" | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURIARMBased' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "UseARMAPI"                     = $true
         "HostPoolName"                  = $WVDHostPool.Name
         "HostPoolResourceGroupName"     = $WVDHostPool.ResourceGroupName           # Optional. Default: same as ResourceGroupName param value
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    Nachdem Sie das Skript ausgeführt haben, sollte die Azure-Logik-App in einer Ressourcengruppe angezeigt werden, wie in der folgenden Abbildung dargestellt.

    >[!div class="mx-imgBorder"]
    >![Darstellung der Übersicht für eine Beispiel-Logik-App in Azure.](media/logic-app.png)

    Wenn Sie Änderungen am Ausführungszeitplan vornehmen möchten, um z. B. das Wiederholungsintervall oder die Zeitzone zu ändern, navigieren Sie zum Planer für die Autoskalierung der Azure-Logik-App, und wählen Sie **Bearbeiten** aus, um den Logik-App-Designer zu öffnen.

    >[!div class="mx-imgBorder"]
    >![Abbildung des Azure-Logik-App-Designers. Die Menüs „Wiederholung“ und „Webhook“, mit denen Benutzer die Wiederholungszeiten und die Webhook-Datei bearbeiten können, sind geöffnet.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Verwalten Ihres Skalierungstools

Nachdem Sie das Skalierungstool erstellt haben, können Sie auf seine Ausgabe zugreifen. In diesem Abschnitt werden einige Features beschrieben, die möglicherweise hilfreich sind.

### <a name="view-job-status"></a>Anzeigen des Auftragsstatus

Sie können im Azure-Portal einen zusammengefassten Status aller Runbookaufträge anzeigen oder ausführlichere Details zum Status eines bestimmten Runbookauftrags einsehen.

Rechts in Ihrem ausgewählten Azure Automation-Konto sehen Sie unter „Auftragsstatistik“ die Zusammenfassung aller Runbookaufträge. Wenn Sie auf der linken Seite des Fensters die Seite **Aufträge** öffnen, werden die aktuellen Auftragsstatus, Startzeiten und Beendigungszeiten angezeigt.

>[!div class="mx-imgBorder"]
>![Screenshot der Auftragsstatusseite.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Anzeigen von Protokollen und der Ausgabe des Skalierungstools

Sie können die Protokolle der Auf- und Abskaliervorgänge anzeigen, indem Sie das Runbook öffnen und den Auftrag auswählen.

Navigieren Sie in der Ressourcengruppe, in der das Azure Automation-Konto gehostet wird, zum Runbook, und wählen Sie **Übersicht** aus. Wählen Sie auf der Seite „Übersicht“ unter **Kürzlich ausgeführte Aufträge** einen Auftrag aus, um die zugehörige Ausgabe des Skalierungstools anzuzeigen, wie in der folgenden Abbildung dargestellt.

>[!div class="mx-imgBorder"]
>![Darstellung des Ausgabefensters für das Skalierungstool.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Überprüfen der Versionsnummer des Runbookskripts

Sie können überprüfen, welche Version des Runbookskripts Sie verwenden, indem Sie die Runbookdatei in Ihrem Azure Automation-Konto öffnen und **Anzeigen** auswählen. Ein Skript für das Runbook wird auf der rechten Seite des Bildschirms angezeigt. In dem Skript ist die Versionsnummer im Abschnitt `SYNOPSIS` im Format `v#.#.#` zu finden. Die neueste Versionsnummer finden Sie [hier](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/ARM_based/basicScale.ps1#L1). Wenn in Ihrem Runbookskript keine Versionsnummer aufgeführt ist, bedeutet dies, dass Sie eine frühere Version des Skripts ausführen und es sofort aktualisieren sollten. Wenn Sie das Runbookskript aktualisieren müssen, befolgen Sie die Anweisungen unter [Erstellen oder Aktualisieren eines Azure Automation-Kontos](#create-or-update-an-azure-automation-account).

### <a name="reporting-issues"></a>Melden von Problemen

Wenn Sie ein Problem melden, sollten Sie die folgenden Informationen bereitstellen, um die Problembehandlung zu vereinfachen:

- Ein vollständiges Protokoll von der Registerkarte **Alle Protokolle** in dem Auftrag, der das Problem verursacht hat. Befolgen Sie zum Abrufen des Protokolls die Anleitung unter [Anzeigen von Protokollen und der Ausgabe des Skalierungstools](#view-logs-and-scaling-tool-output). Wenn das Protokoll vertrauliche oder private Informationen enthält, können Sie diese entfernen, bevor Sie das Problem an uns senden.

- Die Version des Runbookskripts, das Sie verwenden. Informationen zum Abrufen der Versionsnummer finden Sie unter [Überprüfen der Versionsnummer des Runbookskripts](#check-the-runbook-script-version-number).

- Die Versionsnummern der folgenden in Ihrem Azure Automation-Konto installierten PowerShell-Module. Öffnen Sie dazu Ihr Azure Automation-Konto, wählen Sie links im Fenster unter **Freigegebene Ressourcen** die Option **Module** aus, und suchen Sie nach dem Namen des Moduls.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Az.DesktopVirtualization

- Das Ablaufdatum des [ausführenden Kontos](#create-an-azure-automation-run-as-account). Um dieses zu ermitteln, öffnen Sie Ihr Azure Automation-Konto, und wählen Sie dann unter **Kontoeinstellungen** im Bereich links im Fenster die Option **Ausführende Konten** aus. Das Ablaufdatum sollte unter **Ausführendes Azure-Konto** zu finden sein.

### <a name="log-analytics"></a>Log Analytics

Wenn Sie sich für die Verwendung von Log Analytics entschieden haben, können Sie unter **Benutzerdefinierte Protokolle** in der Ansicht **Protokolle** Ihres Log Analytics-Arbeitsbereichs alle Protokolldaten in einem benutzerdefinierten Protokoll namens **WVDTenantScale_CL** anzeigen. Nachstehend sind einige Beispielabfragen aufgelistet, die Sie möglicherweise hilfreich finden.

- Wenn Sie alle Protokolle für einen Hostpool anzeigen möchten, geben Sie die folgende Abfrage ein:

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Wenn Sie alle derzeit aktiven Sitzungshost-VMs und aktiven Benutzersitzungen in Ihrem Hostpool anzeigen möchten, geben Sie die folgende Abfrage ein:

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Geben Sie zum Abfragen des Status aller Sitzungshost-VMs in einem Hostpool die folgende Abfrage ein:

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Wenn Sie Fehler und Warnungen anzeigen möchten, geben Sie die folgende Abfrage ein:

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Melden von Problemen

Problemberichte für das Skalierungstool werden derzeit vom Microsoft-Support bearbeitet. Wenn Sie einen Problembericht erstellen, befolgen Sie die Anweisungen unter [Melden von Problemen](#reporting-issues). Wenn Sie Feedback zum Tool haben oder neue Features anfordern möchten, öffnen Sie auf der [GitHub-Seite für RDS](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool) ein GitHub-Problem mit der Bezeichnung „4-WVD-scaling-tool“.
