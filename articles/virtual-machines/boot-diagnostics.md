---
title: Azure-Startdiagnose
description: Übersicht über die Azure-Startdiagnose und die verwaltete Startdiagnose
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: fdb7b3bcaac2825e64111bdba4ad98554bfc02ae
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339255"
---
# <a name="azure-boot-diagnostics"></a>Azure-Startdiagnose

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Die Startdiagnose ist ein Debuggingfeature für Azure-VMs, die eine Diagnose von Fehlern beim Starten von VMs ermöglicht. Mit der Startdiagnose können Benutzer anhand von Informationen des seriellen Protokolls und Screenshots den Zustand ihrer VMs beim Systemstart beobachten.

## <a name="boot-diagnostics-storage-account"></a>Startdiagnose-Speicherkonto
Wenn Sie eine VM im Azure-Portal erstellen, ist die Startdiagnose standardmäßig aktiviert. Die empfohlene Vorgehensweise bei der Startdiagnose ist die Verwendung eines verwalteten Speicherkontos, da dies zu erheblichen Leistungsverbesserungen bei der Zeit führt, die für die Erstellung einer Azure-VM benötigt wird. Dies liegt daran, dass ein verwaltetes Azure-Speicherkonto verwendet wird, wodurch der Zeitraum, der zum Erstellen eines neuen Benutzerspeicherkontos zum Speichern der Startdiagnosedaten benötigt wird, entfällt.

> [!IMPORTANT]
> Die Datenblobs der Startdiagnose (die aus Protokollen und Images von Momentaufnahmen bestehen) werden in einem verwalteten Speicherkonto gespeichert. Kunden werden nur die von den Blobs genutzten GiBs berechnet, nicht für die bereitgestellte Größe des Datenträgers. Die Momentaufnahmezähler werden zur Abrechnung des verwalteten Speicherkontos verwendet. Da die verwalteten Konten entweder auf „Standard LRS“ oder „Standard ZRS“ erstellt werden, wird den Kunden nur die Größe ihrer Diagnosedatenblobs mit 0,05 USD/GB pro Monat berechnet. Weitere Informationen zu diesen Preisen finden Sie unter [Preise für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/). Kunden können sehen, dass diese Gebühr an ihren URI der VM-Ressource gebunden ist. 

Eine alternative Vorgehensweise bei der Startdiagnose ist die Verwendung eines vom Benutzer verwalteten Speicherkontos. Ein Benutzer kann entweder ein neues Speicherkonto erstellen oder ein vorhandenes Konto verwenden.
> [!NOTE]
> Bei vom Benutzer verwalteten Speicherkonten, die für die Startdiagnose verwendet werden, müssen das Speicherkonto und die zugehörigen VMs in der gleichen Region und im gleichen Abonnement enthalten sein. 



## <a name="boot-diagnostics-view"></a>Ansicht der Startdiagnose
Die Option der Startdiagnose befindet sich im Azure-Portal auf dem Blatt Ihrer VM im Abschnitt *Support und Problembehandlung*. Durch Auswählen der Startdiagnose werden ein Screenshot und Informationen des seriellen Protokolls angezeigt. Das serielle Protokoll enthält Kernelmeldungen, der Screenshot ist eine Momentaufnahme des aktuellen Zustands Ihrer VM. Je nachdem, ob auf der VM Windows oder Linux ausgeführt wird, unterscheiden sich die zu erwartenden Screenshots. Windows-Benutzern wird ein Desktophintergrund angezeigt, Linux-Benutzern eine Anmeldeaufforderung.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Screenshot der Linux-Startdiagnose":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Screenshot der Windows-Startdiagnose":::

## <a name="enable-managed-boot-diagnostics"></a>Aktivieren der verwalteten Startdiagnose 
Die verwaltete Startdiagnose kann über das Azure-Portal, die Befehlszeilenschnittstelle und ARM-Vorlagen aktiviert werden. Die Aktivierung mit PowerShell wird noch nicht unterstützt. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Aktivieren der verwalteten Startdiagnose über das Azure-Portal
Wenn Sie eine VM im Azure-Portal erstellen, ist die Startdiagnose mithilfe eines verwalteten Speicherkontos in der Standardeinstellung aktiviert. Um dies anzuzeigen, navigieren Sie während der Erstellung der VM zur Registerkarte *Verwaltung*. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="Screenshot der Aktivierung der verwalteten Startdiagnose während der VM-Erstellung":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>Aktivieren der verwalteten Startdiagnose mithilfe der Befehlszeilenschnittstelle
Die Startdiagnose mit einem verwalteten Speicherkonto wird ab Version 2.12.0 der Azure-Befehlszeilenschnittstelle unterstützt. Wenn Sie keinen Namen oder URI für ein Speicherkonto eingeben, wird ein verwaltetes Konto verwendet. Weitere Informationen und Codebeispiele finden Sie in der [Dokumentation der Befehlszeilenschnittstelle zur Startdiagnose](/cli/azure/vm/boot-diagnostics).

### <a name="enable-managed-boot-diagnostics-using-powershell"></a>Aktivieren der verwalteten Startdiagnose mithilfe von PowerShell
Startdiagnose mit einem verwalteten Speicherkonto wird ab Version 6.6.0 in Azure PowerShell unterstützt. Wenn Sie keinen Namen oder URI für ein Speicherkonto eingeben, wird ein verwaltetes Konto verwendet. Weitere Informationen und Codebeispiele finden Sie in der [PowerShell-Dokumentation zur Startdiagnose](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic?view=azps-6.6.0).

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Aktivieren der verwalteten Startdiagnose mithilfe von ARM-Vorlagen (Azure Resource Manager)
Ab API-Version 2020-06-01 wird die verwaltete Startdiagnose unterstützt. Weitere Informationen finden Sie unter [Instanzansicht der Startdiagnose](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Einschränkungen
- Die verwaltete Startdiagnose ist nur für VMs verfügbar, die über Azure Resource Manager bereitgestellt wurden. 
- Bei der verwalteten Startdiagnose werden keine VMs unterstützt, die nicht verwaltete Betriebssystemdatenträger verwenden.
- Die Startdiagnose unterstützt keine Storage Premium-Konten oder zonenredundante Speicherkonten. Wenn einer dieser beiden für die Startdiagnose verwendet wird, erhalten die Benutzer beim Starten der VM einen `StorageAccountTypeNotSupported`-Fehler. 
- Verwaltete Speicherkonten werden ab Resource Manager-API-Version 2020-06-01 unterstützt.
- Die serielle Azure-Konsole ist zurzeit nicht mit einem verwalteten Speicherkonto für die Startdiagnose kompatibel. Weitere Informationen zur [seriellen Azure-Konsole](/troubleshoot/azure/virtual-machines/serial-console-overview).
- Das Portal unterstützt nur die Verwendung der Startdiagnose mit einem verwalteten Speicherkonto für Einzelinstanz-VMs.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [serielle Azure-Konsole](/troubleshoot/azure/virtual-machines/serial-console-overview) und die Verwendung der Startdiagnose zur [Behandlung von Problemen mit virtuellen Computern in Azure](/troubleshoot/azure/virtual-machines/boot-diagnostics).
