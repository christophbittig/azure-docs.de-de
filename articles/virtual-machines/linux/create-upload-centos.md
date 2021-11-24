---
title: Erstellen und Hochladen einer CentOS-basierten Linux-VHD
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein CentOS-basiertes Linux-Betriebssystem enthält.
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/10/2021
ms.author: srijangupta
ms.openlocfilehash: 702b57ed728323cfc9ca11dfbac1be785a580c03
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289317"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Vorbereiten eines CentOS-basierten virtuellen Computers für Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein CentOS-basiertes Linux-Betriebssystem enthält.

* [Vorbereiten eines virtuellen CentOS 6.x-Computers für Azure](#centos-6x)
* [Vorbereiten eines virtuellen CentOS 7.0+-Computers für Azure](#centos-70)


## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie bereits ein CentOS-Linux-Betriebssystem (oder eine ähnliche Ableitung) auf einer virtuellen Festplatte installiert haben. Sie können VHD-Dateien mit unterschiedlichen Tools erstellen, beispielsweise mit einer Virtualisierungslösung wie Hyper-V. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Installationshinweise zu CentOS**

* Beachten Sie auch den Artikelabschnitt [Allgemeine Linux-Systemanforderungen](create-upload-generic.md#general-linux-installation-notes), in dem weitere Tipps zur Vorbereitung von Linux für Azure enthalten sind.
* Das VHDX-Format wird in Azure noch nicht unterstützt, dafür jedoch **virtuelle Festplatten mit fester Größe**.  Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren. Wenn Sie VirtualBox verwenden, bedeutet dies, dass Sie **Feste Größe** auswählen und nicht die Standardeinstellung, die dynamisch beim Erstellen des Datenträgers zugewiesen wird.
* Beim Installieren des Linux-Systems wird *empfohlen*, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen identischen virtuellen Computer verbunden wird. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) oder [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) können auf Datenträgern verwendet werden.
* **Kernel-Unterstützung für bereitgestellte UDF-Dateisysteme ist erforderlich.** Beim ersten Start in Azure wird die Bereitstellungskonfiguration über UDF-formatierte Medien, die an den Gast angefügt sind, an den virtuellen Linux-Computer übergeben. Der Azure-Linux-Agent muss das UDF-Dateisystem bereitstellen können, um dessen Konfiguration zu lesen und die VM bereitzustellen.
* Linux-Kernelversionen unter 2.6.37 unterstützen keine NUMA auf Hyper-V mit größeren VM-Größen. Dieses Problem betrifft in erster Linie ältere Verteilungen, die den vorgeschalteten Red Hat 2.6.32 Kernel verwenden, und wurde in RHEL 6.6 (Kernel 2.6.32-504) behoben. Systeme, auf denen benutzerdefinierte Kernel ausgeführt werden, die älter als 2.6.37 sind, bzw. RHEL-basierte Kernel, die älter als 2.6.32-504 sind, müssen den Startparameter `numa=off` in der Kernel-Befehlszeile auf „grub.conf“ festlegen. Weitere Informationen finden Sie unter Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Weitere Informationen dazu finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Beim Konvertieren von einem unformatierten Datenträger in VHD müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers ein Vielfaches von 1 MB vor der Konvertierung beträgt. Weitere Informationen finden Sie in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).

## <a name="centos-6x"></a>CentOS 6.x

1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2. Klicken Sie auf **Verbinden** , um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3. In CentOS 6 kann NetworkManager zu Einschränkungen beim Azure Linux-Agent führen. Deinstallieren Sie dieses Paket, indem Sie den folgenden Befehl ausführen:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Ändern Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle(n) zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Stellen Sie sicher, dass der Netzwerkdienst beim Booten startet, indem Sie den folgenden Befehl ausführen:

    ```bash
    sudo chkconfig network on
    ```

8. Wenn Sie die in Azure-Rechenzentren gehosteten OpenLogic-Datenspiegel verwenden möchten, ersetzen Sie die Datei `/etc/yum.repos.d/CentOS-Base.repo` durch die folgenden Repositorys.  Dadurch wird auch das Repository **[openlogic]** hinzugefügt, das zusätzliche Pakete wie etwa den Azure Linux-Agent enthält:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > Im Rest dieses Leitfadens wird davon ausgegangen, dass Sie mindestens das Repository `[openlogic]` verwenden, das im Folgenden zum Installieren des Azure Linux-Agents verwendet wird.

9. Fügen Sie die folgende Zeile zu "/etc/yum.conf" hinzu:

    ```console
    http_caching=packages
    ```

10. Führen Sie den folgenden Befehl aus, um die aktuellen yum-Metadaten zu löschen und das System durch Installation der neuesten Pakete zu aktualisieren:

    ```bash
    yum clean all
    ```

    Dabei empfiehlt es sich, alle Pakete auf die neueste Version zu aktualisieren (dies gilt nicht, wenn Sie ein Image für eine ältere CentOS-Version erstellen):

    ```bash
    sudo yum -y update
    ```

    Nach dem Ausführen dieses Befehls ist unter Umständen ein Neustart erforderlich.

11. (Optional) Installieren Sie die Treiber für die Linux-Integrationsdienste (LIS).

    > [!IMPORTANT]
    > Dieser Schritt ist für CentOS 6.3 und früher **erforderlich**, für spätere Versionen ist er optional.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Alternativ können Sie auch die Anweisungen für die manuelle Installation auf der [Downloadseite von LIS](https://www.microsoft.com/download/details.aspx?id=55106) ausführen, um das RPM auf Ihrem virtuellen Computer zu installieren.

12. Installieren Sie den Azure Linux-Agent und die Abhängigkeiten. waagent-Dienst starten und aktivieren:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Das WALinuxAgent-Paket entfernt die NetworkManager- und NetworkManager-gnome-Pakete, sofern sie nicht bereits wie in Schritt 3 beschrieben entfernt wurden.

13. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu `/boot/grub/menu.lst` in einem Text-Editor, und stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.

    Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen* :

    ```console
    rhgb quiet crashkernel=auto
    ```

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen.  Die Option `crashkernel` kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

    > [!Important]
    > CentOS 6.5 und früher muss auch den Kernelparameter `numa=off` festlegen. Siehe dazu Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.

15. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.

    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Passen Sie nach dem Installieren des Azure Linux-Agents (siehe vorheriger Schritt) die folgenden Parameter in `/etc/waagent.conf` an:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre virtuelle Linux-Festplatte kann nun [in Azure hochgeladen](/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd) werden.


## <a name="centos-70"></a>CentOS 7.0+

**Änderungen in CentOS 7 (und ähnlichen Ableitungen)**

Die Vorbereitung eines virtuellen CentOS 7-Computers für Azure entspricht in etwa der für CentOS 6. Es gibt jedoch einige wichtige Unterschiede:

* Das NetworkManager-Paket führt nicht mehr zu Konflikten mit dem Azure Linux Agent. Dieses Paket ist standardmäßig installiert, und Sie sollten es nicht entfernen.
* GRUB2 wird nun als standardmäßiger Bootloader verwendet. Daher hat sich die Prozedur für das Bearbeiten der Kernelparameter geändert (siehe unten).
* XFS ist nun das Standarddateisystem. Das EXT4-Dateisystem kann bei Bedarf weiterhin verwendet werden.

**Konfigurationsschritte**

1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.

2. Klicken Sie auf **Verbinden** , um ein Konsolenfenster für den virtuellen Computer zu öffnen.

3. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network`, und fügen Sie ihr den folgenden Text hinzu:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Erstellen oder bearbeiten Sie die Datei `/etc/sysconfig/network-scripts/ifcfg-eth0`, und fügen Sie ihr den folgenden Text hinzu:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Ändern Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle(n) zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Wenn Sie die in Azure-Rechenzentren gehosteten OpenLogic-Datenspiegel verwenden möchten, ersetzen Sie die Datei `/etc/yum.repos.d/CentOS-Base.repo` durch die folgenden Repositorys.  Dadurch wird auch das Repository **[openlogic]** hinzugefügt, das Pakete für den Azure Linux-Agent enthält:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > Im Rest dieses Leitfadens wird davon ausgegangen, dass Sie mindestens das Repository `[openlogic]` verwenden, das im Folgenden zum Installieren des Azure Linux-Agents verwendet wird.

7. Führen Sie den folgenden Befehl aus, um die aktuellen yum-Metadaten zu löschen und um Updates zu installieren.

    ```bash
    sudo yum clean all
    ```

    Dabei empfiehlt es sich, alle Pakete auf die neueste Version zu aktualisieren (dies gilt nicht, wenn Sie ein Image für eine ältere CentOS-Version erstellen):

    ```bash
    sudo yum -y update
    ```

    Nach dem Ausführen dieses Befehls ist unter Umständen ein Neustart erforderlich.

8. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dazu `/etc/default/grub` in einem Text-Editor, und bearbeiten Sie den Parameter `GRUB_CMDLINE_LINUX`. Beispiel:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Dadurch wird zudem sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Außerdem werden die neuen CentOS 7-Benennungskonventionen für Netzwerkkarten deaktiviert. Neben den oben erwähnten Punkten wird es empfohlen, die folgenden Parameter zu *entfernen* :

    ```console
    rhgb quiet crashkernel=auto
    ```

    Weder der Graphical Boot noch der Quiet Boot sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die Option `crashkernel` kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um 128 MB oder mehr reduziert, was bei kleineren virtuellen Computern problematisch sein kann.

9. Sobald Sie die oben beschriebene Bearbeitung von `/etc/default/grub` abgeschlossen haben, führen Sie den folgenden Befehl zum erneuten Erstellen der Grub-Konfiguration aus:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Wenn Sie das Image aus **VMware, VirtualBox oder KVM** erstellen: Stellen Sie sicher, dass die Hyper-V-Treiber in „initramfs“ enthalten sind:

    Bearbeiten Sie `/etc/dracut.conf`und fügen Sie Inhalt hinzu:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Erstellen Sie initramfs neu:

    ```bash
    sudo dracut -f -v
    ```

11. Installieren Sie den Azure Linux-Agent und die Abhängigkeiten für Azure-VM-Erweiterungen:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Installieren von cloud-init zum Behandeln der Bereitstellung

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


13. Auslagerungskonfiguration Erstellen Sie auf dem Betriebssystem-Datenträger keinen Auslagerungsbereich.

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

14. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

     **Hinweis:** Wenn Sie einen bestimmten virtuellen Computer migrieren und kein generalisiertes Image erstellen wollen, überspringen Sie den Schritt zum Aufheben der Bereitstellung.

    ```console
    # sudo rm -f /var/log/waagent.log
    # sudo cloud-init clean
    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

15. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre virtuelle Linux-Festplatte kann nun [in Azure hochgeladen](/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd) werden.

## <a name="next-steps"></a>Nächste Schritte

Sie können jetzt mit Ihrer CentOS-Linux-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).
