---
title: Integrieren eines Computers in Azure Automanage mit einer ARM-Vorlage
description: Hier erfahren Sie, wie Sie einen Computer in Azure Automanage mit einer Azure Resource Manager-Vorlage integrieren.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: cdb10c265ddae4aaf83450c1951ef6cb5c2219df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443849"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Integrieren eines Computers in Automanage mit einer Azure Resource Manager (ARM)-Vorlage


## <a name="overview"></a>Übersicht
Führen Sie die folgenden Schritte aus, um das Onboarding eines Computers bei Automanage mithilfe einer ARM-Vorlage durchzuführen.

## <a name="prerequisites"></a>Voraussetzungen
* Sie müssen über die notwendigen [RBAC-Berechtigungen](./automanage-virtual-machines.md#required-rbac-permissions) verfügen.
* Sie müssen sich in einer unterstützten Region und einem unterstützten VM-Image befinden, das in diesen [Voraussetzungen](./automanage-virtual-machines.md#prerequisites) hervorgehoben ist.


## <a name="arm-template-overview"></a>Übersicht über ARM-Vorlagen
Die folgende ARM-Vorlage integriert Ihren angegebenen Computer in die bewährten Methoden von Azure Automanage. Details zur ARM-Vorlage und Schritte zur Bereitstellung finden Sie [im folgenden Abschnitt](#arm-template-deployment) zur ARM-Vorlagenbereitstellung.
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "configurationProfile": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2021-04-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfile')]",
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM-Vorlagenbereitstellung
Mit der oben genannten ARM-Vorlage wird eine Konfigurationsprofilzuweisung für Ihren angegebenen Computer erstellt. 

Der `configurationProfile`-Wert kann einer der folgenden Werte sein:
* „/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesProduction“
* „/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesDevTest“

Führen Sie diese Schritte aus, um die ARM-Vorlage bereitzustellen:
1. Speichern Sie die obige ARM-Vorlage unter `azuredeploy.json`.
1. Führen Sie die ARM-Vorlagenbereitstellung mit `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json` aus.
1. Geben Sie die Werte für machineName, automanageAccountName und configurationProfileAssignment an, wenn Sie dazu aufgefordert werden.
1. Fertig.

Wie bei jeder ARM-Vorlage ist es möglich, die Parameter in einer separaten `azuredeploy.parameters.json`-Datei zu berücksichtigen und diese bei der Bereitstellung als Argument zu verwenden.

## <a name="next-steps"></a>Nächste Schritte
Hier erfahren Sie mehr über Automanage für [Linux](./automanage-linux.md) und [Windows](./automanage-windows-server.md)