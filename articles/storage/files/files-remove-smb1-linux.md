---
title: Schützen Ihrer Azure-basierten und lokalen Umgebungen durch Entfernen von SMB 1 unter Linux | Microsoft-Dokumentation
description: Azure Files unterstützt SMB 3.x und SMB 2.1, keine unsicheren Legacyversionen von SMB wie SMB 1. Vor dem Herstellen einer Verbindung mit einer Azure-Dateifreigabe sollten Sie ältere Versionen von SMB wie SMB 1 deaktivieren.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8f0e75ebf45839eb75f22a8fb46e76bdebec4688
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124705"
---
# <a name="remove-smb-1-on-linux"></a>Entfernen von SMB 1 unter Linux
Viele Organisationen und Internetdienstanbieter (Internet Service Providers, ISPs) blockieren Port 445, der von SMB für die Kommunikation verwendet wird. Diese Praxis geht auf Sicherheitsempfehlungen im Zusammenhang mit alten und veralteten Versionen des SMB-Protokolls zurück. SMB 3.x ist zwar ein internetsicheres Protokoll, das gilt jedoch nicht für ältere SMB-Versionen (insbesondere SMB 1). SMB 1, auch bekannt als CIFS (Common Internet File System), ist in zahlreichen Linux-Distributionen enthalten. 

SMB 1 ist ein veraltetes, ineffizientes und unsicheres Protokoll. Azure Files unterstützt SMB 1 nicht und ab der Linux-Kernelversion 4.18 ist es möglich, SMB 1 unter Linux zu deaktivieren. Es wird [dringend empfohlen](https://aka.ms/stopusingsmb1), SMB 1 auf Ihren Linux-Clients zu deaktivieren, bevor Sie SMB-Dateifreigaben in der Produktion verwenden.

## <a name="linux-distribution-status"></a>Linux-Distributionsstatus
Ab der Linux-Kernelversion 4.18 stellt das SMB-Kernelmodul (`cifs` genannt, weil es sich um eine Legacyversion handelt) einen neuen Modulparameter (häufig in verschiedenen Dokumentationen als *parm*, hier aber als `disable_legacy_dialects` bezeichnet) zur Verfügung. Obwohl dies mit der Linux-Kernelversion 4.18 eingeführt wurde, haben einige Anbieter diese Änderung auf ältere von ihnen unterstützte Kernelversionen zurückportiert. In der folgenden Tabelle wird die Verfügbarkeit dieses Modulparameters für allgemeine Linux-Distributionen ausführlich erläutert.

| Distribution | SMB 1 deaktivierbar |
|--------------|-------------------|
| Ubuntu 14.04–16.04 | Nein |
| Ubuntu 18.04 | Ja |
| Ubuntu 19.04 und höher | Ja |
| Debian 8–9 | Nein |
| Debian 10 und höher | Ja |
| Fedora 29 und höher | Ja |
| CentOS 7 | Nein | 
| CentOS 8 und höher | Ja |
| Red Hat Enterprise Linux 6.x–7.x | Nein |
| Red Hat Enterprise Linux 8 und höher | Ja |
| openSUSE Leap 15.0 | Nein |
| openSUSE Leap 15.1 und höher | Ja |
| openSUSE Tumbleweed | Ja |
| SUSE Linux Enterprise 11.x–12.x | Nein |
| SUSE Linux Enterprise 15 | Nein |
| SUSE Linux Enterprise 15.1 | Nein |

Mithilfe des folgenden Befehls können Sie überprüfen, ob Ihre Linux-Distribution den Modulparameter `disable_legacy_dialects` unterstützt:

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Es sollte die folgende Meldung ausgegeben werden:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

## <a name="remove-smb-1"></a>Entfernen von SMB 1
Bevor Sie SMB 1 deaktivieren, vergewissern Sie sich, dass das SMB-Modul zum aktuellen Zeitpunkt nicht auf Ihrem System geladen ist. Wenn Sie eine SMB-Freigabe eingebunden haben, wird dies automatisch überprüft. Ansonsten können Sie den folgenden Befehl verwenden, der keine Ausgabe zurückgeben sollte, wenn SMB nicht geladen ist:

```bash
lsmod | grep cifs
```

Wenn Sie das Modul entladen möchten, entfernen Sie zuerst alle SMB-Freigaben (wie oben beschrieben über den Befehl `umount`). Mithilfe des folgenden Befehls können Sie alle eingebunden SMB-Freigaben auf Ihrem System identifizieren:

```bash
mount | grep cifs
```

Nachdem Sie alle SMB-Dateifreigaben entfernt haben, ist es sicher, das Modul zu entladen. Hierfür können Sie den Befehl `modprobe` verwenden:

```bash
sudo modprobe -r cifs
```

Sie können das Modul mithilfe des Befehls `modprobe` manuell laden, wenn SMB 1 entladen ist:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Zum Schluss können Sie überprüfen, ob das SMB-Modul mit dem Parameter geladen wurde, indem Sie sich die geladenen Parameter in `/sys/module/cifs/parameters` ansehen:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Wenn Sie SMB 1 für Ubuntu- und Debian-basierte Distributionen dauerhaft aktivieren möchten, müssen Sie eine neue Datei mit dem Namen `/etc/modprobe.d/local.conf` und der entsprechenden Einstellung erstellen (wenn Sie noch nicht über benutzerdefinierte Optionen für andere Module verfügen). Dafür können Sie den folgenden Befehl verwenden:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Wenn Sie das SMB-Modul laden, können Sie überprüfen, ob der Vorgang erfolgreich war:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files finden Sie unter diesen Links:

- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Verwenden von Azure Files mit Linux](storage-how-to-use-files-linux.md)
- [Problembehandlung](storage-troubleshoot-linux-file-connection-problems.md)