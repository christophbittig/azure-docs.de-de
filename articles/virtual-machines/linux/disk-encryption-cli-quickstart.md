---
title: Erstellen und Verschlüsseln einer Linux-VM mit der Azure CLI
description: In diesem Schnellstart erfahren Sie, wie Sie mit der Azure CLI einen virtuellen Linux-Computer erstellen und verschlüsseln.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9b2e96f288bc2c83f1957aa66a770c09be21282b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588502"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Schnellstart: Erstellen und Verschlüsseln einer Linux-VM mit der Azure CLI

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In diesem Schnellstart wird gezeigt, wie Sie mithilfe der Azure CLI einen virtuellen Linux-Computer (VM) erstellen und verschlüsseln.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Wenn Sie die Azure-Befehlszeilenschnittstelle lokal installieren und verwenden möchten, benötigen Sie für diese Schnellstartanleitung mindestens die Version 2.0.30 der Azure-Befehlszeilenschnittstelle. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* erstellt:

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten. In der folgenden Beispielausgabe wird angezeigt, dass der Vorgang der VM-Erstellung erfolgreich war.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Erstellen eines für Verschlüsselungsschlüssel konfigurierten Schlüsseltresors

Bei der Azure-Datenträgerverschlüsselung werden die Verschlüsselungsschlüssel in Azure Key Vault gespeichert. Erstellen Sie mit [az keyvault create](/cli/azure/keyvault#az_keyvault_create) einen Schlüsseltresor. Um dem Schlüsseltresor die Speicherung von Verschlüsselungscodes zu ermöglichen, verwenden Sie den Parameter „--enabled-for-disk-encryption“.

> [!Important]
> Jeder Schlüsseltresor muss einen Namen aufweisen, der in Azure eindeutig ist. Ersetzen Sie in den folgenden Beispielen \<your-unique-keyvault-name\> durch den Namen, den Sie auswählen.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Verschlüsseln des virtuellen Computers

Verschlüsseln Sie Ihre VM mit [az vm encryption](/cli/azure/vm/encryption), und geben Sie dem Parameter „--disk-encryption-keyvault“ Ihren eindeutigen Schlüsseltresornamen an.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Der Prozess gibt folgende Meldung zurück: „The encryption request was accepted. Please use 'show' command to monitor the progress“ (Die Verschlüsselungsanforderung wurde angenommen. Verwenden Sie den Befehl „show“, um den Fortschritt zu überwachen). Der Befehl „show“ ist [az vm show](/cli/azure/vm/encryption#az_vm_encryption_show).

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

Wenn die Verschlüsselung aktiviert ist, wird in der zurückgegebenen Ausgabe Folgendes angezeigt:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, VM und der Schlüsseltresor nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group) entfernen. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen virtuellen Computer und einen Schlüsseltresor erstellt, der für Verschlüsselungsschlüssel aktiviert wurde, und die VM verschlüsselt.  Fahren Sie mit dem nächsten Artikel fort, um weitere Informationen zu Azure Disk Encryption für virtuelle Linux-Computer zu erhalten.

> [!div class="nextstepaction"]
> [Übersicht über Azure Disk Encryption](disk-encryption-overview.md)
