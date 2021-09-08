---
title: Suchen und Verwenden von Marketplace-Erwerbsplaninformationen mithilfe der CLI
description: Erfahren Sie, wie Sie die Azure CLI verwenden, um Image-URNs und Erwerbsplanparameter, wie Herausgeber, Angebot, SKU und Version, für Marketplace-VM-Images zu finden.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: b0df8d446716363f21c468838523ffe4804f2e9c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691905"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Suchen nach Azure Marketplace-Imageinformationen mithilfe der Azure CLI

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Dieses Thema beschreibt, wie Sie mit der Azure-Befehlszeilenschnittstelle im Azure Marketplace nach VM-Images suchen. Verwenden Sie diese Informationen, um ein Marketplace-Image anzugeben, wenn Sie einen virtuellen Computer programmgesteuert mit der Befehlszeilenschnittstelle, mit Resource Manager-Vorlagen oder anderen Tools erstellen.

Sie können verfügbare Images und Angebote auch über den [Azure Marketplace](https://azuremarketplace.microsoft.com/) oder [Azure PowerShell](../windows/cli-ps-findimage.md) durchsuchen. 

## <a name="terminology"></a>Begriff

Ein Marketplace-Image in Azure hat die folgenden Attribute:

* **Publisher** (Herausgeber): Die Organisation, die das Image erstellt hat. Beispiele: Canonical, MicrosoftWindowsServer
* **Offer** (Angebot): Der Name einer Gruppe verwandter Images, die von einem Herausgeber erstellt wurden. Beispiele: UbuntuServer, WindowsServer
* **SKU**: Eine Instanz eines Angebots, etwa eine Hauptversion einer Distribution. Beispiele: 18.04-LTS, 2019-Datacenter
* **Version**: Die Versionsnummer einer Image-SKU. 

Diese Werte können einzeln oder als Image-*URN* übergeben werden, wobei die Werte durch den Doppelpunkt (:) getrennt kombiniert werden. Beispiel: *Herausgeber*:*Angebot*:*SKU*:*Version*. Sie können die Versionsnummer im URN durch `latest` ersetzen, um die neueste Version des Images zu verwenden. 

Wenn der Imageherausgeber zusätzliche Lizenz- und Kaufbedingungen angibt, müssen Sie diese akzeptieren, bevor Sie das Image verwenden können.  Weitere Informationen finden Sie unter [Überprüfen der Erwerbsplaninformationen](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Liste von beliebten Images

Führen Sie den Befehl [az vm image list](/cli/azure/vm/image) ohne die Option `--all` aus, um eine Liste beliebter VM-Images im Azure Marketplace anzuzeigen. Führen Sie beispielsweise den folgenden Befehl aus, um eine zwischengespeicherte Liste beliebter Images im Tabellenformat anzuzeigen:

```azurecli
az vm image list --output table
```

Die Ausgabe enthält den Image-URN. Sie können auch den *UrnAlias* verwenden, der eine verkürzte Version darstellt, die für beliebte Images wie *UbuntuLTS* erstellt wurde.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Suchen nach bestimmten Images

Wenn Sie ein bestimmtes VM-Image im Marketplace suchen möchten, verwenden Sie den Befehl `az vm image list` mit der Option `--all`. Die Ausführung dieser Befehlsversion dauert einige Zeit und kann eine umfangreiche Ausgabe zurückgeben. Daher filtern Sie in der Regel die Liste nach `--publisher` oder einem anderen Parameter. 

Mit dem folgenden Code werden beispielsweise alle Debian-Angebote angezeigt (zur Erinnerung: Ohne den Switch `--all` wird nur der lokale Cache von allgemeinen Images durchsucht):

```azurecli
az vm image list --offer Debian --all --output table 
```

Hier sehen Sie einen Teil der Ausgabe: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Betrachten Sie alle verfügbaren Images.
 
Eine weitere Möglichkeit für die Suche nach einem Image an einem Standort besteht darin, die Befehle [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) und [az vm image list-skus](/cli/azure/vm/image) nacheinander auszuführen. Mit diesen Befehlen ermitteln Sie folgende Werte:

1. Listet die Imageherausgeber für einen Standort auf. In diesem Beispiel betrachten wir die Region *USA, Westen*.
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Auflistung der Angebote eines bestimmten Anbieters In diesem Beispiel fügen wir *Canonical* als Herausgeber hinzu.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Auflistung der SKUs eines bestimmten Angebots In diesem Beispiel fügen wir *UbuntuServer* als Angebot hinzu.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Zeigen Sie für einen bestimmten Herausgeber, ein bestimmtes Angebot und eine bestimmte SKU alle Versionen des Images an. In diesem Beispiel fügen wir *18.04-LTS* als SKU hinzu.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Übergeben Sie diesen Wert der URN-Spalte mit dem Parameter `--image`, wenn Sie einen virtuellen Computer mit dem Befehl [az vm create](/cli/azure/vm) erstellen. Sie können auch die Versionsnummer im URN durch „latest“ ersetzen, um einfach die aktuelle Version des Images zu verwenden. 

Wenn Sie einen virtuellen Computer mithilfe einer Resource Manager-Vorlage bereitstellen, legen Sie die Imageparameter einzeln in den `imageReference`-Eigenschaften fest. Informationen finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Überprüfen der Informationen zum Erwerbsplan

Für einige VM-Images im Azure Marketplace gelten zusätzliche Lizenz- und Kaufbedingungen, die Sie akzeptieren müssen, bevor Sie sie programmgesteuert bereitstellen können.  

Wenn Sie einen virtuellen Computer aus einem solchen Image bereitstellen möchten, müssen Sie die Bedingungen des Images bei der ersten Verwendung übernehmen, einmal pro Abonnement. Sie müssen auch Parameter für den *Erwerbsplan* angeben, um einen virtuellen Computer aus diesem Image bereitzustellen.

Um die Erwerbsplaninformationen eines Images anzuzeigen, führen Sie den Befehl [az vm image show](/cli/azure/image) mit dem Image-URN aus. Wenn die `plan`-Eigenschaft in der Ausgabe nicht `null` ist, müssen Sie vor der programmgesteuerten Bereitstellung Bedingungen des Images akzeptieren.

Das Image „Canonical Ubuntu Server 18.04 LTS“ verfügt beispielsweise nicht über zusätzliche Bedingungen, da `null` für `plan` angegeben ist:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Ausgabe:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Wenn Sie einen ähnlichen Befehl für das von Bitnami zertifizierte RabbitMQ-Image ausführen, werden die folgenden `plan`-Eigenschaften angezeigt: `name`, `product` und `publisher`. (Einige Images besitzen auch eine `promotion code`-Eigenschaft.) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Ausgabe:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

Zum Bereitstellen dieses Images müssen Sie die Bedingungen akzeptieren und die Erwerbsplanparameter angeben, wenn Sie einen virtuellen Computer mit diesem Image bereitstellen.

## <a name="accept-the-terms"></a>Akzeptieren der Bedingungen

Verwenden Sie zum Anzeigen und Akzeptieren der Lizenzbedingungen den Befehl [az vm image terms](/cli/azure/vm/image/terms). Wenn Sie die Bedingungen akzeptieren, ermöglichen Sie die programmgesteuerte Bereitstellung in Ihrem Abonnement. Sie müssen für das Image die Bedingungen nur einmal pro Abonnement akzeptieren. Beispiel:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Die Ausgabe enthält ein `licenseTextLink`-Element für die Lizenzbedingungen und gibt an, dass für `accepted` der Wert `true` angegeben ist:

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

Geben Sie Folgendes ein, um die Bedingungen zu akzeptieren:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Bereitstellen eines neuen virtuellen Computers unter Verwendung der Imageparameter

Mithilfe der Informationen zum Image können Sie es mit dem Befehl `az vm create` bereitstellen. 

Zum Bereitstellen eines Images, das keine Planinformationen enthält, wie das neueste Ubuntu Server 18.04-Image von Canonical, übergeben Sie den URN für `--image`:

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Für ein Image mit Erwerbsplanparametern, wie das Image „RabbitMQ Certified by Bitnami“, übergeben Sie den URN für `--image` und geben auch die Erwerbsplanparameter an:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Wenn Sie eine Meldung zum Akzeptieren der Nutzungsbedingungen des Images erhalten, überprüfen Sie den Abschnitt [Akzeptieren der Nutzungsbedingungen](#accept-the-terms). Stellen Sie sicher, dass die Ausgabe von `az vm image accept-terms` den Wert `"accepted": true,` zurückgibt, der anzeigt, dass Sie die Bedingungen des Images akzeptiert haben.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Verwenden einer vorhandenen VHD mit Erwerbsplaninformationen

Wenn eine Ihrer virtuellen Festplatten eines virtuellen Computers mit einem kostenpflichtigen Azure Marketplace-Image erstellt wurde, müssen Sie beim Erstellen einer neuen VM von dieser VHD möglicherweise die Informationen zu Ihrem Erwerbsplan angeben. 

Wenn Sie noch über die ursprüngliche VM oder eine andere VM verfügen, die mit demselben Marketplace-Image erstellt wurde, können Sie den Plannamen, den Herausgeber und die Produktinformationen mithilfe von [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) abrufen. Dieses Beispiel ruft eine VM namens *myVM* in der Ressourcengruppe *myResourceGroup* ab und zeigt dann die Erwerbsplaninformationen an.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Wenn Sie die Planinformationen nicht vor dem Löschen der ursprünglichen VM abgerufen haben, können Sie eine [Supportanfrage](https://ms.portal.azure.com/#create/Microsoft.Support) erstellen. Sie benötigen dazu den VM-Namen, die Abonnement-ID und den Zeitstempel des Löschvorgangs.

Wenn Sie die Planinformationen haben, können Sie die neue VM erstellen, indem Sie die VHD mit dem Parameter `--attach-os-disk` angeben.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>Nächste Schritte
Um schnell mithilfe der Imageinformationen einen virtuellen Computer zu erstellen, lesen Sie die Informationen unter [Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle](tutorial-manage-vm.md).
