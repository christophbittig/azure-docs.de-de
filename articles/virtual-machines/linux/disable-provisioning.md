---
title: Deaktivieren oder Entfernen des Bereitstellung-Agents
description: Erfahren Sie, wie Sie den Bereitstellungs-Agent auf Linux-VMs und in Images deaktivieren oder entfernen.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 938e5b88c635213b5614efe4c9dc646a25ee8d06
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471495"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Deaktivieren oder Entfernen des Linux-Agents aus VMs und Images

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Bevor Sie den Linux-Agent entfernen, sollten Sie wissen, welche Aktionen die VM nicht mehr ausführen kann, nachdem der Linux-Agent entfernt wurde.

[Erweiterungen](../extensions/overview.md) für Azure-VMs sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf Azure-VMs nach der Bereitstellung ermöglichen. Erweiterungen werden über die Azure-Steuerungsebene installiert und verwaltet. Es ist Aufgabe des [Azure Linux-Agents](../extensions/agent-linux.md), die Plattformerweiterungsbefehle zu verarbeiten und den ordnungsgemäßen Zustand der Erweiterungen auf der jeweiligen VM sicherzustellen.

Auf der Azure-Plattform werden Erweiterungen für viele Zwecke gehostet, z.B. VM-Konfiguration, Überwachung, Sicherheit und Hilfsprogramme. Es gibt eine große Auswahl an Erst- und Drittanbietererweiterungen. Zu den Beispielen für Hauptszenarien, für die Erweiterungen verwendet werden, gehören:
* Unterstützung von Erstanbieter-Azure-Diensten wie Azure Backup, Überwachung, Datenträgerverschlüsselung, Sicherheit, Standortreplikation und weitere
* SSH / Kennwortzurücksetzungen
* VM-Konfiguration: Ausführen von benutzerdefinierten Skripts, Installieren von Chef, Puppet-Agents usw.
* Produkte von Drittanbietern, z. B. AV-Produkte, Tools für VM-Sicherheitsrisiken, VM- und App-Überwachungstools
* Erweiterungen können mit einer neuen VM-Bereitstellung gebündelt werden. Beispielsweise können sie Teil einer größeren Bereitstellung sein, die Anwendungen können bei der VM-Bereitstellung konfiguriert werden, oder sie können nach der Bereitstellung für alle unterstützten Systeme ausgeführt werden, die für Erweiterungen geeignet sind.

## <a name="disabling-extension-processing"></a>Deaktivieren von Erweiterungsverarbeitung

Je nach Ihren Anforderungen gibt es mehrere Möglichkeiten, die Erweiterungsverarbeitung zu deaktivieren. Aber bevor Sie fortfahren, **MÜSSEN** Sie alle Erweiterungen entfernen, die auf der VM bereitgestellt werden. Dazu können Sie z. B. die Azure CLI verwenden, um [aufzulisten](/cli/azure/vm/extension#az_vm_extension_list) und zu [löschen](/cli/azure/vm/extension#az_vm_extension_delete):

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Wenn Sie dies nicht tun, versucht die Plattform, die Erweiterungskonfiguration zu senden, und nach 40 Minuten ergibt sich ein Timeout für die Plattform.

### <a name="disable-at-the-control-plane"></a>Deaktivieren auf der Steuerungsebene
Wenn Sie nicht sicher sind, ob Sie in Zukunft Erweiterungen benötigen, können Sie den installierten Linux-Agent auf der VM belassen, aber die Funktionalität für die Erweiterungsverarbeitung auf der Plattform deaktivieren. Diese Option ist in der `Microsoft.Compute`-API-Version `2018-06-01` oder höher verfügbar und ist unabhängig von der installierten Linux-Agent-Version.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
Mit dem obigen Befehl können Sie diese Erweiterungsverarbeitung problemlos wieder auf der Plattform aktivieren, wobei Sie die Option aber auf „true“ festlegen.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Entfernen des Linux-Agents aus einer aktiven VM

Vergewissern Sie sich, dass Sie alle vorhandenen Erweiterungen gemäß der obigen Beschreibung **entfernt** haben.

### <a name="step-1-remove-the-azure-linux-agent"></a>Schritt 1: Entfernen des Azure Linux-Agents

Wenn Sie nur den Linux-Agent und nicht die zugehörigen Konfigurationsartefakte entfernen, können Sie den Agent zu einem späteren Zeitpunkt neu installieren. Führen Sie als root einen der folgenden Befehle aus, um den Azure Linux-Agent zu entfernen:

#### <a name="for-ubuntu-1804"></a>Für Ubuntu >=18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Für RedHat >= 7.7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Für SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Schritt 2: (Optional) Entfernen der Artefakte des Azure Linux-Agents
> [!IMPORTANT] 
>
> Sie können alle zugehörigen Artefakte des Linux-Agents entfernen. Dies bedeutet jedoch, dass Sie den Agent nicht zu einem späteren Zeitpunkt erneut installieren können. Daher wird dringend empfohlen, den Linux-Agent zuerst zu deaktivieren, indem Sie den Linux-Agent mit den oben aufgeführten Befehlen nur entfernen. 

Wenn Sie wissen, dass Sie den Linux-Agent nie erneut installieren müssen, können Sie die folgenden Befehle ausführen:

#### <a name="for-ubuntu-1804"></a>Für Ubuntu >=18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Für RedHat >= 7.7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Für SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Vorbereiten eines Images ohne den Linux-Agent
Wenn Sie ein Image haben, das bereits cloud-init enthält, und Sie den Linux-Agent entfernen, aber trotzdem mit cloud-init bereitstellen möchten, führen Sie die Schritte in Schritt 2 (und optional Schritt 3) als root aus, um den Azure Linux-Agent zu entfernen. Mit dem folgenden Befehl werden dann die cloud-init-Konfiguration und die zwischengespeicherten Daten entfernt und wird die VM zum Erstellen eines benutzerdefinierten Images vorbereitet.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Aufheben der Bereitstellung und Erstellen eines Images
Der Linux-Agent kann mit dem Schritt „waagent -deprovision+user“ einige der vorhandenen Imagemetadaten bereinigen. Nachdem diese entfernt wurden, müssen Sie aber Aktionen wie die nachfolgenden ausführen und alle weiteren sensiblen Daten entfernen.

- Entfernen aller vorhandenen SSH-Hostschlüssel

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Löschen des Administratorkontos

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Löschen des root-Kennworts

   ```bash
   passwd -d root
   ```

Nachdem Sie die zuvor aufgeführten Punkte abgeschlossen haben, können Sie das benutzerdefinierte Image mit der Azure CLI erstellen.


**Erstellen eines regulären verwalteten Images**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Erstellen einer Imageversion in einer Azure Compute Gallery-Instanz**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Erstellen einer VM aus einem Image, das keinen Linux-Agent enthält
Wenn Sie die VM aus einem Image erstellen, das keinen Linux-Agent hat, müssen Sie sicherstellen, dass die VM-Bereitstellungskonfiguration angibt, dass Erweiterungen auf dieser VM nicht unterstützt werden.

> [!NOTE] 
> 
> Wenn Sie dies nicht tun, versucht die Plattform, die Erweiterungskonfiguration zu senden, und nach 40 Minuten ergibt sich ein Timeout für die Plattform.

Um die VM mit deaktivierten Erweiterungen bereitzustellen, können Sie die Azure CLI mit [--enable-agent](/cli/azure/vm#az_vm_create) verwenden.

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Alternativ können Sie dies mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) vornehmen, indem Sie `"provisionVMAgent": false,` festlegen.

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Azure Linux-VM-Bereitstellung](provisioning.md).
