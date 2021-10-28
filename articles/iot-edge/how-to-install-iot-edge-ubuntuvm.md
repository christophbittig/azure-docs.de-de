---
title: Ausführen von virtuellen Computern vom Typ „Azure IoT Edge unter Ubuntu“ | Microsoft-Dokumentation
description: Azure IoT Edge-Setupanweisungen für virtuelle Ubuntu 18.04 LTS-Computer
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: pdecarlo
ms.openlocfilehash: 6865a89eae0df708d73d9062dd07c18c46ece4f8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263760"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Ausführen von virtuellen Computern vom Typ „Azure IoT Edge unter Ubuntu“

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Die Azure IoT Edge-Runtime verwandelt ein Gerät in ein IoT Edge-Gerät. Die Runtime kann auf verschiedensten Geräten bereitgestellt werden – vom kleinen Raspberry Pi bis hin zum großen industriellen Server. Wenn ein Gerät mit der IoT Edge-Runtime konfiguriert wurde, können Sie darauf Geschäftslogik aus der Cloud bereitstellen.

Weitere Informationen zur Funktionsweise und zu den Komponenten der IoT Edge-Runtime finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

In diesem Artikel werden die Schritte zum Bereitstellen eines virtuellen Ubuntu 18.04 LTS-Computers mit installierter Azure IoT Edge-Runtime beschrieben, die mithilfe einer vorab bereitgestellten Geräte-Verbindungszeichenfolge installiert und konfiguriert wurde. Die Bereitstellung erfolgt mithilfe einer [cloud-init](../virtual-machines/linux/using-cloud-init.md)-basierten [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md), die im Projektrepository [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) verwaltet wird.

Beim ersten Start wird vom virtuellen Ubuntu 18.04 LTS-Computer [die neueste Version der Azure IoT Edge-Runtime über „cloud-init“ installiert](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Außerdem wird von ihm vor dem Start der Laufzeit eine angegebene Verbindungszeichenfolge festgelegt, sodass Sie das IoT Edge-Gerät problemlos konfigurieren und verbinden können, ohne eine SSH- oder Remotedesktop-Sitzung starten zu müssen.

## <a name="deploy-using-deploy-to-azure-button"></a>Bereitstellen mithilfe der Schaltfläche „In Azure bereitstellen“

Die Schaltfläche [In Azure bereitstellen](../azure-resource-manager/templates/deploy-to-azure-button.md) ermöglicht eine optimierte Bereitstellung von [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md), die auf GitHub verwaltet verwenden.  In diesem Abschnitt wird die Verwendung der Schaltfläche „In Azure bereitstellen“ im Repository [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) gezeigt.  

1. Sie stellen mithilfe der Azure Resource Manager-Vorlage „iotedge-vm-deploy“ eine Azure IoT Edge-fähige Linux-VM bereit.  Klicken Sie zuerst auf die Schaltfläche unten:
   :::moniker range="iotedge-2018-06"
   [![Schaltfläche „In Azure bereitstellen“ für „iotedge-vm-deploy“](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)
   :::moniker-end
   :::moniker range="iotedge-2020-11"
   [![Schaltfläche „In Azure bereitstellen“ für „iotedge-vm-deploy“](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2F1.2.0%2FedgeDeploy.json)
   :::moniker-end

1. Füllen Sie im neu gestarteten Fenster die verfügbaren Formularfelder aus:

    > [!div class="mx-imgBorder"]
    > [![Screenshot der Vorlage „iotedge-vm-deploy“](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonnement**: Das aktive Azure-Abonnement, in dem der virtuelle Computer bereitgestellt werden soll.

    **Ressourcengruppe**: Eine vorhandene oder neu erstellte Ressourcengruppe, die den virtuellen Computer und die ihm zugeordneten Ressourcen enthalten soll.

    **Präfix der DNS-Bezeichnung**: Ein erforderlicher Wert Ihrer Wahl, der dem Hostnamen des virtuellen Computers vorangestellt werden soll.

    **Administratorbenutzername**: Ein Benutzername, der Rootberechtigungen für die Bereitstellung erhält.

    **Geräte-Verbindungszeichenfolge**: Eine [Geräte-Verbindungszeichenfolge](./how-to-provision-single-device-linux-symmetric.md#view-registered-devices-and-retrieve-provisioning-information) für ein Gerät, das in Ihrem vorgesehenen [IoT Hub](../iot-hub/about-iot-hub.md) erstellt wurde.

    **VM-Größe**: Die [Größe](../cloud-services/cloud-services-sizes-specs.md) des bereitzustellenden virtuellen Computers

    **Ubuntu-Betriebssystemversion**: Die Version des Ubuntu-Betriebssystems, die auf dem virtuellen Basiscomputer installiert werden soll.

    **Standort**: Die [geografische Region](https://azure.microsoft.com/global-infrastructure/locations/), in der der virtuelle Computer bereitgestellt werden soll. Dieser Wert wird standardmäßig auf den Speicherort der ausgewählten Ressourcengruppe festgelegt.

    **Authentifizierungstyp**: Wählen Sie je nach Ihrer Einstellung **sshPublicKey** oder **password** aus.

    **Administratorkennwort oder Schlüssel**: Der Wert des öffentlichen SSH-Schlüssels oder der Wert des Kennworts, je nach der Auswahl des Authentifizierungstyps.

    Nachdem Sie alle Felder ausgefüllt haben, aktivieren Sie das Kontrollkästchen unten auf der Seite zum Akzeptieren der Bedingungen, und wählen Sie **Kauf** aus, um die Bereitstellung zu starten.

1. Überprüfen Sie, ob die Bereitstellung erfolgreich abgeschlossen wurde.  Eine virtuelle Computerressource sollte in der ausgewählten Ressourcengruppe bereitgestellt worden sein.  Notieren Sie sich den Computernamen, der das Format `vm-0000000000000` haben sollte. Notieren Sie sich außerdem den zugeordneten **DNS-Namen**, der das Format „`<dnsLabelPrefix>`.`<location>`cloudapp.azure.com“ haben sollte.

    Der **DNS-Name** kann im Azure-Portal aus dem Abschnitt **Übersicht** des neu bereitgestellten virtuellen Computers abgerufen werden.

    > [!div class="mx-imgBorder"]
    > [![Screenshot mit dem DNS-Namen der IoT Edge-VM](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Wenn Sie nach der Einrichtung eine SSH-Verbindung mit dieser VM herstellen möchten, verwenden Sie den zugeordneten **DNS-Namen** mit folgendem Befehl: `ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

1. Vergewissern Sie sich, dass Sie die IoT-Erweiterung der Azure-Befehlszeilenschnittstelle installiert haben, mit:

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

1. Wenn Sie die Azure-Befehlszeilenschnittstelle auf Ihrem Desktop verwenden, melden Sie sich als Nächstes an:

   ```azurecli-interactive
   az login
   ```

1. Wenn Sie mehrere Abonnements haben, wählen Sie das Abonnement aus, das Sie verwenden möchten:
   1. Listen Sie Ihre Abonnements auf:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kopieren Sie das Feld „SubscriptionID“ für das Abonnement, das Sie verwenden möchten.

   1. Legen Sie Ihr Arbeitsabonnement mit der kopierten ID fest:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Erstellen Sie eine neue Ressourcengruppe (oder geben Sie in den nächsten Schritten eine vorhandene Ressourcengruppe an):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Erstellen Sie einen neuen virtuellen Computer:
   :::moniker range="iotedge-2018-06"
   Wenn Sie einen **authenticationType** von `password` verwenden möchten, sehen Sie sich das folgende Beispiel an:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

   Wenn Sie sich mit einem SSH-Schlüssel authentifizieren möchten, geben Sie dazu einen **authenticationType** von `sshPublicKey` und dann den Wert des SSH-Schlüssels im Parameter **adminPasswordOrKey** an.  Ein entsprechendes Beispiel ist nachfolgend dargestellt.

   ```azurecli-interactive
   #Generate the SSH Key
   ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  
    
   #Create a VM using the iotedge-vm-deploy script
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='sshPublicKey' \
   --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
   ```
   :::moniker-end
   :::moniker range="iotedge-2020-11"
   Wenn Sie einen **authenticationType** von `password` verwenden möchten, sehen Sie sich das folgende Beispiel an:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

   Wenn Sie sich mit einem SSH-Schlüssel authentifizieren möchten, geben Sie dazu einen **authenticationType** von `sshPublicKey` und dann den Wert des SSH-Schlüssels im Parameter **adminPasswordOrKey** an.  Ein entsprechendes Beispiel ist nachfolgend dargestellt.

   ```azurecli-interactive
   #Generate the SSH Key
   ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  
    
   #Create a VM using the iotedge-vm-deploy script
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='sshPublicKey' \
   --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
   ```
   :::moniker-end

1. Überprüfen Sie, ob die Bereitstellung erfolgreich abgeschlossen wurde.  Eine virtuelle Computerressource sollte in der ausgewählten Ressourcengruppe bereitgestellt worden sein.  Notieren Sie sich den Computernamen, der das Format `vm-0000000000000` haben sollte. Notieren Sie sich außerdem den zugeordneten **DNS-Namen**, der das Format „`<dnsLabelPrefix>`.`<location>`cloudapp.azure.com“ haben sollte.

    Der **DNS-Name** kann aus der JSON-formatierten Ausgabe des vorherigen Schritts abgerufen werden, die im Abschnitt **outputs** als Teil des Eintrags **public SSH** enthalten ist.  Der Wert dieses Eintrags kann verwendet werden, um eine SSH-Verbindung mit dem neu bereitgestellten Computer herzustellen.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    Der **DNS-Name** kann auch im Azure-Portal aus dem Abschnitt **Übersicht** des neu bereitgestellten virtuellen Computers abgerufen werden.

    > [!div class="mx-imgBorder"]
    > [![Screenshot mit dem DNS-Namen der IoT Edge-VM](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Wenn Sie nach der Einrichtung eine SSH-Verbindung mit dieser VM herstellen möchten, verwenden Sie den zugeordneten **DNS-Namen** mit folgendem Befehl: `ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun ein IoT Edge-Gerät für die installierte Runtime bereitgestellt haben, können Sie [IoT Edge-Module bereitstellen](how-to-deploy-modules-portal.md).

Wenn Sie Probleme mit der ordnungsgemäßen Installation der IoT Edge-Runtime haben, lesen Sie die Informationen auf der Seite [Problembehandlung](troubleshoot.md).

Weitere Informationen zum Aktualisieren einer vorhandenen Installation auf die aktuelle Version von IoT Edge finden Sie unter [Aktualisieren des IoT Edge-Sicherheitsdaemons und der Runtime](how-to-update-iot-edge.md).

Wenn Sie Ports für den Zugriff auf die VM über SSH oder andere eingehende Verbindungen öffnen möchten, lesen Sie in der Dokumentation zu virtuellen Azure-Computern das Thema [Öffnen von Ports und Endpunkten für eine Linux-VM](../virtual-machines/linux/nsg-quickstart.md).