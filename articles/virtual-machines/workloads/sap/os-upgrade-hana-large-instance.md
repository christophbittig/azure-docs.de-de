---
title: Durchführen eines Betriebssystemupgrades für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie ein Betriebssystemupgrade für SAP HANA in Azure (große Instanzen) durchführen.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e980417c2c48455e43143fb2f870a63d6395f94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546204"
---
# <a name="operating-system-upgrade"></a>Betriebssystemupgrade
In diesem Artikel werden die Details von Betriebssystemupgrades für HANA (große Instanzen) – auch als BareMetal-Infrastruktur bezeichnet – beschrieben.

>[!NOTE]
>Für die Durchführung von Upgrades für Ihr Betriebssystem sind Sie verantwortlich. Der Microsoft Operations-Support kann Sie in wichtigen Bereichen des Upgradevorgangs unterstützen. Es ist aber ratsam, dass Sie sich beim Planen eines Upgrades auch an Ihren Betriebssystemanbieter wenden.

Das Microsoft Operations-Team installiert das Betriebssystem während der Bereitstellung der HANA-Instanz (große Instanzen) (im weiteren Verlauf auch als „HLI“ abgekürzt“).
Sie müssen das Betriebssystem verwalten. Beispielsweise müssen Sie für die HLI das Patchen, Optimieren, Aktualisieren usw. durchführen. Bevor Sie größere Änderungen am Betriebssystem vornehmen, z. B. ein Upgrade von SP1 auf SP2, sollten Sie sich zunächst an das Microsoft Operations-Team wenden, indem Sie ein Supportticket erstellen. Sie werden sich mit Ihnen beraten. Wir empfehlen Ihnen, dieses Ticket mindestens eine Woche vor dem Upgrade zu erstellen. 

Geben Sie folgende Informationen in Ihrem Ticket an:

* Ihre HLI-Abonnement-ID
* Ihren Servernamen
* Patchebene, die Sie anwenden möchten
* Datum, an dem Sie diese Änderung vornehmen möchten 

Die Supportmatrix der anderen SAP HANA-Versionen mit den verschiedenen Linux-Versionen finden Sie im [SAP-Hinweis 2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Bekannte Probleme

Für das Upgrade können einige bekannte Probleme auftreten:
- Bei SKUs von SKU-Typ II wird Software Foundation Software (SFS) während der Durchführung eines Betriebssystemupgrades entfernt. Nach Abschluss des Betriebssystemupgrades müssen Sie die kompatible SFS-Version neu installieren.
- Für Ethernet-Kartentreiber (ENIC und FNIC) wird ein Rollback auf eine ältere Version ausgeführt. Nach dem Upgrade müssen Sie die kompatible Version der Treiber neu installieren.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Empfohlene Konfiguration für SAP HANA (große Instanzen, Typ I)

Die Betriebssystemkonfiguration kann im Laufe der Zeit von den empfohlenen Einstellungen abweichen. Diese Abweichung kann aufgrund von Patches, Systemupgrades und anderen Änderungen auftreten, die Sie ggf. vornehmen. Von Microsoft werden die Updates ermittelt, mit denen sichergestellt werden kann, dass HANA (große Instanzen) optimal konfiguriert ist und die beste Leistung und Resilienz erzielt wird. Die folgende Anleitung enthält Empfehlungen, die sich auf die Netzwerkleistung, die Systemstabilität und die optimale HANA-Leistung beziehen.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatible eNIC/fNIC-Treiberversionen
  Stellen Sie zur Erzielung einer hohen Netzwerkleistung und Systemstabilität sicher, dass die richtige betriebssystemspezifische Version der eNIC- und fNIC-Treiber installiert ist. Dies ist in der folgenden Kompatibilitätstabelle angegeben. Server werden mit kompatiblen Versionen an Kunden ausgeliefert. Es kann aber sein, dass Treiber beim Patchen von Betriebssystemen/Kerneln auf die Standardversionen zurückgesetzt werden. Stellen Sie sicher, dass nach Patchvorgängen am Betriebssystem/Kernel die passende Treiberversion ausgeführt wird.
       
      
  |  Betriebssystemhersteller    |  Betriebssystem-Paketversion     |  Firmware Version  |  eNIC-Treiber |  fNIC-Treiber | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP4            |   4.1.1b           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP5            |   3.2.3i           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Befehle für das Treiberupgrade und zum Bereinigen alter RPM-Pakete

#### <a name="command-to-check-existing-installed-drivers"></a>Befehl zum Überprüfen vorhandener installierter Treiber
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Löschen vorhandener eNIC/fNIC rpm
```
rpm -e <old-rpm-package>
```
#### <a name="install-recommended-enicfnic-driver-packages"></a>Installieren der empfohlenen eNIC-/fNIC-Treiberpakete
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-installation"></a>Befehle zum Bestätigen der Installation
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Schritte zur Installation von eNIC/fNIC-Treibern während des Betriebssystemupgrades

* Upgrade der Betriebssystemversion
* Entfernen alter RPM-Pakete
* Installieren kompatibler eNIC/fNIC-Treiber entsprechend der installierten Betriebssystemversion
* Neustarten des Systems
* Überprüfen der eNIC/fNIC-Version nach dem Neustart


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB-Updatefehler
SAP in Azure HANA (große Instanzen, Typ I) kann sich nach dem Upgrade in einem nicht startfähigen Zustand befinden. Dieses Problem kann mit dem folgenden Verfahren behoben werden.

#### <a name="execution-steps"></a>Ausführungsschritte

-   Führen Sie den `multipath -ll`-Befehl aus.
-   Rufen Sie die LUN-ID (Logische Gerätenummer) ab, oder verwenden Sie diesen Befehl: `fdisk -l | grep mapper`
-   Aktualisieren Sie die Datei `/etc/default/grub_installdevice` mit der folgenden Zeile: `/dev/mapper/<LUN ID>`. Beispiel: /dev/mapper/3600a09803830372f483f495242534a56

>[!NOTE]
>Die LUN-ID variiert für die einzelnen Server.


### <a name="disable-error-detection-and-correction"></a>Deaktivieren der Fehlererkennung und -korrektur 
   Mit Modulen für die Fehlererkennung und -korrektur (Error Detection and Correction, EDAC) können Arbeitsspeicherfehler erkannt und korrigiert werden. Auch die zugrunde liegende Hardware mit HLI-Typ I kann Arbeitsspeicherfehler aber bereits erkennen und korrigieren. Die Aktivierung des gleichen Features auf Hardware- und Betriebssystemebene kann zu Konflikten und zum ungeplanten Herunterfahren des Servers führen. Wir empfehlen Ihnen, die EDAC-Module für das Betriebssystem zu deaktivieren.

#### <a name="execution-steps"></a>Ausführungsschritte

- Überprüfen Sie, ob die EDAC-Module aktiviert sind. Wenn vom folgenden Befehl eine Ausgabe zurückgegeben wird, sind die Module aktiviert.

```
lsmod | grep -i edac 
```
- Deaktivieren Sie die Module, indem Sie der Datei `/etc/modprobe.d/blacklist.conf` die folgenden Zeilen anfügen:
```
blacklist sb_edac
blacklist edac_core
```
Ein Neustart ist erforderlich, damit die Änderungen vorgenommen werden. Führen Sie nach dem Neustart den Befehl `lsmod` erneut aus, um sich zu vergewissern, dass die Module nicht mehr aktiviert sind.

### <a name="kernel-parameters"></a>Kernelparameter
Vergewissern Sie sich, dass die richtigen Einstellungen für `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` und `intel_idle.max_cstate` angewendet werden.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>Ausführungsschritte

- Fügen Sie diese Parameter der Zeile `GRB_CMDLINE_LINUX` in der Datei `/etc/default/grub` hinzu:

```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
- Erstellen Sie eine neue Grub-Datei.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
- Starten Sie Ihr System neu.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Einrichten eines SMT-Servers für SUSE Linux.

> [!div class="nextstepaction"]
> [Einrichten eines SMT-Servers für SUSE Linux](hana-setup-smt.md)