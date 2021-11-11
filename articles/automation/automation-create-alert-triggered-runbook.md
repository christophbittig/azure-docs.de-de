---
title: Verwenden einer Warnung zum Auslösen eines Azure Automation-Runbooks
description: In diesem Artikel wird beschrieben, wie Sie bei Auslösung einer Azure-Warnung ein Runbook auslösen.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2584390610aaef5ddd8364d33f3c065ecf3bd321
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443830"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Verwenden einer Warnung zum Auslösen eines Azure Automation-Runbooks

Sie können [Azure Monitor](../azure-monitor/overview.md) verwenden, um in Azure grundlegende Metriken und Protokolle für die meisten Dienste zu überwachen. Sie können Azure Automation-Runbooks aufrufen, indem Sie [Aktionsgruppen](../azure-monitor/alerts/action-groups.md) verwenden, um Aufgaben basierend auf Warnungen zu automatisieren. In diesem Artikel erfahren Sie, wie Sie ein Runbook mit Warnungen konfigurieren und ausführen.


## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Automation-Konto mit mindestens einer benutzerseitig zugewiesenen verwalteten Identität. Weitere Informationen finden Sie unter [Verwenden einer benutzerseitig zugewiesenen verwalteten Identität für ein Azure Automation-Konto](./add-user-assigned-identity.md).
* Az-Module: `Az.Accounts` und `Az.Compute`, importiert in das Automation-Konto. Weitere Informationen finden Sie unter [Importieren von Az-Modulen](./shared-resources/modules.md#import-az-modules).
* Von eine [virtuellen Azure-Computer](../virtual-machines/windows/quick-create-powershell.md).
* Das auf Ihrem Computer installierte [PowerShell-Modul „Azure Az“](/powershell/azure/new-azureps-module-az). Informationen zur einer Installation oder einem Upgrade finden Sie unter [Installieren des PowerShell-Moduls „Azure Az“](/powershell/azure/install-az-ps).
* Allgemeine Vertrautheit mit [Automation-Runbooks](./manage-runbooks.md).

## <a name="alert-types"></a>Warnungstypen

Sie können Automation-Runbooks mit drei Warnungstypen verwenden:

* Allgemeine Warnungen
* Aktivitätsprotokollwarnungen
* Metrikwarnungen nahezu in Echtzeit

> [!NOTE]
> Mit dem allgemeinen Warnungsschema wird die Benutzeroberfläche für Warnungsbenachrichtigungen in Azure standardisiert. Bisher wurden für die Warnungstypen von Azure (Metrik, Protokoll und Aktivitätsprotokoll) eigene E-Mail-Vorlagen, Webhookschemas usw. verwendet. Weitere Informationen finden Sie unter [Allgemeines Warnungsschema](../azure-monitor/alerts/alerts-common-schema.md).

Wenn eine Warnung ein Runbook aufruft, erfolgt der eigentliche Aufruf in Form einer HTTP POST-Anforderung an den Webhook. Der Text der POST-Anforderung enthält ein JSON-formatiertes Objekt, das nützliche Eigenschaften im Zusammenhang mit der Warnung enthält. In der folgenden Tabelle sind Links zu den Nutzlastschemas der einzelnen Warnungstypen angegeben:

|Warnung  |BESCHREIBUNG|Nutzlast und Schema  |
|---------|---------|---------|
|[Allgemeines Warnungsschema](../azure-monitor/alerts/alerts-common-schema.md)|Mit dem allgemeinen Warnungsschema wird die Benutzeroberfläche für Warnungsbenachrichtigungen in Azure standardisiert.|Nutzdatenschema von allgemeinen Warnungen|
|[Aktivitätsprotokollwarnung](../azure-monitor/alerts/activity-log-alerts.md) |Sendet eine Benachrichtigung, wenn ein beliebiges neues Ereignis im Azure-Aktivitätsprotokoll bestimmte Bedingungen erfüllt. Beispiel: Wenn ein `Delete VM`-Vorgang in **myProductionResourceGroup** auftritt oder wenn ein neues Azure Service Health-Ereignis mit dem Status „Aktiv“ angezeigt wird.| [Nutzlastschema vom Typ „Aktivitätsprotokollwarnung“](../azure-monitor/alerts/activity-log-alerts-webhook.md)     |
|[Near Real-Time Metric Alerts](../azure-monitor/alerts/alerts-metric-near-real-time.md) | Sendet eine Benachrichtigung schneller als Metrikwarnungen, wenn mindestens eine Metrik auf Plattformebene bestimmte Bedingungen erfüllt. Beispiel: Wenn der Wert für **CPU in %** auf einer VM größer als „90“ ist und der Wert für **Netzwerk eingehend** in den letzten fünf Minuten über „500 MB“ gelegen hat.| [Nutzlastschema vom Typ „Near Real-Time Metric Alert“](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Da sich die Daten unterscheiden, die von den einzelnen Typen von Warnungen bereitgestellt werden, wird jeder Warnungstyp anders behandelt. Im nächsten Abschnitt erfahren Sie, wie Sie ein Runbook erstellen, um verschiedene Warnungstypen zu behandeln.

## <a name="assign-permissions-to-managed-identities"></a>Zuweisen von Berechtigungen zu verwalteten Identitäten

Weisen Sie der betreffenden [verwalteten Identität](./automation-security-overview.md#managed-identities) Berechtigungen zu, damit sie eine VM beenden kann. Das Runbook kann die vom System zugewiesene verwaltete Identität des Automation-Kontos oder eine von den Benutzer*innen zugewiesene verwaltete Identität verwenden. Schritte zur Zuweisung von Berechtigungen für jede Identität sind angegeben. In den folgenden Schritten wird PowerShell verwendet. Informationen zur Verwendung des Azure-Portals finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](./../role-based-access-control/role-assignments-portal.md).

1. Melden Sie sich interaktiv mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Azure an, und befolgen Sie die Anweisungen.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. Geben Sie einen geeigneten Wert für die unten stehenden Variablen an, und führen Sie dann das Skript aus.

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "AutomationAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    ```

1. Verwenden Sie das PowerShell-Cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), um der systemseitig zugewiesenen verwalteten Identität eine Rolle zu zuweisen.

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. Weisen Sie einer benutzerseitig zugewiesenen verwalteten Identität eine Rolle zu.

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity)
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. Zeigen Sie für die systemseitig zugewiesene verwaltete Identität den Wert `ClientId` an, und speichern Sie ihn für die spätere Verwendung.

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-a-runbook-to-handle-alerts"></a>Erstellen eines Runbooks zum Behandeln von Warnungen

Um bei Warnungen Automation zu verwenden, benötigen Sie ein Runbook, das die an das Runbook übergebenen JSON-Nutzdaten der Warnungen verwaltet. Das folgende Beispielrunbook muss von einer Azure-Warnung aufgerufen werden.

Wie im vorherigen Abschnitt beschrieben, weist jeder Typ von Warnung ein anderes Schema auf. Mit dem Skript werden die Webhookdaten im Runbook-Eingabeparameter `WebhookData` einer Warnung verwendet. Anschließend wertet das Skript die JSON-Nutzlast aus, um zu bestimmen, welcher Warnungstyp verwendet wird.

In diesem Beispiel wird eine Warnung von einer Azure-VM verwendet. Es ruft die VM-Daten aus der Nutzlast ab und verwendet diese Informationen dann, um den virtuellen Computer zu beenden. Die Verbindung muss in dem Automation-Konto eingerichtet sein, unter dem das Runbook ausgeführt wird. Wenn Sie Warnungen zum Auslösen von Runbooks verwenden, müssen Sie unbedingt den Zustand der Warnung im ausgelösten Runbook überprüfen. Das Runbook wird bei jeder Zustandsänderung der Warnung ausgelöst. Warnungen verfügen über mehrere Zustände, deren beiden häufigsten „Aktiviert“ und „Aufgelöst“ sind. Prüfen Sie in Ihrer Runbooklogik auf diesen Zustand, um sicherzustellen, dass Ihr Runbook nur einmal ausgeführt wird. Das Beispiel in diesem Artikel zeigt, wie Sie die Suche auf Warnungen mit dem Zustand „Aktiviert“ einschränken.

Das Runbook verwendet die [systemseitig zugewiesene verwaltete Identität](./automation-security-overview.md#managed-identities) des Automation-Kontos für die Authentifizierung bei Azure, um die Verwaltungsaktion für die VM durchzuführen. Das Runbook kann problemlos geändert werden, um eine benutzerseitig zugewiesene verwaltete Identität zu verwenden.

Verwenden Sie dieses Beispiel, um ein Runbook mit dem Namen **Stop-AzureVmInResponsetoVMAlert** zu erstellen. Sie können das PowerShell-Skript ändern und mit vielen verschiedenen Ressourcen nutzen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem Automation-Konto.

1. Wählen Sie unter **Prozessautomatisierung** die Option **Runbooks** aus.

1. Wählen Sie **+ Runbook erstellen** aus.
    1. Nennen Sie das Runbook `Stop-AzureVmInResponsetoVMAlert`.
    1. Wählen Sie in der Dropdownliste **Runbooktyp** den Wert **PowerShell** aus.
    1. Klicken Sie auf **Erstellen**.

1. Fügen Sie im Runbook-Editor den folgenden Code ein:

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }
    
        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose
    
            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose
    
                # Ensures you do not inherit an AzContext in your runbook
                Disable-AzContextAutosave -Scope Process
     
                # Connect to Azure with system-assigned managed identity
                $AzureContext = (Connect-AzAccount -Identity).context
  
                # set and store context
                $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
    
                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -DefaultProfile $AzureContext -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

1. Wenn Sie möchten, dass das Runbook mit der systemseitig zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code unverändert. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:
    1. Entfernen Sie `$AzureContext = (Connect-AzAccount -Identity).context` aus Zeile 78.
    1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
    1. Geben Sie die zuvor abgerufene Client-ID ein.

1. Wählen Sie **Speichern**, anschließend **Veröffentlichen** und dann **Ja** aus, wenn Sie dazu aufgefordert werden.

1. Schließen Sie die Seite **Runbook**, um zur Seite **Automation-Konto** zurückzukehren.

## <a name="create-the-alert"></a>Erstellen der Warnung

Warnungen verwenden Aktionsgruppen, die Sammlungen von Aktionen sind, die von der Warnung ausgelöst werden. Runbooks stellen lediglich eine der vielen Aktionen dar, die Sie mit Aktionsgruppen verwenden können.

1. Wählen Sie in Ihrem Automation-Konto unter **Überwachung** die Option **Warnungen** aus.

1. Wählen Sie **+ Neue Warnungsregel** aus, um die Seite **Warnungsregel erstellen** zu öffnen.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-alert-rule-portal.png" alt-text="Die Seite „Warnungsregel erstellen“ mit Unterabschnitten":::

1. Wählen Sie unter **Bereich** die Option **Ressource bearbeiten** aus. 

1. Wählen Sie auf der Seite **Ressource auswählen** in der Dropdownliste **Nach Ressourcentyp filtern** die Option **VMs** aus.

1. Aktivieren Sie das Kontrollkästchen neben den VMs, die Sie überwachen möchten. Wählen Sie dann **Fertig** aus, um zurück zur Seite **Warnungsregel erstellen** zu wechseln.

1. Wählen Sie unter **Bedingung** die Option **Bedingung hinzufügen** aus.

1. Geben Sie auf der Seite **Signal auswählen** `Percentage CPU` in das Suchtextfeld ein, und wählen Sie dann in den Ergebnissen **CPU in Prozent** aus.

1. Geben Sie auf der Seite **Signallogik konfigurieren** unter **Schwellenwert** einen anfänglichen niedrigen Wert für Testzwecke ein, z. B. `5`. Sie können diesen Wert später ändern, nachdem Sie sich vergewissert haben, dass die Warnung wie erwartet funktioniert. Wählen Sie dann **Fertig** aus, um zurück zur Seite **Warnungsregel erstellen** zu wechseln.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-signal-logic-portal.png" alt-text="Eingabe des Schwellenwerts für „CPU in Prozent“":::
 
1. Wählen Sie unter **Aktionen** die Option **Aktionsgruppen hinzufügen** und dann **+ Aktionsgruppe erstellen** aus.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-action-group-portal.png" alt-text="Die Seite „Aktionsgruppe erstellen“ mit geöffneter Registerkarte „Grundeinstellungen“":::

1. Führen Sie auf der Seite **Aktionsgruppe erstellen** folgende Schritte aus:
    1. Geben Sie auf der Registerkarte **Grundeinstellungen** einen **Aktionsgruppennamen** und einen **Anzeigenamen** ein.
    1. Geben Sie auf der Registerkarte **Aktionen** im Textfeld **Name** einen Namen ein. Wählen Sie dann in der Dropdownliste **Aktionstyp** die Option **Automation Runbook** aus, um die Seite **Runbook konfigurieren** zu öffnen.
        1. Wählen Sie als **Runbook-Quelle** die Option **Benutzer** aus.  
        1. Wählen Sie in der Dropdownliste **Abonnement** Ihr Abonnement aus.
        1. Wählen Sie in der Dropdownliste **Automation-Konten** Ihr Automation-Konto aus.
        1. Wählen Sie in der Dropdownliste **Runbook** den Eintrag **Stop-AzureVmInResponsetoVMAlert** aus.
        1. Wählen Sie **Ja** für **Allgemeines Warnungsschema aktivieren** aus.
        1. Wählen Sie **OK** aus, um zur Seite **Aktionsgruppe erstellen** zurück zu wechseln.
        
            :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-runbook-portal.png" alt-text="Die Seite „Runbook konfigurieren“ mit Werten":::

    1. Wählen Sie **Überprüfen und erstellen** und dann **Erstellen** aus, um zurück zur Seite **Warnungsregel erstellen** zu wechseln.

1. Geben Sie unter **Warnungsregeldetails** einen Wert in das Textfeld **Name der Warnungsregel** ein.

1. Wählen Sie **Warnungsregel erstellen** aus.  Sie können diese Aktionsgruppe in Ihren [Aktivitätsprotokollwarnungen](../azure-monitor/alerts/activity-log-alerts.md) und [Warnungen in Quasi-Echtzeit](../azure-monitor/alerts/alerts-overview.md) verwenden, die Sie erstellen.

## <a name="verification"></a>Überprüfung

Stellen Sie sicher, dass Ihre VM ausgeführt wird. Navigieren Sie zum Runbook **Stop-AzureVmInResponsetoVMAlert**, und überprüfen Sie, ob die Liste **Zuletzt ausgeführte Aufträge** gefüllt wird. Wenn ein abgeschlossener Auftrag angezeigt wird, wählen Sie den Auftrag aus, und überprüfen Sie die Ausgabe. Überprüfen Sie auch, ob Ihre VM beendet wurde.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/job-result-portal.png" alt-text="Anzeigen der Ausgabe eines Auftrags":::


## <a name="next-steps"></a>Nächste Schritte

* Informationen zu verschiedenen Methoden zum Starten eines Runbooks finden Sie unter [Starten eines Runbooks](./start-runbooks.md).
* Informationen zum Erstellen von Aktivitätsprotokollwarnungen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen](../azure-monitor/alerts/activity-log-alerts.md).
* Informationen zum Erstellen von Near Real-Time Alerts finden Sie unter [Erstellen einer Warnungsregel im Azure-Portal](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json).

