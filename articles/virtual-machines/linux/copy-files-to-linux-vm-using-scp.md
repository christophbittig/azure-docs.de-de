---
title: Verwenden von SCP zum Verschieben von Dateien auf einen und von einem virtuellen Computer
description: Es wird beschrieben, wie Sie Dateien mit SCP und einem SSH-Schlüsselpaar auf sichere Weise auf eine bzw. von einer Linux-VM in Azure verschieben.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5e7c342ae66594e502870030f8e8561ba861b05b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692102"
---
# <a name="use-scp-to-move-files-to-and-from-a-linux-vm"></a>Verwenden von SCP zum Verschieben von Dateien auf einen und von einem virtuellen Linux-Computer 

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird gezeigt, wie Sie Dateien per Secure Copy (SCP) von Ihrer Arbeitsstation auf eine Azure Linux-VM oder von einer Azure Linux-VM auf die Arbeitsstation verschieben. Das schnelle und sichere Verschieben von Dateien zwischen Ihrer Arbeitsstation und einer Linux-VM ist für die Verwaltung Ihrer Azure-Infrastruktur wichtig. 

Für diesen Artikel muss anhand von [Dateien mit öffentlichen und privaten SSH-Schlüsseln](mac-create-ssh-keys.md) eine Linux-VM in Azure bereitgestellt werden. Sie benötigen auch einen SCP-Client für Ihren lokalen Computer. Er basiert auf SSH und ist in der standardmäßigen Bash-Shell der meisten Linux- und Macintosh-Computer sowie in der PowerShell enthalten.


## <a name="quick-commands"></a>Schnellbefehle

Kopieren einer Datei auf die Linux-VM

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopieren einer Datei von der Linux-VM

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Ausführliche exemplarische Vorgehensweise

Als Beispiel verschieben wir eine Azure-Konfigurationsdatei auf eine Linux-VM und einem Protokolldateiverzeichnis von der Linux-VM – jeweils mithilfe von SCP und SSH-Schlüsseln.   

## <a name="ssh-key-pair-authentication"></a>Authentifizierung per SSH-Schlüsselpaar

SCP nutzt SSH für die Transportschicht. SSH übernimmt die Authentifizierung auf dem Zielhost, und verschiebt die Datei über einen verschlüsselten Tunnel, der für SSH standardmäßig bereitgestellt wird. Für die SSH-Authentifizierung können Benutzernamen und Kennwörter verwendet werden. Allerdings wird als bewährte Sicherheitsmethode die Authentifizierung mit einem öffentlichen und einem privaten SSH-Schlüssel empfohlen. Nachdem die Verbindung von SSH authentifiziert wurde, beginnt SCP mit dem Kopieren der Datei. Indem eine richtig konfigurierte Datei `~/.ssh/config` und öffentliche und private SSH-Schlüssel verwendet werden, kann die SCP-Verbindung nur mit einem Servernamen (oder einer IP-Adresse) hergestellt werden. Falls Sie nur über einen SSH-Schlüssel verfügen, sucht SCP im Verzeichnis `~/.ssh/` danach und verwendet ihn standardmäßig zum Anmelden bei der VM.

Weitere Informationen zum Konfigurieren der Datei `~/.ssh/config` und zu öffentlichen und privaten SSH-Schlüsseln finden Sie unter [Erstellen von SSH-Schlüsseln](mac-create-ssh-keys.md).

## <a name="scp-a-file-to-a-linux-vm"></a>Verschieben einer Datei per SCP auf eine Linux-VM

Für das erste Beispiel kopieren wir eine Azure-Konfigurationsdatei auf eine Linux-VM, die zum Bereitstellen von Automation verwendet wird. Da diese Datei Azure-API-Anmeldeinformationen sowie Geheimnisse enthält, spielt die Sicherheit eine große Rolle. Der verschlüsselte, von SSH bereitgestellte Tunnel schützt die Inhalte der Datei.

Mit dem folgenden Befehl wird die lokale Datei *.azure/config* auf eine Azure-VM mit dem FQDN *myserver.eastus.cloudapp.azure.com* kopiert. Wenn Sie keinen [FQDN festgelegt](../create-fqdn.md) haben, können Sie auch die IP-Adresse der VM verwenden. Der Benutzername des Administrators auf der Azure-VM lautet *azureuser*. Die Datei wird im Verzeichnis */home/azureuser/* gespeichert. Fügen Sie Ihre eigenen Werte in diesen Befehl ein.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Verschieben eines Verzeichnisses von einer Linux-VM per SCP

In diesem Beispiel kopieren wir ein Verzeichnis mit Protokolldateien von der Linux-VM auf Ihre Arbeitsstation. Eine Protokolldatei kann möglicherweise sensible oder vertrauliche Daten enthalten. Mit SCP wird jedoch sichergestellt, dass die Inhalte der Protokolldateien verschlüsselt sind. Die Verwendung von SCP zum Übertragen der Dateien ist der einfachste und gleichzeitig ein sicherer Weg zur Übertragung des Protokollverzeichnisses und der Dateien auf Ihre Arbeitsstation.

Mit dem folgenden Befehl werden Dateien im Verzeichnis */home/azureuser/logs/* auf der Azure-VM in das lokale Verzeichnis „/tmp“ kopiert:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Mit dem Flag `-r` wird SCP angewiesen, die Dateien und Verzeichnisse rekursiv von dem Punkt des Verzeichnisses zu kopieren, der im Befehl angegeben ist.  Beachten Sie auch, dass die Befehlszeilensyntax dem Kopierbefehl `cp` ähnelt.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Benutzern, SSH und Überprüfen oder Reparieren von Datenträgern auf Azure-Linux-VMs mit der VMAccess-Erweiterung](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
