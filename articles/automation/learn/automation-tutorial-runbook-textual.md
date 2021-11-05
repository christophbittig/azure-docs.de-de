---
title: 'Tutorial: Erstellen eines PowerShell-Workflow-Runbooks in Azure Automation'
description: Dieses Tutorial vermittelt Ihnen, wie Sie ein PowerShell Workflow-Runbook erstellen, testen und veröffentlichen.
services: automation
ms.subservice: process-automation
ms.date: 10/28/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c677d7690acc3ab05c5d8df2ab516d396be2eb4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465605"
---
# <a name="tutorial-create-a-powershell-workflow-runbook-in-automation"></a>Tutorial: Erstellen eines PowerShell-Workflow-Runbooks in Automation

Dieses Tutorial führt Sie durch die Erstellung eines [PowerShell-Workflow-Runbooks](../automation-runbook-types.md#powershell-workflow-runbooks) in Azure Automation. PowerShell-Workflow-Runbooks sind Textrunbooks, die auf einem Windows PowerShell-Workflow basieren. Sie können den Code des Runbooks mit dem Text-Editor im Azure-Portal erstellen und bearbeiten.

>[!NOTE]
>  Dieser Artikel gilt für PowerShell 5.1. PowerShell 7.1 (Vorschauversion) unterstützt keine Workflows.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines PowerShell-Workflow-Runbooks
> * Testen und Veröffentlichen des Runbooks
> * Ausführen des Runbookauftrags und Nachverfolgen seines Status
> * Hinzufügen von Authentifizierungsfunktionen für die Verwaltung von Azure-Ressourcen
> * Aktualisieren der Runbook-Parameter zum Starten einer Azure-VM

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Automation-Konto mit mindestens einer benutzerseitig zugewiesenen verwalteten Identität. Weitere Informationen finden Sie unter [Aktivieren einer verwalteten Identität](../quickstarts/enable-managed-identity.md).
* Az-Module: `Az.Accounts` und `Az.Compute`, importiert in das Automation-Konto. Weitere Informationen finden Sie unter [Importieren von Az-Modulen](../shared-resources/modules.md#import-az-modules).
* Mindestens zwei [Azure-VMs](../../virtual-machines/windows/quick-create-powershell.md). Da Sie diese Computer beenden und starten, dürfen sie keine Produktions-VMs sein.
* Das auf Ihrem Computer installierte [PowerShell-Modul „Azure Az“](/powershell/azure/new-azureps-module-az). Informationen zur einer Installation oder einem Upgrade finden Sie unter [Installieren des PowerShell-Moduls „Azure Az“](/powershell/azure/install-az-ps).

## <a name="assign-permissions-to-managed-identities"></a>Zuweisen von Berechtigungen zu verwalteten Identitäten

Weisen Sie der betreffenden [verwalteten Identität](../automation-security-overview.md#managed-identities) Berechtigungen zu, damit sie eine VM beenden kann. Das Runbook kann die systemseitig zugewiesene verwaltete Identität des Automation-Kontos oder eine benutzerseitig zugewiesene verwaltete Identität verwenden. Schritte zur Zuweisung von Berechtigungen für jede Identität sind angegeben. Die folgenden Schritte verwenden das Azure-Portal. Wenn Sie lieber PowerShell verwenden möchten, finden Sie entsprechende Informationen unter [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Automation-Konto.

1. Wählen Sie unter **Kontoeinstellungen** die Option **Identität (Vorschau)** aus.

1. Wählen Sie auf der Registerkarte **Vom System zugewiesen** unter **Berechtigungen** die Option **Azure-Rollenzuweisungen** aus, um die Seite **Azure-Rollenzuweisungen** zu öffnen.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-role-assignments-portal.png" alt-text="Auswahl von Azure-Rollenzuweisungen im Portal":::

1. Wählen Sie **+ Rollenzuweisung hinzufügen (Vorschau)** aus, um die Seite **Rollenzuweisung hinzufügen (Vorschau)** zu öffnen. 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-add-role-assignment-portal.png" alt-text="Hinzufügen von Rollenzuweisungen im Portal":::

1. Wählen Sie die entsprechenden Werte aus.

   |Eigenschaft |BESCHREIBUNG |
   |---|---|
   |`Scope`| „Scope“ bezeichnet eine Gruppe von Ressourcen, für die die Rollenzuweisung gilt. Wählen Sie in der Dropdownliste die Option **Ressourcengruppe** aus.|
   |Subscription|Dieses Feld sollte automatisch mit Ihrem Abonnement ausgefüllt werden.|
   |Ressourcengruppe|Wählen Sie in der Dropdownliste die Ressourcengruppe aus, für die die Identitätsberechtigungen erteilt werden sollen.|
   |Rolle|Wählen Sie in der Dropdownliste den Eintrag **DevTest Labs-Benutzer** aus.|

1. Wählen Sie **Speichern** aus, und schließen Sie dann die Seite **Azure-Rollenzuweisungen**, um zur Registerkarte **Vom System zugewiesen** zurückzukehren.

1. Wählen Sie die Registerkarte **Benutzerseitig zugewiesen** aus.

1. Wählen Sie in der Liste Ihre benutzerseitig zugewiesene verwaltete Identität aus, um die Seite **Verwaltete Identität** zu öffnen.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/select-user-assigned-identity-portal.png" alt-text="Auswahl einer benutzerseitig zugewiesene verwaltete Identität im Portal":::

1. Notieren Sie die **Client-ID** zur späteren Verwendung.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/managed-identity-client-id-portal.png" alt-text="Anzeigen der Client-ID für die verwaltete Identität im Portal":::

1. Wählen Sie im linken Menü die Option **Azure-Rollenzuweisungen** und dann **+ Rollenzuweisung hinzufügen (Vorschau)** aus, um die Seite **Rollenzuweisung hinzufügen (Vorschau)** zu öffnen. 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/user-assigned-add-role-assignment-portal.png" alt-text="Hinzufügen von Rollenzuweisungen im Portal für eine vom Benutzer zugewiesene Identität":::

1. Wählen Sie die entsprechenden Werte aus.

   |Eigenschaft |BESCHREIBUNG |
   |---|---|
   |`Scope`| Wählen Sie in der Dropdownliste die Option **Ressourcengruppe** aus.|
   |Subscription|Dieses Feld sollte automatisch mit Ihrem Abonnement ausgefüllt werden.|
   |Ressourcengruppe|Wählen Sie in der Dropdownliste die Ressourcengruppe aus, für die die Identitätsberechtigungen erteilt werden sollen.|
   |Rolle|Wählen Sie in der Dropdownliste den Eintrag **DevTest Labs-Benutzer** aus.|

1. Wählen Sie **Speichern** aus, und schließen Sie dann die Seite **Azure-Rollenzuweisungen**, um zur Registerkarte **Vom Benutzer zugewiesen** zurückzukehren.

## <a name="create-new-runbook"></a>Erstellen eines neuen Runbooks

Erstellen Sie zunächst ein einfaches [PowerShell-Workflow-Runbook](../automation-runbook-types.md#powershell-workflow-runbooks). Ein Vorteil von Windows PowerShell-Workflows besteht darin, dass sie einen Satz an Befehlen parallel – und nicht wie in einem typischen Skript sequenziell – ausführen können.

>[!NOTE]
> Mit dem Release hat die Runbookerstellung im Azure-Portal eine neue Qualität erhalten. Wenn Sie das Blatt **Runbooks** > **Runbook erstellen** auswählen, wird eine neue Seite **Runbook erstellen** mit den entsprechenden Optionen geöffnet. 

1. Wählen Sie auf Ihrer offenen Automation-Kontoseite unter **Prozessautomatisierung** die Option **Runbooks** aus.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook.png" alt-text="Erstellen eines PowerShell-Workflow-Runbooks im Portal":::

1. Wählen Sie **+ Runbook erstellen** aus.
    1. Benennen Sie das Runbook. Beispiel: Test.
    1. Wählen Sie in der Dropdownliste **Runbook-Typ** den Wert **PowerShell** aus.
    1. Wählen Sie in der Dropdownliste **Runtimeversion** die Version **5.1** aus.
    1. Geben Sie eine anwendbare **Beschreibung** ein.
    1. Klicken Sie auf **Erstellen**.
   
    :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook-options.png" alt-text="Runbookoptionen des PowerShell-Workflows im Portal":::
   

## <a name="add-code-to-the-runbook"></a>Hinzufügen von Code zum Runbook

Sie können direkt Code in das Runbook eingeben, oder Sie wählen Cmdlets, Runbooks und Objekte im Bibliotheksteuerelement aus und fügen diese dem Runbook mit den zugehörigen Parametern hinzu. In diesem Tutorial geben Sie den Code direkt in das Runbook ein.

Ihr Runbook ist zurzeit leer und enthält lediglich das erforderliche Schlüsselwort `Workflow`, den Namen des Runbooks und die geschweiften Klammern, die den gesamten Workflow umschließen.

```powershell
Workflow MyFirstRunbook-Workflow
{
}
```

1. Sie können mit dem Schlüsselwort `Parallel` einen Skriptblock mit mehreren Befehlen erstellen, die gleichzeitig ausgeführt werden sollen. Geben Sie den folgenden Code *zwischen* den geschweiften Klammern ein:

   ```powershell
   Parallel {
        Write-Output "Parallel"
        Get-Date
        Start-Sleep -s 3
        Get-Date
    }

   Write-Output " `r`n"
   Write-Output "Non-Parallel"
   Get-Date
   Start-Sleep -s 3
   Get-Date  
   ```

1. Wählen Sie **Speichern** aus, um das Runbook zu speichern.

## <a name="test-the-runbook"></a>Testen des Runbooks

Bevor Sie das Runbook für die Verwendung in der Produktionsumgebung veröffentlichen, sollten Sie es testen, um sicherzustellen, dass es ordnungsgemäß funktioniert. Beim Testen eines Runbooks führen Sie die Entwurfsversion aus und sehen sich interaktiv die Ausgabe an.

1. Klicken Sie auf **Testbereich**, um die Seite **Test** zu öffnen.

1. Wählen Sie die Option **Starten** aus, um den Test zu starten.  Ein [Runbookauftrag](../automation-runbook-execution.md) wird erstellt, und der zugehörige Status wird im Bereich angezeigt.

   Der Auftrag weist zunächst den Status „In Warteschlange“ auf, der angibt, dass der Auftrag darauf wartet, dass in der Cloud ein Runbook Worker verfügbar wird. Der Status ändert sich in „Wird gestartet“, wenn ein Worker den Auftrag beansprucht. Schließlich ändert sich der Status in „Wird ausgeführt“, wenn die Ausführung des Runbooks beginnt.

1. Nach Abschluss des Runbookauftrags wird die Ausgabe auf der Seite **Test** angezeigt. Die Ausgabe sollte in etwa wie auf der folgenden Abbildung aussehen:

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-parallel-output.png" alt-text="Parallele Ausgabe des PowerShell-Workflow-Runbooks":::

   Überprüfen Sie die Ausgabe. Alles im `Parallel`-Block, einschließlich des `Start-Sleep`-Befehls, wurde gleichzeitig ausgeführt. Die gleichen Befehle außerhalb des `Parallel`-Blocks wurden sequenziell ausgeführt, wie die unterschiedlichen Datums-/Uhrzeitstempel zeigen. 

1. Schließen Sie die Seite **Test**, um zum Canvas-Panel zurückzukehren.

## <a name="publish-and-start-the-runbook"></a>Veröffentlichen und Starten des Runbooks

Das erstellte Runbook befindet sich immer noch im Entwurfsmodus. Sie müssen das Runbook zuerst veröffentlichen, um es in der Produktionsumgebung ausführen zu können. Beim Veröffentlichen eines Runbooks wird die vorhandene veröffentlichte Version durch die Entwurfsversion überschrieben. In diesem Fall ist noch keine veröffentlichte Version vorhanden, da Sie das Runbook gerade erst erstellt haben.

1. Klicken Sie auf **Veröffentlichen**, um das Runbook zu veröffentlichen, und bestätigen Sie den Vorgang mit **Ja**.

1. Im Feld **Status** wird jetzt **Veröffentlicht** angezeigt. Beachten Sie die Optionen im oberen Bereich, mit denen Sie das Runbook jetzt starten, den Start für einen späteren Zeitpunkt planen oder einen [Webhook](../automation-webhooks.md) erstellen können, um das Runbook über einen HTTP-Aufruf zu starten. Klicken Sie zum Starten des Runbooks auf **Starten** und anschließend auf **Ja**.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-overview.png" alt-text="Übersichtsseite für PowerShell-Workflow-Runbooks":::

1. Für den erstellten Runbookauftrag wird die Seite **Auftrag** geöffnet. Lassen Sie in diesem Fall die Seite geöffnet, damit Sie den Fortschritt des Auftrags beobachten können. Das Feld **Status** stimmt mit einem der Status überein, die Sie beim Testen des Runbooks gesehen haben.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-page-overview.png" alt-text="Screenshot der Seite „Auftrag“ des Runbooks":::

1. Wählen Sie die Option **Ausgabe** aus, wenn der Runbookstatus **Abgeschlossen** lautet. Die Ausgabe sollte ähnlich wie die Testausgabe aussehen.

1. Schließen Sie die Seite **Auftrag**, um zur **Übersichtsseite** des Runbooks zurückzukehren.

1. Wählen Sie unter **Ressourcen** die Option **Aufträge** aus. Auf dieser Seite werden alle von diesem Runbook erstellten Aufträge aufgeführt. Es sollte nur ein Auftrag aufgeführt sein, da Sie den Auftrag bislang erst einmal ausgeführt haben.

1. Wählen Sie den Auftrag aus, um die Seite **Auftrag** zu öffnen, die auch beim Starten des Runbooks angezeigt wurde. Auf dieser Seite können Sie Details zu jedem Auftrag anzeigen, der für das Runbook erstellt wurde. Schließen Sie die Seite **Auftrag**, um zur **Übersichtsseite** des Runbooks zurückzukehren.

## <a name="add-authentication-to-manage-azure-resources"></a>Hinzufügen von Authentifizierungsfunktionen für die Verwaltung von Azure-Ressourcen

Sie haben Ihr Runbook inzwischen zwar getestet und veröffentlicht, bislang ist es aber noch nicht sonderlich hilfreich. Sie möchten damit ja eigentlich Azure-Ressourcen verwalten. Dies ist nur möglich, wenn die Authentifizierung mit den Anmeldeinformationen für das Abonnement erfolgt. Das Runbook verwendet die systemseitig zugewiesene verwaltete Identität des Automation-Kontos für die Authentifizierung bei Azure, um die Verwaltungsaktion für die VM durchzuführen. Das Runbook kann problemlos geändert werden, um eine benutzerseitig zugewiesene verwaltete Identität zu verwenden.

1. Wählen Sie **Übersicht** und dann **Bearbeiten** aus, um den Text-Editor zu öffnen.

1. Ersetzen Sie den gesamten vorhandenen Code durch den folgenden:

   ```powershell
   workflow MyFirstRunbook-Workflow
   {
   $resourceGroup = "resourceGroupName"
    
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process
    
   # Connect to Azure with system-assigned managed identity
   $AzureContext = (Connect-AzAccount -Identity).context
    
   # set and store context
   $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
   }
   ```

   Bearbeiten Sie die `$resourceGroup`-Variable mit einem gültigen Wert, der Ihre Ressourcengruppe darstellt.

1. Wenn Sie möchten, dass das Runbook mit der systemseitig zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code unverändert. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:
    1. Entfernen Sie `$AzureContext = (Connect-AzAccount -Identity).context` aus Zeile 9.
    1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
    1. Geben Sie die zuvor abgerufene Client-ID ein.

1. Wählen Sie **Speichern** und anschließend **Testbereich** aus.

1. Wählen Sie die Option **Starten** aus, um den Test zu starten. Nach Abschluss des Tests sollte eine Ausgabe ähnlich der nachstehenden mit allgemeinen Informationen aus Ihrem Konto angezeigt werden. Diese Aktion bestätigt, dass die Anmeldeinformationen gültig sind.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/runbook-auth-output.png" alt-text="Grundlegende Informationen zur Bestätigung von Anmeldeinformationen":::

1. Schließen Sie die Seite **Test**, um zum Canvas-Panel zurückzukehren.

## <a name="add-code-to-start-a-virtual-machine"></a>Hinzufügen von Code zum Starten eines virtuellen Computers

Nachdem das Runbook jetzt im Azure-Abonnement authentifiziert ist, können Sie Ressourcen verwalten. Fügen Sie einen Befehl zum Starten einer VM hinzu. Sie können eine beliebige VM in Ihrem Azure-Abonnement auswählen. Vorerst geben Sie diesen Namen im Runbook fest ein. 

1. Fügen Sie den folgenden Code als letzte Zeile unmittelbar vor der schließenden geschweiften Klammer hinzu. Ersetzen Sie `VMName` durch den tatsächlichen Namen einer VM. 

   ```powershell
   Start-AzVM -Name "VMName" -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Testen Sie das Runbook, und vergewissern Sie sich, dass die VM gestartet wurde. Kehren Sie dann zum Canvas-Panel zurück.

## <a name="add-input-parameters-to-the-runbook"></a>Fügen Sie dem Runbook Eingabeparameter hinzu.

Ihr Runbook startet derzeit die im Runbook hartcodierte VM. Es ist nützlicher, wenn Sie die VM beim Start des Runbooks angeben können. Fügen Sie zu diesem Zweck dem Runbook Eingabeparameter hinzu.

1. Ersetzen Sie Zeile 3, `$resourceGroup = "resourceGroupName"`, durch folgenden Code:

    ```powershell
    Param(
        [string]$resourceGroup,
        [string]$VMName
    )
   ```

1. Ersetzen Sie den vorherigen `Start-AzVM`-Befehl durch den folgenden Befehl:

   ```powershell
   Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Testen Sie das Runbook, und vergewissern Sie sich, dass die VM gestartet wurde. Kehren Sie dann zum Canvas-Panel zurück.

## <a name="manage-multiple-vms-simultaneously"></a>Gleichzeitiges Verwalten mehrerer VMs

Sie können das Konstrukt `ForEach -Parallel` verwenden, um Befehle für jedes Element in einer Auflistung gleichzeitig zu verarbeiten. Überarbeiten Sie den Code so, dass das Runbook jetzt:
- eine Sammlung von Namen virtueller Maschinen akzeptiert,
- einen Parameter zum Beenden oder Starten der VMs akzeptiert und
- die Aktionen für alle VMs parallel ausführt.

1. Ersetzen Sie den gesamten vorhandenen Code durch den folgenden:

    ```powershell
    workflow MyFirstRunbook-Workflow
    {
    Param(
        [string]$resourceGroup,
        [string[]]$VMs,
        [string]$action
    )
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
    
    # Start or stop VMs in parallel
    if($action -eq "Start")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Start-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
            }
        }
    elseif ($action -eq "Stop")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Stop-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
            }
        }
    else {
            Write-Output "`r`n Action not allowed. Please enter 'stop' or 'start'."
        }
    }
    ```

1. Wenn Sie möchten, dass das Runbook mit der systemseitig zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code unverändert. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:
    1. Entfernen Sie `$AzureContext = (Connect-AzAccount -Identity).context` aus Zeile 13.
    1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
    1. Geben Sie die zuvor abgerufene Client-ID ein.

1. Wählen Sie **Speichern**, dann **Veröffentlichen** und dann **Ja**, wenn Sie dazu aufgefordert werden.

1. Wählen Sie auf der Seite **Übersicht** die Option **Starten** aus.

1. Geben Sie die Parameter an, und wählen Sie dann **OK** aus.

   |Parameter |BESCHREIBUNG |
   |---|---|
   |RESOURCEGROUP|Geben Sie den Namen der Ressourcengruppe der VMs ein.|
   |VMs|Geben Sie die Namen der VMs mit der folgenden Syntax ein: `["VM1","VM2","VM3"]`|
   |Aktion|Geben Sie `stop` oder `start` ein.|

1. Navigieren Sie zu Ihrer VM-Liste, und aktualisieren Sie die Seite alle paar Sekunden. Beachten Sie, dass die Aktion für jede VM parallel erfolgt. Ohne das `-Parallel`-Schlüsselwort wären die Aktionen sequenziell ausgeführt worden. Während die VMs sequenziell gestartet werden, kann jede VM die **Ausführungsphase** je nach den Merkmalen der einzelnen VMs zu etwas anderen Zeiten erreichen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie dieses Runbook nicht weiterverwenden möchten, sollten Sie es wie folgt löschen:

1. Navigieren Sie zu Ihrem Automation-Konto.
1. Wählen Sie unter **Prozessautomatisierung** die Option **Runbooks** aus.
1. Wählen Sie das Runbook aus.
1. Wählen Sie auf der **Übersichtsseite** des Runbooks die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein PowerShell-Workflow-Runbook erstellt. Informationen zu Python 3-Runbooks finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines Python 3-Runbooks (Vorschau)](automation-tutorial-runbook-textual-python-3.md)