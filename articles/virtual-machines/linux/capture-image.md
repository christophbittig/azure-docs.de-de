---
title: Erfassen eines verwalteten Images einer Linux-VM mit der Azure-Befehlszeilenschnittstelle
description: Erfassen Sie mit der Azure-Befehlszeilenschnittstelle ein verwaltetes Image einer Azure-VM, das für Massenbereitstellungen verwendet werden soll.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 08/27/2021
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: 0aae67dbe347c8299e00d741163370d9097c8114
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432850"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Erstellen eines verwalteten Images eines virtuellen Computers oder einer VHD

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Um mehrere Kopien eines virtuellen Computers (VM) für die Verwendung zum Entwickeln und Testen in Azure zu erstellen, erfassen Sie ein verwaltetes Image der VM oder der Betriebssystem-VHD. Informationen zum Erstellen, Speichern und Freigeben von Images in jeder Größenordnung finden Sie unter [Azure Compute Galleries](../create-gallery.md).

Ein verwaltetes Image unterstützt bis zu 20 Bereitstellungen gleichzeitig. Wenn Sie versuchen, mehr als 20 VMs gleichzeitig aus demselben verwalteten Image zu erstellen, kann dies aufgrund der Einschränkungen bei der Speicherleistung einer einzelnen VHD zu Timeouts bei der Bereitstellung führen. Wenn Sie mehr als 20 virtuelle Computer gleichzeitig erstellen möchten, verwenden Sie ein Image für [Azure Compute Gallery](../shared-image-galleries.md) (früher Shared Image Gallery genannt), das mit jeweils 1 Replikat pro 20 gleichzeitiger Bereitstellungen an virtuellen Computern konfiguriert wurde.

Wenn Sie ein verwaltetes Image erstellen möchten, müssen Sie persönliche Kontoinformationen entfernen. In den folgenden Schritten heben Sie die Bereitstellung eines vorhandenen virtuellen Computers auf, heben dessen Zuordnung auf und erstellen ein Image. Sie können dieses Image verwenden, um virtuelle Computer über jede Ressourcengruppe innerhalb Ihres Abonnements hinweg zu erstellen.

Wenn Sie zum Sichern oder Debuggen eine Kopie Ihres vorhandenen virtuellen Linux-Computers erstellen oder eine Linux-VHD über einen lokalen virtuellen Computer hochladen möchten, lesen Sie [Erstellen eines virtuellen Linux-Computers auf der Grundlage eines benutzerdefinierten Datenträgers mithilfe der Azure-Befehlszeilenschnittstelle](upload-vhd.md).  

Sie können **Azure VM Image Builder** nutzen, um Ihr benutzerdefiniertes Image zu erstellen, ohne vorher den Umgang mit Tools zu erlernen oder eine Buildpipeline einzurichten. Sie geben einfach nur eine Imagekonfiguration ein und der Image Builder erstellt das Image. Weitere Informationen finden Sie unter [Erste Schritte mit Azure VM Image Builder](../image-builder-overview.md).

Damit Sie ein Image erstellen können, müssen die folgenden Voraussetzungen erfüllt sein:

* Im Resource Manager-Bereitstellungsmodell muss ein virtueller Azure-Computer mit verwalteten Datenträgern erstellt worden sein. Wenn Sie noch keinen virtuellen Linux-Computer erstellt haben, können Sie das [Portal](quick-create-portal.md), die [Azure-Befehlszeilenschnittstelle](quick-create-cli.md) (Azure CLI) oder [Resource Manager-Vorlagen](create-ssh-secured-vm-from-template.md) dazu verwenden. Konfigurieren Sie die VM den Anforderungen entsprechend. Sie können beispielsweise [Datenträger hinzufügen](add-disk.md), Updates einspielen und Anwendungen installieren. 

* Die neueste Version von [Azure CLI](/cli/azure/install-az-cli2) muss installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/reference-index#az_login) bei einem Azure-Konto angemeldet sein.

## <a name="prefer-a-tutorial-instead"></a>Bevorzugen Sie stattdessen ein Tutorial?

Eine vereinfachte Version dieses Artikels sowie Informationen zum Testen und Auswerten oder zu VMs in Azure finden Sie unter [Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mit der Azure-Befehlszeilenschnittstelle](tutorial-custom-images.md).  Lesen Sie andernfalls hier weiter, um das vollständige Bild zu erhalten.


## <a name="step-1-deprovision-the-vm"></a>Schritt 1: Aufheben der Bereitstellung des virtuellen Computers
Zuerst heben Sie die Bereitstellung des virtuellen Computers mithilfe des Azure-VM-Agents auf, um computerspezifische Dateien und Daten zu löschen. Verwenden Sie auf dem virtuellen Linux-Quellcomputer den Befehl `waagent` mit dem Parameter `-deprovision+user`. Weitere Informationen erhalten Sie im [Benutzerhandbuch für Azure Linux-Agent](../extensions/agent-linux.md). Dieser Vorgang kann nicht rückgängig gemacht werden.

1. Stellen Sie mit einem SSH-Client eine Verbindung mit Ihrem virtuellen Linux-Computer her.
2. Geben Sie im SSH-Fenster den folgenden Befehl ein:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Führen Sie diesen Befehl nur auf einem virtuellen Computer aus, den Sie als Image erfassen möchten. Die Ausführung dieses Befehls garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden oder dass es für eine erneute Verteilung geeignet ist. Der Parameter `+user` entfernt auch das zuletzt bereitgestellte Benutzerkonto. Wenn die Anmeldeinformationen des Benutzerkontos auf dem virtuellen Computer verbleiben sollen, verwenden Sie nur `-deprovision`.
 
3. Geben Sie **y** ein, um fortzufahren. Sie können den Parameter `-force` hinzufügen, um diesen Bestätigungsschritt zu vermeiden.
4. Geben Sie nach der Ausführung dieses Befehls den Befehl **exit** ein, um den SSH-Client zu schließen.  Der virtuelle Computer wird zu diesem Zeitpunkt immer noch ausgeführt.

## <a name="step-2-create-vm-image"></a>Schritt 2: Erstellen des VM-Image
Verwenden Sie Azure CLI, um die VM als generalisiert zu kennzeichnen und das Image zu erfassen. Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind u.a. *myResourceGroup*, *myVnet* und *myVM*.

1. Heben Sie die Zuordnung des virtuellen Computers auf, dessen Bereitstellung Sie mit [az vm deallocate](/cli/azure/vm) aufgehoben haben. Im folgenden Beispiel wird die Zuordnung des virtuellen Computers *myVM* in der Ressourcengruppe *myResourceGroup* aufgehoben.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Warten Sie, bis die Zuordnung des virtuellen Computers vollständig aufgehoben wurde. Dies kann einige Minuten in Anspruch nehmen.  Der virtuelle Computer wird während der Aufhebung der Zuordnung heruntergefahren.

2. Kennzeichnen Sie die VM mit [az vm generalize](/cli/azure/vm) als generalisiert. Im folgenden Beispiel wird der virtuelle Computer *myVM* in der Ressourcengruppe *myResourceGroup* als generalisiert gekennzeichnet.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Eine VM, die generalisiert wurde, kann nicht mehr neu gestartet werden.

3. Erstellen Sie ein Image der VM-Ressource mit [az image create](/cli/azure/image#az_image_create). Im folgenden Beispiel wird ein Image mit dem Namen *myImage* in der Ressourcengruppe *myResourceGroup* mit der VM-Ressource *myVM* erstellt.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
    --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Das Image wird in derselben Ressourcengruppe wie der virtuelle Quellcomputer erstellt. Sie können aus diesem Image virtuelle Computer in einer beliebigen Ressourcengruppe in Ihrem Abonnement erstellen. Angesichts der Verwaltung können Sie eine spezifische Ressourcengruppe für die VM-Ressourcen und Images erstellen.
   >
   > Wenn Sie ein Image eines virtuellen Computers der Generation 2 erfassen, verwenden Sie auch den `--hyper-v-generation V2`-Parameter. Weitere Informationen finden Sie unter [VMs der Generation 2](../generation-2.md).
   > 
   > Wenn Sie das Image in Speicher mit Zonenresilienz speichern möchten, müssen Sie es in einer Region erstellen, die [Verfügbarkeitszonen](../../availability-zones/az-overview.md) unterstützt, und den `--zone-resilient true`-Parameter einbeziehen.
   
Dieser Befehl gibt JSON zurück, das das VM-Image beschreibt. Speichern Sie diese Ausgabe zur späteren Referenznahme.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Schritt 3: Bereitstellen eines virtuellen Computers anhand des erfassten Images
Erstellen Sie anhand des erstellten Images einen virtuellen Computer mit [az vm create](/cli/azure/vm). Im folgenden Beispiel wird ein virtueller Computer mit dem Namen *myVMDeployed* anhand des Images *myImage* erstellt.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Erstellen der VM in einer anderen Ressourcengruppe 

Sie können VMs aus einem Image in jeder Ressourcengruppe innerhalb Ihres Abonnements erstellen. Zum Erstellen eines virtuellen Computers in einer anderen Ressourcengruppe als dem Image geben Sie die vollständige Ressourcen-ID im Image an. Verwenden Sie [az image list](/cli/azure/image#az_image_list), um eine Liste von Images anzuzeigen. Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Im folgenden Beispiel wird [az vm create](/cli/azure/vm#az_vm_create) verwendet, um einen virtuellen Computer in einer anderen Ressourcengruppe als das Quellimage zu erstellen, indem die Imageressourcen-ID angegeben wird.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Schritt 4: Überprüfen der Bereitstellung

Stellen Sie über SSH eine Verbindung mit dem von Ihnen erstellten virtuellen Computer her, um die Bereitstellung zu überprüfen und den neuen virtuellen Computer zu verwenden. Zum Herstellen einer Verbindung über SSH ermitteln Sie die IP-Adresse oder den FQDN Ihres virtuellen Computers mit [az vm show](/cli/azure/vm#az_vm_show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Erstellen, Speichern und Freigeben von Images in jeder Größenordnung finden Sie unter [Azure Compute Galleries](../create-gallery.md).
