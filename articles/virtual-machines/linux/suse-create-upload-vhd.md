---
title: Erstellen und Hochladen einer SUSE-Linux-VHD in Azure
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein SUSE-Linux-Betriebssystem enthält.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 30246166650cddc1c89fa3a2ca377c163fb5847b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697750"
---
# <a name="prepare-a-sles-or-opensuse-leap-virtual-machine-for-azure"></a>Vorbereiten eines virtuellen SLES- oder openSUSE Leap-Computers für Azure

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen **Gilt für:** :heavy_check_mark: Einheitliche Skalierungsgruppen

In diesem Artikel wird davon ausgegangen, dass Sie bereits ein SUSE- oder openSUSE Leap-Linux-Betriebssystem auf einer virtuellen Festplatte installiert haben. Sie können VHD-Dateien mit unterschiedlichen Tools erstellen, beispielsweise mit einer Virtualisierungslösung wie Hyper-V. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-leap-installation-notes"></a>Installationshinweise zu SLES/openSUSE Leap
* Beachten Sie auch den Artikelabschnitt [Allgemeine Linux-Systemanforderungen](create-upload-generic.md#general-linux-installation-notes), in dem weitere Tipps zur Vorbereitung von Linux für Azure enthalten sind.
* Das VHDX-Format wird in Azure noch nicht unterstützt, dafür jedoch **virtuelle Festplatten mit fester Größe**.  Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.
* Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) oder [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) können wahlweise auf Datenträgern verwendet werden.
* Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen.  Weitere Informationen dazu finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Beim Konvertieren von einem unformatierten Datenträger in VHD müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers ein Vielfaches von 1 MB vor der Konvertierung beträgt. Weitere Informationen finden Sie in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-suse-studio"></a>Verwenden von SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) können Sie Ihre SLES- und openSUSE Leap-Images für Azure und Hyper-V auf einfache Weise erstellen und verwalten. Diese Vorgehensweise wird zum Anpassen Ihrer eigenen SLES- und openSUSE Leap-Images empfohlen.

Als Alternative zum Erstellen einer eigenen VHD veröffentlicht SUSE auf [VM Depot](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)auch BYOS-Images (Bring Your Own Subscription) für SLES.

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Vorbereiten von SUSE Linux Enterprise Server für Azure
1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.
2. Klicken Sie auf **Verbinden** , um das Fenster für den virtuellen Computer zu öffnen.
3. Registrieren Sie Ihr SUSE Linux Enterprise-System, um das Herunterladen von Updates und das Installieren von Paketen zu ermöglichen.
4. Aktualisieren Sie das System mit den neuesten Patches:

    ```console
    # sudo zypper update
    ```
    
5. Installieren Sie den Azure Linux-Agent und cloud-init:

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Aktivieren Sie das Starten von waagent und cloud-init beim Systemstart:

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Aktualisieren Sie die Konfiguration von waagent und cloud-init:

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Bearbeiten Sie die Datei „/etc/default/grub“, um sicherzustellen, dass Konsolenprotokolle an den seriellen Port gesendet werden, und aktualisieren Sie anschließend die Hauptkonfigurationsdatei mit „grub2-mkconfig -o /boot/grub2/grub.cfg“:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.
    
9. Stellen Sie sicher, dass von der Datei „/etc/fstab“ per UUID (by-uuid) auf den Datenträger verwiesen wird.
         
10. Ändern Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle(n) zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. Es wird empfohlen, die Datei "/etc/sysconfig/network/dhcp" zu bearbeiten und den Parameter `DHCLIENT_SET_HOSTNAME` wie folgt zu ändern:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. Kommentieren Sie in „/etc/sudoers“ die folgenden Zeilen aus, sofern sie vorhanden sind, oder entfernen Sie sie:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist für gewöhnlich die Standardeinstellung.

14. Auslagerungskonfiguration
 
    Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.

    Früher wurde der Azure Linux-Agent zum automatischen Konfigurieren des Auslagerungsbereichs mit dem lokalen Ressourcendatenträger verwendet, der nach der Bereitstellung der VM in Azure an die VM angefügt ist. Dies wird jetzt jedoch von cloud-init behandelt. Sie dürfen **nicht** den Linux-Agent zum Erstellen des Ressourcendatenträgers verwenden. Erstellen Sie die Auslagerungsdatei, und ändern Sie die folgenden Parameter in `/etc/waagent.conf` entsprechend:

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Beim Einbinden, Formatieren und Erstellen einer Auslagerung können Sie eine der folgenden Aktionen ausführen:
    * Übergeben Sie dies bei jeder VM-Erstellung als cloud-init-Konfiguration.
    * Verwenden Sie eine im Image integrierte cloud-init-Anweisung, die dies bei jeder VM-Erstellung durchführt:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

15. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

---
## <a name="prepare-opensuse-152"></a>Vorbereiten von openSUSE 15.2 oder höher
1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.
2. Klicken Sie auf **Verbinden** , um das Fenster für den virtuellen Computer zu öffnen.
3. Führen Sie in der Shell den Befehl "`zypper lr`" aus. Wenn dieser Befehl den folgenden ähnelnde Ausgaben zurückgibt, sind Repositorys erwartungsgemäß konfiguriert, und es sind keine Anpassungen erforderlich (Versionsnummern können abweichen):

   | # | Alias                 | Name                  | Aktiviert | Aktualisieren
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Cloud:Tools_15.2      | Cloud:Tools_15.2      | Ja     | Ja
   | 2 | openSUSE_15.2_OSS     | openSUSE_15.2_OSS     | Ja     | Ja
   | 3 | openSUSE_15.2_Updates | openSUSE_15.2_Updates | Ja     | Ja

    Wenn der Befehl „No repositories defined...“ zurückgibt, verwenden Sie die folgenden Befehle zum Hinzufügen dieser Repositorys:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_15.2 Cloud:Tools_15.2
    # sudo zypper ar -f https://download.opensuse.org/distribution/15.2/repo/oss openSUSE_15.2_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/15.2 openSUSE_15.2_Updates
    ```

    Sie können dann überprüfen, ob die Repositorys hinzugefügt wurden, indem Sie den Befehl "`zypper lr`" erneut ausführen. Falls eines der relevanten Update-Repositorys nicht aktiviert ist, können Sie es mit dem folgenden Befehl aktivieren:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Aktualisieren Sie den Kernel auf die neueste verfügbare Version:

    ```console
    # sudo zypper up kernel-default
    ```

    Oder aktualisieren Sie das System mit allen neuen Patches:

    ```console
    # sudo zypper update
    ```

5. Installieren des Azure Linux Agent:

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür "/boot/grub/menu.lst" in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Entfernen Sie außerdem die folgenden Parameter aus der Kernel-Boot-Zeile, sofern diese vorhanden sind:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. Es wird empfohlen, die Datei "/etc/sysconfig/network/dhcp" zu bearbeiten und den Parameter `DHCLIENT_SET_HOSTNAME` wie folgt zu ändern:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Wichtig:** Kommentieren Sie in „/etc/sudoers“ die folgenden Zeilen aus, sofern sie vorhanden sind, oder entfernen Sie sie:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.
10. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Stellen Sie sicher, dass der Azure Linux Agent beim Start ausgeführt wird:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt mit Ihrer SUSE-Linux-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).
