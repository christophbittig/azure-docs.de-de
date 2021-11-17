---
title: Erstellen und Hochladen einer VHD-Datei mit Red Hat Enterprise Linux zur Verwendung in Azure
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Red Hat-Linux-Betriebssystem enthält.
author: srijang
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 11/10/2021
ms.author: srijangupta
ms.openlocfilehash: 11a7931126d451b2fbeff301a337f15fd6aecbf3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301227"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Vorbereiten eines auf Red Hat basierenden virtuellen Computers für Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen 

In diesem Artikel erfahren Sie, wie Sie einen auf Red Hat Enterprise Linux (RHEL) basierenden virtuellen Computer für die Verwendung in Azure vorbereiten. In diesem Artikel werden die RHEL-Versionen 6.7+ und 7.1+ behandelt. Darüber hinaus werden in diesem Artikel die Hypervisoren Hyper-V, KVM und VMware für die Vorbereitung vorgestellt. Weitere Informationen zu den Berechtigungsvoraussetzungen für die Teilnahme am Cloud Access-Programm von Red Hat finden Sie auf der [Red Hat Cloud Access-Website](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) und unter [Running RHEL on Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure) (Ausführen von RHEL in Azure). Weitere Informationen zu den Möglichkeiten zum Automatisieren der Erstellung von RHEL-Images finden Sie unter [Azure Image Builder](../image-builder-overview.md).

## <a name="hyper-v-manager"></a>Hyper-V-Manager

In diesem Abschnitt erfahren Sie, wie Sie mit dem Hyper-V-Manager eine [RHEL 6](#rhel-6-using-hyper-v-manager)-, [RHEL 7](#rhel-7-using-hyper-v-manager)- oder [RHEL 8](#rhel-8-using-hyper-v-manager)-VM vorbereiten.

### <a name="prerequisites"></a>Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits eine ISO-Datei von der Red Hat-Website beschafft und das RHEL-Image auf einer virtuellen Festplatte (VHD) installiert haben. Weitere Informationen zum Installieren eines Betriebssystemimage mit dem Hyper-V-Manager finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Installationshinweise zu RHEL**

* Das VHDX-Format wird von Azure nicht unterstützt. Azure unterstützt nur feste virtuelle Festplatten. Sie können Hyper-V Manager verwenden, um den Datenträger in das VHD-Format zu konvertieren, oder Sie können das convert-vhd-Cmdlet verwenden. Wählen Sie bei Verwendung von VirtualBox die Option **Feste Größe** und nicht die standardmäßig dynamisch zugeordnete Option, wenn Sie den Datenträger erstellen.
* Azure unterstützt die virtuellen Computer Gen1 (BIOS-Start) und Gen2 (UEFI-Start).
* Die maximal zulässige Größe für die virtuelle Festplatte beträgt 1.023 GB.
* Logical Volume Manager (LVM) wird unterstützt und kann auf dem Betriebssystemdatenträger oder den Datenträgern mit Daten auf virtuellen Azure-Computern verwendet werden. Im Allgemeinen empfiehlt es sich jedoch, für Betriebssystemdatenträger Standardpartitionen und nicht LVM zu verwenden. Bei diesem Verfahren werden LVM-Namenskonflikte mit geklonten virtuellen Computern verhindert. Dies gilt besonders, falls Sie einen Betriebssystem-Datenträger zur Problembehandlung mit einem anderen virtuellen Computer verbinden müssen, der identisch ist. Lesen Sie auch die Informationen in der Dokumentation zu [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) und [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid).
* **Kernelunterstützung für die Bereitstellung von UDF-Dateisystemen (Universal Disk Format) ist erforderlich**. Beim ersten Starten unter Azure übergibt das Medium mit UDF-Formatierung, das an den Gast angefügt ist, die Bereitstellungskonfiguration an den virtuellen Linux-Computer. Der Azure-Linux-Agent muss das UDF-Dateisystem einbinden können, um dessen Konfiguration zu lesen und den virtuellen Computer bereitzustellen. Andernfalls schlägt die Bereitstellung fehl.
* Konfigurieren Sie auf dem Betriebssystem-Datenträger keine Swap-Partition. Weitere Informationen hierzu finden Sie unter den folgenden Schritten.

* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Beim Konvertieren von einem unformatierten Datenträger in VHD müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers ein Vielfaches von 1 MB vor der Konvertierung beträgt. Einzelheiten erfahren Sie im folgenden Schritt. Weitere Informationen finden Sie auch in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).

### <a name="rhel-6-using-hyper-v-manager"></a>RHEL 6 mit dem Hyper-V-Manager

1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

1. Klicken Sie auf **Verbinden** , um ein Konsolenfenster für den virtuellen Computer zu öffnen.

1. In RHEL 6 kann NetworkManager zu Einschränkungen beim Azure Linux-Agent führen. Deinstallieren Sie dieses Paket, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo chkconfig network on
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie für diese Änderung `/boot/grub/menu.lst` in einem Text-Editor, und stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.
    
    Außerdem wird empfohlen, die folgenden Parameter zu entfernen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.  Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Beachten Sie, dass der verfügbare Arbeitsspeicher des virtuellen Computers mit diesem Parameter um mindestens 128 MB reduziert wird. Diese Konfiguration kann für kleinere virtuelle Computer problematisch sein.


1. Stellen Sie sicher, dass der SSH-Server (Secure Shell) installiert und für das Starten während des Startvorgangs konfiguriert ist. Dies ist normalerweise die Standardeinstellung. Ändern Sie die Datei „/etc/ssh/sshd_config“, sodass sie die folgende Zeile enthält:

    ```config
    ClientAliveInterval 180
    ```

1. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    Durch eine Installation des WALinuxAgent-Pakets werden die NetworkManager- und NetworkManager-gnome-Pakete entfernt, sofern sie nicht bereits in Schritt 3 entfernt wurden.

1. Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.

    Der Azure Linux-Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Ändern Sie nach dem Installieren des Azure Linux-Agents im vorherigen Schritt entsprechend die folgenden Parameter in „/etc/waagent.conf“:

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo subscription-manager unregister
    ```

1. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. Klicken Sie im Hyper-V-Manager auf **Aktion** > **Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.


### <a name="rhel-7-using-hyper-v-manager"></a>RHEL 7 mit dem Hyper-V-Manager

1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

1. Klicken Sie auf **Verbinden** , um ein Konsolenfenster für den virtuellen Computer zu öffnen.

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

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

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo systemctl enable network
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie für diese Änderung `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`. Beispiel:

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    Dadurch wird außerdem sichergestellt, dass alle Konsolennachrichten an den ersten seriellen Anschluss gesendet werden, und die Interaktion mit der seriellen Konsole ermöglicht, was den Azure Support bei Debugproblemen unterstützen kann. Außerdem werden bei dieser Konfiguration die neuen RHEL 7-Benennungskonventionen für Netzwerkkarten deaktiviert.

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Beachten Sie, dass die Menge an verfügbarem Arbeitsspeicher auf dem virtuellen Computer mit diesem Parameter um mindestens 128 MB reduziert wird. Dies kann bei kleineren virtuellen Computern problematisch sein.

1. Nachdem Sie die Bearbeitung von `/etc/default/grub`abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > Beim Hochladen einer UEFI-fähigen VM lautet der Befehl zum Upgraden von GRUB `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg`.

1. Stellen Sie sicher, dass der SSH-Server installiert und so konfiguriert ist, dass er beim Booten hochfährt. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

    ```config
    ClientAliveInterval 180
    ```

1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installieren Sie den Azure Linux Agent, cloud-init und andere notwendige Hilfsprogramme, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Konfigurieren von cloud-init zum Behandeln der Bereitstellung:

    1. Konfigurieren von waagent für cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Wenn Sie einen bestimmten virtuellen Computer migrieren, anstatt ein generalisiertes Image zu erstellen, legen Sie in der `/etc/waagent.conf`-Konfigurationsdatei `Provisioning.Agent=disabled` fest.
    
    1. Konfigurieren der Einbindungen:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Konfigurieren der Azure-Datenquelle:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF
    ```

    1. Falls sie konfiguriert ist, entfernen Sie die vorhandene Auslagerungsdatei:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Konfigurieren der cloud-init-Protokollierung:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. Auslagerungskonfiguration Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.

    Früher wurde der Azure Linux-Agent zum automatischen Konfigurieren des Auslagerungsbereichs mit dem lokalen Ressourcendatenträger verwendet, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Dies wird jetzt jedoch von cloud-init behandelt. Sie dürfen **nicht** den Linux-Agent zum Erstellen des Ressourcendatenträgers verwenden. Erstellen Sie die Auslagerungsdatei, und ändern Sie die folgenden Parameter in `/etc/waagent.conf` entsprechend:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    Beim Einbinden, Formatieren und Erstellen einer Auslagerung können Sie eine der folgenden Aktionen ausführen:
    * Übergeben Sie dies bei jeder VM-Erstellung über customdata als cloud-init-Konfiguration. Dies ist die empfohlene Methode.
    * Verwenden Sie eine im Image integrierte cloud-init-Anweisung, die dies bei jeder VM-Erstellung durchführt.

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
          - ["ephemeral0.2", "none", "swap", "sw,nofail,x-systemd.requires=cloud-init.service", "0", "0"]
        EOF
        ```
1. Wenn Sie die Registrierung  des Abonnements aufheben möchten, führen Sie den folgenden Befehl aus:

    ```console
    # sudo subscription-manager unregister
    ```

1. Aufheben der Bereitstellung

    Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    > [!CAUTION]
    > Wenn Sie einen bestimmten virtuellen Computer migrieren, anstatt ein generalisiertes Image zu erstellen, überspringen Sie den Schritt zum Aufheben der Bereitstellung. Durch das Ausführen des Befehls `waagent -force -deprovision+user` wird der Quellcomputer unbrauchbar. Dieser Schritt ist nur für das Erstellen von generalisierten Images vorgesehen.
    ```console
    # sudo rm -f /var/log/waagent.log
    # sudo cloud-init clean
    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```
    

1. Klicken Sie im Hyper-V-Manager auf **Aktion** > **Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

### <a name="rhel-8-using-hyper-v-manager"></a>RHEL 8 mit dem Hyper-V-Manager

1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

1. Klicken Sie auf **Verbinden** , um ein Konsolenfenster für den virtuellen Computer zu öffnen.

1. Stellen Sie sicher, dass der Netzwerk-Managerdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. Konfigurieren Sie die Netzwerkschnittstelle so, dass sie beim Systemstart automatisch gestartet wird und DHCP verwendet:

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer GRUB-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden und die serielle Konsole zu aktivieren. 

    1. Entfernen der aktuellen GRUB-Parameter:
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. Bearbeiten Sie `/etc/default/grub` in einem Text-Editor, und fügen Sie die folgenden Parameter hinzu:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   Dadurch wird außerdem sichergestellt, dass alle Konsolennachrichten an den ersten seriellen Anschluss gesendet werden, und die Interaktion mit der seriellen Konsole ermöglicht, was den Azure Support bei Debugproblemen unterstützen kann. Außerdem werden bei dieser Konfiguration die neuen Benennungskonventionen für NICs deaktiviert.
   
   1. Außerdem empfehlen wir, die folgenden Parameter zu entfernen:

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Beachten Sie, dass die Menge an verfügbarem Arbeitsspeicher auf dem virtuellen Computer mit diesem Parameter um mindestens 128 MB reduziert wird. Dies kann bei kleineren virtuellen Computern problematisch sein.

1. Nachdem Sie die Bearbeitung von `/etc/default/grub`abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    Führen Sie für eine UEFI-fähige VM den folgenden Befehl aus:

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. Stellen Sie sicher, dass der SSH-Server installiert und so konfiguriert ist, dass er beim Booten hochfährt. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

    ```config
    ClientAliveInterval 180
    ```

1. Installieren Sie den Azure Linux Agent, cloud-init und andere notwendige Hilfsprogramme, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Konfigurieren von cloud-init zum Behandeln der Bereitstellung:

    1. Konfigurieren von waagent für cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Wenn Sie einen bestimmten virtuellen Computer migrieren, anstatt ein generalisiertes Image zu erstellen, legen Sie in der `/etc/waagent.conf`-Konfigurationsdatei `Provisioning.Agent=disabled` fest.
    
    1. Konfigurieren der Einbindungen:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Konfigurieren der Azure-Datenquelle:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF
    ```

    1. Falls sie konfiguriert ist, entfernen Sie die vorhandene Auslagerungsdatei:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Konfigurieren der cloud-init-Protokollierung:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. Auslagerungskonfiguration Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.

    Früher wurde der Azure Linux-Agent zum automatischen Konfigurieren des Auslagerungsbereichs mit dem lokalen Ressourcendatenträger verwendet, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Dies wird jetzt jedoch von cloud-init behandelt. Sie dürfen **nicht** den Linux-Agent zum Erstellen des Ressourcendatenträgers verwenden. Erstellen Sie die Auslagerungsdatei, und ändern Sie die folgenden Parameter in `/etc/waagent.conf` entsprechend:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    * Übergeben Sie dies bei jeder VM-Erstellung über customdata als cloud-init-Konfiguration. Dies ist die empfohlene Methode.
    * Verwenden Sie eine im Image integrierte cloud-init-Anweisung, die dies bei jeder VM-Erstellung durchführt.

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
          - ["ephemeral0.2", "none", "swap", "sw,nofail,x-systemd.requires=cloud-init.service", "0", "0"]
        EOF
        ```
1. Wenn Sie die Registrierung  des Abonnements aufheben möchten, führen Sie den folgenden Befehl aus:

    ```console
    # sudo subscription-manager unregister
    ```

1. Aufheben der Bereitstellung

    Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    ```console
    # sudo cloud-init clean
    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # sudo rm -f /var/log/waagent.log
    # export HISTSIZE=0
    # logout
    ```
    > [!CAUTION]
    > Wenn Sie einen bestimmten virtuellen Computer migrieren, anstatt ein generalisiertes Image zu erstellen, überspringen Sie den Schritt zum Aufheben der Bereitstellung. Durch das Ausführen des Befehls `waagent -force -deprovision+user` wird der Quellcomputer unbrauchbar. Dieser Schritt ist nur für das Erstellen von generalisierten Images vorgesehen.


1. Klicken Sie im Hyper-V-Manager auf **Aktion** > **Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.


## <a name="kvm"></a>KVM

In diesem Abschnitt erfahren Sie, wie Sie mit KVM eine [RHEL 6](#rhel-6-using-kvm)- oder [RHEL 7](#rhel-7-using-kvm)-Distribution für den Upload in Azure vorbereiten. 

### <a name="rhel-6-using-kvm"></a>RHEL 6 mit KVM

1. Laden Sie das KVM-Image von RHEL 6 von der Red Hat-Website herunter.

1. Legen Sie ein Stammkennwort fest.

    Generieren Sie ein verschlüsseltes Kennwort, und kopieren Sie die Ausgabe des Befehls:

    ```console
    # openssl passwd -1 changeme
    ```

    Legen Sie ein Stammkennwort mit Guestfish fest:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Ändern Sie im zweiten Feld des Stammbenutzers "!!" in das verschlüsselte Kennwort.

1. Erstellen Sie einen virtuellen Computer in KVM über das qcow2-Image. Legen Sie den Datenträgertyp auf **qcow2** und als Gerätemodell der virtuellen Netzwerkschnittstelle **virtio** fest. Starten Sie anschließend den virtuellen Computer, und melden Sie sich als Stammbenutzer an.

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Azure oder Hyper-V zu Problemen führen:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # chkconfig network on
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie für diese Konfiguration `/boot/grub/menu.lst` in einem Text-Editor, und stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.
    
    Neben den oben erwähnten Punkten ist es ratsam, die folgenden Parameter zu entfernen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Beachten Sie, dass die Menge an verfügbarem Arbeitsspeicher auf dem virtuellen Computer mit diesem Parameter um mindestens 128 MB reduziert wird. Dies kann bei kleineren virtuellen Computern problematisch sein.


1. Fügen Sie „initramfs“ Hyper-V-Module hinzu:  

    Bearbeiten Sie `/etc/dracut.conf`, und fügen Sie den folgenden Inhalt hinzu:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Erstellen Sie „initramfs“ neu:

    ```config-conf
    # dracut -f -v
    ```

1. Deinstallieren Sie die Cloud-Initialisierung:

    ```console
    # yum remove cloud-init
    ```

1. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Starten hochfährt:

    ```console
    # chkconfig sshd on
    ```

    Ändern Sie die Datei „/etc/ssh/sshd_config“, sodass sie die folgenden Zeilen enthält:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen:

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. Der Azure Linux-Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Ändern Sie nach dem Installieren des Azure Linux-Agents im vorherigen Schritt entsprechend die folgenden Parameter in **/etc/waagent.conf**:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

    ```console-conf
    # subscription-manager unregister
    ```

1. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. Fahren Sie den virtuellen Computer in KVM herunter.

1. Konvertieren Sie das qcow2-Image in das VHD-Format.

    > [!NOTE]
    > In den Versionen qemu-img-Versionen > oder = 2.2.1 taucht ein bekannter Bug auf, der zu Fehlern bei der Formatierung der VHD-führt. Dieses Problem wurde in QEMU 2.6 behoben. Sie sollten entweder qemu-img 2.2.0 oder niedriger verwenden oder auf 2.6 oder höher aktualisieren. Referenz: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Konvertieren Sie das Bild zuerst in das raw-Format:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Mit QEMU, Version **2.6+** , müssen Sie außerdem die Option `force_size` einschließen:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>RHEL 7 mit KVM

1. Laden Sie das KVM-Image von RHEL 7 von der Red Hat-Website herunter. Bei diesem Verfahren wird RHEL 7 als Beispiel verwendet.

1. Legen Sie ein Stammkennwort fest.

    Generieren Sie ein verschlüsseltes Kennwort, und kopieren Sie die Ausgabe des Befehls:

    ```console
    # openssl passwd -1 changeme
    ```

    Legen Sie ein Stammkennwort mit Guestfish fest:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Ändern Sie im zweiten Feld des Stammbenutzers "!!" in das verschlüsselte Kennwort.

1. Erstellen Sie einen virtuellen Computer in KVM über das qcow2-Image. Legen Sie den Datenträgertyp auf **qcow2** und als Gerätemodell der virtuellen Netzwerkschnittstelle **virtio** fest. Starten Sie anschließend den virtuellen Computer, und melden Sie sich als Stammbenutzer an.

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

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

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo systemctl enable network
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie für diese Konfiguration `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`. Beispiel:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Mit diesem Befehl wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Außerdem werden mit dem Befehl die neuen RHEL 7-Benennungskonventionen für Netzwerkkarten deaktiviert. Neben den oben erwähnten Punkten ist es ratsam, die folgenden Parameter zu entfernen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Beachten Sie, dass die Menge an verfügbarem Arbeitsspeicher auf dem virtuellen Computer mit diesem Parameter um mindestens 128 MB reduziert wird. Dies kann bei kleineren virtuellen Computern problematisch sein.

1. Nachdem Sie die Bearbeitung von `/etc/default/grub`abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Fügen Sie „initramfs“ Hyper-V-Module hinzu:

    Bearbeiten Sie `/etc/dracut.conf` , und fügen Sie Inhalt hinzu:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Erstellen Sie „initramfs“ neu:

    ```config-conf
    # dracut -f -v
    ```

1. Deinstallieren Sie die Cloud-Initialisierung:

    ```console
    # yum remove cloud-init
    ```

1. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Starten hochfährt:

    ```console
    # systemctl enable sshd
    ```

    Ändern Sie die Datei „/etc/ssh/sshd_config“, sodass sie die folgenden Zeilen enthält:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen:

    ```console
    # yum install WALinuxAgent
    ```

    Aktivieren Sie den Waagent-Dienst:

    ```console
    # systemctl enable waagent.service
    ```

1. Installieren von cloud-init Führen Sie Schritt 12, „Installieren von cloud-init zum Behandeln der Bereitstellung“ im Abschnitt zum Vorbereiten einer RHEL 7-VM mit dem Hyper-V-Manager aus.

1. Auslagerungskonfiguration 

    Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.
    Führen Sie Schritt 13, „Auslagerungskonfiguration“ im Abschnitt zum Vorbereiten einer RHEL 7-VM mit dem Hyper-V-Manager aus.


1. Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

    ```console
    # subscription-manager unregister
    ```


1. Aufheben der Bereitstellung

    Führen Sie Schritt 15, „Aufheben der Bereitstellung“ im Abschnitt zum Vorbereiten einer RHEL 7-VM mit dem Hyper-V-Manager aus.

1. Fahren Sie den virtuellen Computer in KVM herunter.

1. Konvertieren Sie das qcow2-Image in das VHD-Format.

    > [!NOTE]
    > In den Versionen qemu-img-Versionen > oder = 2.2.1 taucht ein bekannter Bug auf, der zu Fehlern bei der Formatierung der VHD-führt. Dieses Problem wurde in QEMU 2.6 behoben. Sie sollten entweder qemu-img 2.2.0 oder niedriger verwenden oder auf 2.6 oder höher aktualisieren. Referenz: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Konvertieren Sie das Bild zuerst in das raw-Format:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Mit QEMU, Version **2.6+** , müssen Sie außerdem die Option `force_size` einschließen:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

In diesem Abschnitt erfahren Sie, wie Sie eine [RHEL 6](#rhel-6-using-vmware)- oder [RHEL 7](#rhel-6-using-vmware)-Distribution über VMware vorbereiten.

### <a name="prerequisites"></a>Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits einen virtuellen Computer von RHEL in VMware installiert haben. Weitere Informationen zum Installieren eines Betriebssystems in VMware finden Sie im [VMware-Installationshandbuch für Gastbetriebssysteme](https://partnerweb.vmware.com/GOSIG/home.html).

* Beim Installieren des Linux-Betriebssystems wird empfohlen, anstelle von LVM – bei vielen Installationen oftmals voreingestellt – die Standardpartitionen zu verwenden. Bei diesem Verfahren werden LVM-Namenskonflikte mit geklonten virtuellen Computern vermieden. Dies gilt besonders, falls Sie einen Betriebssystem-Datenträger zur Problembehandlung mit einem anderen virtuellen Computer verbinden müssen. LVM oder RAID können bei Bedarf auf Datenträgern verwendet werden.
* Konfigurieren Sie auf dem Betriebssystem-Datenträger keine Swap-Partition. Sie können den Linux-Agent zur Erstellung einer Auslagerungsdatei auf dem temporären Ressourcendatenträger konfigurieren. Weitere Informationen hierzu finden Sie in den folgenden Schritten.
* Wählen Sie beim Erstellen der virtuellen Festplatte **Virtuellen Datenträger als einzelne Datei speichern** aus.

### <a name="rhel-6-using-vmware"></a>RHEL 6 mit VMware
1. In RHEL 6 kann NetworkManager zu Einschränkungen beim Azure Linux-Agent führen. Deinstallieren Sie dieses Paket, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Erstellen Sie eine Datei mit der Benennung **network** im Verzeichnis "/etc/sysconfig/", die den folgenden Text enthält:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Verschieben (oder entfernen) Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Azure oder Hyper-V zu Problemen führen:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo chkconfig network on
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie hierzu `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`. Beispiel:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Neben den oben erwähnten Punkten ist es ratsam, die folgenden Parameter zu entfernen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Beachten Sie, dass die Menge an verfügbarem Arbeitsspeicher auf dem virtuellen Computer mit diesem Parameter um mindestens 128 MB reduziert wird. Dies kann bei kleineren virtuellen Computern problematisch sein.

1. Fügen Sie „initramfs“ Hyper-V-Module hinzu:

    Bearbeiten Sie `/etc/dracut.conf`, und fügen Sie den folgenden Inhalt hinzu:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Erstellen Sie „initramfs“ neu:

    ```config-cong
    # dracut -f -v
    ```

1. Stellen Sie sicher, dass der SSH-Server installiert und so konfiguriert ist, dass er beim Booten hochfährt. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

    ```config
    ClientAliveInterval 180
    ```

1. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.

    Der Azure Linux-Agent kann den Auslagerungsbereich automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung des virtuellen Computers in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein temporärer Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents im vorherigen Schritt die folgenden Parameter in `/etc/waagent.conf` entsprechend an:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Heben Sie das Abonnement (falls erforderlich) auf, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo subscription-manager unregister
    ```

1. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. Fahren Sie den virtuellen Computer herunter, und konvertieren Sie die VMDK-Datei in eine VHD-Datei.

    > [!NOTE]
    > In den Versionen qemu-img-Versionen > oder = 2.2.1 taucht ein bekannter Bug auf, der zu Fehlern bei der Formatierung der VHD-führt. Dieses Problem wurde in QEMU 2.6 behoben. Sie sollten entweder qemu-img 2.2.0 oder niedriger verwenden oder auf 2.6 oder höher aktualisieren. Referenz: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Konvertieren Sie das Bild zuerst in das raw-Format:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Mit QEMU, Version **2.6+** , müssen Sie außerdem die Option `force_size` einschließen:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>RHEL 7 mit VMware
1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

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

1. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo systemctl enable network
    ```

1. Registrieren Sie mit dem folgenden Befehl das Red Hat-Abonnement, um die Installation von Paketen aus dem RHEL-Repository zu ermöglichen:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie für diese Änderung `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`. Beispiel:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Durch diese Konfiguration wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Außerdem werden die neuen RHEL 7-Benennungskonventionen für Netzwerkkarten deaktiviert. Neben den oben erwähnten Punkten ist es ratsam, die folgenden Parameter zu entfernen:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Sie können die Option `crashkernel` bei Bedarf konfiguriert lassen. Beachten Sie, dass die Menge an verfügbarem Arbeitsspeicher auf dem virtuellen Computer mit diesem Parameter um mindestens 128 MB reduziert wird. Dies kann bei kleineren virtuellen Computern problematisch sein.

1. Nachdem Sie die Bearbeitung von `/etc/default/grub`abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der GRUB-Konfiguration aus:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Fügen Sie „initramfs“ Hyper-V-Module hinzu.

    Bearbeiten Sie `/etc/dracut.conf`und fügen Sie Inhalt hinzu:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Erstellen Sie „initramfs“ neu:

    ```console
    # dracut -f -v
    ```

1. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt. Dies ist normalerweise die Standardeinstellung. Ergänzen Sie `/etc/ssh/sshd_config` um die folgende Zeile:

    ```config
    ClientAliveInterval 180
    ```

1. Das WALinuxAgent-Paket `WALinuxAgent-<version>` wurde in das Red Hat Extras-Repository übertragen. Aktivieren Sie das Extras-Repository, indem Sie den folgenden Befehl ausführen:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installieren Sie den Azure Linux-Agent, indem Sie den folgenden Befehl ausführen:

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. Installieren von cloud-init

    Führen Sie Schritt 12, „Installieren von cloud-init zum Behandeln der Bereitstellung“ im Abschnitt zum Vorbereiten einer RHEL 7-VM mit dem Hyper-V-Manager aus.

1. Auslagerungskonfiguration

    Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.
    Führen Sie Schritt 13, „Auslagerungskonfiguration“ im Abschnitt zum Vorbereiten einer RHEL 7-VM mit dem Hyper-V-Manager aus.

1. Wenn Sie die Registrierung  des Abonnements aufheben möchten, führen Sie den folgenden Befehl aus:

    ```console
    # sudo subscription-manager unregister
    ```

1. Aufheben der Bereitstellung

    Führen Sie Schritt 15, „Aufheben der Bereitstellung“ im Abschnitt zum Vorbereiten einer RHEL 7-VM mit dem Hyper-V-Manager aus.


1. Fahren Sie den virtuellen Computer herunter, und konvertieren Sie die VMDK-Datei in das VHD-Format.

    > [!NOTE]
    > In den Versionen qemu-img-Versionen > oder = 2.2.1 taucht ein bekannter Bug auf, der zu Fehlern bei der Formatierung der VHD-führt. Dieses Problem wurde in QEMU 2.6 behoben. Sie sollten entweder qemu-img 2.2.0 oder niedriger verwenden oder auf 2.6 oder höher aktualisieren. Referenz: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Konvertieren Sie das Bild zuerst in das raw-Format:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    Stellen Sie sicher, dass das RAW-Image auf 1 MB ausgerichtet ist. Runden Sie andernfalls die Größe auf 1 MB auf:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Konvertieren Sie den RAW-Datenträger in das VHD-Format mit festgelegter Größe:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Mit QEMU, Version **2.6+** , müssen Sie außerdem die Option `force_size` einschließen:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>Kickstart-Datei

In diesem Abschnitt erfahren Sie, wie Sie mit einer Kickstart-Datei eine RHEL 7-Distribution aus einem ISO-Image vorbereiten.

### <a name="rhel-7-from-a-kickstart-file"></a>RHEL 7 mit einer Kickstart-Datei

1.  Erstellen Sie eine Kickstart-Datei mit dem folgenden Inhalt, und speichern Sie die Datei. Weitere Informationen zur Kickstart-Installation finden Sie im [Kickstart-Installationshandbuch](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/installation_guide/chap-kickstart-installations).

    ```text
    # Kickstart for provisioning a RHEL 7 Azure VM

    # System authorization information
      auth --enableshadow --passalgo=sha512

    # Use graphical install
    text

    # Do not run the Setup Agent on first boot
    firstboot --disable

    # Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    # System language
    lang en_US.UTF-8

    # Network information
    network  --bootproto=dhcp

    # Root password
    rootpw --plaintext "to_be_disabled"

    # System services
    services --enabled="sshd,waagent,NetworkManager"

    # System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    # Partition clearing information
    clearpart --all --initlabel

    # Clear the MBR
    zerombr

    # Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    # System bootloader configuration
    bootloader --location=mbr

    # Firewall configuration
    firewall --disabled

    # Enable SELinux
    selinux --enforcing

    # Don't configure X
    skipx

    # Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    # Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    # Install latest repo update
    yum update -y

    # Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    # Install WALinuxAgent
    yum install -y WALinuxAgent

    # Unregister Red Hat subscription
    subscription-manager unregister

    # Enable waaagent at boot-up
    systemctl enable waagent

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
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
    - ["ephemeral0.2", "none", "swap", "sw,nofail,x-systemd.requires=cloud-init.service", "0", "0"]
    EOF

    # Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    # Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    # Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    # Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

1. Legen Sie die Kickstart-Datei an einem Speicherort ab, an dem das Installationssystem darauf zugreifen kann.

1. Erstellen Sie in Hyper-V Manager einen neuen virtuellen Computer. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** die Option **Virtuelle Festplatte später zuordnen**, und schließen Sie den Assistenten für neue virtuelle Computer ab.

1. Öffnen Sie die VM-Einstellungen:

    a.  Ordnen Sie dem virtuellen Computer eine neue virtuelle Festplatte zu. Wählen Sie **VHD-Format** und **Feste Größe** aus.

    b.  Ordnen Sie die ISO-Installationsdatei dem DVD-Laufwerk zu.

    c.  Legen Sie fest, dass BIOS von der CD starten soll.

1. Starten Sie den virtuellen Computer. Wenn die Installationsanweisungen angezeigt werden, drücken Sie die **TAB-TASTE** , um die Startoptionen zu konfigurieren.

1. Geben Sie am Ende der Startoptionen `inst.ks=<the location of the kickstart file>` ein und drücken Sie die **EINGABETASTE**.

1. Warten Sie, bis die Installation abgeschlossen ist. Wenn sie abgeschlossen ist, wird der virtuelle Computer automatisch heruntergefahren. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## <a name="known-issues"></a>Bekannte Probleme
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Der Hyper-V-Treiber konnte dem anfänglichen RAM-Datenträger nicht hinzugefügt werden, wenn ein anderer Hypervisor als ein Hyper-V-Hypervisor verwendet wurde.

In einigen Fällen enthalten Linux-Installationsprogramme unter Umständen keine Treiber für Hyper-V auf dem anfänglichen RAM-Datenträger („initrd“ oder „initramfs“), sofern von Linux nicht die Ausführung in einer Hyper-V-Umgebung erkannt wird.

Wenn ein anderes Virtualisierungssystem (z. B. VirtualBox, Xen usw.) zur Vorbereitung des Linux-Image verwendet wird, müssen Sie initrd unter Umständen neu erstellen, um sicherzustellen, dass mindestens die Kernelmodule hv_vmbus und hv_storvsc auf dem anfänglichen RAM-Datenträger verfügbar sind. Dies ist zumindest auf Systemen, die auf der Red Hat-Upstream-Verteilung basieren, ein bekanntes Problem.

Um dieses Problem zu beheben, müssen Sie „initramfs“ Hyper-V-Module hinzufügen und das Archiv neu erstellen:

Bearbeiten Sie `/etc/dracut.conf`, und fügen Sie den folgenden Inhalt hinzu:

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

Erstellen Sie „initramfs“ neu:

```console
# dracut -f -v
```

Weitere Informationen finden Sie unter [Neuerstellung von „initramfs“](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Nächste Schritte
* Sie können jetzt mit Ihrer Red Hat Enterprise Linux-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).
* Weitere Informationen zu den Hypervisoren, die zum Ausführen von Red Hat Enterprise Linux zertifiziert sind, finden Sie auf der [Red Hat-Website](https://access.redhat.com/certified-hypervisors).
* Weitere Informationen zur Verwendung von produktionsfähigen RHEL-BYOS-Images finden Sie auf der Dokumentationsseite zu [BYOS](../workloads/redhat/byos.md).
