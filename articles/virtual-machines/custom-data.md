---
title: Benutzerdefinierte Daten und virtuelle Azure-Computer
description: Details zur Verwendung von benutzerdefinierten Daten und cloud-init auf virtuellen Azure-Computern
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 729c988bcf64cab30cf7644c85a5749ff9c592b7
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137015"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Benutzerdefinierte Daten und cloud-init auf virtuellen Azure-Computern

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible Skalierungsgruppen

Möglicherweise müssen Sie zum Zeitpunkt der Bereitstellung ein Skript oder andere Metadaten in einen virtuellen Microsoft Azure-Computer einfügen.  In anderen Clouds wird dieses Konzept häufig als Benutzerdaten bezeichnet.  In Microsoft Azure verfügen wir über ein ähnliches Feature, das als benutzerdefinierte Daten bezeichnet wird. 

Benutzerdefinierte Daten werden nur beim ersten Start bzw. beim ersten Setup für die VM verfügbar gemacht. Dies wird als Bereitstellung bezeichnet. Die Bereitstellung ist der Vorgang, bei dem VM-Erstellungsparameter (z. B. Hostname, Benutzername, Kennwort, Zertifikate, benutzerdefinierte Daten, Schlüssel usw.) für den virtuellen Computer verfügbar gemacht werden. Dabei werden sie von einem Bereitstellungs-Agent verarbeitet, z. B. dem [Linux-Agent](./extensions/agent-linux.md) und [cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Übergeben von benutzerdefinierten Daten an die VM
Wenn Sie benutzerdefinierte Daten verwenden möchten, müssen Sie die Inhalte zunächst mit Base64 codieren, bevor Sie sie an die API übergeben. Dies gilt jedoch nicht, wenn Sie ein CLI-Tool verwenden, das diese Konvertierung durchführt, z. B. die Azure-Befehlszeilenschnittstelle (AZ CLI). Die Dateigröße darf 64 KB nicht überschreiten.

In der Befehlszeilenschnittstelle können Sie Ihre benutzerdefinierten Daten als Datei übergeben. Sie werden dann in Base64 konvertiert.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

In Azure Resource Manager (ARM) gibt es eine [Base64-Funktion](../azure-resource-manager/templates/template-functions-string.md#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Verarbeiten von benutzerdefinierten Daten
Die auf den virtuellen Computern installierten Bereitstellungs-Agents verarbeiten die Verbindung mit der Plattform und das Platzieren im Dateisystem. 

### <a name="windows"></a>Windows
Benutzerdefinierte Daten werden in *%SYSTEMDRIVE%\AzureData\CustomData.bin* als Binärdatei abgelegt, jedoch nicht verarbeitet. Wenn Sie diese Datei verarbeiten möchten, müssen Sie ein benutzerdefiniertes Image erstellen und Code schreiben, der die Datei „CustomData.bin“ verarbeitet.

### <a name="linux"></a>Linux  
Unter Linux-Betriebssystemen werden benutzerdefinierte Daten über die Datei „ovf-env.xml“ an den virtuellen Computer weitergegeben, die während der Bereitstellung in das Verzeichnis */var/lib/waagent* kopiert wird.  Neuere Versionen des Microsoft Azure-Linux-Agents kopieren die Base64-codierten Daten zur Vereinfachung ebenfalls in */var/lib/waagent/CustomData*.

Azure unterstützt derzeit zwei Bereitstellungs-Agents:
* Linux-Agent: Standardmäßig verarbeitet der Agent keine benutzerdefinierten Daten. Sie müssen ein benutzerdefiniertes Image erstellen, in dem diese Verarbeitung aktiviert ist. Die relevanten Einstellungen gemäß der [Dokumentation](https://github.com/Azure/WALinuxAgent#configuration) sind:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Wenn Sie benutzerdefinierte Daten aktivieren und ein Skript ausführen, wird die Meldung der VM, dass sie bereit ist oder die Bereitstellung erfolgreich war, bis zum Abschluss des Skripts verzögert. Wenn das Skript die zulässige VM-Bereitstellungdauer von 40 Minuten überschreitet, tritt beim Erstellen des virtuellen Computers ein Fehler auf. Beachten Sie: Wenn beim Ausführen des Skripts ein Fehler auftritt, wird dies nicht als schwerwiegender Bereitstellungsfehler angesehen. Sie müssen einen Benachrichtigungspfad erstellen, um über den Abschlussstatus des Skripts informiert zu werden.

Weitere Informationen zum Troubleshooting für die Ausführung von benutzerdefinierten Daten finden Sie unter */var/log/waagent.log*.

* cloud-init: Standard für die Verarbeitung von benutzerdefinierten Daten. cloud-init kann benutzerdefinierte Daten in [mehreren Formaten](https://cloudinit.readthedocs.io/en/latest/topics/format.html) verarbeiten, z. B. Konfiguration für cloud-init, Skripts usw. Die Verarbeitung von benutzerdefinierten Daten mit cloud-init erfolgt ähnlich wie beim Linux-Agent. Wenn beim Ausführen der Konfigurationsverarbeitung oder von Skripts Fehler auftreten, wird dies nicht als schwerwiegender Bereitstellungsfehler angesehen. Sie müssen einen Benachrichtigungspfad erstellen, um über den Abschlussstatus des Skripts informiert zu werden. Anders als beim Linux-Agent wartet cloud-init jedoch nicht auf den Abschluss der Konfiguration benutzerdefinierter Daten, bevor an die Plattform gemeldet wird, dass die VM bereit ist. Weitere Informationen zu cloud-init unter Azure finden Sie in der [Dokumentation](./linux/using-cloud-init.md).


Informationen zum Troubleshooting bei der Ausführung benutzerdefinierter Daten finden Sie in der [Dokumentation](./linux/using-cloud-init.md#troubleshooting-cloud-init) im Thema zum Troubleshooting.


## <a name="faq"></a>Häufig gestellte Fragen
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Kann ich benutzerdefinierte Daten nach dem Erstellen der VM aktualisieren?
Für einzelne VMs können benutzerdefinierte Daten im VM-Modell nicht aktualisiert werden, aber für VMSS können Sie benutzerdefinierte VMSS-Daten über [REST API](/rest/api/compute/virtualmachinescalesets/update), [Az CLI](/cli/azure/vmss?view=azure-cli-latest#az_vmss_update) oder [Az PowerShell](/powershell/module/az.compute/update-azvmss?view=azps-6.6.0) aktualisieren. Beim Aktualisieren von benutzerdefinierten Daten im VMSS-Modell geschieht Folgendes:
* Vorhandene Instanzen in der VMSS erhalten die aktualisierten benutzerdefinierten Daten erst, wenn Sie das Reimaging durchführen.
* Vorhandene Instanzen in der VMSS, die aktualisiert werden, erhalten nicht die aktualisierten benutzerdefinierten Daten.
* Neue Instanzen erhalten die neuen benutzerdefinierten Daten.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Kann ich vertrauliche Werte in benutzerdefinierte Daten einschließen?
Es wird empfohlen, vertrauliche Daten **nicht** in benutzerdefinierten Daten zu speichern. Weitere Informationen finden Sie unter [Bewährte Methoden für die Sicherheit und Verschlüsselung in Azure](../security/fundamentals/data-encryption-best-practices.md).


### <a name="is-custom-data-made-available-in-imds"></a>Werden benutzerdefinierte Daten in IMDS zur Verfügung gestellt?
Benutzerdefinierte Daten sind in IMDS nicht verfügbar. Wir empfehlen stattdessen die Verwendung von Benutzerdaten über IMDS. Weitere Informationen finden Sie unter [Benutzerdaten über Azure Instance Metadata Service](./linux/instance-metadata-service.md?tabs=linux#get-user-data).
