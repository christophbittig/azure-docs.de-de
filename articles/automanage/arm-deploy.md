---
title: Integrieren eines Computers in Azure Automanage mit einer ARM-Vorlage
description: Hier erfahren Sie, wie Sie einen Computer in Azure Automanage mit einer Azure Resource Manager-Vorlage integrieren.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 34f20a50fc62cff98362380222822ac016b76a48
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355802"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Integrieren eines Computers in Automanage mit einer Azure Resource Manager (ARM)-Vorlage


## <a name="overview"></a>Übersicht
Führen Sie die folgenden Schritte aus, um einen Computer in die bewährten Methoden von Automanage zu integrieren. Mit der folgenden ARM-Vorlage wird ein `configurationProfileAssignment`-Objekt erstellt, bei dem es sich um die Azure-Ressource handelt, die einen Computer darstellt, der in Automanage integriert wurde.

## <a name="prerequisites"></a>Voraussetzungen
* Sie müssen ein vorhandenes Automanage-Konto erstellt und ihm die richtigen Berechtigungen zugewiesen haben. Weitere Informationen zum Automanage-Konto, seiner Erstellung und der Zuweisung von Berechtigungen finden Sie in [diesem Dokument](./automanage-account.md).
* Wenn Sie über ein bestehendes Automanage-Konto mit zugewiesenen Berechtigungen verfügen, müssen Sie auch über die Rolle **Mitwirkender** für die Ressourcengruppe verfügen, die die Computer enthält, die Sie ein Onboarding bei Automanage durchführen möchten.


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
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM-Vorlagenbereitstellung
Mit der oben genannten ARM-Vorlage wird eine Konfigurationsprofilzuweisung für Ihren angegebenen Computer unter Verwendung eines angegebenen Automanage-Kontos erstellt. Wenn Sie noch kein Automanage-Konto erstellt haben, finden Sie weitere Informationen in [diesem Dokument](./automanage-account.md).

Der `configurationProfileAssignment`-Wert kann einer der folgenden Werte sein:
* "Production"
* "DevTest"

Führen Sie diese Schritte aus, um die ARM-Vorlage bereitzustellen:
1. Speichern Sie die obige ARM-Vorlage unter `azuredeploy.json`.
1. Führen Sie die ARM-Vorlagenbereitstellung mit `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json` aus.
1. Geben Sie die Werte für machineName, automanageAccountName und configurationProfileAssignment an, wenn Sie dazu aufgefordert werden.
1. Fertig.

Wie bei jeder ARM-Vorlage ist es möglich, die Parameter in einer separaten `azuredeploy.parameters.json`-Datei zu berücksichtigen und diese bei der Bereitstellung als Argument zu verwenden.

## <a name="next-steps"></a>Nächste Schritte
Hier erfahren Sie mehr über Automanage für [Linux](./automanage-linux.md) und [Windows](./automanage-windows-server.md)