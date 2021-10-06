---
title: Verwalten von Zeitplänen in Azure Automation
description: In diesem Artikel wird beschrieben, wie in Azure Automation ein Zeitplan erstellt und verwendet wird.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/29/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 012380b98987bc09440d025b6533b62cc1a4c043
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271648"
---
# <a name="manage-schedules-in-azure-automation"></a>Verwalten von Zeitplänen in Azure Automation

Um ein Runbook in Azure-Automation für die Ausführung zu einer bestimmten Uhrzeit zu planen, müssen Sie es mit einem oder mehreren Zeitplänen verknüpfen. Ein Zeitplan kann so konfiguriert werden, dass er entweder einmalig oder nach einem sich wiederholenden stündlichen oder täglichen Zeitplan für Runbooks im Azure-Portal ausgeführt wird. Sie können Zeitpläne auch wöchentlich, monatlich, für bestimmte Wochentage oder Tage des Monats oder einen bestimmten Tag des Monats festlegen. Ein Runbook kann mit mehreren Zeitplänen verknüpft werden, und mit einem Zeitplan können mehrere Runbooks verknüpft sein.

> [!NOTE]
> Azure Automation unterstützt die Sommerzeit und plant entsprechend für Automatisierungsvorgänge.

> [!NOTE]
> Zeitpläne sind derzeit nicht für Azure Automation DSC-Konfigurationen aktiviert.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>PowerShell-Cmdlets für den Zugriff auf Zeitpläne

Über die Cmdlets in der folgenden Tabelle können Sie Zeitpläne für Automation mit PowerShell erstellen und verwalten. Diese sind im Lieferumfang der Az-Module enthalten.

| Cmdlets | BESCHREIBUNG |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Ruft einen Zeitplan ab. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Ruft geplante Runbooks ab. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Erstellt einen neuen Zeitplan. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Ordnet ein Runbook einem Zeitplan zu. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Entfernt einen Zeitplan. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Legt die Eigenschaften für einen vorhandenen Zeitplan fest. |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Hebt die Zuordnung eines Runbook zu einem Zeitplan auf. |

## <a name="create-a-schedule"></a>Erstellen eines Zeitplans

Sie können einen neuen Zeitplan für Ihre Runbooks erstellen, indem Sie das Azure-Portal, PowerShell oder eine ARM-Vorlage (Azure Resource Manager) verwenden. Zum Vermeiden von Auswirkungen auf Ihre Runbooks und die Prozesse, die Sie automatisieren, testen Sie zuerst alle Runbooks mit verknüpften Zeitplänen mit einem für Testzwecke vorgesehenen Automation-Konto. Bei einem Test wird überprüft, ob Ihre geplanten Runbooks weiterhin ordnungsgemäß funktionieren. Wenn ein Problem auftritt, können Sie es beheben und alle erforderlichen Änderungen anwenden, bevor Sie die aktualisierte Runbookversion zur Produktion migrieren.

> [!NOTE]
> Ihr Automation-Konto erhält nicht automatisch neue Versionen der Module. Dazu müssen Sie sie manuell aktualisieren, indem Sie unter **Module** die Option [Azure-Module aktualisieren](../automation-update-azure-modules.md) auswählen. In Azure Automation werden die neuesten Module in Ihrem Automation-Konto verwendet, wenn ein neuer geplanter Auftrag ausgeführt wird. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Erstellen eines neuen Zeitplans im Azure-Portal

1. Wählen Sie in Ihrem Automation-Konto im linken Bereich unter **Freigegebene Ressourcen** die Option **Zeitpläne** aus.
2. Wählen Sie auf der Seite **Zeitpläne** die Option **Zeitplan hinzufügen** aus.
3. Geben Sie auf der Seite **Neuer Zeitplan** einen Namen und optional eine Beschreibung für den neuen Zeitplan ein.

    >[!NOTE]
    >Namen von Automation-Zeitplänen dürfen aktuell keine Sonderzeichen enthalten.
    >

4. Wählen Sie aus, ob der Zeitplan einmalig oder wiederholt ausgeführt werden soll, indem Sie **Einmalig** oder **Wiederholt** auswählen. Geben Sie bei Auswahl von **Einmalig** eine Startzeit an, und wählen Sie **Erstellen** aus. Geben Sie bei Auswahl von **Wiederholt** eine Startzeit an. Wählen Sie bei **Wiederholen alle** aus, wie oft das Runbook wiederholt werden soll. Sie können nach Stunde, Tag, Woche oder Monat auswählen.

    * Wenn Sie **Woche** auswählen, werden die Tage der Woche angezeigt, aus denen Sie auswählen können. Wählen Sie beliebig viele Tage aus. Die erste Ausführung Ihres Zeitplans erfolgt am ersten Tag, den Sie nach der Startzeit ausgewählt haben. Um z. B. einen Wochenendzeitplan auszuwählen, wählen Sie „Samstag“ und „Sonntag“ aus.

    ![Festlegen eines sich wiederholenden Zeitplans für das Wochenende](../media/schedules/week-end-weekly-recurrence.png)

    * Bei Auswahl von **Monat** werden verschiedene Optionen angezeigt. Wählen Sie für die Option **Monatliche Vorkommen** entweder **Tage im Monat** oder **Wochentage** aus. Wenn Sie **Tage im Monat** auswählen, wird ein Kalender angezeigt, in dem Sie beliebig viele Tage auswählen können. Wenn Sie einen Tag wie den 31. auswählen, der im aktuellen Monat nicht vorkommt, wird der Zeitplan nicht ausgeführt. Wenn der Zeitplan am letzten Tag ausgeführt werden soll, wählen Sie unter **Am letzten Tag des Monats ausführen** die Option **Ja** aus. Bei Auswahl von **Wochentage** wird die Option **Wiederholen alle** angezeigt. Wählen Sie **Erster**, **Zweiter**, **Dritter**, **Vierter** oder **Letzter** aus. Wählen Sie abschließend einen Tag für die Wiederholung aus.

    ![Monatlicher Zeitplan am ersten, 15. und letzten Tag des Monats](../media/schedules/monthly-first-fifteenth-last.png)

5. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

### <a name="create-a-new-schedule-with-powershell"></a>Erstellen eines neuen Zeitplans mit PowerShell

Verwenden Sie das Cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule), um Zeitpläne zu erstellen. Geben Sie die Startzeit für den Zeitplan und die Häufigkeit der Ausführung an. Die folgenden Beispiele zeigen, wie Sie verschiedene Zeitplanszenarien erstellen können.

>[!NOTE]
>Namen von Automation-Zeitplänen dürfen aktuell keine Sonderzeichen enthalten.
>

#### <a name="create-a-one-time-schedule"></a>Erstellen eines einmaligen Zeitplans

Im folgenden Beispiel wird ein einmaliger Zeitplan erstellt.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Erstellen eines sich wiederholenden Zeitplans

Das folgende Beispiel zeigt, wie Sie einen sich wiederholenden Zeitplan erstellen, der ein Jahr lang jeden Tag um 13:00 Uhr ausgeführt wird.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Erstellen eines wöchentlichen sich wiederholenden Zeitplans

Das folgende Beispiel zeigt, wie Sie einen wöchentlichen Zeitplan erstellen, der nur unter der Woche ausgeführt wird.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Erstellen eines wöchentlichen sich wiederholenden Zeitplans für Wochenenden

Das folgende Beispiel zeigt, wie Sie einen wöchentlichen Zeitplan erstellen, der nur an Wochenenden ausgeführt wird.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Erstellen eines sich wiederholenden Zeitplans für den ersten, den 15. und den letzten Tag des Monats

Das folgende Beispiel zeigt, wie Sie einen sich wiederholenden Zeitplan erstellen, der am ersten, am 15. und am letzten Tag eines Monats ausgeführt wird.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Erstellen eines Zeitplans mithilfe einer Resource Manager-Vorlage

In diesem Beispiel verwenden wir eine ARM-Vorlage (Automation Resource Manager), die einen neuen Auftragszeitplan erstellt. Allgemeine Informationen zu dieser Vorlage für die Verwaltung von Automation-Auftragszeitplänen finden Sie in der [Vorlagenreferenz für Microsoft.Automation automationAccounts/jobSchedules](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates).

Kopieren Sie diese Vorlagendatei in einen Text-Editor:

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

Bearbeiten Sie die folgenden Parameterwerte, und speichern Sie die Datei als JSON-Datei:

* Name des Auftragszeitplanobjekts: Als Name des Auftragszeitplanobjekts wird eine GUID (Globally Unique Identifier, global eindeutiger Bezeichner) verwendet.

   >[!IMPORTANT]
   > Die GUID muss für jeden Auftragszeitplan, der mit einer ARM-Vorlage bereitgestellt wird, eindeutig sein. Auch wenn Sie einen vorhandenen Zeitplan neu planen, müssen Sie die GUID ändern. Dies gilt auch dann, wenn Sie zuvor einen vorhandenen Auftragszeitplan gelöscht haben, der mit derselben Vorlage erstellt wurde. Die Wiederverwendung einer GUID führt zu einem Fehler in der Bereitstellung.</br></br>
   > Im Internet finden Sie zahlreiche Dienste, die eine neue GUID für Sie generieren, wie z. B. diesen [kostenlosen Online-GUID-Generator](https://guidgenerator.com/).

* Name des Zeitplans: Der Name des Automation-Auftragszeitplans, der mit dem angegebenen Runbook verknüpft wird.
* Runbookname: Der Name des Automation-Runbooks, dem der Auftragszeitplan zugeordnet werden soll.

Nachdem die Datei gespeichert wurde, können Sie mit folgendem PowerShell-Befehl den Zeitplan für den Runbookauftrag erstellen. Der Befehl verwendet den Parameter `TemplateFile`, um den Pfad und den Dateinamen der Vorlage anzugeben.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Verknüpfen eines Zeitplans mit einem Runbook

Ein Runbook kann mit mehreren Zeitplänen verknüpft werden, und mit einem Zeitplan können mehrere Runbooks verknüpft sein. Wenn ein Runbook über Parameter verfügt, können Sie Werte für diese angeben. Sie müssen Werte für alle obligatorischen Parameter angeben; für optionale Parameter können Sie Werte angeben. Diese Werte werden jedes Mal verwendet, wenn das Runbook durch diesen Zeitplan gestartet wird. Sie können das gleiche Runbook einem anderen Zeitplan zuordnen und dabei andere Parameterwerte festlegen.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Verknüpfen eines Zeitplans mit einem Runbook über das Azure-Portal

1. Wählen Sie im Azure-Portal in Ihrem Automation-Konto unter **Prozessautomatisierung** die Option **Runbooks** aus.
1. Wählen Sie den Namen des Runbooks aus, das Sie mit einem Zeitplan verknüpfen möchten.
1. Wenn das Runbook gegenwärtig nicht mit einem Zeitplan verknüpft ist, werden Ihnen Optionen zum Erstellen eines neuen Zeitplans oder zum Verknüpfen mit einem vorhandenen Zeitplan angeboten.
1. Wenn das Runbook über Parameter verfügt, können Sie die Option **Ausführungseinstellungen ändern (Standard: Azure)** auswählen, und der Bereich **Parameter** wird angezeigt. Hier können Sie Parameterinformationen eingeben.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Verknüpfen eines Zeitplans mit einem Runbook mithilfe von PowerShell

Verwenden Sie das Cmdlet [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook), um einen Zeitplan zu verknüpfen. Sie können Werte für die Runbookparameter im Parameter "Parameters" angeben. Weitere Informationen zum Angeben von Parameterwerten finden Sie unter [Starten eines Runbooks in Azure Automation](../start-runbooks.md).
Das folgende Beispiel verdeutlicht, wie Sie einen Zeitplan mit einem Runbook verknüpfen, indem Sie ein Azure Resource Manager-Cmdlet mit Parametern verwenden.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ScheduleName $scheduleName -Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Planen einer häufigeren Ausführung von Runbooks

Das kürzeste Intervall, mit dem ein Zeitplan in Azure Automation konfiguriert werden kann, ist eine Stunde. Wenn Sie Zeitpläne noch häufiger ausführen müssen, stehen Ihnen zwei Optionen zur Verfügung:

* Erstellen Sie einen [Webhook](../automation-webhooks.md) für das Runbook, und rufen Sie den Webhook mit [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) auf. Azure Logic Apps bietet eine höhere Granularität bei der Definition von Zeitplänen.

* Erstellen Sie vier Zeitpläne, die alle einmal pro Stunde ausgeführt werden und mit einem Abstand von 15 Minuten voneinander beginnen. In diesem Szenario kann das Runbook alle 15 Minuten mit unterschiedlichen Zeitplänen ausgeführt werden.

## <a name="disable-a-schedule"></a>Deaktivieren eines Zeitplans

Wenn Sie einen Zeitplan deaktivieren, werden sämtliche damit verknüpfte Runbooks nicht mehr nach diesem Zeitplan ausgeführt. Sie können einen Zeitplan manuell deaktivieren oder während der Erstellung für Zeitpläne eine Ablaufzeit festlegen. Wenn der Ablaufzeitpunkt erreicht ist, wird der Zeitplan deaktiviert.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Deaktivieren eines Zeitplans über das Azure-Portal

1. Wählen Sie in Ihrem Automation-Konto im linken Bereich unter **Freigegebene Ressourcen** die Option **Zeitpläne** aus.
1. Wählen Sie den Namen eines Zeitplans aus, um den Detailbereich zu öffnen.
1. Ändern Sie **Aktiviert** in **Nein**.

> [!NOTE]
> Wenn Sie einen Zeitplan deaktivieren möchten, dessen Startuhrzeit in der Vergangenheit liegt, müssen Sie das Startdatum auf einen Zeitpunkt in der Zukunft ändern, bevor Sie den Plan speichern.

### <a name="disable-a-schedule-with-powershell"></a>Deaktivieren eines Zeitplans mithilfe von PowerShell

Verwenden Sie das Cmdlet [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule), um die Eigenschaften eines vorhandenen Zeitplans zu ändern. Geben Sie zum Deaktivieren des Zeitplans „False“ für den Parameter `IsEnabled` an.

Das folgende Beispiel verdeutlicht, wie Sie einen Zeitplan für ein Runbook mithilfe eines Azure Resource Manager-Cmdlets deaktivieren.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Entfernen eines Zeitplans

Wenn Sie Zeitpläne entfernen möchten, können Sie das Azure-Portal oder PowerShell verwenden. Beachten Sie, dass Sie nur Zeitpläne entfernen können, die wie im vorherigen Abschnitt beschrieben deaktiviert wurden.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Entfernen eines Zeitplans über das Azure-Portal

1. Wählen Sie in Ihrem Automation-Konto im linken Bereich unter **Freigegebene Ressourcen** die Option **Zeitpläne** aus.
2. Wählen Sie den Namen eines Zeitplans aus, um den Detailbereich zu öffnen.
3. Klicken Sie auf **Löschen**.

### <a name="remove-a-schedule-with-powershell"></a>Entfernen eines Zeitplans mithilfe von PowerShell

Mit dem Cmdlet `Remove-AzAutomationSchedule` können Sie wie unten dargestellt einen vorhandenen Zeitplan löschen.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Cmdlets, die für den Zugriff auf Zeitpläne verwendet werden, finden Sie unter [Verwalten von Modulen in Azure Automation](modules.md).
* Allgemeine Informationen zu Runbooks finden Sie unter [Ausführen von Runbooks in Azure Automation](../automation-runbook-execution.md).
