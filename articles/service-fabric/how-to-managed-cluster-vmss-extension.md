---
title: Hinzufügen einer VM-Skalierungsgruppenerweiterung zu einem verwalteten Service Fabric-Clusterknotentyp
description: Im Folgenden wird beschrieben, wie Sie eine VM-Skalierungsgruppenerweiterung einem verwalteten Service Fabric-Clusterknotentyp hinzufügen.
ms.topic: article
ms.date: 8/02/2021
ms.openlocfilehash: 8755eb7551dd7f7e572632ee50eb57467f675822
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866391"
---
# <a name="virtual-machine-scale-set-extension-support-on-service-fabric-managed-cluster-node-types"></a>Hinzufügen von Unterstützung für VM-Skalierungsgruppenerweiterungen zu einem verwalteten Service Fabric-Clusterknotentyp

Jeder Knotentyp in einem verwalteten Service Fabric-Cluster wird von einer VM-Skalierungsgruppe unterstützt. Im Folgenden wird beschrieben, wie Sie [VM-Skalierungsgruppenerweiterungen](../virtual-machines/extensions/overview.md) einem verwalteten Service Fabric-Clusterknotentyp hinzufügen. Erweiterungen sind kleine Anwendungen, die Konfiguration und Automatisierung für virtuelle Azure-Computer nach der Bereitstellung bereitstellen. Auf der Azure-Plattform werden Erweiterungen für viele Zwecke gehostet, z. B. VM-Konfiguration, Überwachung, Sicherheit und Hilfsprogramme. Herausgeber nehmen eine Anwendung, umschließen diese in einer Erweiterung und vereinfachen die Installation. Sie müssen lediglich noch die obligatorischen Parameter angeben. 

## <a name="add-a-virtual-machine-scale-set-extension"></a>Hinzufügen einer VM-Skalierungsgruppenerweiterung
Sie können einem verwalteten Service Fabric-Clusterknotentyp über den PowerShell-Befehl [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) eine VM-Skalierungsgruppenerweiterung hinzufügen.

Alternativ können Sie eine VM-Skalierungsgruppenerweiterung für einen verwalteten Service Fabric-Clusterknotentyp in Ihrer Azure Resource Manager-Vorlage hinzufügen. Beispiel:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Weitere Informationen zum Konfigurieren von verwalteten Service Fabric-Clusterknotentypen finden Sie unter [Verwalteter Clusterknotentyp](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu verwalteten Service Fabric-Clustern finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Häufig gestellte Fragen zu verwalteten Service Fabric-Clustern](./faq-managed-cluster.yml)
