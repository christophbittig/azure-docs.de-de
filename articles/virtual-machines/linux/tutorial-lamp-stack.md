---
title: 'Tutorial: Bereitstellen von LAMP und WordPress auf einem virtuellen Computer'
description: In diesem Tutorial erfahren Sie, wie Sie den LAMP-Stack und WordPress auf einem virtuellen Linux-Computer in Azure installieren.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: ec73bc6c6f7563f1513f4575b5ee3c9b4a244e7c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698998"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Tutorial: Installieren eines LAMP-Stacks auf einem virtuellen Azure Linux-Computer

**Gilt für**: :heavy_check_mark: Linux-VMs 

In diesem Artikel werden Sie durch die Bereitstellung eines Apache-Webservers sowie von MySQL und PHP (LAMP-Stack) auf einem virtuellen Ubuntu-Computer in Azure geführt. Um den LAMP-Server in Aktion zu sehen, können Sie optional eine WordPress-Website installieren und konfigurieren. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Ubuntu-Computers 
> * Öffnen von Port 80 für Webdatenverkehr
> * Installieren von Apache, MySQL und PHP
> * Überprüfen der Installation und Konfiguration
> * Installieren von WordPress 

Dieses Setup ist für schnelle Tests oder Proof of Concept gedacht. Weitere Informationen zum LAMP-Stack, einschließlich Empfehlungen für eine Produktionsumgebung, finden Sie in der [Ubuntu-Dokumentation](https://help.ubuntu.com/community/ApacheMySQLPHP).

Dieses Tutorial verwendet die CLI innerhalb des Diensts [Azure Cloud Shell](../../cloud-shell/overview.md), der ständig auf die neueste Version aktualisiert wird. Wählen Sie zum Öffnen von Cloud Shell oben in einem Codeblock die Option **Ausprobieren** aus.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm) einen virtuellen Computer. 

Das folgende Beispiel erstellt einen virtuellen Computer mit dem Namen *myVM* und SSH-Schlüssel, falls sie nicht bereits an einem Standardschlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`. Der Befehl legt zudem *azureuser* als Administratorbenutzernamen fest. Sie verwenden diesen Namen später, um eine Verbindung mit dem virtuellen Computer herzustellen. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Nach dem Erstellen der VM zeigt die Azure CLI ähnliche Informationen wie im folgenden Beispiel an. Notieren Sie sich den Wert von `publicIpAddress`. Diese Adresse wird später verwendet, um auf den virtuellen Computer zuzugreifen.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr 

Standardmäßig sind für in Azure bereitgestellte virtuelle Linux-Computer nur eingehende SSH-Verbindungen zulässig. Da dieser virtuelle Computer als Webserver fungieren soll, muss Port 80 für Datenverkehr aus dem Internet geöffnet werden. Verwenden Sie zum Öffnen des gewünschten Ports den Befehl [az vm open-port](/cli/azure/vm).  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Weitere Informationen zum Öffnen von Ports für Ihren virtuellen Computer finden Sie unter [Öffnen von Ports und Endpunkten für eine VM über die Azure-Befehlszeilenschnittstelle](nsg-quickstart.md).

## <a name="ssh-into-your-vm"></a>Herstellen einer SSH-Verbindung mit Ihrem virtuellen Computer

Wenn Sie die öffentliche IP-Adresse des virtuellen Computers nicht bereits kennen, führen Sie den Befehl [az network public-ip list](/cli/azure/network/public-ip) aus. Sie benötigen diese IP-Adresse für mehrere nachfolgende Schritte.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Ersetzen Sie die korrekte öffentliche IP-Adresse Ihres virtuellen Computers. In diesem Beispiel lautet die IP-Adresse *40.68.254.142*. *azureuser* ist der Administratorbenutzername, der beim Erstellen des virtuellen Computers festgelegt wurde.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Installieren von Apache, MySQL und PHP

Führen Sie den folgenden Befehl aus, um die Ubuntu-Paketquellen zu aktualisieren und Apache, MySQL und PHP zu installieren. Beachten Sie das Caretzeichen (^) am Ende des Befehls, das Teil des Paketnamens `lamp-server^` ist. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Sie werden aufgefordert, die Pakete und andere Abhängigkeiten zu installieren. Dieser Prozess installiert die PHP-Erweiterungen, die mindestens für die Verwendung von PHP mit MySQL erforderlich sind.  

## <a name="verify-apache"></a>Überprüfen von Apache

Überprüfen Sie die Version von Apache mit dem folgenden Befehl:
```bash
apache2 -v
```

Nachdem Apache installiert und Port 80 für den virtuellen Computer geöffnet wurde, ist der Zugriff auf den Webserver über das Internet möglich. Öffnen Sie zum Anzeigen der Apache2 Ubuntu-Standardseite einen Webbrowser, und geben Sie die öffentliche IP-Adresse des virtuellen Computers ein. Verwenden Sie die öffentliche IP-Adresse, die Sie zum Herstellen einer SSH-Verbindung mit dem virtuellen Computer verwendet haben:

![Apache-Standardseite][3]


## <a name="verify-and-secure-mysql"></a>Überprüfen und Sichern von MySQL

Überprüfen Sie die Version von MySQL mit dem folgenden Befehl (beachten Sie die Großschreibung des `V`-Parameters):

```bash
mysql -V
```

Führen Sie das Skript `mysql_secure_installation` aus, um die Installation von MySQL zu sichern, u.a. durch Festlegen eines Stammkennworts. 

```bash
sudo mysql_secure_installation
```

Sie können optional das Plug-In zur Kennwortüberprüfung einrichten (empfohlen). Legen Sie dann ein Kennwort für den MySQL-Root-Benutzer fest, und konfigurieren Sie die übrigen Sicherheitseinstellungen für Ihre Umgebung. Es wird empfohlen, auf alle Fragen mit „Y“ (ja) zu antworten.

Wenn Sie MySQL-Features (MySQL-Datenbank erstellen, Benutzer hinzufügen oder Konfigurationseinstellungen ändern) ausprobieren möchten, melden Sie sich bei MySQL an. Dieser Schritt ist für den Abschluss des Tutorials nicht erforderlich.

```bash
sudo mysql -u root -p
```

Beenden Sie anschließend die MySQL-Eingabeaufforderung durch Eingabe von `\q`.

## <a name="verify-php"></a>Überprüfen von PHP

Überprüfen Sie die Version von PHP mit dem folgenden Befehl:

```bash
php -v
```

Wenn Sie weitere Tests durchführen möchten, erstellen Sie schnell eine PHP-Infoseite zum Anzeigen in einem Browser. Die PHP-Infoseite wird mit dem folgenden Befehl erstellt:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Nun können Sie die erstellte PHP-Infoseite überprüfen. Öffnen Sie einen Browser, und rufen Sie `http://yourPublicIPAddress/info.php` auf. Ersetzen Sie die öffentliche IP-Adresse Ihres virtuellen Computers. Die Seite sollte ähnlich wie diese Abbildung aussehen.

![PHP-Infoseite][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen LAMP-Server in Azure bereitgestellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Ubuntu-Computers
> * Öffnen von Port 80 für Webdatenverkehr
> * Installieren von Apache, MySQL und PHP
> * Überprüfen der Installation und Konfiguration
> * Installieren von WordPress auf dem LAMP-Server

Im nächsten Tutorial erfahren Sie, wie Sie Webserver mit TLS/SSL-Zertifikaten schützen.

> [!div class="nextstepaction"]
> [Schützen von Webservern mit TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
