---
title: Bearbeiten von Textrunbooks in Azure Automation
description: In diesem Artikel wird beschrieben, wie Sie den Azure Automation-Text-Editor für das Arbeiten mit PowerShell- und PowerShell-Workflow-Runbooks verwenden.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 08/01/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dfab1cb98a15bb919ac017607a56366134149764
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435432"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Bearbeiten von Textrunbooks in Azure Automation

Mit dem Text-Editor in Azure Automation können Sie [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) und [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) bearbeiten. Dieser Editor hat die typischen Merkmale anderer Code-Editoren, z. B. IntelliSense. Er verfügt außerdem über eine Farbcodierung mit zusätzlichen speziellen Features, die Ihnen den Zugriff auf Ressourcen erleichtern, die für Runbooks üblich sind.

Der Text-Editor enthält ein Feature zum Einfügen von Code für Cmdlets, Objekte und untergeordnete Runbooks in ein Runbook. Anstatt den Code selbst einzugeben, können Sie aus einer Liste verfügbarer Ressourcen auswählen und der Editor fügt den entsprechenden Code in das Runbook ein.

Jedes Runbook in Azure Automation umfasst zwei Versionen: eine Entwurfsversion und eine veröffentlichte Version. Sie bearbeiten die Entwurfsversion des Runbooks und veröffentlichen sie anschließend, damit sie ausgeführt werden kann. Die veröffentlichte Version kann nicht bearbeitet werden. Weitere Informationen finden Sie unter [Veröffentlichen eines Runbooks](manage-runbooks.md#publish-a-runbook).

Dieser Artikel enthält detaillierte Schritte zum Ausführen verschiedener Funktionen mit diesem Editor. Diese gelten nicht für [Grafische Runbooks](automation-runbook-types.md#graphical-runbooks). Informationen zur Arbeit mit diesen Runbooks finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).

> [!IMPORTANT] 
> Fügen Sie das Schlüsselwort AzureRm nicht in ein Skript ein, das mit dem Az-Modul ausgeführt werden soll. Die Einbeziehung des Schlüsselworts, auch in einen Kommentar, kann dazu führen, dass AzureRm geladen wird und dann in Konflikt mit dem Az-Modul gerät.

## <a name="edit-a-runbook-with-the-azure-portal"></a>Bearbeiten eines Runbooks mit dem Azure-Portal

1. Wählen Sie im Azure-Portal Ihr Automation-Konto aus.
2. Klicken Sie unter **PROZESSAUTOMATISIERUNG** auf **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Wählen Sie das zu bearbeitende Runbook aus, und klicken Sie dann auf **Bearbeiten**.
4. Bearbeiten Sie das Runbook.
5. Klicken Sie auf **Speichern** , wenn die Bearbeitung abgeschlossen ist.
6. Klicken Sie auf **Veröffentlichen**, wenn Sie die neueste Entwurfsversion des Runbooks veröffentlichen möchten.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Einfügen eines Cmdlets in ein Runbook

1. Platzieren Sie den Cursor im Canvas des Text-Editors an der Position, an der Sie das Cmdlet einfügen möchten.
2. Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets** .
3. Erweitern Sie das Modul mit dem zu verwendenden Cmdlet.
4. Klicken Sie mit der rechten Maustaste auf den Namen des einzufügenden Cmdlets, und wählen Sie **Zur Canvas hinzufügen** aus. Wenn das Cmdlet über mehrere Parametersätze verfügt, fügt der Editor den Standardsatz hinzu. Sie können das Cmdlet auch erweitern, um einen anderen Parametersatz auszuwählen.
5. Beachten Sie, dass der Code für das Cmdlet mit der gesamten Liste der Parameter eingefügt wird.
6. Geben Sie für jeden erforderlichen Parameter einen geeigneten Wert anstelle des von spitzen Klammern (<>) umgebenen Werts an. Entfernen Sie alle nicht erforderlichen Parameter.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Einfügen von Code für ein untergeordnetes Runbook in ein Runbook

1. Platzieren Sie den Cursor in der Canvas des Text-Editors an der Position, an der Sie den Code für das [untergeordnete Runbook](automation-child-runbooks.md) einfügen möchten.
2. Erweitern Sie im Bibliotheksteuerelement den Knoten **Runbooks** .
3. Klicken Sie mit der rechten Maustaste auf das einzufügende Runbook, und wählen Sie **Add to canvas** (Zur Canvas hinzufügen).
4. Der Code für das untergeordnete Runbook wird mit allen Platzhaltern für alle Runbook-Parameter eingefügt.
5. Ersetzen Sie die Platzhalter durch entsprechende Werte für jeden Parameter.

### <a name="insert-an-asset-into-a-runbook"></a>Einfügen eines Objekts in ein Runbook

1. Platzieren Sie den Cursor im Canvassteuerelement des Text-Editors an der Position, an der Sie den Code für das untergeordnete Runbook einfügen möchten.
2. Erweitern Sie im Bibliotheksteuerelement den Knoten **Objekte** .
3. Erweitern Sie den Knoten für den gewünschten Objekttyp.
4. Klicken Sie mit der rechten Maustaste auf den Namen des einzufügenden Objekts, und wählen Sie **Zur Canvas hinzufügen** aus. Wählen Sie für [Variablenobjekte](./shared-resources/variables.md) entweder **„Variable abrufen“ zum Zeichenbereich hinzufügen** oder **„Variable festlegen“ zum Zeichenbereich hinzufügen** aus. Dies ist abhängig davon, ob Sie die Variable abrufen oder festlegen möchten.
5. Beachten Sie, dass der Code für das Objekt in das Runbook eingefügt wird.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Bearbeiten eines Azure Automation-Runbooks mit Windows PowerShell

Zum Bearbeiten eines Runbooks mit Windows PowerShell verwenden Sie einen Editor Ihrer Wahl und speichern dann das Runbook in einer **PS1**-Datei. Sie können das Cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) verwenden, um den Inhalt des Runbooks abzurufen. Sie können das Cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) verwenden, um den vorhandenen Entwurf des Runbooks durch das geänderte Runbook zu ersetzen.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Abrufen des Inhalts eines Runbooks mit Windows PowerShell

Die folgenden Beispielbefehle zeigen, wie das Skript für ein Runbook abgerufen und in einer Skriptdatei gespeichert wird. In diesem Beispiel wird die Entwurfsversion abgerufen. Sie können auch die veröffentlichte Version des Runbooks abrufen, auch wenn diese Version nicht geändert werden kann.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Ändern des Inhalts eines Runbooks mit Windows PowerShell

Die folgenden Beispielbefehle zeigen, wie der vorhandene Inhalt eines Runbooks durch den Inhalt einer Skriptdatei ersetzt wird. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen unter [Verwalten von Runbooks in Azure Automation](manage-runbooks.md).
* Informationen zu den ersten Schritten mit PowerShell-Workflow-Runbooks finden Sie unter [Informationen zum PowerShell-Workflow](automation-powershell-workflow.md).
* [Zertifikate](./shared-resources/certificates.md):
* [Verbindungen](automation-connections.md).
* [Anmeldeinformationen:](./shared-resources/credentials.md)
* [Zeitpläne:](./shared-resources/schedules.md)
* [Variablen:](./shared-resources/variables.md)
* Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
