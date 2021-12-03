---
title: Verschieben von Log Analytics-Arbeitsbereichen in Azure Monitor | Microsoft-Dokumentation
description: Informieren Sie sich, wie Sie Log Analytics-Arbeitsbereich in ein anderes Abonnement oder in eine andere Ressourcengruppe verschieben.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/12/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4962849bb08983bb821d5ffed90a8312a52e9172
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335782"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Verschieben von Log Analytics-Arbeitsbereichen in ein anderes Abonnement oder eine andere Ressourcengruppe

In diesem Artikel erfahren Sie, wie Sie Log Analytics-Arbeitsbereiche in eine andere Ressourcengruppe oder ein anderes Abonnement in derselben Region verschieben. Weitere Informationen zum Verschieben von Azure-Ressourcen über das Azure-Portal oder mithilfe von PowerShell, der Azure CLI oder der REST-API erhalten Sie im Artikel [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Sie können keinen Arbeitsbereich in eine andere Region verschieben.

## <a name="verify-active-directory-tenant"></a>Überprüfen eines Azure Active Directory-Mandanten
Die Quell- und Zielabonnements des Arbeitsbereichs müssen in demselben Azure Active Directory-Mandanten vorhanden sein. Verwenden Sie Azure PowerShell, um zu überprüfen, ob beide Abonnements dieselbe Mandanten-ID aufweisen.

```powershell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Überlegungen zum Verschieben von Arbeitsbereichen
- Verwaltete Lösungen, die im Arbeitsbereich installiert sind, werden mit dem Verschiebungsvorgang für den Log Analytics-Arbeitsbereich verschoben. 
- Arbeitsbereichsschlüssel (primär und sekundär) werden beim Verschiebungsvorgang für den Arbeitsbereich neu generiert. Wenn Sie eine Kopie der Arbeitsbereichsschlüssel im Schlüsseltresor aufbewahren, aktualisieren Sie diese mit den neuen Schlüsseln, die nach dem Verschieben des Arbeitsbereichs generiert werden. 
- Verbundene Agents bleiben verbunden und senden nach der Verschiebung weiterhin Daten an den Arbeitsbereich. 
- Da für den Verschiebungsvorgang keine verknüpften Dienste aus dem Arbeitsbereich vorhanden sein dürfen, müssen Lösungen, die von diesem Link abhängig sind, entfernt werden, damit der Arbeitsbereich verschoben werden kann. Folgende Lösungen müssen vor dem Aufheben der Verknüpfung mit Ihrem Automation-Konto entfernt werden:
  - Updateverwaltung
  - Change Tracking
  - Starten/Beenden von VMs außerhalb der Kernzeit
  - Microsoft Defender für Cloud

>[!IMPORTANT]
> **Microsoft Sentinel-Kunden**
> - Derzeit wird nach der Bereitstellung von Microsoft Sentinel in einem Arbeitsbereich das Verschieben des Arbeitsbereichs in eine andere Ressourcengruppe oder ein anderes Abonnement nicht unterstützt. 
> - Haben Sie den Arbeitsbereich bereits verschoben, deaktivieren Sie alle aktiven Regeln unter **Analytics**, und aktivieren Sie sie nach fünf Minuten wieder. Dieser Vorgang sollte in den meisten Fällen eine effektive Lösung sein. Für die Iteration wird er jedoch nicht unterstützt und auf eigenes Risiko ausgeführt.
> - Azure Resource Manager braucht evtl. einige Stunden, bis der Vorgang abgeschlossen ist, und Lösungen reagieren während des Vorgangs möglicherweise nicht.
> 
> **Neuerstellen von Warnungen**
> - Alle Warnungen müssen neu erstellt werden, da die Berechtigungen auf der Arbeitsbereichsressourcen-ID basieren, die sich während der Verschiebung des Arbeitsbereichs oder einer Änderung des Ressourcennamens ändert. Warnungen in Arbeitsbereichen, die nach dem 1. Juni 2019 erstellt wurden, oder in Arbeitsbereichen, die [von der Log Analytics-Warnungs-Legacy-API auf die scheduledQueryRules-API aktualisiert wurden](../alerts/alerts-log-api-switch.md), können in eine Vorlage exportiert und nach dem Verschieben bereitgestellt werden. Sie können überprüfen, [ob die scheduledQueryRules-API für Warnungen in Ihrem Arbeitsbereich verwendet wird](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace). Alternativ können Sie Warnungen manuell im Zielarbeitsbereich konfigurieren.
>
> **Aktualisieren von Ressourcenpfaden**
> - Nach dem Verschieben eines Arbeitsbereichs müssen alle Azure- oder externen Ressourcen, die auf den Arbeitsbereich verweisen, überprüft und aktualisiert werden, damit sie auf den neuen Ressourcenzielpfad verweisen.
> 
>   *Beispiele:*
>   - [Azure Monitor-Warnungsregeln](../alerts/alerts-resource-move.md)
>   - Drittanbieteranwendungen
>   - Benutzerdefinierte Skripterstellung
>

### <a name="delete-solutions-in-azure-portal"></a>Löschen von Lösungen aus dem Azure-Portal
Verwenden Sie das folgende Verfahren, um die Lösungen mithilfe des Azure-Portals zu entfernen:

1. Öffnen Sie das Menü für die Ressourcengruppe, in der alle Lösungen installiert sind.
2. Wählen Sie die Lösung aus, die Sie entfernen möchten.
3. Klicken Sie auf **Ressourcen löschen**, und bestätigen Sie dann, dass die Ressourcen entfernt werden sollen, indem Sie auf **Löschen** klicken.

![Löschen von Lösungen](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Löschen mithilfe von PowerShell

Verwenden Sie wie im folgenden Beispiel gezeigt das [Remove-AzResource](/powershell/module/az.resources/remove-azresource)-Cmdlet, um die Lösungen mithilfe von PowerShell zu entfernen:

```powershell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Entfernen von Warnungsregeln für die Lösung zum Starten/Beenden von VMs
Für die Lösung **zum Starten/Beenden von VMs** müssen Sie ebenfalls die Warnungsregeln entfernen, die von der Lösung erstellt werden. Verwenden Sie das folgende Verfahren, um diese Regeln im Azure-Portal zu entfernen.

1. Öffnen Sie das Menü **Monitor**, und klicken Sie dann auf **Warnungen**.
2. Klicken Sie auf **Warnungsregeln verwalten**.
3. Wählen Sie die folgenden drei Warnungsregeln aus, und klicken Sie dann auf **Löschen**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Löschen von Regeln](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Aufheben von Verknüpfungen mit Automation-Konten
Verwenden Sie das folgende Verfahren, um mithilfe des Azure-Portals die Verknüpfung des Automation-Kontos mit dem Arbeitsbereich aufzuheben:

1. Öffnen Sie das Menü **Automation-Konten**, und wählen Sie dann das zu entfernende Konto aus.
2. Klicken Sie im Abschnitt **Zugehörige Ressourcen** des Menüs auf **Linked workspace** (Verknüpfter Arbeitsbereich). 
3. Klicken Sie auf **Unlink workspace** (Verknüpfung des Arbeitsbereichs aufheben), um für den Arbeitsbereich die Verknüpfung mit Ihrem Automation-Konto aufzuheben.

    ![Aufheben der Verknüpfung mit dem Arbeitsbereich](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Verschieben des Arbeitsbereichs

### <a name="azure-portal"></a>Azure-Portal
Verwenden Sie das folgende Verfahren, um den Arbeitsbereich mithilfe des Azure-Portals zu verschieben:

1. Öffnen Sie das Menü **Log Analytics-Arbeitsbereiche**, und wählen Sie dann den Arbeitsbereich aus.
2. Klicken Sie auf der Seite **Übersicht** neben **Ressourcengruppe** oder **Abonnement** auf **Ändern**.
3. Es wird eine neue Seite mit einer Liste von Ressourcen geöffnet, die mit dem Arbeitsbereich verknüpft sind. Wählen Sie die Ressourcen aus, die in das gleiche Zielabonnement und die gleiche Ressourcengruppe wie der Arbeitsbereich verschoben werden sollen. 
4. Wählen Sie ein gültiges **Zielabonnement** und eine **Ressourcengruppe** aus. Wenn Sie den Arbeitsbereich in eine andere Ressourcengruppe im gleichen Abonnement verschieben, wird die Option **Abonnement** nicht angezeigt.
5. Klicken Sie auf **OK**, um den Arbeitsbereich und die ausgewählten Ressourcen zu verschieben.

    ![Screenshot des Bereichs „Übersicht“ im Log Analytics-Arbeitsbereich mit Optionen zum Ändern der Ressourcengruppe und des Abonnementnamens.](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Verwenden Sie wie im folgenden Beispiel [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource), um den Arbeitsbereich mithilfe von PowerShell zu verschieben:

```powershell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> Nach dem Verschiebungsvorgang sollten Lösungen und Automation-Kontoverknüpfungen neu konfiguriert werden, um den Arbeitsbereich wieder in den vorherigen Zustand zu bringen.


## <a name="next-steps"></a>Nächste Schritte
- Eine Liste der Ressourcen, die das Verschieben unterstützen, finden Sie unter [Unterstützung des Verschiebevorgangs für Ressourcen](../../azure-resource-manager/management/move-support-resources.md).
