---
title: Erstellen einer Vorlage zur Problembehandlung
description: Beschreibt, wie Sie eine Vorlage erstellen,um Probleme mit Azure-Ressourcen zu beheben, die mit Azure Resource Manager Vorlagen (ARM-Vorlagen) oder Bicep-Dateien bereitgestellt wurden.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 0c9b1a6ebd35a6ebe58b568a1a56e44c0395b630
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478622"
---
# <a name="create-a-troubleshooting-template"></a>Erstellen einer Vorlage zur Problembehandlung

In einigen Fällen ist die beste Möglichkeit zur Problembehandlung ihrer Vorlage das Isolieren und Testen bestimmter Teile der Vorlage. Sie können eine Vorlage zur Problembehandlung erstellen, die sich auf die Ressource konzentriert, von der Sie glauben, dass sie den Fehler verursacht.

Beispielsweise tritt ein Fehler auf, wenn Ihre Bereitstellungsvorlage auf eine vorhandene Ressource verweist. Anstatt eine gesamte Bereitstellungsvorlage zu bewerten, erstellen Sie eine Problembehandlungsvorlage, die Daten über die Ressource zurückgibt. Die Ausgaben helfen Ihnen, zu ermitteln, ob die richtigen Parameter übergeben, Vorlagenfunktionen ordnungsgemäß genutzt und die Ressourcen abgerufen werden, die Sie erwarten.

## <a name="deploy-a-troubleshooting-template"></a>Bereitstellen einer Vorlage zur Problembehandlung

Die folgende ARM-Vorlage und Bicep-Datei erhalten Informationen von einem vorhandenen Speicherkonto. Sie führen die Bereitstellung mit Azure PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) oder Azure CLI [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) aus. Geben Sie den Namen und die Ressourcengruppe des Speicherkontos an. Die Ausgabe ist ein Objekt mit den Eigenschaftennamen und -werten des Speicherkontos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    },
    "storageResourceGroup": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-04-01')]",
      "type": "object"
    }
  }
}
```

Nutzen Sie in Bicep das Schlüsselwort `existing` und führen Sie die Bereitstellung aus der Ressourcengruppe aus, in der das Speicherkonto vorhanden ist. Verwenden Sie `scope`, um auf eine Ressource in einer anderen Ressourcengruppe zu zugreifen. Weitere Informationen finden Sie unter [Bestehende Ressourcen](../bicep/resource-declaration.md#existing-resources).

```bicep
param storageName string

resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: storageName
}

output exampleOutput object = stg.properties
```

## <a name="alternate-troubleshooting-method"></a>Alternative Problembehandlungsmethode

Wenn Sie der Meinung sind, dass die Bereitstellungsfehler durch falsche Abhängigkeiten verursacht werden, können Sie Tests ausführen, indem Sie die Vorlage in vereinfachte Vorlagen aufschlüsseln. Erstellen Sie zunächst eine Vorlage, mit der nur eine einzige Ressource bereitgestellt wird (z.B. eine SQL Server-Instanz). Wenn Sie sicher sind, dass die Ressourcenbereitstellung richtig ist, fügen Sie eine Ressource hinzu, die davon abhängig ist (z. B. SQL-Datenbank). Wenn diese beiden Ressourcen richtig definiert sind, fügen Sie weitere abhängige Ressourcen hinzu (z. B. Überwachungsrichtlinien). Löschen Sie zwischen den jeweiligen Testbereitstellungen die Ressourcengruppe, um sicherzustellen, dass Sie die Abhängigkeiten angemessen testen.

## <a name="next-steps"></a>Nächste Schritte

- [Häufige Bereitstellungsfehler](common-deployment-errors.md)
- [Fehlercodes suchen](find-error-code.md)
- [Debugprotokollierung aktivieren](enable-debug-logging.md)
