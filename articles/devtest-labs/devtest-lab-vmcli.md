---
title: Erstellen und Verwalten von virtuellen Maschinen in Azure DevTest Labs mit Azure CLI
description: Erfahren Sie, wie Sie mit Azure DevTest Labs virtuelle Computer mit der Azure CLI erstellen und verwalten.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 52d98993de35ccc4b02fb95579719fb68b4017ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599542"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Erstellen und Verwalten virtueller Computer in DevTest Labs mit der Azure-Befehlszeilenschnittstelle
Dieser Schnellstart führt Sie durch das Erstellen, Starten, Verbinden, Aktualisieren und Bereinigen von Entwicklungscomputern im Lab. 

Vorbereitungen

* Falls Sie noch kein Lab erstellt haben, finden Sie [hier](devtest-lab-create-lab.md) Anweisungen dazu.

* [Installieren Sie die Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli). Führen Sie zum Starten „az login“ aus, um eine Verbindung mit Azure herzustellen. 

## <a name="create-and-verify-the-virtual-machine"></a>Erstellen und Überprüfen von virtuellen Computern 
Bevor Sie DevTest Labs-bezogene Befehle ausführen, legen Sie den entsprechenden Azure-Kontext mit dem Befehl `az account set` fest:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Der Befehl zum Erstellen eines virtuellen Computers lautet: `az lab vm create`. Die Ressourcengruppe für das Labs, den der Name des Labs und den Namen des virtuellen Computers sind alle erforderlich. Der Rest der Argumente ändert sich je nach Typ des virtuellen Computers.

Der folgende Befehl erstellt ein Windows-basiertes Image von Azure Market Place. Der Name des Images ist derselbe, der beim Erstellen eines virtuellen Computers über das Azure-Portal angezeigt wird. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Der folgende Befehl erstellt einen virtuellen Computer basierend auf einem benutzerdefinierten Image, das im Lab verfügbar ist:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Das Argument **image-type** wurde von **gallery** auf **custom** geändert. Der Name des Images entspricht dem, was angezeigt wird, wenn Sie den virtuellen Computer im Azure-Portal erstellen.

Mit dem folgenden Befehl erstellen Sie einen virtuellen Computer aus einem Marketplace-Image mit SSH-Authentifizierung:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

Sie können auch virtuelle Computer basierend auf Formeln erstellen, indem Sie den Parameter **image-type** auf **formula** festlegen. Wenn Sie ein bestimmtes virtuelles Netzwerk für Ihren virtuellen Computer auswählen müssen, verwenden Sie die Parameter **vnet-name** und **subnet**. Weitere Informationen finden Sie unter [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create).

## <a name="verify-that-the-vm-is-available"></a>Überprüfen Sie, ob der virtuelle Computer verfügbar ist.
Verwenden Sie den Befehl `az lab vm show`, um sicherzustellen, dass die VM verfügbar ist, bevor Sie sie starten und sich mit ihr verbinden. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Starten und Verbinden virtueller Computer
Der folgende Beispielbefehl startet eine VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Stellen Sie eine Verbindung mit einem virtuellen Computer her: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) oder [Remotedesktop](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aktualisieren virtueller Computer
Der folgende Beispielbefehl wendet Artefakte auf eine VM an:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Auflisten der im Lab verfügbaren Artefakte

Um auf einem virtuellen Computer in einem Lab verfügbare Artefakte aufzulisten, führen Sie die folgenden Befehle aus.

**Cloud Shell – PowerShell**: Beachten Sie die Verwendung des Graviszeichens (\`) vor dem „$“ in „$expand“ (d. h. `$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell – Bash**: Beachten Sie die Verwendung des Schrägstrichs (\\) vor „$“ im Befehl. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Beispielausgabe: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Beenden und Löschen virtueller Computer    
Der folgende Beispielbefehl stoppt eine VM.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Löschen Sie einen virtuellen Computer.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der [Azure CLI-Dokumentation zu Azure DevTest Labs](/cli/azure/lab). 
