---
title: Problembehandlung mit cloud-init
description: Problembehandlung bei der Bereitstellung einer Azure-VM mit cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.subservice: cloud-init
ms.openlocfilehash: b9a113c4c7f54bfb47d10795325f163cd6f35429
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687927"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>Problembehandlung bei VM-Bereitstellung mit cloud-init

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn Sie generalisierte benutzerdefinierte Images erstellt haben, die mit cloud-init bereitgestellt werden, dann aber feststellen, dass die jeweilige VM nicht ordnungsgemäß erstellt wurde, müssen Sie für Ihre benutzerdefinierten Images eine Problembehandlung vornehmen.

Einige Beispiele für Probleme bei einer Bereitstellung:
- Die VM bleibt 40 Minuten lang beim „Erstellen“ hängen, und die VM-Erstellung wird als fehlerhaft markiert.
- `CustomData` wird nicht verarbeitet
- Der kurzlebige Datenträger kann nicht eingebunden werden.
- Benutzer werden nicht erstellt, oder es gibt Probleme beim Benutzerzugriff.
- Das Netzwerk wird nicht ordnungsgemäß eingerichtet.
- Es treten Auslagerungsdatei- oder Partitionsfehler auf.

Dieser Artikel enthält die Schritte zur Problembehandlung von cloud-init: Ausführlichere Informationen finden Sie unter [Tiefere Einblicke in cloud-init](./cloud-init-deep-dive.md).

## <a name="step-1-test-the-deployment-without-customdata"></a>Schritt 1: Testen der Bereitstellung ohne `customData`

Cloud-init kann das `customData`-Element akzeptieren, das bei der Erstellung der VM übergeben wird. Zuerst sollten Sie sicherstellen, dass dadurch keine Probleme mit Bereitstellungen verursacht werden. Versuchen Sie, die VM bereitzustellen, ohne dabei eine Konfiguration zu übergeben. Wenn Sie feststellen, dass die VM nicht bereitgestellt werden kann, fahren Sie mit den folgenden Schritten fort. Wenn Sie feststellen, dass die übergebene Konfiguration nicht angewendet wird, wechseln Sie zu [Schritt 4](). 

## <a name="step-2-review-image-requirements"></a>Schritt 2: Überprüfen der Imageanforderungen
Die Hauptursache für VM-Bereitstellungfehler besteht darin, dass das Betriebssystemimage nicht die Voraussetzungen für ein Ausführen in Azure erfüllt. Stellen Sie sicher, dass Ihre Images ordnungsgemäß vorbereitet sind, bevor Sie versuchen, diese in Azure bereitzustellen. 


In den folgenden Artikeln sind die Schritte aufgeführt, mit denen die verschiedenen Linux-Distributionen vorbereitet werden, die in Azure unterstützt werden:

- [CentOS-basierte Verteilungen](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES &amp; openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Sonstige: nicht unterstützte Distributionen](create-upload-generic.md)

Für die [unterstützten cloud-init-Images für Azure](./using-cloud-init.md) enthalten die Linux-Distributionen bereits alle erforderlichen Pakete und Konfigurationen, um das jeweilige Images ordnungsgemäß in Azure bereitstellen zu können. Wenn Sie feststellen, dass Ihre VM aus dem von Ihnen zusammengestellten Image nicht erstellt werden kann, sollten Sie es mit einem unterstützten Azure Marketplace-Image versuchen, das bereits für cloud-init konfiguriert ist (mit Ihren optionalen Daten vom Typ `customData`). Falls die korrekte Verarbeitung von `customData` mit einem Azure Marketplace-Image möglich ist, liegt wahrscheinlich ein Problem mit dem von Ihnen zusammengestellten Image vor.

## <a name="step-3-collect--review-vm-logs"></a>Schritt 3: Sammeln und Auswerten der VM-Protokolle

Kann die VM nicht bereitgestellt werden, zeigt Azure für 20 Minuten den Status „wird erstellt“ an, startet die VM neu, und wartet weitere 20 Minuten, bevor es die VM-Bereitstellung schließlich als fehlgeschlagen und dann endgültig mit einem `OSProvisioningTimedOut`-Fehler markiert.

Während die VM ausgeführt wird, benötigen Sie die Protokolle der VM, um erkennen zu können, warum die Bereitstellung fehlgeschlagen ist.  Damit Sie erkennen können, warum die VM-Bereitstellung fehlgeschlagen ist, dürfen Sie die VM nicht anhalten. Lassen Sie die VM weiter ausführen. Sie müssen die fehlerhafte VM im Status „wird ausgeführt“ belassen, um Protokolle zu erfassen. Verwenden Sie eine der folgenden Methoden, um die Protokolle zu erfassen:

- [Serielle Konsole](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)

- [Aktivieren Sie die Startdiagnose](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics), bevor Sie die VM erstellen, und [zeigen Sie die Startdiagnose an](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics), während die VM gestartet wird.

- [Führen Sie AZ VM Repair aus](/troubleshoot/azure/virtual-machines/repair-linux-vm-using-azure-virtual-machine-repair-commands), um den Betriebssystemdatenträger anzufügen und einzubinden, wonach Sie diese Protokolle erfassen können:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
Beginnen Sie die Problembehandlung mit dem Auswerten der cloud-init-Protokolle, um zu ermitteln, wo der Fehler aufgetreten ist. Verwenden Sie dann die anderen Protokolle, um tiefer einzusteigen und zusätzliche Einblicke zu erhalten. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Serial/boot logs

Beginnen Sie in allen Protokollen damit, nach „Failed“, „WARNING“, „WARN“, „err“, „error“, „ERROR“ zu suchen. Es empfiehlt, die Suchvorgänge so zu konfigurieren, dass Groß-/Kleinschreibung nicht beachtet wird. 

> [!TIP]
> Wenn Sie die Problembehandlung für ein benutzerdefiniertes Image ausführen, empfiehlt es sich, während des Images einen Benutzer hinzuzufügen. Wenn es bei der Bereitstellung nicht möglich ist, den Administratorbenutzer festzulegen, können Sie sich weiterhin beim Betriebssystem anmelden.

## <a name="analyzing-the-logs"></a>Analysieren der Protokolle

Nachstehend sind weitere Details dazu aufgeführt, wonach Sie in jedem cloud-init-Protokoll suchen sollten.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

Standardmäßig werden alle cloud-init-Ereignisse, die die Priorität „debug“ oder höher haben, in `/var/log/cloud-init.log` geschrieben. Dadurch werden ausführliche Protokolle für jedes Ereignis bereitstellt, das während der cloud-init-Initialisierung aufgetreten ist. 

Beispiel:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


Nachdem Sie einen Fehler oder eine Warnung gefunden haben, lesen Sie das cloud-init-Protokoll rückwärts, um zu erkennen, was cloud-init versucht hat, bevor der Fehler oder die Warnung aufgetreten ist. In vielen Fällen hat cloud-init vor dem Fehler Betriebssystembefehle oder Bereitstellungsvorgänge ausgeführt, wodurch sich Einblicke gewinnen lassen, warum der Fehler in das Protokoll geschrieben wurde. Im folgenden Beispiel ist gezeigt, dass cloud-init unmittelbar vor dem Auslösen des Fehlers versucht hat, ein Gerät einzubinden.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Wenn Sie Zugriff auf die [serielle Konsole](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode) haben, können Sie versuchen, den Befehl erneut auszuführen, den cloud-init ausführen wollte.

Die Protokollierung für `/var/log/cloud-init.log` kann in „/etc/cloud/cloud.cfg.d/05_logging.cfg“ neu konfiguriert werden. Weitere Informationen zur cloud-init-Protokollierung finden Sie in der [cloud-init-Dokumentation](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

Sie können während der [Phasen von cloud-init](cloud-init-deep-dive.md) Informationen von der Standardausgabe (`stdout`) und der Standardfehlerausgabe (`stderr`) erhalten. Dies umfasst üblicherweise Routingtabelleninformationen, Netzwerkinformationen, SSH-Hostschlüssel-Prüfinformationen, `stdout` und `stderr` für jede Phase von cloud-init samt dem Zeitstempel für jede Phase. Bei Bedarf kann die `stderr`- und `stdout`-Protokollierung in `/etc/cloud/cloud.cfg.d/05_logging.cfg` neu konfiguriert werden.

### <a name="serialboot-logs"></a>Serial/boot logs 

Für cloud-init gibt es mehrere Abhängigkeiten, die in den Voraussetzungen für Images in Azure dokumentiert sind. Dazu gehören Netzwerk, Speicherung, Fähigkeit zum Einbinden einer ISO-Datei sowie Einbinden und Formatieren des temporären Datenträgers. Für jeden dieser Punkte können Fehler ausgelöst werden, die bewirken, dass cloud-init fehlschlägt. Cloud-init schlägt beispielsweise fehl, wenn die VM keine DHCP-Lease abrufen kann.

Wenn Sie immer noch nicht erkennen können, warum die Bereitstellung mit cloud-init fehlgeschlagen ist, müssen Sie wissen, welche cloud-init-Phasen es gibt und wann Module ausgeführt werden. Ausführlichere Informationen hierzu finden Sie unter [Tiefere Einblicke in cloud-init](cloud-init-deep-dive.md).


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>Schritt 4: Ermitteln, warum die Konfiguration nicht angewendet wird
Nicht jeder Fehler in cloud-init führt zu einem schwerwiegenden Bereitstellungsfehler. Wenn Sie z. B. das Modul `runcmd` in einer cloud-init-Konfiguration verwenden, schlägt die VM-Bereitstellung fehl, wenn der Befehl, den das Modul ausführt, einen Exitcode ungleich 0 (null) zurückgibt. Dies liegt daran, dass der Befehl nach der Kernbereitstellungsfunktionalität ausgeführt wird, die in den ersten drei Phasen von cloud-init abgearbeitet wurde. Um zu ermitteln, warum die Konfiguration nicht angewendet wurde, prüfen Sie die Protokolle in Schritt 3 und die cloud-init-Module manuell. Beispiel:

- `runcmd`: Werden die Skripts ohne Fehler ausgeführt? Führen Sie die Konfiguration über Terminal aus, um sich zu vergewissern, dass die Skripts erwartungsgemäß ausgeführt werden.
- Installieren von Paketen: Hat die VM Zugriff auf Paketrepositorys?
- Sie sollten auch die `customData`-Datenkonfiguration überprüfen, die für die VM bereitgestellt wurde. Diese Konfiguration befindet sich in `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt`.


## <a name="next-steps"></a>Nächste Schritte

Wenn Sie immer noch nicht ermitteln können, warum cloud-init die Konfiguration nicht ausgeführt hat, müssen Sie sich genauer ansehen, was in den einzelnen cloud-init-Phasen geschieht und wann die Module ausgeführt werden. Weitere Informationen finden Sie unter [Tiefere Einblicke in cloud-init](./cloud-init-deep-dive.md).