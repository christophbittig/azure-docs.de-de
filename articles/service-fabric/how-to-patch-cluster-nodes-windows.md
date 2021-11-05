---
title: Patchen des Windows-Betriebssystem in Ihrem Service Fabric-Cluster
description: Erfahren Sie, wie Sie automatische Upgrades für Betriebssystemimages aktivieren, um Service Fabric-Clusterknoten mit Windows zu patchen.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 940a0af02f5355e9d28bb057798004fc963dfeca
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019398"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Patchen des Windows-Betriebssystem in Ihrem Service Fabric-Cluster

Das Durchführen [automatischer Upgrades von Betriebssystemimages in Ihrer Virtual Machine Scale Sets-Instanz](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) gilt als bewährte Methode, um das Betriebssystem in Azure gepatcht zu halten. Für automatische Betriebssystemupgrades, die auf VM-Skalierungsgruppen basieren, ist für eine Skalierungsgruppe mindestens die Dauerhaftigkeitsstufe „Silver“ (Silber) erforderlich.

## <a name="requirements-for-automatic-os-image-upgrades-by-virtual-machine-scale-sets"></a>Anforderungen an automatische Upgrades von Betriebssystemimages durch Virtual Machine Scale Sets

- Die [Dauerhaftigkeitsstufe](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) von Service Fabric lautet „Silber“ oder „Gold“ (nicht „Bronze“).
- Die Service Fabric-Erweiterung in der Skalierungsgruppenmodell-Definition muss über TypeHandlerVersion 1.1 oder höher verfügen.
- Die Dauerhaftigkeitsstufe sollte im Service Fabric-Cluster und für die Service Fabric-Erweiterung in der Skalierungsgruppenmodell-Definition gleich sein.
- Ein zusätzlicher Integritätstest oder die Verwendung einer Anwendungsintegritätserweiterung ist für Virtual Machine Scale Sets nicht erforderlich.
- Zustandslose Knotentypen sind die einzige Ausnahme, die dauerhaft als Bronze festgelegt ist, aber automatische Upgrades von Betriebssystemimages können weiterhin für sie konfiguriert werden. Weitere Informationen erhalten Sie unter [Bereitstellen eines Azure Service Fabric-Clusters mit ausschließlich zustandslosen Knotentypen](service-fabric-stateless-node-types.md)

Stellen Sie sicher, dass die Dauerhaftigkeitseinstellungen im Service Fabric-Cluster und für die Service Fabric-Erweiterung nicht in Konflikt stehen, weil dies zu Upgradefehlern führt. Dauerhaftigkeitsstufen können mit den Richtlinien geändert werden, die auf [dieser Seite](service-fabric-cluster-capacity.md#changing-durability-levels) beschrieben sind.

Beim Dauerhaftigkeitsgrad „Bronze“ sind keine automatischen Betriebssystemupgrades verfügbar. Während die [Anwendung zur Patchorchestrierung](service-fabric-patch-orchestration-application.md) (nur für nicht in Azure gehostete Cluster) für den Dauerhaftigkeitsgrad Silber oder höher *nicht empfohlen* wird, stellt diese die einzige Möglichkeit dar, Windows-Updates unter Einbeziehung von Service Fabric-Upgradedomänen zu automatisieren.

Wenn Sie von einer Patchorchestrierungsanwendung auf ein automatisches Betriebssystem-Imageupgrade umstellen möchten, müssen Sie zunächst die Verwendung der Patchorchestrierungsanwendung einstellen.

## <a name="enable-auto-os-upgrades-and-disable-windows-update"></a>Aktivieren automatischer Betriebssystemupgrades und Deaktivieren von Windows Update

Wenn Sie automatische Betriebssystemupdates aktivieren, müssen Sie auch Windows Update in der Bereitstellungsvorlage deaktivieren. Nachdem diese Änderungen bereitgestellt wurden, wird für alle Computer in der Skalierungsgruppe ein Reimaging durchgeführt, und für die Skalierungsgruppe werden automatische Updates aktiviert.

> [!IMPORTANT]
> Upgrades auf einer VM, bei denen Windows Update Betriebssystempatches anwendet, ohne den Betriebssystemdatenträger zu ersetzen, werden in Service Fabric nicht unterstützt.


1. Aktivieren automatischer Upgrades von Betriebssystemimages und Deaktivieren von Windows Update in der Bereitstellungsvorlage:
 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ```

1. Aktualisieren Sie das Skalierungsgruppenmodell. Nach dieser Konfigurationsänderung ist ein Reimaging aller Computer erforderlich, um das Skalierungsgruppenmodell zu aktualisieren und damit die Änderung anzuwenden:

    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [automatische Upgrades von Betriebssystemimages in Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) aktivieren.