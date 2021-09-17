---
title: Bereitstellen von OKD in Azure
description: Bereitstellen von OKD in Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: e011b48323246d9cff2b6cc6116cb6746fb81299
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692665"
---
# <a name="deploy-okd-in-azure"></a>Bereitstellen von OKD in Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Sie können eine von zwei Methoden verwenden, um OKD (vormals OpenShift Origin) in Azure bereitzustellen:

- Sie können manuell alle erforderlichen Azure-Infrastrukturkomponenten bereitstellen und dann den Anweisungen in der [Dokumentation zu OKD](https://docs.okd.io) folgen.
- Sie können auch eine vorhandene [Resource Manager-Vorlage](https://github.com/Microsoft/openshift-origin) verwenden, die die Bereitstellung des OKD-Clusters vereinfacht.

## <a name="deploy-using-the-okd-template"></a>Bereitstellung mithilfe der OKD-Vorlage

Bei der Bereitstellung mithilfe der Resource Manager-Vorlage werden die Eingabeparameter über eine Parameterdatei angegeben. Forken Sie zur weiteren Anpassung der Bereitstellung das GitHub-Repository, und ändern Sie die entsprechenden Elemente.

Im Folgenden finden Sie eine Auswahl allgemeiner Optionen, die Sie anpassen können:

- Größe der geschützten VM (Variable in „azuredeploy.json“)
- Namenskonvention (Variable in „azuredeploy.json“)
- OpenShift-Clusterbesonderheiten, angepasst über „hosts“-Datei („deployOpenShift.sh“)

Die [OKD-Vorlage](https://github.com/Microsoft/openshift-origin) stellt mehrere Branches für verschiedene Versionen von OKD zur Verfügung.  Die Bereitstellung kann direkt über das Repository erfolgen. Bei Bedarf können Sie aber auch das Repository forken und vor der Bereitstellung Änderungen vornehmen.

Verwenden Sie den Wert `appId` des zuvor erstellten Dienstprinzipals für den `aadClientId`-Parameter.

Im Folgenden sehen Sie ein Beispiel einer Parameterdatei namens „azuredeploy.parameters.json“ mit allen erforderlichen Eingaben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Ersetzen Sie die Parameter durch Ihre eigenen Informationen.

Die Parameter können sich je nach Release unterschieden. Überprüfen Sie daher die erforderlichen Parameter für den verwendeten Branch.

### <a name="deploy-using-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle


> [!NOTE] 
> Der folgende Befehl erfordert Azure CLI 2.0.8 oder höher. Sie können die CLI-Version mit dem `az --version`-Befehl überprüfen. Informationen zum Aktualisieren der CLI-Version finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Das folgende Beispiel stellt den OKD-Cluster und alle zugehörigen Ressourcen in der Ressourcengruppe „openshiftrg“ mit dem Bereitstellungsnamen „myOpenShiftCluster“ bereit. Auf die Vorlage wird direkt aus dem GitHub-Repository verwiesen. Dazu wird eine lokale Parameterdatei namens „azuredeploy.parameters.json“ verwendet.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Die Bereitstellung dauert mindestens 30 Minuten – die Dauer hängt von der Gesamtzahl der bereitgestellten Knoten ab. Die URL der OpenShift-Konsole und der DNS-Name des OpenShift-Masters werden am Terminal ausgegeben, wenn die Bereitstellung abgeschlossen ist. Alternativ dazu können Sie den Ausgabeabschnitt der Bereitstellung im Azure-Portal anzeigen.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Wenn Sie die Befehlszeile nicht blockieren möchten, bis die Bereitstellung abgeschlossen ist, fügen Sie `--no-wait` als eine der Optionen für die Gruppenbereitstellung hinzu. Die Ausgabe der Bereitstellung kann über das Azure-Portal im Bereitstellungsabschnitt für die Ressourcengruppe abgerufen werden.

## <a name="connect-to-the-okd-cluster"></a>Verbindungsherstellung mit dem OKD-Cluster

Wenn die Bereitstellung abgeschlossen ist, stellen Sie mithilfe von `OpenShift Console Url` im Browser eine Verbindung mit der OpenShift-Konsole her. Alternativ dazu können Sie auch eine SSH-Verbindung mit dem OKD-Master herstellen. Im Folgenden finden Sie ein Beispiel, das die Ausgabe der Bereitstellung verwendet:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, OpenShift-Cluster und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group) entfernen.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nächste Schritte

- [Aufgaben nach der Bereitstellung](./openshift-container-platform-3x-post-deployment.md)
- [Beheben von Problemen bei der Bereitstellung von OpenShift](./openshift-container-platform-3x-troubleshooting.md)
- [Erste Schritte mit OKD](https://docs.okd.io)
