---
title: Erstellen und Verwenden eines SSH-Schlüsselpaars für virtuelle Linux-Computer in Azure
description: Es wird beschrieben, wie Sie ein SSH-Schlüsselpaar (öffentlicher und privater Schlüssel) für virtuelle Linux-Computer in Azure erstellen und verwenden, um die Sicherheit des Authentifizierungsprozesses zu verbessern.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c618ae7f63c1191bf440b5629057660531dd3d7c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122353646"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Kurzanleitung: Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure

Mit einem SSH-Schlüsselpaar (Secure Shell) können Sie virtuelle Computer (VMs) in Azure erstellen, bei deren Authentifizierung SSH-Schlüssel verwendet werden. In diesem Artikel erfahren Sie, wie Sie für virtuelle Linux-Computer schnell eine Datei mit einem SSH-Schlüsselpaar (ein öffentlicher und ein privater Schlüssel) generieren. Sie können diese Schritte mit Azure Cloud Shell, einem macOS- oder einem Linux-Host ausführen. 

Hilfreiche Informationen zur Behandlung von SSH-Problemen finden Sie unter [Behandeln von Problemen, Fehlern oder Ablehnungen im Zusammenhang mit der SSH-Verbindung mit einem virtuellen Azure Linux-Computer](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).

> [!NOTE]
> Mit SSH-Schlüsseln erstellte VMs sind standardmäßig mit deaktivierten Kennwörtern konfiguriert, was die Schwierigkeit für Brute-Force-Angriffe wesentlich erhöht. 

Weitere Hintergrundinformationen und Beispiele finden Sie unter [Ausführliche Schritte zum Erstellen von SSH-Schlüsselpaaren](create-ssh-keys-detailed.md).

Weitere Möglichkeiten zum Generieren und Verwenden von SSH-Schlüsseln auf einem Windows-Computer finden Sie unter [Gewusst wie: Verwenden von SSH-Schlüsseln mit Windows in Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Verwenden Sie den Befehl `ssh-keygen`, um Dateien mit öffentlichen und privaten SSH-Schlüsseln zu generieren. Diese Dateien werden standardmäßig im Verzeichnis „~/.ssh“ erstellt. Sie können einen anderen Speicherort und ein optionales, zusätzliche Kennwort (*Passphrase*) für den Zugriff auf die Datei des privaten Schlüssels angeben. Wenn an dem angegebenen Speicherort bereits ein SSH-Schlüsselpaar mit demselben Namen vorhanden ist, werden diese Dateien überschrieben.

Der folgende Befehl erstellt ein SSH-Schlüsselpaar mittels RSA-Verschlüsselung und einer Bitlänge von 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Bei Verwendung der [Azure CLI](/cli/azure) zum Erstellen Ihres virtuellen Computers mit dem Befehl [az vm create](/cli/azure/vm#az_vm_create) können Sie optional Dateien für öffentliche und private SSH-Schlüssel mit der Option `--generate-ssh-keys` generieren. Die Schlüsseldateien im Verzeichnis „~/.ssh“ gespeichert, sofern nicht mit der Option `--ssh-dest-key-path` anders angegeben. Wenn ein SSH-Schlüsselpaar bereits vorhanden ist und die Option `--generate-ssh-keys` verwendet wird, wird kein neues Schlüsselpaar generiert, sondern stattdessen wird das vorhandene Schlüsselpaar verwendet. Ersetzen Sie im folgenden Befehl *VMname* und *RGname* durch Ihre eigenen Werte:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Angeben eines öffentlichen SSH-Schlüssels beim Bereitstellen eines virtuellen Computers

Um eine Linux-VM zu erstellen, die SSH-Schlüssel zur Authentifizierung verwendet, geben Sie Ihren öffentlichen SSH-Schlüssel beim Erstellen des virtuellen Computers mit Azure-Portal, Azure CLI, Azure Resource Manager-Vorlagen oder anderen Methoden ein:

* [Erstellen Sie eines virtuellen Linux-Computers mit dem Azure-Portal](quick-create-portal.md)
* [Erstellen einer Linux-VM mit der Azure CLI](quick-create-cli.md)
* [Erstellen einer Linux-VM mit einer Azure-Vorlage](create-ssh-secured-vm-from-template.md)

Falls Sie nicht mit dem Format öffentlicher SSH-Schlüssel vertraut sein sollten, können Sie Ihren öffentlichen Schlüssel mit dem folgenden `cat`-Befehl anzeige, wobei Sie hierbei `~/.ssh/id_rsa.pub` durch den Pfad und Dateinamen Ihrer eigenen Datei mit dem öffentlichen Schlüssel ersetzen:

```bash
cat ~/.ssh/id_rsa.pub
```

Ein typischer Wert eines öffentlicher Schlüssels sieht aus wie in diesem Beispiel:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Stellen Sie sicher, dass Sie keine nachstehenden Leerzeichen kopieren, wenn Sie den Inhalt der Datei mit dem öffentlichen Schlüssel für die Verwendung im Azure-Portal oder in einer Resource Manager-Vorlage kopieren und einfügen. Um einen öffentlichen Schlüssel in macOS zu kopieren, können Sie die Datei des öffentlichen Schlüssels per Pipe an `pbcopy` übergeben. Unter Linux können Sie auf ähnliche Weise die Datei des öffentlichen Schlüssels per Pipe an Programme wie `xclip` übergeben.

Der öffentliche Schlüssel, den Sie in Ihrer Linux-VM in Azure ablegen, wird standardmäßig in „~/.ssh/id_rsa.pub“ gespeichert, sofern Sie den Speicherort nicht bei der Erstellung der Schlüsselpaars geändert haben. Um die [Azure CLI 2.0](/cli/azure) zum Erstellen Ihres virtuellen Computers mit einem vorhandenen öffentlichen Schlüssel zu verwenden, geben Sie den Wert und optional den Speicherort dieses öffentlichen Schlüssels mithilfe des Befehls [az vm create](/cli/azure/vm#az_vm_create) mit der Option `--ssh-key-values` an. Ersetzen Sie im folgenden Befehl *myVM*, *myResourceGroup*, *UbuntuLTS*, *azureuser* und *mysshkey.pub* mit ihren eigenen Werten:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Wenn Sie mehrere SSH-Schlüssel mit dem virtuellen Computer verwenden möchten, können Sie diese in eine durch Leerzeichen getrennte Liste eingeben, z. B. `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`.


## <a name="ssh-into-your-vm"></a>Herstellen einer SSH-Verbindung mit Ihrem virtuellen Computer

Stellen Sie mit dem auf Ihrer Azure-VM bereitgestellten öffentlichen Schlüssel und dem privaten Schlüssel auf Ihrem lokalen System mithilfe der IP-Adresse oder dem DNS-Namen Ihres virtuellen Computers eine SSH-Verbindung mit Ihrem virtuellen Computer her. Ersetzen Sie im folgenden Befehl *azureuser* und *myvm.westus.cloudapp.azure.com* durch den Benutzernamen des Administrators und dem vollständig qualifizierten Domänennamen (oder der IP-Adresse):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Wenn Sie zum ersten Mal eine Verbindung mit diesem virtuellen Computer herstellen, werden Sie aufgefordert, den Fingerabdruck des Hosts zu überprüfen. Es ist zwar verlockend, den angebotenen Fingerabdruck einfach zu akzeptieren, aber mit dieser Vorgehensweise setzen Sie sich einem möglichen „Person-in-the-Middle“-Angriff aus. Sie sollten den Fingerabdruck des Hosts immer überprüfen. Dies ist nur erforderlich, wenn Sie zum ersten Mal von einem Client aus eine Verbindung herstellen. Verwenden Sie das Feature für die Skriptausführung zum Ausführen des Befehls `ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub | awk '{print $2}'`, um den Hostfingerabdruck über das Portal abzurufen.

:::image type="content" source="media/ssh-from-windows/run-command-validate-host-fingerprint.png" alt-text="Screenshot: Verwenden der Skriptausführung zum Überprüfen des Hostfingerabdrucks":::

Verwenden Sie [`az vm run-command invoke`](/cli/azure/vm/run-command), um den Befehl über die CLI auszuführen.

Falls Sie beim Erstellen des Schlüsselpaars eine Passphrase angegeben haben, müssen Sie diese Passphrase eingeben, wenn Sie während des Anmeldeprozesses dazu aufgefordert werden. Die VM wird Ihrer Datei „~/.ssh/known_hosts“ hinzugefügt, und Sie werden erst wieder zum Herstellen der Verbindung aufgefordert, wenn sich der öffentliche Schlüssel auf Ihrer Azure-VM ändert oder der Servername aus „~/.ssh/known_hosts“ entfernt wird.

Wenn der virtuelle Computer die JIT-Zugriffsrichtlinie (Just-In-Time) verwendet, müssen Sie den Zugriff anfordern, bevor Sie eine Verbindung mit dem virtuellen Computer herstellen können. Weitere Informationen zur Just-In-Time-Richtlinie finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe der Just-In-Time-Richtlinie](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Arbeit mit SSH-Schlüsselpaaren finden Sie unter [Ausführliche Schritte zum Erstellen und Verwalten von SSH-Schlüsselpaaren](create-ssh-keys-detailed.md).

* Wenn beim Herstellen von SSH-Verbindungen mit einer Azure-VM Probleme auftreten, finden Sie weitere Informationen unter [Behandeln von Problemen mit SSH-Verbindungen mit einem virtuellen Azure Linux-Computer](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).
