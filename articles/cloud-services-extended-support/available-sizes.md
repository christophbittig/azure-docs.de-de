---
title: Verfügbare Größen für Azure Cloud Services (erweiterter Support)
description: Verfügbare Größen für Bereitstellungen von Azure Cloud Services (erweiterter Support)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8075176302e81788b3b180501c951c27ee4105eb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434158"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Verfügbare Größen für Azure Cloud Services (erweiterter Support)

In diesem Artikel werden die verfügbaren VM-Größen für Cloud Services-Instanzen (erweiterter Support) beschrieben.   

| SKU-Familie |  ACU/Kern | 
|---|---|
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230-260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230-260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 - 190* |
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180 - 240* |
|[H](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> Mit * gekennzeichnete ACUs verwenden die Intel® Turbo-Technologie, um die CPU-Frequenz zu erhöhen eine Leistungssteigerung zu erzielen. Das Maß der Leistungssteigerung variiert basierend auf der Größe und Workload des virtuellen Computers sowie auf anderen Workloads, die auf dem gleichen Host ausgeführt werden.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Konfigurieren von Größen für Cloud Services (erweiterter Support)

Sie können die VM-Größe einer Rolleninstanz als Teil des Dienstmodells in der Dienstdefinitionsdatei angeben. Die Größe einer Rolle bestimmt die Anzahl der CPU-Kerne, die Speicherkapazität und die lokale Dateisystemgröße.

Legen Sie z. B. die Größe der Webrolleninstanz auf `Standard_D2` fest: 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Ändern der Größe einer vorhandenen Rolle

Um die Größe einer vorhandenen Rolle zu ändern, ändern Sie die VM-Größe in der Dienstdefinitionsdatei (CSDEF-Datei), packen Sie den Clouddienst erneut, und stellen Sie ihn dann erneut bereit. 

## <a name="get-a-list-of-available-sizes"></a>Abrufen einer Liste der verfügbaren Größen 

Informationen zum Abrufen einer Liste der verfügbaren Größen finden Sie unter [Ressourcen-SKUs: Liste](/rest/api/compute/resourceskus/list). Wenden Sie die folgenden Filter an:

```powershell
    # Update the location
    $location = 'WestUS2'
    # Get all Compute Resource Skus
    $allSkus = Get-AzComputeResourceSku
    # Filter virtualMachine skus for given location
    $vmSkus = $allSkus.Where{$_.resourceType -eq 'virtualMachines' -and $_.LocationInfo.Location -like $location}
    # From filtered virtualMachine skus, select PaaS Skus
    $passVMSkus = $vmSkus.Where{$_.Capabilities.Where{$_.name -eq 'VMDeploymentTypes'}.Value.Contains("PaaS")}
    # Optional step to format and sort the output by Family
    $passVMSkus | Sort-Object Family, Name | Format-Table -Property Family, Name, Size
```

## <a name="next-steps"></a>Nächste Schritte 
- Überprüfen Sie die [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Cloud Services (erweiterter Support).
- Sehen Sie sich die [häufig gestellten Fragen](faq.yml) zu Cloud Services (erweiterter Support) an.
- Stellen Sie eine Cloud Service-Instanz (erweiterter Support) über das [Azure-Portal](deploy-portal.md), mit [PowerShell](deploy-powershell.md), einer [Vorlage](deploy-template.md) oder [Visual Studio](deploy-visual-studio.md) bereit.
