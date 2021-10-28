---
title: Bereitstellung der SAP BusinessObjects BI-Plattform in Azure für Linux | Microsoft-Dokumentation
description: Bereitstellen und Konfigurieren der SAP BusinessObjects BI-Plattform in Azure für Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: e3cc7420a976812d462b3f5c5e60878ba67641ff
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249151"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Bereitstellungsleitfaden für die SAP BusinessObjects BI-Plattform für Linux in Azure

In diesem Artikel wird die Strategie zum Bereitstellen der SAP BusinessObjects BI-Plattform (BOBI) in Azure für Linux beschrieben. In diesem Beispiel konfigurieren Sie zwei virtuelle Computer mit verwalteten SSD Premium-Datenträgern (Solid State Drive) als Installationsverzeichnis. Sie verwenden Azure Database for MySQL für Ihre CMS-Datenbank und geben Azure NetApp Files für Ihren Dateirepositoryserver auf beiden Servern frei. Die Tomcat Java-Standard-Webanwendung und die BI-Plattformanwendung werden zusammen auf beiden virtuellen Computern installiert. Für den Lastenausgleich von Benutzeranforderungen wird das Azure Application Gateway verwendet, das native TLS/SSL-Auslagerungsfunktionen besitzt.

Diese Art von Architektur eignet sich für kleine Bereitstellungen oder Nicht-Produktionsumgebungen. Bei großen Bereitstellungen oder Produktionsumgebungen können Sie separate Hosts für Ihre Webanwendung verwenden. Sie können auch mehrere BOBI-Anwendungshosts verwenden, sodass der Server mehr Informationen verarbeiten kann.

![Diagramm: SAP BOBI-Bereitstellung in Azure für Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

Hier sehen Sie die Produktversion und das Dateisystemlayout für dieses Beispiel:

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (Version: 8.0.15)
- MySQL C API-Connector – libmysqlclient (Version: 6.1.11)

| Dateisystem        | BESCHREIBUNG                                                                                                               | Größe (GB)             | Besitzer  | Group  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Das Dateisystem für die Installation der SAP BOBI-Instanz, der Tomcat-Standard-Webanwendung und der Datenbanktreiber (falls erforderlich). | SAP-Richtlinien zur Dimensionierung | bl1adm | sapsys | Managed Disk – SSD Premium |
| /usr/sap/frsinput  | Das Einbindungsverzeichnis ist für die freigegebenen Dateien aller BOBI-Hosts vorgesehen, die als Eingabedatei-Repositoryverzeichnis verwendet werden.  | Geschäftliche Anforderung         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | Das Einbindungsverzeichnis ist für die freigegebenen Dateien aller BOBI-Hosts vorgesehen, die als Ausgabedatei-Repositoryverzeichnis verwendet werden. | Geschäftliche Anforderung         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Bereitstellen von virtuellen Linux-Computern über das Azure-Portal

In diesem Abschnitt erstellen Sie zwei virtuelle Computer mit dem Linux-Betriebssystemimage für die SAP BOBI-Plattform. Die allgemeinen Schritte zur Erstellung der virtuellen Computer sind folgende:

1. Erstellen Sie eine [Ressourcengruppe](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups).

2. Erstellen Sie ein [virtuelles Netzwerk](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Verwenden Sie bei der Bereitstellung der SAP BI-Plattform nicht ein einzelnes Subnetz für alle Azure-Dienste. Basierend auf der Architektur der SAP BI-Plattform müssen Sie mehrere Subnetze erstellen. In dieser Bereitstellung erstellen Sie drei Subnetze: jeweils eins für die Anwendung, den Dateirepositoryspeicher und Application Gateway.
   - In Azure müssen sich Application Gateway und Azure NetApp Files immer in einem separaten Subnetz befinden. Weitere Informationen finden Sie unter [Azure Application Gateway](../../../application-gateway/configuration-overview.md) und [Richtlinien für die Azure NetApp Files-Netzwerkplanung](../../../azure-netapp-files/azure-netapp-files-network-topologies.md).

3. Erstellen Sie eine Verfügbarkeitsgruppe. Stellen Sie zur Erzielung von Redundanz für jede Ebene in einer Bereitstellung mit mehreren Instanzen virtuelle Computer für jede Ebene in einer Verfügbarkeitsgruppe zusammen. Stellen Sie sicher, dass Sie die Verfügbarkeitsgruppen für jede Ebene auf der Grundlage Ihrer Architektur trennen.

4. Erstellen Sie den virtuellen Computer 1 **(azusbosl1)** .

   - Sie können entweder ein benutzerdefiniertes Image verwenden oder ein Image aus Azure Marketplace auswählen. Weitere Informationen finden Sie unter [Bereitstellen eines virtuellen Computers aus dem Azure Marketplace für SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) oder [Bereitstellen eines virtuellen Computers mit einem benutzerdefinierten Image für SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap).

5. Erstellen Sie den virtuellen Computer 2 **(azusbosl2)** .
6. Fügen Sie einen SSD Premium-Datenträger hinzu. Sie verwenden ihn als SAP BOBI-Installationsverzeichnis.

## <a name="provision-azure-netapp-files"></a>Bereitstellen von Azure NetApp Files

Bevor Sie mit der Einrichtung von Azure NetApp Files beginnen, sollten Sie sich mit der [Azure NetApp Files-Dokumentation](../../../azure-netapp-files/azure-netapp-files-introduction.md) vertraut machen.

Azure NetApp Files ist in verschiedenen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) verfügbar. Überprüfen Sie, ob Azure NetApp Files in Ihrer ausgewählten Azure-Region angeboten wird.

Verwenden Sie [Verfügbarkeit von Azure NetApp Files nach Azure-Region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all), um die Verfügbarkeit von Azure NetApp Files nach Region zu prüfen.

### <a name="deploy-azure-netapp-files-resources"></a>Bereitstellen von Azure NetApp Files-Ressourcen

In den folgenden Anweisungen wird davon ausgegangen, dass Sie bereits [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) bereitgestellt haben. Die Azure NetApp Files-Ressourcen und die virtuellen Computer, auf denen die Azure NetApp Files-Ressourcen eingebunden werden, müssen im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken bereitgestellt werden.

1. [Erstellen Sie ein Azure NetApp Files-Konto](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) in Ihrer ausgewählten Azure-Region.

2. [Richten Sie einen Azure NetApp Files-Kapazitätspool ein](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md). Die in diesem Artikel vorgestellte Architektur der SAP BI-Plattform verwendet einen einzigen Azure NetApp Files-Kapazitätspool auf der Dienstebene „Premium“. Für SAP BI File Repository Server in Azure wird die Verwendung einer Azure NetApp Files-[Dienstebene](../../../azure-netapp-files/azure-netapp-files-service-levels.md) vom Typ „Premium“ oder „Ultra“ empfohlen.

3. [Delegieren Sie ein Subnetz an Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Stellen Sie Azure NetApp Files-Volumes entsprechend den Anweisungen in [Erstellen eines NFS-Volumes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereit.

   Sie können die Volumes als NFSv3 und NFSv4.1 bereitstellen, da beide Protokolle für die SAP BOBI-Plattform unterstützt werden. Stellen Sie die Volumes in ihrem jeweiligen Azure NetApp Files-Subnetz bereit. Die IP-Adressen der Azure NetApp Files-Volumes werden automatisch zugewiesen.

Denken Sie daran, dass sich die Azure NetApp Files-Ressourcen und die virtuellen Azure-Computer im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken befinden müssen. Beispielsweise sind *azusbobi-frsinput* und *azusbobi-frsoutput* die Volumenamen und *nfs://10.31.2.4/azusbobi-frsinput* und *nfs://10.31.2.4/azusbobi-frsoutput* die Dateipfade für die Azure NetApp Files-Volumes.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Wichtige Hinweise

Beachten Sie Folgendes beim Erstellen Ihrer Azure NetApp Files-Instanz für den Dateirepository-Server der SAP BOBI-Plattform:

- Die Mindestgröße eines Kapazitätspools beträgt 4 TiB (Tebibyte).
- Die Mindestvolumegröße ist 100 GiB (Gibibyte).
- Azure NetApp Files und alle virtuellen Computer, auf denen die Azure NetApp Files-Volumes eingebunden werden sollen, müssen sich in demselben virtuellen Azure-Netzwerk oder in [über Peering gekoppelten virtuellen Netzwerken](../../../virtual-network/virtual-network-peering-overview.md) in derselben Region befinden. Azure NetApp Files-Zugriff über das Peering virtueller Netzwerke in derselben Region wird jetzt unterstützt. Azure NetApp Files-Zugriff über globales Peering wird derzeit nicht unterstützt.
- Das ausgewählte virtuelle Netzwerk muss ein Subnetz besitzen, das an Azure NetApp Files delegiert wurde.
- Mit den [Exportrichtlinien](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md) von Azure NetApp Files können Sie die zulässigen Clients und den Zugriffstyp (z. B. Lese- und Schreibzugriff oder schreibgeschützt) steuern.
- Das Azure NetApp Files-Feature wertet derzeit noch keine Zonen aus. Das Feature wird bisher nicht in allen Verfügbarkeitszonen in einer Azure-Region bereitgestellt. Achten Sie auf mögliche Latenzauswirkungen in einigen Azure-Regionen.
- Azure NetApp Files-Volumes können als NFSv3- oder NFSv4.1-Volumes bereitgestellt werden. Für die Anwendungen der SAP BI-Plattform werden beide Protokolle unterstützt.

## <a name="configure-file-systems-on-linux-servers"></a>Konfigurieren von Dateisystemen auf Linux-Servern

Für die Schritte in diesem Abschnitt wird das folgende Präfix verwendet:

**[A]** : Der Schritt gilt für alle Hosts.

### <a name="format-and-mount-the-sap-file-system"></a>Formatieren und Einbinden des SAP-Dateisystems

1. **[A]** Listen Sie alle angefügten Datenträger auf.

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to virtual machine, whose device name is sdc
    ```

2. **[A]** Formatieren Sie das Blockgerät für „/usr/sap“.

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** Erstellen Sie das Einbindungsverzeichnis.

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** Rufen Sie die UUID des Blockgeräts ab.

    ```bash
    sudo blkid

    # It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** Verwalten Sie den Einbindungseintrag des Dateisystems in „/etc/fstab“.

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** Binden Sie das Dateisystem ein.

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-the-azure-netapp-files-volume"></a>Einbinden des Azure NetApp Files-Volumes

1. **[A]** Erstellen Sie Einbindungsverzeichnisse.

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Konfigurieren Sie das Clientbetriebssystem für die Unterstützung der NFSv4.1-Einbindung (nur anwendbar, wenn NFSv4.1 verwendet wird).

   Wenn Sie Azure NetApp Files-Volumes mit dem NFSv4.1-Protokoll verwenden, führen Sie die folgende Konfiguration auf allen VMs aus, auf denen NFSv4.1-Volumes von Azure NetApp Files eingebunden werden müssen.

   In diesem Schritt müssen Sie die NFS-Domäneneinstellungen überprüfen. Stellen Sie sicher, dass die Domäne als Azure NetApp Files-Standarddomäne (`defaultv4iddomain.com`) konfiguriert und die Zuordnung auf `nobody` festgelegt ist.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   > Stellen Sie sicher, dass die NFS-Domäne in „/etc/idmapd.conf“ auf der VM so festgelegt ist, dass sie mit der Standarddomänenkonfiguration für Azure NetApp Files übereinstimmt (`defaultv4iddomain.com`). Bei einem Konflikt werden die Berechtigungen für Dateien auf Azure NetApp Files Volumes, die auf den virtuellen Computer bereitgestellt werden, als `nobody` angezeigt.

   Überprüfen von `nfs4_disable_idmapping` Diese Angabe sollte auf `Y` (Ja) festgelegt sein. Führen Sie den Einbindungsbefehl aus, um bei `nfs4_disable_idmapping` die Verzeichnisstruktur zu erstellen. Sie können das Verzeichnis unter „/sys/modules“ nicht manuell erstellen, da der Zugriff für den Kernel bzw. die Treiber reserviert ist.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]**  Fügen Sie Bereitstellungseinträge hinzu.

   Wenn Sie NFSv3 verwenden:

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Wenn Sie NFSv4.1 verwenden:

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** Binden Sie NFS-Volumes ein.

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-azure-database-for-mysql"></a>Konfigurieren von Azure Database for MySQL

Dieser Abschnitt enthält ausführliche Informationen zur Bereitstellung von Azure Database for MySQL mithilfe des Azure-Portals. Er enthält auch Anweisungen zum Erstellen von CMS- und Überwachungsdatenbanken für die SAP BOBI-Plattform und ein Benutzerkonto für den Zugriff auf die Datenbank.

Die Anweisungen gelten nur, wenn Sie Azure Database for MySQL verwenden. Informationen zu anderen Datenbanken finden Sie in der SAP- oder datenbankspezifischen Dokumentation.

### <a name="create-a-database"></a>Erstellen einer Datenbank

Melden Sie sich beim Azure-Portal an, und führen Sie die Schritte unter [Schnellstart: Erstellen eines Azure Database for MySQL-Servers über das Azure-Portal](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md) aus. Beim Bereitstellen von Azure Database for MySQL sind einige Punkte zu beachten:

- Wählen Sie für Azure Database for MySQL dieselbe Region aus, in der Ihre Anwendungsserver der SAP BI-Plattform ausgeführt werden.

- Wählen Sie eine unterstützte Datenbankversion basierend auf der [Produktverfügbarkeitsmatrix (Product Availability Matrix, PAM) für SAP BI](https://support.sap.com/pam) aus, die für Ihre SAP BOBI-Version spezifisch ist.

- Wählen Sie unter **Compute und Speicher** die Option **Server konfigurieren** und anschließend den entsprechenden Tarif auf der Grundlage Ihrer Dimensionierungsausgabe aus.

- Die **automatische Speichervergrößerung** ist standardmäßig aktiviert. Denken Sie daran, dass der [Speicher](../../../mysql/concepts-pricing-tiers.md#storage) nur hoch- und nicht herunterskaliert werden kann.

- Die **Standardaufbewahrungsdauer für Sicherungen** beträgt sieben Tage. Mit einer [optionalen Konfiguration](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) können Sie einen Zeitraum von bis zu 35 Tagen festlegen.

- Sicherungen von Azure Database for MySQL sind standardmäßig lokal redundant. Wenn Sie Serversicherungen in georedundantem Speicher wünschen, wählen Sie unter **Optionen für Sicherungsredundanz** die Option **Georedundant** aus.

>[!Important]
>Das Ändern der [Optionen für die Sicherungsredundanz](../../../mysql/concepts-backup.md#backup-redundancy-options) nach der Erstellung des Servers wird nicht unterstützt.

>[!Note]
>Das Feature „Private Link“ ist nur für Azure Database for MySQL-Server in den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Datenbankserver einer dieser Tarife festgelegt wird.

### <a name="configure-azure-private-link"></a>Konfigurieren von Azure Private Link

In diesem Abschnitt erstellen Sie eine private Verbindung, die es virtuellen SAP BOBI-Computern ermöglicht, sich über einen privaten Endpunkt mit Azure Database for MySQL zu verbinden. Azure Private Link bringt Azure-Dienste innerhalb Ihres privaten virtuellen Netzwerks.

1. Wählen Sie die im vorherigen Abschnitt erstellte Datenbank aus.
2. Navigieren Sie zu **Sicherheit** > **Private Endpunktverbindungen**.
3. Wählen Sie unter **Private Endpunktverbindungen** die Option **Privater Endpunkt** aus.
4. Wählen Sie **Abonnement** > **Ressourcengruppe** > **Standort** aus.
5. Geben Sie im Feld **Name** den Namen des privaten Endpunkts ein.
6. Geben Sie im Abschnitt **Ressource** Folgendes an:
   - Ressourcentyp: Microsoft.DBforMySQL/servers
   - Ressource: im vorherigen Abschnitt erstellte MySQL-Datenbank
   - Untergeordnete Zielressource: mysqlServer
7. Wählen Sie im Abschnitt **Netzwerk** das **virtuelle Netzwerk** und das **Subnetz** aus, in dem die SAP BOBI-Anwendung bereitgestellt wird.
   >[!NOTE]
   >Wenn für das Subnetz eine Netzwerksicherheitsgruppe (NSG) aktiviert ist, wird diese nur für private Endpunkte in diesem Subnetz deaktiviert. Für andere Ressourcen im Subnetz wird die NSG weiterhin durchgesetzt.
8. Übernehmen Sie für **In private DNS-Zone integrieren** die **Standardeinstellung (Ja)** .
9.  Wählen Sie in der Dropdownliste Ihre **private DNS-Zone** aus.
10. Wählen Sie **Überprüfen und erstellen** aus, und erstellen Sie einen privaten Endpunkt.

Weitere Informationen finden Sie unter [Private Link für Azure Database for MySQL](../../../mysql/concepts-data-access-security-private-link.md).

### <a name="create-the-cms-and-audit-databases"></a>Erstellen der CMS- und der Überwachungsdatenbank

1. Laden Sie MySQL Workbench von der [MySQL-Website](https://dev.mysql.com/downloads/workbench/) herunter, und installieren Sie die Anwendung. Stellen Sie sicher, dass Sie MySQL Workbench auf dem Server installieren, der auf Azure Database for MySQL zugreifen kann.

2. Stellen Sie mit MySQL Workbench eine Verbindung mit dem Server her. Befolgen Sie die Anweisungen unter [Abrufen von Verbindungsinformationen](../../../mysql/connect-workbench.md#get-connection-information). Wenn der Verbindungstest erfolgreich ausgeführt wurde, erhalten Sie folgende Meldung:

   ![Screenshot: Meldung in MySQL Workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Führen Sie auf der Registerkarte „SQL-Abfrage“ folgende Abfrage aus, um ein Schema für die CMS- und Überwachungsdatenbank zu erstellen.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```

4. Erstellen Sie ein Benutzerkonto, um eine Verbindung mit dem Schema herzustellen.

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. So überprüfen Sie die Berechtigungen und Rollen des MySQL-Benutzerkontos:

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-on-a-linux-server"></a>Installieren des MySQL C API-Connectors auf einem Linux-Server

Damit der SAP BOBI-Anwendungsserver auf eine Datenbank zugreifen kann, benötigt er einen Datenbankclient bzw. Datenbanktreiber. Für den Zugriff auf die CMS- und Überwachungsdatenbank müssen Sie den MySQL C API-Connector für Linux verwenden. Eine ODBC-Verbindung mit der CMS-Datenbank wird nicht unterstützt. Dieser Abschnitt enthält Anweisungen zum Einrichten des MySQL C API-Connectors unter Linux.

1. Weitere Informationen finden Sie unter [MySQL-Treiber und -Verwaltungstools, die mit Azure Database for MySQL kompatibel sind](../../../mysql/concepts-compatibility.md). Suchen Sie im Artikel nach dem Treiber **MySQL Connector/C (libmysqlclient)** .

2. Informationen zum Herunterladen von Treibern finden Sie unter [MySQL-Produktarchive](https://downloads.mysql.com/archives/c-c/).

3. Wählen Sie das Betriebssystem aus, und laden Sie das rpm-Paket der freigegebenen Komponente von MySQL Connector herunter. In diesem Beispiel wird die Version „mysql-connector-c-shared-6.1.11 connector“ verwendet.

4. Installieren Sie den Connector in allen SAP BOBI-Anwendungsinstanzen.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Überprüfen Sie den Pfad von „libmysqlclient.so“.

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Legen Sie `LD_LIBRARY_PATH` so fest, dass auf das Verzeichnis `/usr/lib64` des Benutzerkontos verwiesen wird, das für die Installation verwendet wird.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Servervorbereitung

Für die Schritte in diesem Abschnitt wird das folgende Präfix verwendet:

**[A]** : Der Schritt gilt für alle Hosts.

1. **[A]** Basierend auf der Variante von Linux (SLES oder RHEL) müssen Sie Kernelparameter festlegen und erforderliche Bibliotheken installieren. Weitere Informationen finden Sie im Abschnitt „Systemanforderungen“ unter [Business-Intelligence-Installationsleitfaden für Unix](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3).

2. **[A]** Stellen Sie sicher, dass die Zeitzone auf Ihrem Computer ordnungsgemäß eingestellt ist. Weitere Informationen finden Sie im Abschnitt [Zusätzliche Anforderungen für Unix und Linux](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/46b143336e041014910aba7db0e91070.html) im Installationsleitfaden.

3. **[A]** Erstellen Sie ein Benutzerkonto (**bl1** adm) und eine Benutzergruppe (sapsys), unter denen die Hintergrundprozesse der Software ausgeführt werden können. Verwenden Sie dieses Konto, um die Installation und die Software auszuführen. Das Konto erfordert keine Stammberechtigungen.

4. **[A]** Legen Sie die Umgebung des Benutzerkontos (**bl1** adm) so fest, dass ein unterstütztes UTF-8-Gebietsschema verwendet wird, und stellen Sie sicher, dass Ihre Konsolensoftware UTF-8-Zeichensätze unterstützt. Damit sichergestellt ist, dass Ihr Betriebssystem das richtige Gebietsschema verwendet, legen Sie die Umgebungsvariablen **und** auf Ihr bevorzugtes Gebietsschema in Ihrer Benutzerumgebung (`LC_ALL`bl1`LANG`adm) fest.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Konfigurieren Sie das Benutzerkonto (**bl1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Laden Sie Medien für die SAP BusinessObjects BI-Plattform vom SAP Service Marketplace herunter und extrahieren Sie sie.

## <a name="installation"></a>Installation

Überprüfen Sie das Gebietsschema für das Benutzerkonto **bl1** adm auf dem Server:

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Wechseln Sie zu den Medien der SAP BOBI-Plattform, und führen Sie den folgenden Befehl mit dem Benutzer **bl1** adm aus:

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Gehen Sie nach dem [SAP BOBI-Plattform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM)-Installationsleitfaden für Unix vor, das spezifisch für Ihre Version ist. Beachten Sie bei der Installation der SAP BOBI-Plattform die folgenden Punkte:

- Unter **Produktregistrierung konfigurieren** können Sie entweder einen temporären Lizenzschlüssel für SAP BusinessObjects Solutions aus dem SAP-Hinweis [1288121](https://launchpad.support.sap.com/#/notes/1288121) verwenden oder einen Lizenzschlüssel über den SAP Service Marketplace generieren.

- Wählen Sie auf dem Bildschirm **Select Install Type** (Installationstyp auswählen) die **vollständige** Installation auf dem ersten Server (`azusbosl1`) aus. Wählen Sie für den anderen Server (`azusbosl2`) die Option zum **Anpassen/Erweitern** aus, mit der das vorhandene BOBI-Setup erweitert wird.

- Wählen Sie zur **Auswahl der Standard- oder einer vorhandenen Datenbank** die Option zum **Konfigurieren einer vorhandenen Datenbank** aus, wodurch Sie aufgefordert werden, die CMS- und Überwachungsdatenbank auszuwählen. Wählen Sie **MySQL** für diese Datenbanktypen aus.

  Sie können auch die Option für **Keine Überwachungsdatenbank** auswählen, wenn Sie die Überwachung während der Installation nicht konfigurieren möchten.

- Wählen Sie auf dem Bildschirm zum **Auswählen des Java-Webanwendungsservers** geeignete Optionen basierend auf Ihrer SAP BOBI-Architektur aus. In diesem Beispiel haben wir Option 1 ausgewählt, bei der ein Tomcat-Server auf derselben SAP BOBI-Plattform installiert wird.

- Geben Sie CMS-Datenbankinformationen in **Configure CMS Repository Database – MySQL** (CMS-Repositorydatenbank konfigurieren – MySQL) ein. Im folgenden Beispiel ist eine Eingabe für CMS-Datenbankinformationen für eine Linux-Installation dargestellt. Azure Database for MySQL wird am dem Standardport 3306 verwendet.
  
  ![Screenshot: SAP BOBI-Bereitstellung unter Linux – CMS-Datenbank](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Optional) Geben Sie Überwachungsdatenbankinformationen in **Configure Audit Repository Database – MySQL** (Überwachungsrepositorydatenbank konfigurieren – MySQL) ein. Im folgenden Beispiel ist eine Eingabe für Überwachungsdatenbankinformationen für eine Linux-Installation dargestellt.

  ![Screenshot: SAP BOBI-Bereitstellung unter Linux – Überwachungsdatenbank](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Befolgen Sie die Anweisungen, und geben Sie die erforderlichen Eingaben ein, um die Installation abzuschließen.

Bei Bereitstellung mehrerer Instanzen führen Sie das Installationssetup auf einem zweiten Host (`azusbosl2`) aus. Wählen Sie für die **Auswahl des Installationstyps** die Option zum **Anpassen/Erweitern** aus, wodurch das bestehende BOBI-Setup erweitert wird.

In Azure Database for MySQL wird ein Gateway verwendet, um Verbindungen zu Serverinstanzen umzuleiten. Sobald die Verbindung hergestellt ist, zeigt der MySQL-Client die im Gateway festgelegte Version von MySQL an, nicht die tatsächliche Version, die auf Ihrer MySQL-Serverinstanz ausgeführt wird. Um die Version Ihrer MySQL-Serverinstanz zu ermitteln, geben Sie den `SELECT VERSION();`-Befehl an der MySQL-Eingabeaufforderung ein. Weitere Informationen finden Sie unter [Unterstützte Azure Database for MySQL-Serverversionen](../../../mysql/concepts-supported-versions.md).

![Screenshot: SAP BOBI-Bereitstellung unter Linux – CMC-Einstellungen](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Nach der Installation

Nach einer Installation der SAP BOBI-Plattform mit mehreren Instanzen müssen Sie zusätzliche Nachbearbeitungsschritte ausführen, um die Hochverfügbarkeit von Anwendungen zu unterstützen.

### <a name="configure-the-cluster-name"></a>Konfigurieren des Clusternamens

Bei einer Bereitstellung der SAP BOBI-Plattform mit mehreren Instanzen sollen mehrere CMS-Server gemeinsam in einem Cluster ausgeführt werden. Ein Cluster besteht aus zwei oder mehr CMS-Servern, die mit einer gemeinsamen CMS-Systemdatenbank zusammenarbeiten. Wenn ein Knoten ausfällt, der auf CMS ausgeführt wird, bedient ein Knoten mit einem anderen CMS weiterhin BI-Plattformanforderungen. Standardmäßig wird mit einem Clusternamen auf der SAP BOBI-Plattform der Hostname des ersten CMS angegeben, das Sie installieren.

Befolgen Sie die Anleitung im [Administratorleitfaden für die SAP Business Intelligence-Plattform](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3), um den Clusternamen unter Linux zu konfigurieren. Befolgen Sie nach dem Konfigurieren des Clusternamens den SAP-Hinweis [1660440](https://launchpad.support.sap.com/#/notes/1660440), um den Standardsystemeintrag auf der CMC- oder BI Launchpad-Anmeldeseite festzulegen.

### <a name="configure-input-and-output-filestore-location-to-azure-netapp-files"></a>Konfigurieren des Speicherorts des Eingabe- und Ausgabefilestore für Azure NetApp Files

Filestore bezieht sich auf die Datenträgerverzeichnisse, in denen sich die eigentlichen SAP BusinessObjects-Dateien befinden. Der Standardspeicherort des Dateirepositoryservers für die SAP BOBI-Plattform befindet sich im lokalen Installationsverzeichnis. Bei einer Bereitstellung mit mehreren Instanzen ist es wichtig, den Filestore in einem freigegebenen Speicher einzurichten, z. B. Azure NetApp Files. Dies ermöglicht den Zugriff auf den Filestore von allen Speicherebenenservern.

1. Wenn Sie noch keine NFS-Volumes erstellt haben, erstellen Sie sie in Azure NetApp Files. (Befolgen Sie die Anweisungen im vorherigen Abschnitt „Bereitstellen von Azure NetApp Files“.)

2. Binden Sie das NFS-Volume ein. (Befolgen Sie die Anweisungen im vorherigen Abschnitt „Einbinden des Azure NetApp Files-Volumes“.)

3. Befolgen Sie den SAP-Hinweis [2512660](https://launchpad.support.sap.com/#/notes/0002512660), um den Pfad des Dateirepositorys (Eingabe und Ausgabe) zu ändern.

### <a name="session-replication-in-tomcat-clustering"></a>Sitzungsreplikation im Tomcat-Clustering

Tomcat unterstützt das Clustering von mindestens zwei Anwendungsservern für Sitzungsreplikation und Failover. SAP BOBI-Plattformsitzungen sind serialisiert, sodass eine Benutzersitzung reibungslos ein Failover auf eine andere Instanz von Tomcat ausführen kann, selbst wenn ein Anwendungsserver ausfällt.

Gehen Sie z. B. von einem Benutzer aus, der mit einem Webserver verbunden ist, welcher ausfällt, während der Benutzer in einer Ordnerhierarchie in einer SAP BI-Anwendung navigiert. Bei einem richtig konfigurierten Cluster kann der Benutzer weiterhin in der Ordnerhierarchie navigieren, ohne zur Anmeldeseite umgeleitet zu werden.

Im SAP-Hinweis [2808640](https://launchpad.support.sap.com/#/notes/2808640) sind die Schritte zum Konfigurieren von Tomcat-Clustering mithilfe von Multicast angegeben. Beachten Sie aber, dass Azure Multicast nicht unterstützt. Damit der Tomcat-Cluster in Azure funktioniert, müssen Sie [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) verwenden (SAP-Hinweis [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Weitere Informationen finden Sie im Blogbeitrag [Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/).

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Lastenausgleich für die Webebene der SAP BI-Plattform

Bei einer Bereitstellung von SAP BOBI mit mehreren Instanzen werden Java-Webanwendungsserver (Webebene) auf zwei oder mehr Hosts ausgeführt. Um die Benutzerauslastung gleichmäßig auf die Webserver zu verteilen, können Sie einen Lastenausgleich zwischen Endbenutzern und Webservern verwenden. In Azure können Sie entweder Azure Load Balancer oder Azure Application Gateway verwenden, um den Datenverkehr zu Ihren Webanwendungsservern zu verwalten. Einzelheiten zum jeweiligen Angebot werden im folgenden Abschnitt erläutert.

#### <a name="azure-load-balancer"></a>Azure Load Balancer

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) ist ein Layer 4-Lastenausgleichsmodul (TCP, UDP) mit hoher Leistung und geringer Latenz, mit dem der Datenverkehr auf fehlerfreie VMs verteilt wird. Der Integritätstest eines Load Balancers überwacht einen angegebenen Port auf jeder VM und verteilt Datenverkehr nur an eine betriebsbereite VM. Sie können entweder einen öffentlichen Lastenausgleich oder einen internen Lastenausgleich wählen, je nachdem, ob die SAP BI-Plattform über das Internet zugänglich sein soll. Er ist zonenredundant und gewährleistet verfügbarkeitszonenübergreifend Hochverfügbarkeit.

Im folgenden Diagramm finden Sie weitere Informationen im Abschnitt „Internal Load Balancer“. Der Webanwendungsserver wird an Port 8080 ausgeführt (Standard-HTTP-Port von Tomcat), der vom Integritätstest überwacht wird. Alle eingehenden Anforderungen, die von Endbenutzern stammen, werden an die Webanwendungsserver (`azusbosl1` oder `azusbosl2`) umgeleitet. Load Balancer unterstützt keine TLS/SSL-Terminierung (auch bekannt als TLS/SSL-Abladung). Wenn Sie Load Balancer verwenden, um Datenverkehr auf Webserver zu verteilen, verwenden Sie Load Balancer Standard.

> [!NOTE]
> Wenn VMs ohne öffentliche IP-Adressen im Pool einer internen Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, besteht keine Internetkonnektivität in ausgehender Richtung, sofern Sie nicht durch zusätzliche Konfigurationsschritte das Routing an öffentliche Endpunkte zulassen. Weitere Informationen finden Sie unter [Konnektivität öffentlicher Endpunkte für VMs, die Azure Load Balancer Standard in SAP-Hochverfügbarkeitsszenarios verwenden](high-availability-guide-standard-load-balancer-outbound-connections.md).

![Diagramm: Azure Load Balancer, der Datenverkehr zwischen Webservern ausgleicht](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway"></a>Azure Application Gateway

[Azure Application Gateway](../../../application-gateway/overview.md) stellt Application Delivery Controller (ADC) als Dienst bereit. Mit diesem Dienst können Anwendungen den Benutzerdatenverkehr zu einem oder mehreren Webanwendungsservern leiten. Sie können für Ihre Anwendungen verschiedene Layer 7-Lastenausgleichsfunktionen nutzen, z. B. TLS/SSL-Abladung, Web Application Firewall (WAF) und cookiebasierte Sitzungsaffinität.

Auf einer SAP BI-Plattform leitet Application Gateway den Webdatenverkehr von Anwendungen an die angegebenen Ressourcen weiter, entweder `azusbosl1` oder `azusbos2`. Sie weisen einem Port einen Listener zu, erstellen Regeln und fügen Ressourcen zu einem Pool hinzu. Im folgenden Diagramm verfügt Application Gateway über eine private IP-Adresse (10.31.3.20), die als Einstiegspunkt für Benutzer fungiert. Außerdem werden eingehende TLS/SSL-Verbindungen (HTTPS – TCP/443) verarbeitet, TLS/SSL entschlüsselt und die unverschlüsselte Anforderung (HTTP – TCP/8080) an die Server übergeben. Es vereinfacht Vorgänge, um nur ein TLS/SSL-Zertifikat auf Application Gateway zu verwalten.

![Diagramm: Application Gateway, das Datenverkehr zwischen Webservern ausgleicht](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Informationen zum Konfigurieren von Application Gateway für einen SAP BOBI-Webserver finden Sie im Blogbeitrag [Load Balancing SAP BOBI Web Servers using Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/).

> [!NOTE]
> Azure Application Gateway ist dem Lastenausgleich des Datenverkehrs an einen Webserver vorzuziehen. Es bietet hilfreiche Features wie SSL-Abladung, zentralisierte SSL-Verwaltung, um den Verschlüsselungs- und Entschlüsselungsaufwand auf dem Server zu reduzieren, einen Roundrobin-Algorithmus zum Verteilen von Datenverkehr, WAF-Funktionen und Hochverfügbarkeit.

## <a name="sap-bobi-platform-reliability-on-azure"></a>Zuverlässigkeit der SAP BOBI-Plattform in Azure

Die SAP BOBI-Plattform umfasst unterschiedliche Ebenen, die für bestimmte Aufgaben und Vorgänge optimiert sind. Wenn eine Komponente aus einer Ebene nicht mehr verfügbar ist, kann auf eine SAP BOBI-Anwendung nicht mehr zugegriffen werden, oder ihre Funktionalität ist eingeschränkt. Stellen Sie sicher, dass jede Ebene auf Zuverlässigkeit ausgelegt ist, um die Anwendung betriebsbereit zu halten und eine Unterbrechung des Geschäftsbetriebs zu vermeiden.

In diesem Leitfaden wird beschrieben, wie native Features von Azure kombiniert mit einer SAP BOBI-Plattformkonfiguration die Verfügbarkeit einer SAP-Bereitstellung verbessern. In diesem Abschnitt werden die folgenden Optionen behandelt:

- **Sicherung und Wiederherstellung** ist ein Prozess, bei dem regelmäßig Kopien von Daten und Anwendungen an einem separaten Speicherort erstellt werden. Wenn die ursprünglichen Daten oder Anwendungen verloren gehen oder beschädigt sind, können Sie den vorherigen Zustand wiederherstellen.

- **Hochverfügbarkeit**: Eine Plattform mit Hochverfügbarkeit verfügt innerhalb einer Azure-Region von jeder Komponente über mindestens zwei Kopien, damit die Anwendung betriebsbereit bleibt, wenn einer der Server nicht mehr verfügbar ist.
- **Notfallwiederherstellung** ist ein Prozess zur Wiederherstellung der Funktionalität Ihrer Anwendung, wenn es in einer Notfallsituation zu einem Ausfall kommt, z. B. wenn die gesamte Azure-Region aufgrund einer Naturkatastrophe nicht mehr verfügbar ist.

Die Implementierung dieser Lösung variiert je nach Art des Systemsetups in Azure. Passen Sie Ihre Sicherungs-/Wiederherstellungs-, Hochverfügbarkeits- und Notfallwiederherstellungslösungen an Ihre geschäftlichen Anforderungen an.

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Sichern und Wiederherstellen sind ein wesentlicher Bestandteil jeder Strategie zur Notfallwiederherstellung in Unternehmen. Identifizieren Sie zur Entwicklung einer umfassenden Strategie für die SAP BOBI-Plattform die Komponenten, die zu Systemausfällen oder Störungen der Anwendung führen. Auf der SAP BOBI-Plattform ist die Sicherung der folgenden Komponenten für den Schutz der Anwendung unerlässlich:

- SAP BOBI-Installationsverzeichnis (verwaltete Premium-Datenträger)
- File Repository Server (Azure NetApp Files oder Azure Files Premium)
- CMS-Datenbank (Azure Database for MySQL oder eine Datenbank auf Azure Virtual Machines)

Im folgenden Abschnitt wird beschrieben, wie eine Sicherungs- und Wiederherstellungsstrategie für die einzelnen Komponenten implementiert wird.

### <a name="backup-and-restore-for-sap-bobi-installation-directory"></a>Sicherung und Wiederherstellung für das SAP BOBI-Installationsverzeichnis

In Azure ist die einfachste Möglichkeit zur Sicherung von Anwendungsservern und allen angeschlossenen Datenträgern die Verwendung von [Azure Backup](../../../backup/backup-overview.md). Azure Backup bietet unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen der Daten auf Ihren VMs. Sicherungen werden in einem Recovery Services-Tresor mit integrierter Verwaltung von Wiederherstellungspunkten gespeichert. Konfiguration und Skalierung sind unkompliziert. Sicherungen werden optimiert und können bei Bedarf problemlos wiederhergestellt werden.

Im Rahmen des Sicherungsvorgangs wird eine Momentaufnahme erstellt, und die Daten werden ohne Auswirkung auf Produktionsworkloads in den Tresor übertragen. Weitere Informationen finden Sie unter [Konsistenz von Momentaufnahmen](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency). Sie können sich auch dafür entscheiden, eine Teilmenge der Datenträger Ihrer VM zu sichern, indem Sie die Sicherungs- und Wiederherstellungsfunktionen für selektive Datenträger verwenden. Weitere Informationen finden Sie unter [Ein Überblick über die Sicherung von Azure-VMs](../../../backup/backup-azure-vms-introduction.md) und unter [Häufig gestellte Fragen – Sicherung von Azure-VMs](../../../backup/backup-azure-vm-backup-faq.yml).

### <a name="backup-and-restore-for-file-repository-server"></a>Sicherung und Wiederherstellung für File Repository Server

Basierend auf Ihrer SAP BOBI-Bereitstellung unter Linux können Sie Azure NetApp Files als Filestore Ihrer SAP BOBI-Plattform verwenden. Wählen Sie basierend auf dem Speicher, den Sie für den Dateispeicher verwenden, eine der folgenden Optionen zum Sichern und Wiederherstellen aus.

- **Azure NetApp Files:** Sie können On-Demand-Momentaufnahmen erstellen und mithilfe von Momentaufnahmerichtlinien automatische Momentaufnahmen planen. Momentaufnahmekopien stellen eine Zeitpunktkopie Ihres Volumes dar. Weitere Informationen finden Sie unter [Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

- Wenn Sie einen separaten NFS-Server erstellt haben, stellen Sie sicher, dass Sie für den Server die Sicherungs- und Wiederherstellungsstrategie implementieren.

### <a name="backup-and-restore-for-cms-and-audit-databases"></a>Sicherung und Wiederherstellung für CMS- und Überwachungsdatenbank

Auf Linux-VMs können die CMS- und Überwachungsdatenbank auf jeder der unterstützten Datenbanken ausgeführt werden. Weitere Informationen finden Sie in der [Supportmatrix](businessobjects-deployment-guide.md#support-matrix). Es ist wichtig, dass Sie die Sicherungs- und Wiederherstellungsstrategie basierend auf der Datenbank übernehmen, die für CMS und den Überwachungsdatenspeicher verwendet wird.

- Für Azure Database for MySQL werden Sicherungen automatisch erstellt und in einem vom Benutzer konfigurierten, lokal redundanten oder georedundanten Speicher gespeichert. Von Azure Database for MySQL werden die Datendateien und das Transaktionsprotokoll gesichert. Abhängig von der unterstützten maximalen Speichergröße werden entweder vollständige oder differenzielle Sicherungen (Server mit maximal 4 TB Speicher) oder Momentaufnahmesicherungen (Server mit bis zu 16 TB Speicher) erstellt. Diese Sicherungen ermöglichen es Ihnen, für einen Server den Zustand zu jedem Zeitpunkt innerhalb des von Ihnen konfigurierten Aufbewahrungszeitraums für Sicherungen wiederherzustellen. Der Aufbewahrungszeitraum für Sicherungen beträgt standardmäßig sieben Tage, den Sie [optional auf drei Tage festlegen können](../../../mysql/howto-restore-server-portal.md#set-backup-configuration). Für die Verschlüsselung aller Sicherungen wird die AES-256-Bit-Verschlüsselung verwendet. Diese Sicherungsdateien sind nicht für Benutzer verfügbar und können nicht exportiert werden. Sie können nur für Wiederherstellungsvorgänge in Azure Database for MySQL verwendet werden. Zum Kopieren einer Datenbank können Sie [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) verwenden. Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung in Azure Database for MySQL](../../../mysql/concepts-backup.md).

- Für eine Datenbank, die auf einem virtuellen Azure-Computer installiert ist, können Sie Standardsicherungstools oder [Azure Backup](../../../backup/sap-hana-db-about.md) für unterstützte Datenbanken verwenden. Sie können auch unterstützte Sicherungstools von Drittanbietern verwenden, die über einen Agent für die Sicherung und Wiederherstellung aller Komponenten der SAP BOBI-Plattform verfügen.

## <a name="high-availability"></a>Hochverfügbarkeit

*Hochverfügbarkeit* bezieht sich auf eine Reihe von Technologien, mit denen IT-Störungen minimiert werden können, indem die Geschäftskontinuität von Anwendungen und Diensten gewährleistet wird. Dies erfolgt über redundante, fehlertolerante oder failovergeschützte Komponenten innerhalb desselben Rechenzentrums. In unserem Fall befinden sich die Rechenzentren innerhalb einer Azure-Region. Weitere Informationen finden Sie unter [Architektur und Szenarios für die Hochverfügbarkeit von SAP NetWeaver](sap-high-availability-architecture-scenarios.md).

Basierend auf dem Dimensionierungsergebnis der SAP BOBI-Plattform müssen Sie die Umgebung entwerfen und die Verteilung der BI-Komponenten auf Azure Virtual Machines und Subnetze bestimmen. Der Redundanzgrad der verteilten Architektur richtet sich nach dem RTO- (Recovery Time Objective) und dem RPO-Wert (Recovery Point Objective), die für Ihre geschäftlichen Anforderungen benötigt werden. Die SAP BOBI-Plattform umfasst verschiedene Ebenen, und die Komponenten auf den einzelnen Ebenen sollten so konzipiert sein, dass Redundanz erreicht wird. Zum Beispiel:

- Redundante Anwendungsserver wie BI-Anwendungsserver und -Webserver
- Eindeutige Komponenten wie CMS-Datenbank, File Repository Server und Load Balancer

In den folgenden Abschnitten wird beschrieben, wie Sie für jede Komponente der SAP BOBI-Plattform Hochverfügbarkeit erzielen können.

### <a name="high-availability-for-application-servers"></a>Hochverfügbarkeit für Anwendungsserver

Sie können Hochverfügbarkeit für Anwendungsserver erreichen, indem Sie Redundanz einsetzen. Konfigurieren Sie hierzu mehrere Instanzen von BI- und Webservern auf verschiedenen Azure-VMs.

Zur Reduzierung der Auswirkungen von Ausfallzeiten aufgrund eines oder mehrerer Ereignisse ist Folgendes sinnvoll:

- Verwenden Sie Verfügbarkeitszonen, um vor Ausfällen des Rechenzentrums zu schützen.
- Konfigurieren Sie mehrere VMs in einer Verfügbarkeitsgruppe für Redundanz.
- Verwenden Sie verwaltete Datenträger für VMs in einer Verfügbarkeitsgruppe.
- Konfigurieren Sie die einzelnen Logikschichten in separate Verfügbarkeitsgruppen.

Weitere Informationen finden Sie unter [Verfügbarkeitsoptionen für virtuelle Azure-Computer](../../availability.md).

>[!Important]
>Die Konzepte von Azure-Verfügbarkeitszonen und Azure-Verfügbarkeitsgruppen schließen sich gegenseitig aus. Sie können zwei oder mehr VMs entweder in einer bestimmten Verfügbarkeitszone oder in einer Verfügbarkeitsgruppe bereitstellen, aber nicht in beiden.

### <a name="high-availability-for-a-cms-database"></a>Hochverfügbarkeit für eine CMS-Datenbank

Wenn Sie Azure Database for MySQL für Ihre CMS- und Überwachungsdatenbank verwenden, verfügen Sie standardmäßig über ein lokal redundantes Hochverfügbarkeitsframework. Sie müssen nur die Region und den Dienst auswählen, der Funktionen für Hochverfügbarkeit, Redundanz und Resilienz bietet, ohne dass zusätzliche Komponenten konfiguriert werden müssen. Wenn die Bereitstellungsstrategie für die SAP BOBI-Plattform per Verfügbarkeitszone erfolgt, müssen Sie sicherstellen, dass Sie Zonenredundanz für Ihre CMS- und Überwachungsdatenbanken erzielen. Weitere Informationen finden Sie unter [Hochverfügbarkeit in Azure Database for MySQL](../../../mysql/concepts-high-availability.md) und [Hochverfügbarkeit für Azure SQL-Datenbank](../../../azure-sql/database/high-availability-sla.md).

Informationen zu anderen Bereitstellungen für die CMS-Datenbank finden Sie in den Informationen zur Hochverfügbarkeit in den [DBMS-Bereitstellungshandbüchern für SAP-Workloads](dbms_guide_general.md).

### <a name="high-availability-for-filestore"></a>Hochverfügbarkeit für Filestore

Filestore bezieht sich auf die Datenträgerverzeichnisse, in denen Inhalte wie Berichte, Universen und Verbindungen gespeichert werden. Es wird von allen Anwendungsservern dieses Systems gemeinsam genutzt. Daher müssen Sie sicherstellen, dass für Hochverfügbarkeit gesorgt ist (wie auch für die anderen Komponenten der SAP BOBI-Plattform).

Für die SAP BOBI-Plattform, die auf Linux läuft, können Sie [Azure Premium Files](../../../storage/files/storage-files-introduction.md) oder [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) für Dateifreigaben wählen, die auf hohe Verfügbarkeit und hohe Haltbarkeit ausgelegt sind. Weitere Informationen finden Sie unter [Redundanz](../../../storage/files/storage-files-planning.md#redundancy) für Azure Files.

> [!Important]
> Das SMB-Protokoll für Azure Files ist allgemein verfügbar, aber die Unterstützung des NFS-Protokolls für Azure Files befindet sich derzeit in der Vorschauversion. Weitere Informationen finden Sie unter [NFS 4.1-Unterstützung für Azure Files ab sofort als Vorschau verfügbar](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/).

Beachten Sie, dass dieser Dateifreigabedienst nicht in allen Regionen verfügbar ist. Aktuelle Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Wenn der Dienst in Ihrer Region nicht verfügbar ist, können Sie einen NFS-Server erstellen, über den Sie das Dateisystem für die SAP BOBI-Anwendung freigeben. Sie müssen aber auch seine Hochverfügbarkeit berücksichtigen.

### <a name="high-availability-for-load-balancer"></a>Hochverfügbarkeit für Load Balancer

Für die Verteilung des Datenverkehrs über einen Webserver können Sie entweder Azure Load Balancer oder Azure Application Gateway verwenden. Die Redundanz für einen der beiden kann auf der Grundlage der SKU erreicht werden, die Sie für die Bereitstellung auswählen.

- Für Azure Load Balancer kann Redundanz erreicht werden, indem Load Balancer Standard als zonenredundant konfiguriert wird. Weitere Informationen finden Sie unter [Load Balancer Standard und Verfügbarkeitszonen](../../../load-balancer/load-balancer-standard-availability-zones.md).

- Für Application Gateway kann Hochverfügbarkeit basierend auf der Art der während der Bereitstellung ausgewählten Ebene erreicht werden.
   -  Die v1-SKU unterstützt Szenarien mit Hochverfügbarkeit, wenn Sie mindestens zwei Instanzen bereitgestellt haben. Azure verteilt diese Instanzen auf Update- und Fehlerdomänen, um sicherzustellen, dass nicht alle Instanzen gleichzeitig ausfallen. Sie erreichen Redundanz innerhalb der Zone.
   -  Die v2-SKU stellt automatisch sicher, dass neue Instanzen über Fehlerdomänen und Updatedomänen hinweg verteilt werden. Haben Sie Zonenredundanz gewählt, werden die neuesten Instanzen darüber hinaus über Verfügbarkeitszonen hinweg verteilt, um Resilienz gegen Zonenausfälle zu erreichen. Ausführlichere Informationen finden Sie unter [Automatische Skalierung und zonenredundantes Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md).

### <a name="reference-high-availability-architecture-for-sap-bobi-platform"></a>Referenzarchitektur für Hochverfügbarkeit für die SAP BOBI-Plattform

Das folgende Diagramm zeigt die Einrichtung der SAP BOBI-Plattform, wenn Sie eine Verfügbarkeitsgruppe verwenden, die auf einem Linux-Server ausgeführt wird. Die Architektur veranschaulicht die Verwendung unterschiedlicher Dienste, z. B. Azur Application Gateway, Azure NetApp Files und Azure Database for MySQL. Diese Dienste bieten integrierte Redundanz, die die Komplexität der Verwaltung verschiedener Hochverfügbarkeitslösungen reduziert.

Beachten Sie, dass der Lastenausgleich für den eingehenden Datenverkehr (HTTPS – TCP/443) mithilfe der Azure Application Gateway v1-SKU erfolgt, die bei der Bereitstellung auf zwei oder mehr Instanzen eine entsprechende Hochverfügbarkeit aufweist. Mehrere Instanzen des Webservers, der Verwaltungsserver und Verarbeitungsserver werden auf separaten VMs bereitgestellt, um Redundanz zu erreichen, und jede Ebene wird in separaten Verfügbarkeitsgruppen bereitgestellt. Azure NetApp Files verfügt über integrierte Redundanz innerhalb des Rechenzentrums, sodass Ihre Azure NetApp Files-Volumes für File Repository Server hochverfügbar sind. Die CMS-Datenbank wird für Azure Database for MySQL bereitgestellt, das über eine inhärente Hochverfügbarkeit verfügt. Weitere Informationen finden Sie unter [Hochverfügbarkeit in Azure Database for MySQL](../../../mysql/concepts-high-availability.md).

![Diagramm: SAP BusinessObjects BI-Plattformredundanz mit Verfügbarkeitsgruppen](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

Die obige Architektur gewährt Ihnen einen Einblick, wie eine SAP BOBI-Bereitstellung in Azure erfolgen kann. Aber sie behandelt nicht alle möglichen Konfigurationsoptionen. Sie können Ihre Bereitstellung an Ihre geschäftlichen Anforderungen anpassen.

In mehreren Azure-Regionen können Sie Verfügbarkeitszonen verwenden. Dies bedeutet, dass Sie eine unabhängige Bereitstellung von Stromversorgung, Kühlung und Netzwerk nutzen können. Dadurch können Sie die Anwendung in zwei oder drei Verfügbarkeitszonen bereitstellen. Wenn Sie verfügbarkeitszonenübergreifend Hochverfügbarkeit erreichen möchten, können Sie die SAP BOBI-Plattform über diese Verfügbarkeitszonen hinweg bereitstellen, wobei sichergestellt wird, dass jede Komponente in der Anwendung zonenredundant ist.

## <a name="disaster-recovery"></a>Notfallwiederherstellung

Dieser Abschnitt erläutert die Strategie zur Bereitstellung von Schutz für die Notfallwiederherstellung für eine SAP BOBI-Plattform, die auf Linux läuft. Sie ergänzt das Dokument [Notfallwiederherstellung für SAP](../../../site-recovery/site-recovery-sap.md), das die primären Ressourcen für den gesamten SAP-Ansatz zur Notfallwiederherstellung darstellt. Sehen Sie sich für SAP BOBI den SAP-Hinweis [2056228](https://launchpad.support.sap.com/#/notes/2056228) an. Darin werden die folgenden Methoden für die sichere Implementierung einer Umgebung für die Notfallwiederherstellung beschrieben:

- Vollständige oder selektive Verwendung der Lebenszyklusverwaltung oder eines Verbunds, um den Inhalt des Hauptsystems höher zu stufen und zu verteilen
- Regelmäßiges Kopieren der CMS- und File Repository Server-Inhalte

Dieser Leitfaden konzentriert sich auf die zweite Option. Es werden nicht alle möglichen Konfigurationsoptionen für die Notfallwiederherstellung behandelt, sondern eine Lösung, die native Azure-Dienste in Kombination mit einer SAP BOBI-Plattformkonfiguration bietet.

>[!Important]
>Die Verfügbarkeit jeder Komponente der SAP BOBI-Plattform sollte in die sekundäre Region integriert werden, und die gesamte Notfallwiederherstellungsstrategie muss gründlich getestet werden.

### <a name="reference-disaster-recovery-architecture-for-sap-bobi-platform"></a>Referenzarchitektur für Notfallwiederherstellung für die SAP BOBI-Plattform

Bei dieser Referenzarchitektur wird eine Bereitstellung mit mehreren Instanzen der SAP BOBI-Plattform mit redundanten Anwendungsservern ausgeführt. Für die Notfallwiederherstellung sollten Sie ein Failover aller Komponenten der SAP BOBI-Plattform in eine sekundäre Region ausführen. Im folgenden Diagramm wird Azure NetApp Files als Filestore verwendet, Azure Database for MySQL als CMS- und Überwachungsrepository und Azure Application Gateway als Lastenausgleich. Die Strategie zum Erreichen des Notfallwiederherstellungsschutzes für jede Komponente unterscheidet sich. Diese Unterschiede werden in den folgenden Abschnitten beschrieben.

![Diagramm: Notfallwiederherstellung für die SAP BusinessObjects BI-Plattform](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

### <a name="load-balancer"></a>Load Balancer

Ein Load Balancer wird verwendet, um Datenverkehr auf die Webanwendungsserver einer SAP BOBI-Plattform zu verteilen. In Azure können Sie dafür entweder Azure Load Balancer oder Azure Application Gateway verwenden. Damit Notfallwiederherstellung für die Lastenausgleichsdienste erreicht wird, müssen Sie einen weiteren Azure Load Balancer oder ein Azure Application Gateway auf der sekundären Region implementieren. Damit die URL nach einem Failover im Rahmen der Notfallwiederherstellung beibehalten wird, müssen Sie den Eintrag im DNS ändern, damit dieser auf den Lastenausgleichsdienst auf der sekundären Region verweist.

### <a name="vms-running-web-and-bi-application-servers"></a>VMs mit Web- und BI-Anwendungsservern

Verwenden Sie [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md), um VMs, auf denen Web- und BI-Anwendungsserver ausgeführt werden, in der sekundären Region zu replizieren. Es repliziert die Server sowie die verbundenen verwalteten Datenträger auf die sekundäre Region, damit bei Notfällen und Ausfällen ein einfaches Failover auf die replizierte Umgebung möglich ist und Sie weiterarbeiten können. Befolgen Sie die Anleitung zum [Replizieren eines virtuellen Computers in Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md), um mit dem Replizieren aller VMs für SAP-Anwendungen in das Azure-Rechenzentrum für die Notfallwiederherstellung zu beginnen.

### <a name="file-repository-servers"></a>File Repository Server

Filestore ist ein Datenträgerverzeichnis, in dem die eigentlichen Dateien, z. B. Berichte und BI-Dokumente, gespeichert werden. Es ist wichtig, dass alle im Filestore enthaltenen Dateien mit der Region für die Notfallwiederherstellung synchron sind. Basierend auf dem Typ des Dateifreigabediensts, den Sie für die Ausführung der SAP BOBI-Plattform unter Linux verwenden, muss die passende Strategie für die Notfallwiederherstellung verwendet werden, damit der Inhalt synchron ist.

- **Azure NetApp Files** stellt NFS- und SMB-Volumes bereit, sodass Sie jedes dateibasierte Kopiertool zur Replikation von Daten zwischen Azure-Regionen verwenden können. Weitere Informationen zum Kopieren eines Volumes in eine andere Region finden Sie unter [Häufig gestellte Fragen zu Azure NetApp Files](../../../azure-netapp-files/faq-data-migration-protection.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

  Sie können die regionsübergreifende Replikation von Azure NetApp Files verwenden, die sich derzeit in der [Vorschauversion](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) befindet. Dabei werden nur die geänderten Blöcke in einem komprimierten, effizienten Format über das Netzwerk gesendet. Dies minimiert die Menge an Daten, die regionsübergreifend repliziert werden muss, wodurch Datenübertragungskosten eingespart werden. Darüber hinaus wird die Replikationsdauer reduziert, damit Sie einen niedrigeren RPO-Wert erzielen können. Weitere Informationen finden Sie unter [Regionsübergreifende Replikation: Anforderungen und Überlegungen](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md).

- **Azure Files Premium** unterstützt nur lokal redundanten Speicher (LRS) und zonenredundanten Speicher (ZRS). Für die Strategie zur Notfallwiederherstellung können Sie [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) oder [Azure PowerShell](/powershell/module/az.storage/) verwenden, um die Dateien in ein anderes Speicherkonto in einer anderen Region zu kopieren. Weitere Informationen finden Sie unter [Notfallwiederherstellung und Failover des Speicherkontos](../../../storage/common/storage-disaster-recovery-guidance.md).

   > [!Important]
   > Das SMB-Protokoll für Azure Files ist allgemein verfügbar, aber die Unterstützung des NFS-Protokolls für Azure Files befindet sich derzeit in der Vorschauversion. Weitere Informationen finden Sie unter [NFS 4.1-Unterstützung für Azure Files ab sofort als Vorschau verfügbar](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/).

### <a name="cms-database"></a>CMS-Datenbank

Die CMS- und Überwachungsdatenbank in der Notfallwiederherstellungsregion müssen Kopien der Datenbanken sein, die in der primären Region ausgeführt werden. Ausgehend von dem Datenbanktyp ist es wichtig, die Datenbank basierend auf dem RTO und RPO, die Ihr Unternehmen benötigt, in die Notfallwiederherstellungsregion zu kopieren.

#### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL verfügt über mehrere Optionen für die Wiederherstellung einer Datenbank, wenn es zu einem Katastrophenfall kommt. Wählen Sie eine entsprechende Option aus, die für Ihr Unternehmen geeignet ist.

- Aktivieren Sie regionsübergreifende Lesereplikate, um Ihre BCDR-Planung (Business Continuity & Disaster Recovery) zu verbessern. Sie können vom Quellserver auf bis zu fünf Replikate replizieren. Lesereplikate werden mithilfe der binären Protokollreplikation von MySQL asynchron aktualisiert. Replikate sind neue Server, die Sie ähnlich wie normale Server in Azure Database for MySQL verwalten. Weitere Informationen finden Sie unter [Lesereplikate in Azure Database for MySQL](../../../mysql/concepts-read-replicas.md).

- Verwenden Sie das Geowiederherstellungsfeature, um den Server mithilfe von georedundanten Sicherungen wiederherzustellen. Auf diese Sicherungen kann selbst dann zugegriffen werden, wenn die Region, in der Ihr Server gehostet wird, offline ist. Sie können eine Wiederherstellung aus diesen Sicherungen in eine andere Region ausführen und den Server wieder online schalten.

  > [!NOTE]
  > Die Geowiederherstellung ist nur möglich, wenn Sie den Server mit einem georedundanten Sicherungsspeicher bereitgestellt haben. Das Ändern der **Optionen für die Sicherungsredundanz** nach der Erstellung des Servers wird nicht unterstützt. Weitere Informationen finden Sie unter [Optionen für Sicherungsredundanz](../../../mysql/concepts-backup.md#backup-redundancy-options).

In der folgenden Tabelle ist die Empfehlung für eine Notfallwiederherstellung der einzelnen Ebenen angegeben, die in diesem Beispiel verwendet werden.

| Ebenen der SAP BOBI-Plattform   | Empfehlung                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Parallele Einrichtung von Application Gateway in einer sekundären Region                                             |
| Webanwendungsserver   | Replizieren über Azure Site Recovery                                                                  |
| BI-Anwendungsserver    | Replizieren über Site Recovery                                                                        |
| Azure NetApp Files        | Dateibasiertes Kopiertool zum Replizieren von Daten in eine sekundäre Region oder mithilfe der regionsübergreifenden Replikation      |
| Azure Database for MySQL  | Regionsübergreifende Lesereplikate oder Wiederherstellung einer Sicherung aus georedundanten Sicherungen                                |

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines – Planung und Implementierung für SAP](planning-guide.md)
- [Azure Virtual Machines – Bereitstellung für SAP](deployment-guide.md)
- [Azure Virtual Machines – DBMS-Bereitstellung für SAP](./dbms_guide_general.md)
