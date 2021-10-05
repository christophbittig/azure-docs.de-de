---
title: Konfigurieren des Log Analytics-Arbeitsbereichs für VM Insights
description: In diesem Artikel wird beschrieben, wie der Log Analytics-Arbeitsbereich mithilfe von VM Insights erstellt und konfiguriert wird.
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: a5a65f99fec0bb0db245450ead9747776e194b46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614427"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Konfigurieren des Log Analytics-Arbeitsbereichs für VM Insights
VM Insights erfasst die Daten von mindestens einem Log Analytics-Arbeitsbereich in Azure Monitor. Vor dem Integrieren von Agents müssen Sie einen Arbeitsbereich erstellen und konfigurieren. In diesem Artikel werden die Anforderungen für den Arbeitsbereich und dessen Konfiguration für VM Insights beschrieben.

## <a name="overview"></a>Übersicht
Für ein einzelnes Abonnement kann je nach Ihren Anforderungen eine beliebige Anzahl von Arbeitsbereichen verwendet werden. Die einzige Anforderung für den Arbeitsbereich ist, dass er sich an einem unterstützten Standort befindet und mit der *VMInsights*-Lösung konfiguriert ist.

Nach der Konfiguration des Arbeitsbereichs können Sie die erforderlichen Agents über eine der verfügbaren Optionen auf der VM und in VM-Skalierungsgruppen installieren und einen Arbeitsbereich angeben, an den diese ihre Daten senden. VM Insights erfasst Daten aus jedem konfigurierten Arbeitsbereich in seinem Abonnement.

> [!NOTE]
> Wenn Sie VM Insights für eine einzelne VM oder VM-Skalierungsgruppe über das Azure-Portal aktivieren, können Sie einen vorhandenen Arbeitsbereich auswählen oder einen neuen erstellen. Die *VMInsights*-Lösung wird in diesem Arbeitsbereich installiert, falls dies noch nicht geschehen ist. Anschließend können Sie diesen Arbeitsbereich für andere Agents verwenden.


## <a name="create-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

>[!NOTE]
>Die in diesem Abschnitt beschriebenen Informationen gelten auch für die [Dienstzuordnungslösung](service-map.md). 

Greifen Sie auf Log Analytics-Arbeitsbereiche im Azure-Portal über das Menü **Log Analytics-Arbeitsbereiche** zu.

[![Log Analytics-Arbeitsbereiche](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Sie können einen neuen Log Analytics-Arbeitsbereich mit einer der folgenden Methoden erstellen. Eine Anleitung zum Bestimmen der Anzahl von Arbeitsbereichen, die Sie in Ihrer Umgebung verwenden sollten, und zum Entwerfen Ihrer Zugriffsstrategie finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../logs/design-logs-deployment.md).


* [Azure portal](../logs/quick-create-workspace.md)
* [Azure-Befehlszeilenschnittstelle](../logs/resource-manager-workspace.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Unterstützte Regionen
VM Insights unterstützt Log Analytics-Arbeitsbereiche in jeder der [von Log Analytics unterstützten Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).


>[!NOTE]
>Sie können Azure-VMs in jeder Region überwachen. Die VMs selbst sind nicht auf die vom Log Analytics-Arbeitsbereich unterstützten Bereiche beschränkt.

## <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure
Wenn Sie die Features in VM Insights aktivieren und darauf zugreifen möchten, müssen Sie im Arbeitsbereich über die Rolle [Log Analytics-Mitwirkender](../logs/manage-access.md#manage-access-using-azure-permissions) verfügen. Sie müssen über die Rolle [Überwachungsleser](../roles-permissions-security.md#built-in-monitoring-roles) für die Azure-VM verfügen, um Leistungs-, Integritäts- und Zuordnungsdaten anzeigen zu können. Weitere Informationen zur Zugriffssteuerung auf einen Log Analytics-Arbeitsbereich finden Sie unter [Verwalten von Arbeitsbereichen](../logs/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Hinzufügen der VMInsights-Lösung zum Arbeitsbereich
Bevor ein Log Analytics-Arbeitsbereich mit VM Insights verwendet werden kann, muss die Lösung *VMInsights* installiert werden. Die Methoden zum Konfigurieren des Arbeitsbereichs werden in den folgenden Abschnitten beschrieben.

> [!NOTE]
> Wenn Sie dem Arbeitsbereich die *VMInsights*-Lösung hinzufügen, beginnen alle vorhandenen virtuellen Computer, die mit dem Arbeitsbereich verbunden sind, mit dem Senden von Daten an InsightsMetrics. Daten für die anderen Datentypen werden erst erfasst, wenn Sie den Dependency-Agent zu den vorhandenen virtuellen Computern hinzufügen, die mit dem Arbeitsbereich verbunden sind.

### <a name="azure-portal"></a>Azure-Portal
Es gibt drei Optionen zum Konfigurieren eines vorhandenen Arbeitsbereichs über das Azure-Portal. Die einzelnen Elemente sind unten beschrieben.

Um einen einzelnen Arbeitsbereich zu konfigurieren, wechseln Sie im Menü **Azure Monitor** zur Option **Virtuelle Computer**, und wählen Sie **Weitere Onboardingoptionen** und dann **Arbeitsbereich konfigurieren** aus. Wählen Sie ein Abonnement und einen Arbeitsbereich aus, und klicken Sie dann auf **Konfigurieren**.

[![Konfigurieren eines Arbeitsbereichs](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Wenn Sie mehrere Arbeitsbereiche konfigurieren möchten, wählen Sie im Azure-Portal im Menü **Überwachen** im Menü **Virtuelle Computer** die Registerkarte **Arbeitsbereichskonfiguration** aus. Legen Sie die Filterwerte so fest, dass eine Liste der vorhandenen Arbeitsbereiche angezeigt wird. Aktivieren Sie die Kontrollkästchen neben den einzelnen Arbeitsbereichen, um sie zu aktivieren, und klicken Sie dann auf **Ausgewählte konfigurieren**.

[![Arbeitsbereichskonfiguration](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Wenn Sie VM Insights für eine einzelne VM oder VM-Skalierungsgruppe über das Azure-Portal aktivieren, können Sie einen vorhandenen Arbeitsbereich auswählen oder einen neuen erstellen. Die *VMInsights*-Lösung wird in diesem Arbeitsbereich installiert, falls dies noch nicht geschehen ist. Anschließend können Sie diesen Arbeitsbereich für andere Agents verwenden.

[![Aktivieren einer einzelnen VM im Portal](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager-Vorlage
Die Azure Resource Manager-Vorlagen für VM Insights werden in einer Archivdatei (.zip) bereitgestellt, die Sie aus [diesem GitHub-Repository herunterladen](https://aka.ms/VmInsightsARMTemplates) können. Es enthält eine Vorlage namens **ConfigureWorkspace**, mit der ein Log Analytics-Arbeitsbereich für VM Insights konfiguriert wird. Sie stellen diese Vorlage mithilfe einer der Standardmethoden bereit. Dazu gehören auch die folgenden PowerShell- und CLI-Beispielbefehle: 

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Verbinden von Agents mit VM Insights finden Sie unter [Durchführen des Onboardings für Agents in VM Insights](vminsights-enable-overview.md).
- Informationen zum Einschränken der Datenmenge, die von einer Lösung an den Arbeitsbereich gesendet wird, finden Sie unter [Zielgruppenadressierung für Überwachungslösungen in Azure Monitor (Vorschau)](../insights/solution-targeting.md).