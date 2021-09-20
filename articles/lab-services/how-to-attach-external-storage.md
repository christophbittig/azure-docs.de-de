---
title: Verwenden von externem Dateispeicher in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Lab einrichten, das externen Dateispeicher in Lab Services verwendet.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: dc0f2a4f51fb12c61d0e1e16cb23d030a5dc9cc6
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969273"
---
# <a name="use-external-file-storage-in-lab-services"></a>Verwenden von externem Dateispeicher in Lab Services

In diesem Artikel werden einige der Optionen für externen Dateispeicher behandelt, wenn Sie Azure Lab Services verwenden. [Azure Files](https://azure.microsoft.com/services/storage/files/) bietet vollständig verwaltete Dateifreigaben in der Cloud, [auf die über SMB 2.1. und SMB 3.0 zugegriffen werden kann](../storage/files/storage-how-to-use-files-windows.md). Eine Azure Files-Freigabe kann entweder öffentlich oder privat innerhalb eines virtuellen Netzwerks verbunden werden. Sie können die Freigabe auch so konfigurieren, dass die Active Directory-Anmeldeinformationen eines Kursteilnehmers zum Herstellen einer Verbindung mit der Dateifreigabe verwendet werden. Wenn Sie auf einem Linux-Computer arbeiten, können Sie auch Azure NetApp Files mit NFS-Volumes für externen Dateispeicher mit Azure Lab Services verwenden.  

## <a name="which-solution-to-use"></a>Zu verwendende Lösung

Jede Lösung hat unterschiedliche Anforderungen und Funktionen. In der folgenden Tabelle werden wichtige Punkte aufgeführt, die bei jeder Lösung zu beachten sind.  

|     Lösung    |    Wichtige Information    |
| -------------- | ------------------------ |
| [Azure Files-Freigabe mit öffentlichem Endpunkt](#azure-files-share) | <ul><li>Jeder verfügt über Lese-/Schreibzugriff.</li><li>Es ist kein Peering virtueller Netzwerke erforderlich.</li><li>Zugriff für alle virtuellen Computer möglich, nicht nur für virtuelle Lab-Computer.</li><li>Bei Verwendung von Linux haben Kursteilnehmer Zugriff auf den Speicherkontoschlüssel.</li></ul> |
| [Azure Files-Freigabe mit privatem Endpunkt](#azure-files-share) | <ul><li>Jeder verfügt über Lese-/Schreibzugriff.</li><li>Peering virtueller Netzwerke ist erforderlich.</li><li>Zugriff nur für VMs im selben Netzwerk (oder Peernetzwerk) wie das Speicherkonto möglich.</li><li>Bei Verwendung von Linux haben Kursteilnehmer Zugriff auf den Speicherkontoschlüssel.</li></ul> |
| [Azure Files mit identitätsbasierter Autorisierung](#azure-files-with-identity-based-authorization) | <ul><li>Entweder Lese- oder Lese-/Schreibzugriffsberechtigungen können für einen Ordner oder eine Datei festgelegt werden.</li><li>Peering virtueller Netzwerke ist erforderlich.</li><li>Das Speicherkonto muss mit Active Directory verbunden sein.</li><li>Lab-VMs müssen in eine Domäne eingebunden sein.</li><li>Der Speicherkontoschlüssel wird für Kursteilnehmer nicht zum Herstellen einer Verbindung mit der Dateifreigabe verwendet.</li></ul> |
| [NetApp Files mit NFS-Volumes](#netapp-files-with-nfs-volumes) | <ul><li>Entweder Lese- oder Lese-/Schreibzugriff kann für Volumes festgelegt werden.</li><li>Berechtigungen werden mithilfe der IP-Adresse der VM des Kursteilnehmers festgelegt.</li><li>Peering virtueller Netzwerke ist erforderlich.</li><li>Sie müssen sich möglicherweise für die Verwendung des NetApp Files-Diensts registrieren.</li><li>Nur Linux.</li></ul>

Die Kosten für die Verwendung von externem Speicher sind nicht in den Kosten für die Verwendung von Azure Lab Services enthalten.  Weitere Informationen zu den Preisen finden Sie unter [Azure Files – Preise](https://azure.microsoft.com/pricing/details/storage/files/) und [Azure NetApp Files – Preise](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Azure Files-Freigabe

Der Zugriff auf Azure Files-Freigaben erfolgt über einen öffentlichen oder privaten Endpunkt. Verwenden Sie den Speicherkontoschlüssel als Kennwort, um die Freigaben einzubinden. Bei diesem Ansatz besitzt jeder Lese-/Schreibzugriff auf die Dateifreigabe.

Wenn Sie einen öffentlichen Endpunkt für die Azure Files-Freigabe verwenden, ist es wichtig, Folgendes zu beachten:

- Es muss kein Peering des virtuellen Netzwerks für das Speicherkonto mit dem Lab-Konto erfolgen.  Die Dateifreigabe kann jederzeit vor der Veröffentlichung der Vorlage für virtuelle Computer erstellt werden.
- Der Zugriff auf die Dateifreigabe kann von jedem beliebigen Computer aus erfolgen, wenn ein Benutzer über den Speicherkontoschlüssel verfügt.  
- Linux-Kursteilnehmer können den Speicherkontoschlüssel sehen. Anmeldeinformationen für das Einbinden einer Azure Files-Freigabe werden auf Linux-VMs in `{file-share-name}.cred` gespeichert und können von sudo gelesen werden. Da Kursteilnehmer standardmäßig sudo-Zugriff auf Azure Lab Services-VMs erhalten, können Sie den Speicherkontoschlüssel lesen. Wenn der Endpunkt des Speicherkontos öffentlich ist, können Kursteilnehmer Zugriff auf die Dateifreigabe außerhalb Ihres virtuellen Kursteilnehmer-Computers erhalten. Erwägen Sie, den Speicherkontoschlüssel im Anschluss an das Ende des Kurses zu rotieren und private Dateifreigaben zu verwenden.

Wenn Sie einen privaten Endpunkt für die Azure Files-Freigabe verwenden, ist es wichtig, Folgendes zu beachten:

- Der Zugriff ist auf Datenverkehr beschränkt, der aus dem privaten Netzwerk stammt, und es kann den nicht über das öffentliche Internet zugegriffen werden. Nur VMs im privaten virtuellen Netzwerk, VMs in einem Netzwerk, das mit dem privaten virtuellen Netzwerk gepeert ist, oder Computer, die mit einem VPN für das private Netzwerk verbunden sind, können auf die Dateifreigabe zugreifen.  
- Linux-Kursteilnehmer können den Speicherkontoschlüssel sehen. Anmeldeinformationen für das Einbinden einer Azure Files-Freigabe werden auf Linux-VMs in `{file-share-name}.cred` gespeichert und können von sudo gelesen werden. Da Kursteilnehmer standardmäßig sudo-Zugriff auf Azure Lab Services-VMs erhalten, können Sie den Speicherkontoschlüssel lesen. Erwägen Sie, den Speicherkontoschlüssel im Anschluss an das Ende des Kurses zu rotieren.
- Dieser Ansatz erfordert, dass das virtuelle Netzwerk der Dateifreigabe mit dem Lab-Konto gepeert ist. Das virtuelle Netzwerk für das Azure Storage-Konto muss mit dem virtuellen Netzwerk für das Lab-Konto gepeert werden, bevor das Lab erstellt wird.

> [!NOTE]
> Standardmäßig können Standarddateifreigaben einen Umfang von bis zu 5 TiB haben. Informationen zur Erstellung von Dateifreigaben mit einem Umfang von bis zu 100 TiB finden Sie unter [Erstellen einer Azure-Dateifreigabe](../storage/files/storage-how-to-create-file-share.md).

Führen Sie die folgenden Schritte aus, um eine mit einer Azure-Dateifreigabe verbundene VM zu erstellen.

1. Erstellen Sie ein [Azure Storage-Konto](../storage/files/storage-how-to-create-file-share.md). Wählen Sie auf der Seite **Verbindungsmethode** die Option **öffentlicher Endpunkt** oder **privater Endpunkt** aus.
2. Wenn Sie die private Methode ausgewählt haben, erstellen Sie einen [privaten Endpunkt](../private-link/tutorial-private-endpoint-storage-portal.md), damit aus dem virtuellen Netzwerk auf die Dateifreigaben zugegriffen werden kann.
3. [Erstellen Sie eine Azure-Dateifreigabe](../storage/files/storage-how-to-create-file-share.md). Die Dateifreigabe ist bei Verwendung eines öffentlichen Endpunkts über den öffentlichen Hostnamen des Speicherkontos erreichbar.  Die Dateifreigabe ist bei Verwendung eines privaten Endpunkts über die private IP-Adresse erreichbar.  
4. Binden Sie die Azure-Dateifreigabe in die Vorlage für virtuelle Computer ein:
    - [Windows](../storage/files/storage-how-to-use-files-windows.md)
    - [Linux](../storage/files/storage-how-to-use-files-linux.md). Weitere Informationen zum Vermeiden von Problemen auf VMs von Kursteilnehmer*innen finden Sie im Abschnitt [Verwenden von Azure Files mit Linux](#use-azure-files-with-linux).
5. [Veröffentlichen](how-to-create-manage-template.md#publish-the-template-vm) Sie die Vorlage für virtuelle Computer.

> [!IMPORTANT]
> Stellen Sie sicher, dass Windows Defender Firewall die ausgehende SMB-Verbindung über Port 445 nicht blockiert. SMB ist standardmäßig für Azure-VMs zulässig.

### <a name="use-azure-files-with-linux"></a>Verwenden von Azure Files mit Linux

Wenn Sie die Standardanweisungen zum Einbinden einer Azure Files-Freigabe verwenden, scheint die Dateifreigabe auf VMs von Kursteilnehmern zu verschwinden, nachdem die Vorlage veröffentlicht wurde. Mit dem folgenden geänderten Skript wird dieses Problem behoben.  

Für Dateifreigaben mit einem öffentlichen Endpunkt:
```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Für Dateifreigaben mit einem privaten Endpunkt:
```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_ip=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_ip/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Wenn die Vorlagen-VM, die die Azure Files-Freigabe in das Verzeichnis `/mnt` einbindet, bereits veröffentlicht ist, kann der Kursteilnehmer eine der folgenden Aktionen ausführen:

- Verschieben Sie die Anweisung zum Einbinden von `/mnt` den Anfang der Datei `/etc/fstab`.  
- Ändern Sie die Anweisung zum Einbinden von `/mnt/{file-share-name}` in ein anderes Verzeichnis, z. B. in `/prm-mnt/{file-share-name}`.

Kursteilnehmer sollten `mount -a` ausführen, um Verzeichnisse erneut einzubinden.

Allgemeinere Informationen finden Sie unter [Verwenden von Azure Files mit Linux](../storage/files/storage-how-to-use-files-linux.md).

## <a name="azure-files-with-identity-based-authorization"></a>Azure Files mit identitätsbasierter Autorisierung

Auf Azure Files-Freigaben kann auch über Active Directory-Authentifizierung zugegriffen werden, wenn folgende Bedingungen erfüllt sind:

- Die VM des Kursteilnehmers ist in eine Domäne eingebunden.
- Active Directory-Authentifizierung ist für das [Azure Storage-Konto aktiviert](../storage/files/storage-files-active-directory-overview.md), das die Dateifreigabe hostet.  

Das Netzwerklaufwerk wurde unter Verwendung der Identität des Benutzers auf dem virtuellen Computer eingebunden, nicht mit dem Schlüssel für das Speicherkonto. Öffentliche oder private Endpunkte bieten Zugriff auf das Speicherkonto.

Berücksichtigen Sie dabei die folgenden wichtigen Punkte:

- Berechtigungen können auf Verzeichnis- oder Dateiebene festgelegt werden.
- Die aktuellen Benutzeranmeldeinformationen können verwendet werden, um sich bei der Dateifreigabe zu authentifizieren.

Für einen öffentlichen Endpunkt muss kein Peering des virtuellen Netzwerks für das Speicherkonto mit dem Lab-Konto ausgeführt werden. Die Dateifreigabe kann jederzeit vor der Veröffentlichung der VM-Vorlage erstellt werden.

Für einen privaten Endpunkt:

- Der Zugriff ist auf Datenverkehr beschränkt, der aus dem privaten Netzwerk stammt, und es kann den nicht über das öffentliche Internet zugegriffen werden. Nur VMs im privaten virtuellen Netzwerk, VMs in einem Netzwerk, das mit dem privaten virtuellen Netzwerk gepeert ist, oder Computer, die mit einem VPN für das private Netzwerk verbunden sind, können auf die Dateifreigabe zugreifen.  
- Dieser Ansatz erfordert, dass das virtuelle Netzwerk der Dateifreigabe mit dem Lab-Konto gepeert ist. Das virtuelle Netzwerk für das Azure Storage-Konto muss mit dem virtuellen Netzwerk für das Lab-Konto gepeert werden, bevor das Lab erstellt wird.

Gehen Sie wie folgt vor, um eine Azure Files-Freigabe zu erstellen, die für Active Directory-Authentifizierung aktiviert ist, und um die Lab-VMs in die Domäne einzubinden:

1. Erstellen Sie ein [Azure Storage-Konto](../storage/files/storage-how-to-create-file-share.md).
2. Wenn Sie die private Methode ausgewählt haben, erstellen Sie einen [privaten Endpunkt](../private-link/tutorial-private-endpoint-storage-portal.md), damit aus dem virtuellen Netzwerk auf die Dateifreigaben zugegriffen werden kann. Erstellen Sie eine [private DNS-Zone](../dns/private-dns-privatednszone.md), oder verwenden Sie eine vorhandene Zone. Private Azure DNS-Zonen bieten Namensauflösung in einem virtuellen Netzwerk.
3. [Erstellen Sie eine Azure-Dateifreigabe](../storage/files/storage-how-to-create-file-share.md).
4. Führen Sie die Schritte zum Aktivieren von identitätsbasierter Autorisierung aus. Wenn Sie Active Directory lokal verwenden und eine Synchronisierung mit Azure Active Directory (Azure AD) ausführen, finden Sie weitere Informationen unter [Lokale Active Directory Domain Services-Authentifizierung über SMB für Azure Files-Freigaben](../storage/files/storage-files-identity-auth-active-directory-enable.md). Wenn Sie nur Azure AD verwenden, finden Sie weitere Informationen unter [Aktivieren der Azure Active Directory Domain Services-Authentifizierung für Azure Files](../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md).
    >[!IMPORTANT]
    >Sprechen Sie mit dem Team, das Ihre Active Directory-Instanz verwaltet, um sicherzustellen, dass alle in den Anweisungen aufgeführten Voraussetzungen erfüllt sind.
5. Weisen Sie Rollen für die SMB-Freigabeberechtigung in Azure zu. Ausführliche Informationen zu den Berechtigungen, die jeder Rolle gewährt werden, finden Sie unter [Berechtigungen auf Freigabeebene](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).
    - Die Rolle **Mitwirkender mit erhöhten Rechten für Speicherdateidaten-SMB-Freigabe** muss der Person oder Gruppe zugewiesen werden, die Berechtigungen für den Inhalt der Dateifreigabe einrichtet.
    - Die Rolle **Mitwirkender für Speicherdateidaten-SMB-Freigabe** sollte Kursteilnehmern zugewiesen werden, die Dateien auf der Dateifreigabe hinzufügen oder bearbeiten müssen.
    - Die Rolle **Leser für Speicherdateidaten-SMB-Freigabe** sollte Kursteilnehmern zugewiesen werden, die die Dateien auf der Dateifreigabe nur lesen müssen.
6. Richten Sie Berechtigungen auf Verzeichnis- und/oder Dateiebene für die Dateifreigabe ein. Sie müssen Berechtigungen von einem in die Domäne eingebundenen Computer aus einrichten, der über Netzwerkzugriff auf die Dateifreigabe verfügt. Um Berechtigungen auf Verzeichnis- und/oder Dateiebene zu ändern, binden Sie die Dateifreigabe mit dem Speicherschlüssel ein, nicht mit Ihren Azure AD-Anmeldeinformationen. Verwenden Sie zum Zuweisen von Berechtigungen den PowerShell-Befehl [Set-Acl](/powershell/module/microsoft.powershell.security/set-acl) oder [icacls](/windows-server/administration/windows-commands/icacls) in Windows.
7. [Peeren Sie das virtuelle Netzwerk](how-to-connect-peer-virtual-network.md) für das Speicherkonto mit dem Lab-Konto.
8. [Erstellen Sie das Classroom-Lab](how-to-manage-classroom-labs.md).
9. Speichern Sie ein Skript auf der Vorlage für virtuelle Computer, das Kursteilnehmer ausführen können, um eine Verbindung mit dem Netzwerklaufwerk herzustellen. So rufen Sie das Beispielskript ab
    1. Öffnen Sie das Speicherkonto im Azure-Portal.
    1. Wählen Sie unter **Dateidienst** die Option **Dateifreigaben** aus.
    1. Suchen Sie die Freigabe, mit der Sie eine Verbindung herstellen möchten, wählen Sie ganz rechts die Schaltfläche mit den Auslassungspunkten aus, und wählen Sie **Verbinden** aus.
    1. Es werden Anweisungen für Windows, Linux und macOS angezeigt. Wenn Sie Windows verwenden, legen Sie die **Authentifizierungsmethode** auf **Active Directory** fest.
    1. Kopieren Sie den Code im Beispiel, und speichern Sie ihn auf dem Vorlagencomputer in einer `.ps1`-Datei für Windows oder in einer `.sh`-Datei für Linux.
10. Laden Sie das Skript auf den Vorlagencomputer herunter, und führen Sie es aus, um [Computer von Kursteilnehmern in die Domäne einzubinden](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage). Das `Join-AzLabADTemplate`-Skript [veröffentlicht die Vorlagen-VM](how-to-create-manage-template.md#publish-the-template-vm) automatisch.  
    > [!NOTE]
    > Der Vorlagencomputer ist nicht in die Domäne eingebunden. Um Dateien auf der Freigabe anzeigen zu können, müssen Dozenten selbst eine Kursteilnehmer-VM verwenden.
11. Kursteilnehmer, die Windows verwenden, können über den [Datei-Explorer](../storage/files/storage-how-to-use-files-windows.md) mit ihren Anmeldeinformationen eine Verbindung mit der Azure Files-Freigabe herstellen, sobald sie den Pfad zur Dateifreigabe erhalten haben. Alternativ können Kursteilnehmer das oben genannte Skript ausführen, um eine Verbindung mit dem Netzwerklaufwerk herzustellen. Führen Sie für Kursteilnehmer, die Linux verwenden, das oben genannte Skript aus.

## <a name="netapp-files-with-nfs-volumes"></a>NetApp Files mit NFS-Volumes

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) ist ein leistungsstarker Dateispeicherdienst auf Unternehmensniveau.

- Zugriffsrichtlinien können pro Volume festgelegt werden.
- Berechtigungsrichtlinien sind für jedes Volume IP-basiert.
- Wenn Kursteilnehmer ein eigenes Volume benötigen, auf das andere Kursteilnehmer nicht zugreifen können, müssen Sie nach der Veröffentlichung des Labs Berechtigungsrichtlinien zuweisen.
- Im Zusammenhang mit Azure Lab Services werden nur Linux-Computer unterstützt.
- Das virtuelle Netzwerk für den Azure NetApp Files-Kapazitätspool muss mit dem virtuellen Netzwerk für das Lab-Konto gepeert werden, **bevor** das Lab erstellt wird.

Führen Sie die folgenden Schritte aus, um eine Azure NetApp Files-Freigabe in Azure Lab Services zu verwenden:

1. Greifen Sie bei Bedarf auf [Azure NetApp Files](https://aka.ms/azurenetappfiles) zu.
2. Informationen zum Erstellen eines NetApp Files-Kapazitätspools und mindestens eines NFS-Volumes finden Sie unter [Einrichten von Azure NetApp Files und NFS-Volumes](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md). Informationen zu Servicelevels finden Sie unter [Servicelevels für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-service-levels.md).
3. [Peeren Sie das virtuelle Netzwerk](how-to-connect-peer-virtual-network.md) für den NetApp Files-Kapazitätspool mit dem Lab-Konto.
4. [Erstellen Sie das Classroom-Lab](how-to-manage-classroom-labs.md).
5. Installieren Sie auf der Vorlagen-VM die erforderlichen Komponenten, um NFS-Dateifreigaben zu verwenden.
    - Ubuntu:

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS:

        ```bash
        sudo yum install nfs-utils
        ```

6. Speichern Sie auf der Vorlagen-VM das folgende Skript als `mount_fileshare.sh`, um [die NetApp Files-Freigabe einzubinden](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). Weisen Sie der Variablen `capacity_pool_ipaddress` die IP-Adresse des Einbindungsziels für den Kapazitätspool zu. Sehen Sie in den Anweisungen zum Einbinden für das Volume nach, um den entsprechenden Wert zu ermitteln. Das Skript erwartet den Pfadnamen des NetApp Files-Volumes. Führen Sie `chmod u+x mount_fileshare.sh` aus, um sicherzustellen, dass Benutzer das Skript ausführen können.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ might cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Wenn alle Kursteilnehmer gemeinsam Zugriff auf dasselbe NetApp Files-Volume haben, können Sie das Skript `mount_fileshare.sh` vor der Veröffentlichung auf dem Vorlagencomputer ausführen. Wenn Kursteilnehmer jeweils ein eigenes Volume erhalten, speichern Sie das Skript, damit es später vom jeweiligen Kursteilnehmer ausgeführt werden kann.
8. [Veröffentlichen](how-to-create-manage-template.md#publish-the-template-vm) Sie die Vorlage für virtuelle Computer.
9. [Konfigurieren Sie die Richtlinie](../azure-netapp-files/azure-netapp-files-configure-export-policy.md) für die Dateifreigabe. Die Exportrichtlinie kann zulassen, dass ein einzelner virtueller Computer oder mehrere VMs auf ein Volume zugreifen können. Sie können schreibgeschützten Zugriff oder Lese-/Schreibzugriff gewähren.
10. Kursteilnehmer müssen Ihre VMs starten und das Skript ausführen, um die Dateifreigabe einzubinden. Sie müssen das Skript nur einmal ausführen. Der Befehl sieht wie folgt aus: `./mount_fileshare.sh myvolumename`.

## <a name="next-steps"></a>Nächste Schritte

Diese Schritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Vorlage](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)
