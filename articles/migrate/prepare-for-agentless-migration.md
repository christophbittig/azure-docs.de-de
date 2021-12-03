---
title: Vorbereiten von Computern für die Migration ohne Agents mit Azure Migrate
description: Es wird beschrieben, wie Sie lokale Computer für die Migration ohne Agents mit Azure Migrate vorbereiten.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 1765b671f09dc90506636cc643337e55957c95cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084632"
---
# <a name="prepare-for-vmware-agentless-migration"></a>Vorbereiten der VMware-Migration ohne Agent

Dieser Artikel bietet eine Übersicht über die Änderungen, die bei der [Migration von VMware-VMs zu Azure über die Migrationsmethode ohne Agent](./tutorial-migrate-vmware.md) mithilfe des Tools Azure Migrate: Servermigration vorgenommen werden.

Bevor Sie Ihre lokale VM zu Azure migrieren, müssen Sie möglicherweise einige Änderungen vornehmen, damit die VM für Azure bereit ist. Diese Änderungen sind wichtig, um sicherzustellen, dass die migrierte VM erfolgreich in Azure gestartet werden und eine Verbindung mit der Azure-VM hergestellt werden kann.
Azure Migrate verarbeitet diese Konfigurationsänderungen automatisch für die unten genannten Betriebssystemversionen für Linux und Windows. Dieser Vorgang wird als *Hydration* bezeichnet.

**Unterstützte Betriebssystemversionen für Hydration**

- Windows Server 2008 oder höher
- Red Hat Enterprise Linux 8.x, 7.9, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x
- CentOS 8.x, 7.7, 7.6, 7.5, 7.4, 6.x
- SUSE Linux Enterprise Server 15 SP0, 15 SP1, 12
- Ubuntu 20.04, 19.04, 19.10, 18.04LTS, 16.04LTS, 14.04LTS
- Ubuntu 18.04LTS, 16.04LTS
- Debian 9, 8, 7
- Oracle Linux 6, 7.7, 7.7-CI

Sie können diesen Artikel auch verwenden, um die VMs für die Migration zu Azure für nicht oben aufgeführte Betriebssystemversionen manuell vorzubereiten. Diese Änderungen umfassen auf hoher Ebene Folgendes:

- Überprüfen des Vorhandenseins der erforderlichen Treiber
- Aktivieren der seriellen Konsole
- Konfigurieren der Netzwerkeinstellungen
- Installieren des VM-Gast-Agents

## <a name="hydration-process"></a>Hydrationsvorgang

Sie müssen vor der Migration einige Änderungen an der Konfiguration der VMs vornehmen, um sicherzustellen, dass die migrierten VMs in Azure ordnungsgemäß funktionieren. Azure Migrate behandelt diese Konfigurationsänderungen über den *Hydrationsprozess*. Der Hydrationsprozess wird nur für die oben angegebenen Versionen der von Azure unterstützten Betriebssysteme ausgeführt. Vor der Migration müssen Sie möglicherweise erforderliche Änderungen manuell für andere Betriebssystemversionen vornehmen, die oben nicht aufgeführt sind. Wenn die VM ohne die erforderlichen Änderungen migriert wird, wird sie möglicherweise nicht gestartet, oder Sie verfügen nicht über eine Verbindung mit der migrierten VM. Die folgende Abbildung zeigt, dass Azure Migrate den Hydrationsprozess ausführt.

 [![Hydrationsschritte](./media/concepts-prepare-vmware-agentless-migration/hydration-process-inline.png)](./media/concepts-prepare-vmware-agentless-migration/hydration-process-expanded.png#lightbox)

Wenn ein Benutzer *Testmigration* oder *Migrieren* auslöst, führt Azure Migrate den Hydrationsprozess durch, um die lokale VM für die Migration zu Azure vorzubereiten.
Zum Einrichten des Hydrationsprozesses erstellt Azure Migrate einen temporäre Azure-VM und fügt die Datenträger der Quell-VM an, um Änderungen vorzunehmen, damit die Quell-VM für Azure bereit ist. Bei der temporären Azure-VM handelt es sich um eine Zwischen-VM, die während des Migrationsprozesses erstellt wird, bevor die endgültige migrierte VM erstellt wird. Die temporäre VM wird mit einem ähnlichen Betriebssystemtyp (Windows/Linux) mit einem der Marketplace-Betriebssystemimages erstellt. Wenn die lokale VM Windows ausführt, wird der Betriebssystemdatenträger der lokalen VM zur Durchführung von Änderungen als Datenträger an die temporäre VM angefügt. Wenn es sich um einen Linux-Server handelt, werden alle an die lokale VM angefügten Datenträger als Datenträger an die temporäre Azure-VM angefügt.

Azure Migrate erstellen die Netzwerkschnittstelle, ein neues virtuelles Netzwerk, ein Subnetz und eine Netzwerksicherheitsgruppe (NSG) zum Hosten der temporären VM. Diese Ressourcen werden im Abonnement des Kunden erstellt. Wenn es in Konflikt stehende Richtlinien gibt, die die Erstellung der Netzwerkartefakte verhindern, versucht Azure Migrate, die temporäre Azure-VM im virtuellen Netzwerk und Subnetz zu erstellen, das als Teil der Optionen für die Replikationszieleinstellungen bereitgestellt wird.

Nachdem der virtuelle Computer erstellt wurde, ruft Azure Migrate die [benutzerdefinierte Skripterweiterung](../virtual-machines/extensions/custom-script-windows.md) auf der temporären VM mithilfe der Azure Virtual Machine-Rest-API auf. Das Hilfsprogramm für die benutzerdefinierte Skripterweiterung führt ein Vorbereitungsskript mit der erforderlichen Konfiguration für die Azure-Bereitschaft auf den lokalen VM-Datenträgern aus, die an die temporäre Azure-VM angefügt sind. Das Vorbereitungsskript wird aus einem Speicherkonto im Besitz von Azure Migrate heruntergeladen. Die Regeln der Netzwerksicherheitsgruppe des virtuellen Netzwerks werden so konfiguriert, dass die temporäre Azure-VM zum Aufrufen des Skripts auf das Azure Migrate Speicherkonto zugreifen kann.

 ![Schritte bei der Migration](./media/concepts-vmware-agentless-migration/migration-steps.png)

## <a name="changes-performed-during-the-hydration-process"></a>Während des Hydrationsprozesses durchgeführte Änderungen

Das Vorbereitungsskript führt die folgenden Änderungen basierend auf dem Betriebssystemtyp der zu migrierenden Quell-VM aus. Sie können diesen Abschnitt auch als Leitfaden verwenden, um die VMs manuell auf die Migration für Betriebssystemversionen vorzubereiten, für die Hydration nicht unterstützt wird.

### <a name="changes-performed-on-windows-servers"></a>Auf Windows-Servern vorgenommene Änderungen

1. **Ermitteln und Vorbereiten des Windows Betriebssystemvolumes**

   Vor dem Ausführen relevanter Konfigurationsänderungen überprüft das Vorbereitungsskript, ob der richtige Betriebssystemdatenträger für die Migration ausgewählt wurde. Das Vorbereitungsskript durchsucht alle angefügten Volumes, die für das System sichtbar sind, und sucht nach dem SYSTEM-Registrierungsstruktur-Dateipfad, um das Quellbetriebssystemvolume zu finden.

   Die folgenden Aktionen werden in diesem Schritt ausgeführt:
   - Einbinden jeder Partition auf dem Betriebssystemdatenträger, der an die temporäre VM angefügt ist.

   - Suchen nach \Windows\System32\Config\System-Registrierungsdateien nach dem Einbinden der Partition.
   - Wenn die Dateien nicht gefunden werden, wird die Bereitstellung der Partition aufgehoben, und die Suche nach der richtigen Partition wird fortgesetzt.
   - Wenn die Dateien nicht auf einer der Partitionen vorhanden sind, kann dies darauf hinweisen, dass ein falscher Betriebssystemdatenträger ausgewählt wurde oder der Betriebssystemdatenträger beschädigt ist. Azure Migrate schlägt bei der Migration mit einem entsprechenden Fehler fehl.

   >[!NOTE]
   >Dieser Schritt ist nicht relevant, wenn Sie die Server manuell für die Migration vorbereiten.

1. **Vornehmen von Änderungen im Zusammenhang mit Start und Konnektivität**

   Nachdem die Dateien des Quellbetriebssystemvolumes erkannt wurden, lädt das Vorbereitungsskript die SYSTEM-Registrierungsstruktur in den Registrierungs-Editor der temporären Azure-VM und führt die folgenden Änderungen aus, um den VM-Start und Konnektivität sicherzustellen. Sie müssen diese Einstellungen manuell konfigurieren, wenn die Betriebssystemversion nicht für Hydration unterstützt wird.

   1. **Überprüfen des Vorhandenseins der erforderlichen Treiber**
      
      Stellen Sie sicher, dass die erforderlichen Treiber installiert und so festgelegt sind, dass sie beim **Start** geladen werden. Diese Windows-Treiber ermöglichen es dem Server, mit der Hardware und anderen verbundenen Geräten zu kommunizieren.

      - IntelIde.sys
      - Atapi
      - Storfit
      - Storvsc
      - VMbus

   1. **Festlegen der SAN-Richtlinie (Storage Area Network) auf Online All**

      Dadurch wird sichergestellt, dass für Windows-Volumes auf der Azure-VM die gleichen Laufwerkbuchstabenzuweisungen verwendet werden wie auf dem lokalen virtuellen Computer. Standardmäßig wird Azure-VMs das Laufwerk D als temporärer Speicher zugewiesen. Diese Laufwerkzuweisung führt dazu, dass alle anderen angefügten Zuweisungen von Speicherlaufwerken um einen Buchstaben inkrementiert werden. Legen Sie die SAN-Richtlinie (Storage Area Network) auf „Online All“ fest, um diese automatische Zuweisung zu verhindern und sicherzustellen, dass Azure dem temporären Volume den nächsten freien Laufwerkbuchstaben zuweist.

      Konfigurieren Sie diese Einstellung manuell wie folgt:

      - Öffnen Sie auf dem lokalen Server die Eingabeaufforderung mit erhöhten Rechten, und geben Sie **diskpart** ein.

        ![Manuelle Konfiguration](./media/concepts-prepare-vmware-agentless-migration/command-prompt-diskpart.png)

      - Geben Sie SAN ein. Wenn der Laufwerkbuchstabe des Gastbetriebssystems nicht beibehalten wird, wird Offline – Alle oder Offline – Freigegeben zurückgegeben.

      - Geben Sie an der Eingabeaufforderung DISKPART Folgendes ein: SAN Policy=OnlineAll. Durch diese Einstellung wird sichergestellt, dass Datenträger online geschaltet werden und dass Sie von beiden Datenträgern lesen sowie auf beide Datenträger schreiben können.

        ![Onlinerichtlinie „diskpart“ der Administratoreingabeaufforderung](./media/concepts-prepare-vmware-agentless-migration/diskpart-online-policy.png)

1. **Festlegen des DHCP-Starttyps**

   Das Vorbereitungsskript legt auch den Starttyp des DHCP-Diensts auf „Automatisch“ fest. Dadurch kann die migrierte VM eine IP-Adresse abrufen und Konnektivität nach der Migration herstellen. Stellen Sie sicher, dass der DHCP-Dienst konfiguriert ist und der Status „wird ausgeführt“ lautet.

    ![Festlegen des DHCP-Starttyps](./media/concepts-prepare-vmware-agentless-migration/get-service-dhcp.png)

   Führen Sie das folgende Beispiel in Windows PowerShell aus, um die DHCP-Starteinstellungen manuell zu bearbeiten:

   ```
   Get-Service -Name Dhcp
   Where-Object StartType -ne Automatic
   Set-Service -StartupType Automatic
   ```

1. **Deaktivieren von VMware-Tools**

   Deaktivieren Sie den Dienststarttyp „VMware Tools“, wenn er vorhanden ist, da die Tools für die VM in Azure nicht erforderlich sind.

   >[!NOTE]                        
   >Zum Herstellen einer Verbindung mit Windows Server 2003-VMs muss Hyper-V Integration Services auf der Azure-VM installiert sein. Dies ist auf Windows Server 2003-Computern standardmäßig nicht installiert. Weitere Informationen zur Installation und Vorbereitung der Migration finden Sie in diesem [Artikel](./prepare-windows-server-2003-migration.md).

1. **Installieren des Windows Azure-Gast-Agents**

    Azure Migrate versucht, den Microsoft Azure Virtual Machine-Agent (VM-Agent) zu installieren, einen sicheren, einfachen Prozess zur Verwaltung der VM-Interaktion mit dem Azure Fabric Controller. Der VM-Agent nimmt eine primäre Rolle beim Aktivieren und Ausführen von Azure-VM-Erweiterungen ein, die die Konfiguration der VM nach der Bereitstellung ermöglichen, z. B. das Installieren und Konfigurieren von Software. Azure Migrate installiert den Windows-VM-Agent automatisch auf Windows Server 2008 R2 und höheren Versionen.

    Der Windows-VM-Agent kann manuell mithilfe eines Windows-Installationspakets installiert werden. Laden Sie für die manuelle Installation des Windows-VM-Agents [das Installationsprogramms für den VM-Agent herunter](https://go.microsoft.com/fwlink/?LinkID=394789). In den [GitHub-Releases für den Windows-VM-Agent (IaaS)](https://github.com/Azure/WindowsVMAgent/releases) können Sie auch nach einer bestimmten Version suchen. Der VM-Agent wird unter Windows Server 2008 R2 (64 Bit) und höher unterstützt.

    Öffnen Sie zum Überprüfen, ob der Azure-VM-Agent ordnungsgemäß installiert wurde, den Task-Manager, wählen Sie die Registerkarte **Details** aus, und suchen sie nach dem Prozessnamen *WindowsAzureGuestAgent.exe*. Ist dieser Prozess vorhanden, ist der VM-Agent installiert. Sie können auch [PowerShell verwenden, um den VM-Agent zu erkennen.](../virtual-machines/extensions/agent-windows.md#powershell)

    ![Erfolgreiche Installation des Azure-VM-Agents](./media/concepts-prepare-vmware-agentless-migration/installation-azure-vm-agent.png)

    Nachdem die oben genannten Änderungen vorgenommen wurden, wird die Systempartition entladen. Die VM ist nun bereit für die Migration.
    [Erfahren Sie mehr über die Änderungen für Windows-Server.](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

### <a name="changes-performed-on-linux-servers"></a>Auf Linux-Servern vorgenommene Änderungen

1. **Entdecken und Einbinden von Linux-Betriebssystempartitionen**  

   Vor dem Ausführen relevanter Konfigurationsänderungen überprüft das Vorbereitungsskript, ob der richtige Betriebssystemdatenträger für die Migration ausgewählt wurde. Das Skript sammelt Informationen zu allen Partitionen, ihren UUIDs und Bereitstellungspunkten. Das Skript durchsucht alle diese sichtbaren Partitionen, um die Partitionen /boot und /root zu finden.

   Die folgenden Aktionen werden in diesem Schritt ausgeführt:

   - Entdecken der /root-Partition:
     - Binden Sie jede sichtbare Partition ein, und suchen Sie nach „etc/fstab“.
      - Wenn die fstab-Dateien nicht gefunden werden, wird die Bereitstellung der Partition aufgehoben, und die Suche nach der richtigen Partition wird fortgesetzt.
      - Wenn die fstab-Dateien gefunden werden, lesen Sie den fstab-Inhalt, um das Stammgerät zu identifizieren, und binden Sie es als Basisbereitstellungspunkt ein.
   - Entdecken der /boot-Partition und anderer Systempartitionen:
     - Verwenden Sie den fstab-Inhalt, um zu bestimmen, ob /boot eine separate Partition ist. Wenn es sich um eine separate Partition handelt, rufen Sie den Gerätenamen der Startpartition aus dem fstab-Inhalt ab, oder suchen Sie nach der Partition, die über das Startflag verfügt.
     - Das Skript wird mit der Suche und Einbinden der /boot-Partition und anderen erforderlichen Partitionen auf „/mnt/azure_sms_root“ fortfahren, um die Stammdateisystemstruktur zu erstellen, die für chroot jail benötigt wird. Andere erforderliche Partitionen sind: /boot/grub/menu.lst, /boot/grub/menu.lst, /boot/grub/grub.conf, /boot/grub2/grub.cfg, /boot/grub/grub.cfg, /boot/efi (for UEFI boot), /var, /lib, /etc, /usr und andere.

1. **Entdecken der Betriebssystemversion**

   Nachdem die Stammpartition ermittelt wurde, verwendet das Skript die folgenden Dateien, um die Distribution und Version des Linux-Betriebssystems zu bestimmen.

   - RHEL/CentOS: etc/redhat-release
   - OL: /etc/oracle-release
   - SLES: /etc/SuSE-release
   - Ubuntu: etc/lsb-release
   - Debian: /etc/debian_version

1. **Installieren von Hyper-V Linux Integration Services und Neugenerieren des Kernelimages**  

   Der nächste Schritt besteht im Überprüfen des Kernelimages und Neuerstellen des Linux-Initialisierungsimages, sodass es die erforderlichen Hyper-V-Treiber (**hv_vmbus, hv_storvsc, hv_netvsc**) auf der anfänglichen Ramdisk enthält. Durch die Neuerstellung des Initialisierungsimages wird sichergestellt, dass der virtuelle Computer in Azure gestartet wird.

   Azure wird auf dem Hyper-V-Hypervisor ausgeführt. Linux erfordert daher, dass bestimmte Kernelmodule in Azure ausgeführt werden. Sie müssen „initrd“ neu erstellen, damit mindestens die Kernelmodule „hv_vmbus“ und „hv_storvsc“ auf der anfänglichen Ramdisk verfügbar sind. Der Mechanismus für die Neuerstellung des Images "initrd" oder "initramfs" variiert je nach der Verteilung. In der Dokumentation zur Distribution oder über den Support erhalten Sie Informationen zur geeigneten Vorgehensweise. Hier sehen Sie ein Beispiel für die Neuerstellung von „initrd“ mit dem Hilfsprogramm mkinitrd:

   1. Suchen der Liste der auf dem System installierten Kernels (/lib/modules)
   1. Untersuchen Sie für jedes Modul, ob die Hyper-V-Treiber bereits enthalten sind.
   1. Wenn einer dieser Treiber fehlt, fügen Sie die erforderlichen Treiber hinzu, und generieren Sie das Image für die entsprechende Kernelversion neu.

      >[!NOTE]
      >Dieser Schritt gilt möglicherweise nicht für Ubuntu- und Debian-VMs, da die Hyper-V-Treiber standardmäßig integriert sind. [Weitere Informationen zu den Änderungen.](../virtual-machines/linux/create-upload-generic.md#installing-kernel-modules-without-hyper-v)

      Ein veranschaulichendes Beispiel für die Neuerstellung von „initrd“

      - Sichern des vorhandenen initrd-Images

        ```
        cd /boot
        sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
        ```

      - Erstellen Sie „initrd“ mit den Kernelmodulen „hv_vmbus“ und „hv_storvsc“ neu:

        ```
          sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
        ```
   In den meisten neuen Versionen von Linux-Distributionen ist dies standardmäßig enthalten. Andernfalls muss die Installation für alle oben nicht angegebenen Versionen manuell mithilfe der oben genannten Schritte durchgeführt werden.

1. **Aktivieren der Protokollierung der seriellen Azure-Konsole**

   Das Skript nimmt dann Änderungen vor, um die Protokollierung der seriellen Azure-Konsole zu aktivieren. Das Aktivieren der Konsolenprotokollierung hilft bei der Behandlung von Problemen auf der Azure-VM. Erfahren Sie mehr über die serielle Azure-Konsole für Linux [Serielle Azure-Konsole für Linux – Virtual Machines | Microsoft-Dokumentation](/troubleshoot/azure/virtual-machines/serial-console-linux).

   Ändern Sie die Zeile für den Kernelstart in GRUB oder GRUB2 so, dass die folgenden Parameter eingeschlossen werden. Auf diese Weise werden alle Konsolennachrichten an den ersten seriellen Port gesendet. Diese Nachrichten helfen dem Azure-Supportteam beim Debuggen von Problemen.

   ```
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
   ```

   Wir empfehlen zudem, die folgenden Parameter zu entfernen, sofern sie vorhanden sind.

   ```
   rhgb quiet crashkernel=auto
   ```
    Weitere Informationen zu spezifischen Änderungen finden Sie in [diesem Artikel](../virtual-machines/linux/create-upload-generic.md#general-linux-system-requirements).

1. **Netzwerkänderungen für Konnektivität**

   Basierend auf der Betriebssystemversion führt das Skript die erforderlichen Netzwerkänderungen für Konnektivität mit der migrierten VM aus. Zu den Änderungen zählt Folgendes:

   1. Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers in Azure zu Problemen führen.

      Ein veranschaulichendes Beispiel für RedHat-Server

      ```console
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
      ```

   1. Entfernen Sie den Netzwerk-Manager, wenn erforderlich. Der Netzwerk-Manager kann den Azure Linux-Agent für einige Betriebssystemversionen beeinträchtigen. Es wird empfohlen, diese Änderungen für Server vorzunehmen, auf denen RedHat- und Ubuntu-Distributionen ausgeführt werden.

   1. Deinstallieren Sie dieses Paket, indem Sie den folgenden Befehl ausführen:
    
      Ein veranschaulichendes Beispiel für RedHat-Server

      ```console
         # sudo rpm -e --nodeps NetworkManager
      ```

   1. Sichern Sie vorhandene NIC-Einstellungen, und erstellen Sie eine eth0-NIC-Konfigurationsdatei mit DHCP-Einstellungen. Hierzu erstellt oder bearbeitet das Skript die Datei /etc/sysconfig/network-scripts/ifcfg-eth0 und fügt den folgenden Text hinzu:

      Ein veranschaulichendes Beispiel für RedHat-Server

      ```config
         DEVICE=eth0
         ONBOOT=yes
         BOOTPROTO=dhcp
         TYPE=Ethernet
         USERCTL=no
         PEERDNS=yes
         IPV6INIT=no
         PERSISTENT_DHCLIENT=yes
         NM_CONTROLLED=yes
      ```

   1. Setzen Sie die Datei etc/sysconfig/network wie folgt zurück:

      Ein veranschaulichendes Beispiel für RedHat-Server

      ```config
         NETWORKING=yes
         HOSTNAME=localhost.localdomain
      ```

1. **Fstab-Überprüfung**

    Azure Migrate überprüft die Einträge der fstab-Datei und ersetzt fstab-Einträge bei Bedarf durch persistente Volumebezeichner, UUIDs. Dadurch wird sichergestellt, dass der Laufwerks-/Partitionsname unabhängig vom System, an das er angefügt ist, konstant bleibt.

   - Wenn der Gerätename ein Standardgerätename ist (z. B. /dev/sdb1), gilt Folgendes:
     - Wenn es sich um eine Stamm- oder Startpartition handelt, wird sie durch UUID ersetzt.
     - Wenn die Partition gleichzeitig mit der Stamm- oder Startpartition als Standardpartitionen auf demselben Datenträger vorhanden ist, wird sie durch UUID ersetzt.
   - Wenn der Gerätename UUID/LABEL/LV ist, werden keine Änderungen vorgenommen.
   - Wenn es sich um ein Netzwerkgerät (nfs, cifs, smbfs und etc) handelt, kommentiert das Skript den Eintrag. Um darauf zuzugreifen, können Sie die Auskommentierung aufheben und die Azure-VM neu starten.

1. **Installieren des Linux-Azure-Gast-Agents**

    Azure Migrate versucht, den Microsoft Azure Linux-Agent (waagent) zu installieren, einen sicheren, schlanken Prozess, der die Linux- und FreeBSD-Bereitstellung und die VM-Interaktion mit dem Azure Fabric Controller verwaltet.  [Erfahren Sie mehr](../virtual-machines/extensions/agent-linux.md) über die Funktionen, die für Linux- und FreeBSD-IaaS-Bereitstellungen über den Linux-Agent aktiviert sind.

    Sehen Sie sich die Liste der [erforderlichen Pakete](../virtual-machines/extensions/agent-linux.md#requirements) zum Installieren des Linux-VM-Agents an. Azure Migrate installiert den Linux-VM-Agent automatisch für RHEL 8.x/7.x/6.x, CentOS 8.x/7.x/6.x, Ubuntu 14.04/16.04/18.04/19.04/19.10/20.04, SUSE 15 SP0/15 SP1/12, Debian 9/8/7 und Oracle 7 bei Verwendung der Methode ohne Agent für VMware-Migration. Befolgen Sie diese Anweisungen, um den Linux-Agent [manuell](../virtual-machines/extensions/agent-linux.md#installation) für andere Betriebssystemversionen zu installieren.

    Sie können den Befehl verwenden, um den Dienststatus des Azure Linux-Agents zu überprüfen, um sicherzustellen, dass er ausgeführt wird. Der Dienstname kann **walinuxagent** oder **waagent** lauten.
    Nachdem die Hydrationsänderungen vorgenommen wurden, hebt das Skript die Bereitstellung aller eingebundenen Partitionen auf, deaktiviert Volumegruppen und löscht dann die Geräte.

   ```
    $ vgchange -an <vg-name>
    $ blockdev –flushbufs <disk-device-name>
   ```

   [Erfahren Sie mehr über die Änderungen für Linux-Server.](../virtual-machines/linux/create-upload-generic.md)

### <a name="clean-up-the-temporary-vm"></a>Bereinigen der temporären VM

Nachdem die erforderlichen Änderungen vorgenommen wurden, fährt Azure Migrate die temporäre VM herunter und gibt die angefügten Betriebssystemdatenträger (und Datenträger) frei. Dieser Vorgang markiert das Ende des *Hydrationsprozesses*.     

Danach werden der geänderte Betriebssystemdatenträger und die Datenträger, die die replizierten Daten enthalten, geklont. Ein neuer virtueller Computer wird in der Zielregion, im virtuellen Netzwerk und im Subnetz erstellt, und die geklonten Datenträger werden an den virtuellen Computer angefügt. Dieser Vorgang kennzeichnet den Abschluss des Migrationsprozesses.

## <a name="learn-more"></a>Erfahren Sie mehr

- [Vorbereiten von lokalen Computern für die Migration zu Azure.](./prepare-for-migration.md)
