---
title: 'Schnellstart: Erstellen einer Linux-VM mit der Azure-Befehlszeilenschnittstelle'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Azure CLI einen virtuellen Linux-Computer erstellen.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 03/30/2021
ms.author: cynthn
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
ms.openlocfilehash: 4329110d117a81d34b70662e44e36d8aed21e114
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774194"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Schnellstart: Erstellen einer Linux-VM mit der Azure CLI

**Gilt für:** :heavy_check_mark: Linux-VMs

In dieser Schnellstartanleitung wird gezeigt, wie Sie mit der Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) einen virtuellen Linux-Computer in Azure bereitstellen. Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts.

In diesem Tutorial wird das neueste Ubuntu LTS-Image installiert. Sie können die VM in Aktion erleben, indem Sie per SSH eine Verbindung damit herstellen und den NGINX-Webserver installieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe**, um sie auszuführen.

Wenn Sie es vorziehen, die Befehlszeilenschnittstelle lokal zu installieren und zu verwenden, müssen Sie für diese Schnellstartanleitung mindestens Azure CLI-Version 2.0.30 verwenden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm) einen virtuellen Computer.

Im folgenden Beispiel wird eine VM mit dem Namen *myVM* erstellt und ein Benutzerkonto mit dem Namen *azureuser* hinzugefügt. Der Parameter `--generate-ssh-keys` wird genutzt, um automatisch einen SSH-Schlüssel zu generieren und am Speicherort für den Standardschlüssel abzulegen ( *~/.ssh*). Um stattdessen einen bestimmten Satz mit Schlüsseln zu verwenden, können Sie die Option `--ssh-key-values` nutzen.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten. In der folgenden Beispielausgabe wird angezeigt, dass der Vorgang der VM-Erstellung erfolgreich war.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Beachten Sie Ihre eigene `publicIpAddress` in der Ausgabe Ihres virtuellen Computers. Diese Adresse wird in den nächsten Schritten verwendet, um auf den virtuellen Computer zuzugreifen.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr

Standardmäßig werden beim Erstellen eines virtuellen Linux-Computers in Azure nur die SSH-Verbindungen geöffnet. Verwenden Sie [az vm open-port](/cli/azure/vm), um TCP-Port 80 für die Verwendung mit dem NGINX-Webserver zu öffnen:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Herstellen der Verbindung mit dem virtuellen Computer

Stellen Sie wie gewohnt eine SSH-Verbindung mit Ihrem virtuellen Computer her. Ersetzen Sie die IP-Adresse im Beispiel durch die öffentliche IP-Adresse Ihres virtuellen Computers, wie in der vorherigen Ausgabe angegeben:

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-web-server"></a>Installieren des Webservers

Wenn Sie den virtuellen Computer in Aktion sehen möchten, installieren Sie den NGINX-Webserver. Aktualisieren Sie Ihre Paketquellen, und installieren Sie dann das aktuelle NGINX-Paket.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Geben Sie abschließend `exit` ein, um die SSH-Sitzung zu verlassen.

## <a name="view-the-web-server-in-action"></a>Anzeigen des Webservers in Aktion

Verwenden Sie einen beliebigen Webbrowser, um die Standardwillkommensseite von NGINX anzuzeigen. Verwenden Sie die öffentliche IP-Adresse Ihres virtuellen Computers als Webadresse. Im folgenden Beispiel ist die NGINX-Standardwebsite dargestellt:

![Anzeigen der NGINX-Willkommensseite](./media/quick-create-cli/view-the-nginx-welcome-page.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group) entfernen. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen virtuellen Computer bereitgestellt, einen Netzwerkport für den Webdatenverkehr geöffnet und einen einfachen Webserver installiert. Fahren Sie mit dem Tutorial für virtuelle Linux-Computer fort, um weitere Informationen zu virtuellen Azure-Computern zu erhalten.


> [!div class="nextstepaction"]
> [Tutorials für virtuelle Azure Linux-Computer](./tutorial-manage-vm.md)
