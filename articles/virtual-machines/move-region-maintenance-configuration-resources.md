---
title: Verschieben von Ressourcen, die einer Wartungskonfiguration zugeordnet sind, in eine andere Region
description: Erfahren Sie, wie Ressourcen, die einer VM-Wartungskonfiguration zugeordnet sind, in eine andere Azure-Region verschoben werden.
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 59707cfb6e54329017e36914bccf9b565ec9643c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690252"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Verschieben von Ressourcen in einer Wartungssteuerungskonfiguration in eine andere Region

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

In diesem Artikel wird beschrieben, wie Sie Ressourcen, die einer Wartungssteuerungskonfiguration zugeordnet sind, in eine andere Azure-Region verschieben. Eine Konfiguration kann aus verschiedenen Gründen verschoben werden. Beispielsweise, um eine neue Region zu nutzen, um Features oder Dienste bereitzustellen, die in einer bestimmten Region verfügbar sind, um interne Richtlinien- und Governanceanforderungen zu erfüllen oder als Reaktion auf die Kapazitätsplanung.

Mit der [Wartungssteuerung](maintenance-control.md) können Sie mit angepassten Wartungskonfigurationen steuern, wie Plattformupdates auf virtuelle Computer und auf Azure Dedicated Host-Instanzen angewendet werden. Es gibt eine Reihe von Szenarien, in denen die Wartungssteuerung regionsübergreifend verschoben wird:

- Befolgen Sie die Schritte diesen Artikel, um die einer Wartungskonfiguration zugeordneten Ressourcen, nicht aber die Konfiguration selbst zu verschieben.
- Wenn Sie Ihre Wartungssteuerungskonfiguration, nicht aber die der Konfiguration zugeordneten Ressourcen verschieben möchten, befolgen Sie [diese Anweisungen](move-region-maintenance-configuration.md).
- Wenn Sie Ihre Wartungssteuerungskonfiguration, nicht aber die der Konfiguration zugeordneten Ressourcen verschieben möchten, befolgen Sie [diese Anweisungen](move-region-maintenance-configuration.md). Befolgen Sie dann die Anweisungen in diesem Artikel.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Verschieben der Ressourcen beginnen, die mit einer Wartungssteuerungskonfiguration verknüpft sind:

- Stellen Sie sicher, dass die zu verschiebenden Ressourcen in der neuen Region vorhanden sind, bevor Sie beginnen.
- Überprüfen Sie die Wartungssteuerungskonfigurationen, die den Azure-VMs und Azure Dedicated Hosts zugeordnet sind, die Sie verschieben möchten. Überprüfen Sie jede Ressource einzeln. Es gibt derzeit keine Möglichkeit, Konfigurationen für mehrere Ressourcen abzurufen.
- Beim Abrufen von Konfigurationen für eine Ressource:
    - Stellen Sie sicher, dass Sie die Abonnement-ID für das Konto und nicht eine Azure Dedicated Host-ID verwenden.
    - Über die CLI: Der Parameter --output table wird nur zur besseren Lesbarkeit verwendet und kann gelöscht oder geändert werden.
    - Mit PowerShell: Der Parameter --Format-Table Name wird nur zur besseren Lesbarkeit verwendet und kann gelöscht oder geändert werden.
    - Wenn Sie PowerShell verwenden, erhalten Sie eine Fehlermeldung, wenn Sie versuchen, Konfigurationen für eine Ressource aufzulisten, der keine Konfigurationen zugeordnet sind. Der Fehler ähnelt der folgenden Meldung: „Vorgang mit folgendem Status fehlgeschlagen: „Nicht gefunden“. Details: 404 Clientfehler: Nicht gefunden für URL“.

    
## <a name="prepare-to-move"></a>Vorbereiten der Verschiebung

1. Bevor Sie beginnen, definieren Sie diese Variablen. Wir haben ein Beispiel für jede Variable bereitgestellt.

    **Variable** | **Details** | **Beispiel**
    --- | ---
    $subId | ID für das Abonnement, das die Wartungskonfigurationen enthält. | „our-subscription-ID“
    $rsrcGroupName | Ressourcengruppenname (Azure-VM) | „VMResourceGroup“
    $vmName | VM-Ressourcenname |  „myVM“
    $adhRsrcGroupName |  Ressourcengruppe (dedizierte Hosts) | „HostResourceGroup“
    $adh | Name des dedizierten Hosts | „myhost“
    $adhParentName | Name der übergeordneten Ressource | „HostGroup“
    
2. Abrufen der Wartungskonfigurationen mithilfe des PowerShell-Befehls [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment):

    - Führen Sie für Azure Dedicated Hosts Folgendes aus:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Führen Sie für Azure-VMs Folgendes aus:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Abrufen der Wartungskonfigurationen mithilfe des CLI-Befehls [az maintenance assignment](/cli/azure/maintenance/assignment):

    - Für Azure Dedicated Hosts:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Für Azure-VMs:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Move 

1. [Befolgen Sie diese Anweisungen](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json), um die Azure-VMs in die neue Region zu verschieben.
2. Wenden Sie nach dem Verschieben der Ressourcen die Wartungskonfigurationen bei Bedarf erneut auf die Ressourcen in der neuen Region an, je nachdem, ob Sie die Wartungskonfigurationen verschoben haben. Sie können eine Wartungskonfiguration mithilfe [von PowerShell](../virtual-machines/maintenance-control-powershell.md) oder der [CLI](../virtual-machines/maintenance-control-cli.md) auf eine Ressource anwenden.


## <a name="verify-the-move"></a>Überprüfen der Verschiebung

Überprüfen Sie die Ressourcen in der neuen Region, und überprüfen Sie die zugehörigen Konfigurationen für die Ressourcen in der neuen Region. 

## <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen

Ziehen Sie nach dem Verschieben in Erwägung, die verschobenen Ressourcen in der Quellregion zu löschen.


## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie [diese Anweisungen](move-region-maintenance-configuration.md), wenn Sie Wartungskonfigurationen verschieben müssen. 
