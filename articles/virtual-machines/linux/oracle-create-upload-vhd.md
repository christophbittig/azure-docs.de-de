---
title: Erstellen und Hochladen einer Oracle Linux-VHD
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Oracle Linux-Betriebssystem enthält.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.subservice: oracle
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2021
ms.author: srijangupta
ms.openlocfilehash: 26eb625604d1947b5e246c27f766af63777b6239
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309546"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Vorbereiten eines virtuellen Oracle Linux-Computers für Azure

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird davon ausgegangen, dass Sie bereits ein Oracle Linux-Betriebssystem auf einer virtuellen Festplatte installiert haben. Sie können VHD-Dateien mit unterschiedlichen Tools erstellen, beispielsweise mit einer Virtualisierungslösung wie Hyper-V. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="oracle-linux-installation-notes"></a>Installationshinweise für Oracle Linux
* Beachten Sie auch den Artikelabschnitt [Allgemeine Linux-Systemanforderungen](create-upload-generic.md#general-linux-installation-notes), in dem weitere Tipps zur Vorbereitung von Linux für Azure enthalten sind.
* Hyper-V und Azure unterstützen Oracle Linux entweder mit dem Unbreakable Enterprise Kernel (UEK) oder dem Red Hat Compatible Kernel.
* Der UEK2 von Oracle wird auf Hyper-V und Azure nicht unterstützt, da er nicht die erforderlichen Treiber enthält.
* Das VHDX-Format wird in Azure noch nicht unterstützt, dafür jedoch **virtuelle Festplatten mit fester Größe**.  Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.
* **Kernel-Unterstützung für bereitgestellte UDF-Dateisysteme ist erforderlich.** Beim ersten Start in Azure wird die Bereitstellungskonfiguration über UDF-formatierte Medien, die an den Gast angefügt sind, an den virtuellen Linux-Computer übergeben. Der Azure-Linux-Agent muss das UDF-Dateisystem bereitstellen können, um dessen Konfiguration zu lesen und die VM bereitzustellen.
* Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) oder [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) können wahlweise auf Datenträgern verwendet werden.
* Linux-Kernelversionen vor Version 2.6.37 unterstützen keinen NUMA (Non-Uniform Memory Access, nicht einheitlicher Speicherzugriff) auf Hyper-V mit größeren VM-Größen. Dieses Problem betrifft in erster Linie ältere Verteilungen, die den vorgeschalteten Red Hat 2.6.32 Kernel verwenden, und wurde in Oracle Linux 6.6 und höher behoben.
* Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Weitere Informationen dazu finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Beim Konvertieren von einem unformatierten Datenträger in VHD müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers ein Vielfaches von 1 MB vor der Konvertierung beträgt. Weitere Informationen finden Sie in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).
* Stellen Sie sicher, dass das `Addons` -Repository aktiviert ist. Bearbeiten Sie die Datei `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) oder `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7), und ändern Sie in dieser Datei unter **[ol6_addons]** oder **[ol7_addons]** die Zeile `enabled=0` in `enabled=1`.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 und höher
Sie müssen die spezifischen Konfigurationsschritte im Betriebssystem für den virtuellen Computer abschließen, welcher unter Azure ausgeführt werden soll.

1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.
2. Klicken Sie auf **Verbinden** , um das Fenster für den virtuellen Computer zu öffnen.
3. Deinstallieren Sie den NetworkManager, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Hinweis:** Wenn das Paket noch nicht installiert ist, schlägt dieser Befehl mit einer Fehlermeldung fehl. Dies entspricht dem erwarteten Verhalten.
4. Erstellen Sie im Verzeichnis `/etc/sysconfig/` eine Datei mit dem Namen **network** und folgendem Text:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Erstellen Sie im Verzeichnis `/etc/sysconfig/network-scripts/` eine Datei mit dem Namen **ifcfg-eth0** und folgendem Text:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Ändern Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle(n) zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # chkconfig network on
    ```

8. Installieren Sie python-pyasn1, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür „/boot/grub/menu.lst“ in einem Text-Editor. Stellen Sie sicher, dass der Kernel die folgenden Parameter enthält:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.
   
   Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen* :

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.
   
   Die Option `crashkernel` kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.
10. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.
11. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen: Die neueste Version ist 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Beachten Sie, dass durch eine Installation des WALinuxAgent-Pakets die NetworkManager- und NetworkManager-gnome-Pakete entfernt werden, sofern sie nicht bereits wie in Schritt 2 beschrieben entfernt wurden.
12. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.
    
    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 und höher
**Änderungen in Oracle Linux 7**

Die Vorbereitung eines virtuellen Oracle Linux 7-Computers für Azure entspricht in etwa der für Oracle Linux 6. Es gibt jedoch einige wichtige Unterschiede:

* Azure unterstützt Oracle Linux entweder mit dem Unbreakable Enterprise Kernel Release (UEK) oder dem Red Hat Compatible Kernel. Oracle Linux mit UEK wird empfohlen.
* Das NetworkManager-Paket führt nicht mehr zu Konflikten mit dem Azure Linux Agent. Dieses Paket ist standardmäßig installiert, und Sie sollten es nicht entfernen.
* GRUB2 wird nun als standardmäßiger Bootloader verwendet. Daher hat sich die Prozedur für das Bearbeiten der Kernelparameter geändert (siehe unten).
* XFS ist nun das Standarddateisystem. Das EXT4-Dateisystem kann bei Bedarf weiterhin verwendet werden.

**Konfigurationsschritte**

1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.
2. Klicken Sie auf **Verbinden** , um ein Konsolenfenster für den virtuellen Computer zu öffnen.
3. Erstellen Sie im Verzeichnis `/etc/sysconfig/` eine Datei mit dem Namen **network** und folgendem Text:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Erstellen Sie im Verzeichnis `/etc/sysconfig/network-scripts/` eine Datei mit dem Namen **ifcfg-eth0** und folgendem Text:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Ändern Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle(n) zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo chkconfig network on
    ```

7. Installieren Sie das Paket python-pyasn1, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Führen Sie den folgenden Befehl aus, um die aktuellen yum-Metadaten zu löschen und um Updates zu installieren.

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu „/etc/default/grub“ in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`. Beispiel:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Außerdem werden die Benennungskonventionen für NICs in Oracle Linux 7 mit dem Unbreakable Enterprise Kernel deaktiviert. Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen* :

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.
   
   Die Option `crashkernel` kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.
10. Sobald Sie die oben beschriebene Bearbeitung von "/etc/default/grub" abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der Grub-Konfiguration aus:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.
12. Installieren Sie den Azure Linux-Agent und die Abhängigkeiten:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. Installieren von cloud-init zum Behandeln der Bereitstellung

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. Auslagerungskonfiguration Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.

    Früher wurde der Azure Linux-Agent zum automatischen Konfigurieren des Auslagerungsbereichs mit dem lokalen Ressourcendatenträger verwendet, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Dies wird jetzt jedoch von cloud-init behandelt. Sie dürfen **nicht** den Linux-Agent zum Erstellen des Ressourcendatenträgers verwenden. Erstellen Sie die Auslagerungsdatei, und ändern Sie die folgenden Parameter in `/etc/waagent.conf` entsprechend:

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Beim Einbinden, Formatieren und Erstellen einer Auslagerung können Sie eine der folgenden Aktionen ausführen:
    * Übergeben Sie dies bei jedem Erstellen einer VM als cloud-init-Konfiguration.
    * Verwenden Sie eine im Image integrierte cloud-init-Anweisung, die dies bei jeder Erstellung der VM durchführt:

        ```console
        echo 'DefaultEnvironment="CLOUD_CFG=/etc/cloud/cloud.cfg.d/00-azure-swap.cfg"' >> /etc/systemd/system.conf
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

    **Hinweis:** Wenn Sie einen bestimmten virtuellen Computer migrieren und kein generalisiertes Image erstellen wollen, überspringen Sie den Schritt zum Aufheben der Bereitstellung
    
    ```console
    # sudo cloud-init clean
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

16. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## <a name="next-steps"></a>Nächste Schritte
Nun können Sie mit Ihrer Oracle Linux-VHD-Datei neue virtuelle Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).
