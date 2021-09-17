---
title: Angefügte Datenträger für Azure Virtual Machine Scale Sets
description: Erfahren Sie anhand von kurz erläuterten Anwendungsfällen, wie Sie angefügte Datenträger mit VM-Skalierungsgruppen verwenden.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 13bdbe7a1f6c328a2797d6f8869c05af9c5cb7f5
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690555"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure-VM-Skalierungsgruppen und angefügte Datenträger

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Zur Erweiterung des verfügbaren Speicherplatzes unterstützen [Azure-VM-Skalierungsgruppen](./index.yml) VM-Instanzen mit angefügten Datenträgern. Datenträger können einer Skalierungsgruppe beim Erstellen der Skalierungsgruppe oder zu einem späteren Zeitpunkt hinzugefügt werden.

> [!NOTE]
> Wenn Sie eine Skalierungsgruppe mit angefügten Datenträgern erstellen, müssen Sie die Datenträger über einen virtuellen Computer einbinden und formatieren, um sie verwenden zu können – genau wie bei eigenständigen virtuellen Azure-Computern. Eine einfache Möglichkeit ist die Verwendung einer benutzerdefinierten Skripterweiterung, die ein Skript aufruft, um alle Datenträger auf einem virtuellen Computer zu partitionieren und zu formatieren. Beispiele hierzu finden Sie unter [Azure-Befehlszeilenschnittstelle und ](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Erstellen und Verwalten von Datenträgern in einer Skalierungsgruppe
Ausführliche Informationen zum Erstellen einer Skalierungsgruppe mit angefügten Datenträgern, zum Vorbereiten und Formatieren sowie zum Hinzufügen und Entfernen von Datenträgern finden Sie in den folgenden Tutorials:

- [Azure-Befehlszeilenschnittstelle](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

Im weiteren Verlauf dieses Artikels werden bestimmte Verwendungsfälle behandelt – beispielsweise Service Fabric-Cluster, die Datenträger benötigen, oder das Anfügen vorhandener Datenträger mit Inhalt an eine Skalierungsgruppe.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Erstellen eines Service Fabric-Clusters mit angefügten Datenträgern
Jedem [Knotentyp](../service-fabric/service-fabric-cluster-nodetypes.md) in einem in Azure ausgeführten [Service Fabric](../service-fabric/index.yml)-Cluster liegt eine VM-Skalierungsgruppe zugrunde. Mit einer Azure Resource Manager-Vorlage können Sie Datenträger an die Skalierungsgruppen anfügen, aus denen sich der Service Fabric-Cluster zusammensetzt. Sie können eine [vorhandene Vorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates) als Ausgangspunkt verwenden. Fügen Sie in der Vorlage im Speicherprofil (_storageProfile_) der Ressourcen vom Typ _Microsoft.Compute/virtualMachineScaleSets_ einen Abschnitt namens _dataDisks_ ein, und stellen Sie die Vorlage bereit. Im folgenden Beispiel wird ein Datenträger mit einer Kapazität von 128 GB angefügt:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Die Datenträger können automatisch partitioniert, formatiert und eingebunden werden, wenn der Cluster bereitgestellt wird. Fügen Sie dem Erweiterungsprofil (_extensionProfile_) des VM-Profils (_virtualMachineProfile_) der Skalierungsgruppen eine benutzerdefinierte Skripterweiterung hinzu.

Fügen Sie Folgendes hinzu, um die Datenträger in einem Windows-Cluster automatisch vorzubereiten:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Fügen Sie Folgendes hinzu, um die Datenträger in einem Linux-Cluster automatisch vorzubereiten:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Hinzufügen von vorab aufgefüllten Datenträgern zu einer vorhandenen Skalierungsgruppe
Im Skalierungsgruppenmodell angegebene Datenträger sind immer leer. Sie können jedoch einen vorhandenen Datenträger an eine bestimmte VM in einer Skalierungsgruppe anfügen. Wenn Sie Daten an alle VMs in der Skalierungsgruppe verteilen möchten, können Sie Ihren Datenträger duplizieren und an jede VM in der Skalierungsgruppe anfügen oder ein benutzerdefiniertes Image erstellen, das die Daten enthält, und die Skalierungsgruppe aus diesem benutzerdefinierten Image bereitstellen. Sie können auch Azure Files oder eine vergleichbare Datenspeicherlösung verwenden.


## <a name="additional-notes"></a>Zusätzliche Hinweise
Unterstützung für Azure Managed Disks und Skalierungsgruppen, die an Datenträger angefügt sind, ist über die API-Version [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) oder höher der Microsoft.Compute-API verfügbar.

Die Unterstützung des Azure-Portals für angefügte Datenträger in Skalierungsgruppen ist begrenzt. Je nach Ihren Anforderungen können Sie Azure-Vorlagen, CLI, PowerShell, SDKs und REST API zum Verwalten angefügter Datenträger verwenden.
