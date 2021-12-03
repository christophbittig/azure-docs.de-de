---
title: Migrieren einer Failoverclusterinstanz
description: Im Folgenden wird beschrieben, wie Sie eine Always On-Failoverclusterinstanz-Hochverfügbarkeitslösung mithilfe von Azure Migrate per Lift-und Shift-Verfahren zu SQL Server auf Azure-VMs verschieben.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
author: rahugup
manager: bsiva
ms.topic: how-to
ms.date: 4/25/2021
ms.author: rahugup
ms.openlocfilehash: a1ae8cd56d705df0234425488029ca7073c6dfea
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347706"
---
# <a name="migrate-failover-cluster-instance-to-sql-server-on-azure-vms"></a>Migrieren einer Failoverclusterinstanz zu SQL Server auf Azure-VMs

In diesem Artikel wird beschrieben, wie Sie Ihre Always On-Failoverclusterinstanz (FCI) mithilfe des Tools [Azure Migrate: Servermigration](../../../migrate/migrate-services-overview.md#azure-migrate-server-migration-tool) zu SQL Server auf Azure-VMs migrieren. Mit dem Migrationstool können Sie jeden Knoten in der Failoverclusterinstanz zu einem virtuellen Azure-Computer migrieren, der SQL Server hostet, sowie zu den Cluster- und FCI-Metadaten.  

In diesem Artikel werden folgende Vorgehensweisen behandelt: 

> [!div class="checklist"]
> * Vorbereiten von Azure und Quellumgebung für die Migration
> * Beginnen mit der Replikation von VMs
> * Überwachen der Replikation
> * Durchführen einer vollständigen VM-Migration
> * Erneutes Konfigurieren des SQL-Failoverclusters mit freigegebenen Azure-Datenträgern.


In diesem Leitfaden wird der Agent-basierte Migrationsansatz von Azure Migrate verwendet, bei dem jeder Server oder virtuelle Computer als physischer Server behandelt wird. Bei der Migration physischer Computer verwendet „Azure Migrate: Servermigration“ die gleiche Replikationsarchitektur wie die Agent-basierte Notfallwiederherstellung des Azure Site Recovery-Diensts. Darüber hinaus weisen einige Komponenten die gleiche Codebasis auf. Einige Inhalte sind daher ggf. mit der Site Recovery-Dokumentation verknüpft.


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

1. Ein Azure-Abonnement. Erstellen Sie ggf. ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/). 
1. Installieren Sie das [`Az`-Modul von Azure PowerShell](/powershell/azure/install-az-ps). 
1. Laden Sie die [PowerShell-Beispielskripts](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/SQL%20Migration) aus dem GitHub-Repository herunter.

## <a name="prepare-azure"></a>Vorbereiten von Azure

Bereiten Sie Azure für die Migration mit der Servermigration vor.

**Aufgabe** | **Details**
--- | ---
**Erstellen eines Azure Migrate-Projekts** | Ihr Azure-Konto benötigt zum [Erstellen eines neuen Projekts](../../../migrate/create-manage-projects.md) Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“.
**Überprüfen der Berechtigungen für Ihr Azure-Konto** | Ihr Azure-Konto benötigt Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für das Azure-Abonnement, Berechtigungen zum Registrieren von AAD-Apps (Azure Active Directory) und Berechtigungen vom Typ „Benutzerzugriffsadministrator“ für das Azure-Abonnement, um eine Key Vault-Instanz zu erstellen, einen virtuellen Computer zu erstellen und auf einen verwalteten Azure-Datenträger zu schreiben.
**Einrichten eines virtuellen Azure-Netzwerks** | Führen Sie die [Einrichtung](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network) eines virtuelles Azure-Netzwerks (VNET) durch. Wenn Sie die Replikation in Azure durchführen, werden virtuelle Azure-Computer erstellt und in das Azure-VNET eingebunden, das Sie beim Einrichten der Migration angeben.


Führen Sie die folgenden Schritte aus, um zu überprüfen, ob Sie über die richtigen Berechtigungen verfügen: 

1. Öffnen Sie im Azure-Portal das Abonnement, und wählen Sie **Zugriffssteuerung (IAM)** aus.
2. Suchen Sie unter **Zugriff überprüfen** nach dem relevanten Konto, und wählen Sie es aus, um die Berechtigungen anzuzeigen.
3. Sie sollten über die Berechtigung **Mitwirkender** oder **Besitzer** verfügen.
    - Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements.
    - Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Rolle zuzuweisen.

Wenn Sie Berechtigungen zuweisen möchten, führen Sie die Schritte unter [Vorbereiten von Azure-Benutzerkonten](../../../migrate/tutorial-discover-vmware.md#prepare-an-azure-user-account) aus.


## <a name="prepare-for-migration"></a>Vorbereiten der Migration

Zur Vorbereitung der Servermigration müssen Sie die Servereinstellungen überprüfen und die Bereitstellung einer Replikationsappliance vorbereiten.

### <a name="check-machine-requirements"></a>Überprüfen der Computeranforderungen

Stellen Sie sicher, dass die Computer die Anforderungen für die Migration zu Azure erfüllen. 

1. [Überprüfen](../../../migrate/migrate-support-matrix-physical-migration.md#physical-server-requirements) Sie die Serveranforderungen.
2. Stellen Sie sicher, dass die Quellcomputer, die Sie in Azure replizieren möchten, die [Anforderungen für Azure-VM](../../../migrate/migrate-support-matrix-physical-migration.md#azure-vm-requirements) erfüllen.
1. Einige [Windows-Quellen](../../../migrate/prepare-for-migration.md#windows-machines) erfordern zusätzliche Änderungen. Wenn Sie die Quelle migrieren, bevor Sie diese Änderungen vornehmen, kann möglicherweise der virtuelle Computer in Azure nicht gestartet wird. Bei einigen Betriebssystemen führt Azure Migrate diese Änderungen automatisch durch. 

### <a name="prepare-for-replication"></a>Vorbereiten der Replikation 

Von der Azure Bei der Servermigration wird eine Replikationsappliance verwendet, um Computer in Azure zu replizieren. Die Replikationsappliance führt die folgenden Komponenten aus: 

- **Konfigurationsserver**: Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation.
- **Prozessserver** Der Prozessserver fungiert als Replikationsgateway. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an ein Cachespeicherkonto in Azure. 

Bereiten Sie die Bereitstellung der Appliance wie folgt vor:

- Erstellen Sie einen Windows Server 2016-Computer für das Hosten der Replikationsappliance.  Überprüfen Sie die [Computeranforderungen](../../../migrate/migrate-replication-appliance.md#appliance-requirements).
- Die Replikationsappliance verwendet MySQL. Überprüfen Sie die [Optionen](../../../migrate/migrate-replication-appliance.md#mysql-installation) für die Installation von MySQL auf der Appliance.
- Überprüfen Sie die Azure-URLs, die die Replikationsappliance für den Zugriff auf [öffentliche](../../../migrate/migrate-replication-appliance.md#url-access) und [behördliche](../../../migrate/migrate-replication-appliance.md#azure-government-url-access) Clouds benötigt.
- Überprüfen Sie die [Portzugriffsanforderungen](../../../migrate/migrate-replication-appliance.md#port-access) für die Replikationsappliance.

> [!NOTE]
> Die Replikationsappliance sollte auf einem anderen Computer als dem Quellcomputer installiert sein, den Sie replizieren oder migrieren, und nicht auf einem Computer, auf dem die Ermittlungs- und Bewertungsappliance von Azure Migrate bereits installiert war.

### <a name="download-replication-appliance-installer"></a>Herunterladen des Installationsprogramms für die Replikationsappliance

Führen Sie die folgenden Schritte aus, um das Installationsprogramm für die Replikationsappliance herunterzuladen 

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** auf **Ermitteln**.

    ![VMs ermitteln](../../../migrate/media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

1. Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Physisch/Andere (AWS, GCP, Xen usw.)** aus.
1. Wählen Sie unter **Zielregion** die Azure-Region aus, zu der Sie die Computer migrieren möchten.
1. Aktivieren Sie das Kontrollkästchen **Bestätigen Sie, dass die Zielregion für die Migration „<Name der Region>“ lautet**.
1. Klicken Sie auf **Ressourcen erstellen**. Daraufhin wird im Hintergrund ein Azure Site Recovery-Tresor erstellt.
    - Falls Sie die Migration bereits mit dem Tool für die Azure Migrate-Servermigration eingerichtet haben, kann die Zieloption nicht konfiguriert werden, da die Ressourcen bereits vorher eingerichtet wurden.    
    - Nach dem Klicken auf diese Schaltfläche kann die Zielregion für dieses Projekt nicht mehr geändert werden.
    - Alle anschließenden Migrationen erfolgen zu dieser Region.

1. Wählen Sie unter **Möchten Sie eine neue Replikationsappliance installieren oder ein vorhandenes Setup horizontal hochskalieren?** die Option **Replikationsappliance installieren**.
1. Führen Sie unter **Laden Sie die Software für die Replikationsappliance herunter, und installieren Sie sie** den Download des Installationsprogramms für die Appliance und des Registrierungsschlüssels durch. Sie benötigen den Schlüssel zum Registrieren der Appliance. Der Schlüssel ist nach dem Herunterladen fünf Tage lang gültig.

    ![Anbieter herunterladen](../../../migrate/media/tutorial-migrate-physical-virtual-machines/download-provider.png)

1. Kopieren Sie die Setupdatei für die Appliance und die Schlüsseldatei auf den Windows Server 2016-Computer, den Sie für die Appliance erstellt haben.
1. Nach Abschluss der Installation wird automatisch der Assistent zur Appliancekonfiguration gestartet. (Sie können den Assistenten auch manuell über die cspsconfigtool-Verknüpfung starten, die auf dem Desktop des Appliancecomputers erstellt wird.) Verwenden Sie die Registerkarte **Konten verwalten** des Assistenten, um ein Dummykonto mit den folgenden Details zu erstellen:

   -  „Gast“ als Anzeigename
   -  „Benutzername“ als Benutzername
   -  „Kennwort“ als Kennwort für das Konto 
   
   Sie verwenden dieses Dummykonto in Schritt „Replikation aktivieren“. 

1. Wählen Sie nach dem Setup und Neustart der Appliance im Bereich **Computer ermitteln** die neue Appliance unter **Konfigurationsserver auswählen** aus, und klicken Sie anschließend auf **Registrierung abschließen**. Beim Abschließen der Registrierung werden einige letzte Schritte ausgeführt, um die Replikationsappliance vorzubereiten.

    ![Registrierung abschließen](../../../migrate/media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)


## <a name="install-the-mobility-service"></a>Installieren des Mobilitätsdiensts 

Installieren Sie den Mobilitätsdienst-Agent auf den Servern, die Sie migrieren möchten. Die Installationsprogramme für den Agent sind auf der Replikationsappliance verfügbar. Sie können nach dem richtigen Installationsprogramm suchen und den Agent auf jedem Computer installieren, den Sie migrieren möchten. 

Führen Sie die folgenden Schritte aus, um den Mobilitätsdienst zu installieren 

1. Melden Sie sich an der Replikationsappliance an.
2. Navigieren Sie zu **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Suchen Sie nach dem richtigen Installationsprogramm für das Betriebssystem und die Version des Computers. Lesen Sie den Artikel zu den [unterstützten Betriebssystemen](../../../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines). 
4. Kopieren Sie die Installationsdatei auf den Computer, den Sie migrieren möchten.
5. Stellen Sie sicher, dass Sie über die Passphrase verfügen, die bei der Bereitstellung des Geräts generiert wurde.
    - Speichern Sie die Datei in einer temporären Textdatei auf dem Computer.
    - Sie können die Passphrase über die Replikationsappliance abrufen. Führen Sie in der Befehlszeile **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** aus, um die aktuelle Passphrase anzuzeigen.
    - Generieren Sie die Passphrase nicht neu. Hierbei wird die Verbindung getrennt, und Sie müssen die Replikationsappliance erneut registrieren.
    - Geben Sie im Parameter */Platform* *VMware* sowohl für VMware-Computer als auch für physische Computer an. 

1. Stellen Sie eine Verbindung mit dem Computer her, und extrahieren Sie den Inhalt der Installationsdatei in einen lokalen Ordner (z. B. C:\temp). Führen Sie den folgenden Befehl in einer Administratorbefehlsaufforderung aus: 

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

2. Führen Sie das Mobility Service-Installationsprogramm aus:

    ```
    UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```

3. Registrieren Sie den Agent bei der Replikationsappliance:

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

Nach der Installation kann es einige Zeit dauern, bis die ermittelten Computer unter „Azure Migrate: Servermigration“ angezeigt werden. Wenn VMs ermittelt werden, erhöht sich die Anzahl unter **Ermittelte Server**.

![Ermittelte Server](../../../migrate/media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)

## <a name="prepare-source-machines"></a>Vorbereiten von Quellcomputern

Zum Vorbereiten von Quellcomputern benötigen Sie Informationen aus dem Cluster. 

> [!CAUTION]
> - Behalten Sie den Datenträgerbesitz während des gesamten Replikationsprozesses bis zur endgültigen Übernahme bei. Wenn sich der Datenträgerbesitz ändert, besteht die Möglichkeit, dass die Volumes beschädigt werden und die Replikation erneut ausgelöst werden muss. Legen Sie den bevorzugten Besitzer für jeden Datenträger fest, um die Besitzübertragung während des Replikationsprozesses zu vermeiden. 
> - Vermeiden Sie Patchaktivitäten und Systemneustarts während des Replikationsprozesses, um die Übertragung des Datenträgerbesitzes zu vermeiden.

Bereiten Sie Quellcomputer folgendermaßen vor: 

1. **Identifizieren des Datenträgerbesitzes:** Melden Sie sich bei einem der Clusterknoten an, und öffnen Sie den Failovercluster-Manager. Identifizieren Sie den Besitzerknoten für die Datenträger, um die Datenträger zu bestimmen, die mit jedem Server migriert werden müssen.  
2. **Abrufen der Clusterinformationen**: Führen Sie das Skript `Get-ClusterInfo.ps1` auf einem Clusterknoten aus, um Informationen zu den Clusterressourcen abzurufen. Das Skript gibt den Rollennamen, den Ressourcennamen, die IP-Adresse und den Testport in der Datei `Cluster-Config.csv` aus. Verwenden Sie diese CSV-Datei, um später in diesem Artikel Ressourcen in Azure zu erstellen und zuzuweisen.  
    
   ```powershell
   ./Get-ClusterInfo.ps1
   ```

## <a name="create-load-balancer"></a>Erstellen eines Load Balancers

Damit die Cluster und Clusterrollen ordnungsgemäß auf Anforderungen reagieren können, ist Azure Load Balancer erforderlich. Ohne Lastenausgleich können die anderen virtuellen Computer die Cluster-IP-Adresse nicht erreichen, da sie nicht als zum Netzwerk oder zum Cluster gehörend erkannt wird. 

1. Füllen Sie die Spalten in der Datei `Cluster-Config.csv` aus: 

    **Spaltenheader** | **Beschreibung**
    --- | ---
    NewIP | Geben Sie die IP-Adresse im virtuellen Azure-Netzwerk (oder Subnetz) für die einzelnen Ressourcen in der CSV-Datei an.
    ServicePort | Geben Sie den Dienstport an, der von den einzelnen Ressourcen in der CSV-Datei verwendet werden soll. Verwenden Sie für die SQL-Clusterressource in der CSV-Datei den gleichen Wert für den Dienstport wie für den Testport. Für die anderen Clusterrollen wird der Standardwert 1433 verwendet. Sie können jedoch weiterhin die Portnummern verwenden, die im aktuellen Setup konfiguriert sind. 


1. Führen Sie das Skript `Create-ClusterLoadBalancer.ps1` aus, um den Lastenausgleich mit den folgenden obligatorischen Parametern zu erstellen: 

    **Parameter** | **Typ** | **Beschreibung**
    --- | --- | ---
    ConfigFilePath | Obligatorisch. |  Geben Sie den Pfad für die Datei `Cluster-Config.csv` an, die Sie im vorherigen Schritt ausgefüllt haben.
    ResourceGroupName | Obligatorisch.| Geben Sie den Namen der Ressourcengruppe an, in der der Lastenausgleich erstellt werden soll. 
    VNetName | Obligatorisch.| Geben Sie den Namen des virtuellen Azure-Netzwerks an, dem der Lastenausgleich zugeordnet werden soll. 
    SubnetName | Obligatorisch.| Geben Sie den Namen des Subnetzes im virtuellen Azure-Netzwerk an, dem der Lastenausgleich zugeordnet werden soll. 
    VNetResourceGroupName | Obligatorisch.| Geben Sie den Namen der Ressourcengruppe für das virtuelle Azure-Netzwerk an, dem der Lastenausgleich zugeordnet werden soll. 
    Location | Obligatorisch.| Geben Sie den Speicherort an, an dem der Lastenausgleich erstellt werden soll. 
    LoadBalancerName | Obligatorisch.| Geben Sie den Namen des zu erstellenden Lastenausgleichs an. 


    ```powershell
    ./Create-ClusterLoadBalancer.ps1 -ConfigFilePath ./cluster-config.csv -ResourceGroupName $resoucegroupname -VNetName $vnetname -subnetName $subnetname -VnetResourceGroupName $vnetresourcegroupname -Location “eastus” -LoadBalancerName $loadbalancername
    ```

## <a name="replicate-machines"></a>Replizieren von Computern

Wählen Sie nun Computer für die Migration aus. Sie können bis zu zehn Computer gleichzeitig replizieren. Müssen Sie mehr Computer replizieren, führen Sie die Replikation in Batches mit jeweils zehn Computern durch.

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** die Option **Replizieren** aus.

    ![Screenshot: Bildschirm „Azure Migrate – Server“ mit hervorgehobener Schaltfläche „Replizieren“ unter „Azure Migrate: Servermigration“ im Abschnitt „Migrationstools“](../../../migrate/media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

1. Wählen Sie unter **Replizieren** > **Quelleinstellungen** > **Sind Ihre Computer virtualisiert?** die Option **Physisch oder andere (AWS, GCP, Xen usw.)** aus.
1. Wählen Sie unter **Lokale Appliance** den Namen der Azure Migrate-Appliance aus, die Sie eingerichtet haben.
1. Wählen Sie unter **Prozessserver** den Namen der Replikationsappliance aus.
1. Wählen Sie unter **Gastanmeldeinformationen** das Dummykonto aus, das Sie beim [Setup des Replikationsinstallationsprogramms](#download-replication-appliance-installer) erstellt haben. Klicken Sie anschließend auf **Weiter: Virtuelle Computer**.

    ![Screenshot: Registerkarte „Quelleinstellungen“ im Bildschirm „Replizieren“ mit hervorgehobenem Feld „Gastanmeldeinformationen“](../../../migrate/media/tutorial-migrate-physical-virtual-machines/source-settings.png)

1. Übernehmen Sie im Bereich **Virtuelle Computer** unter **Migrationseinstellungen aus einer Bewertung importieren?** die Standardeinstellung **Nein, ich gebe die Migrationseinstellungen manuell an**.
1. Wählen Sie alle VMs aus, die Sie migrieren möchten. Wählen Sie anschließend **Next: Zieleinstellungen**.

    ![Auswählen von VMs](../../../migrate/media/tutorial-migrate-physical-virtual-machines/select-vms.png)


1. Wählen Sie unter **Zieleinstellungen** das Abonnement und die Zielregion für die Migration aus, und geben Sie die Ressourcengruppe an, in der sich die Azure-VMs nach der Migration befinden.
1. Wählen Sie unter **Virtuelles Netzwerk** das Azure-VNET/-Subnetz aus, in das die Azure-VMs nach der Migration eingebunden werden.
1. Wählen Sie unter **Verfügbarkeitsoptionen** Folgendes aus:
    -  Verfügbarkeitszone, um den migrierten Computer einer bestimmten Verfügbarkeitszone in der Region anzuheften. Verteilen Sie mit dieser Option Server, die eine Anwendungsebene mit mehreren Knoten bilden, über Verfügbarkeitszonen. Wenn Sie diese Option auswählen, müssen Sie die Verfügbarkeitszone angeben, die für jeden auf der Registerkarte „Compute“ ausgewählten Computer verwendet werden soll. Diese Option ist nur verfügbar, wenn die für die Migration ausgewählte Zielregion Verfügbarkeitszonen unterstützt.
    -  Verfügbarkeitsgruppe, um den migrierten Computer in einer Verfügbarkeitsgruppe zu platzieren. Um diese Option verwenden zu können, muss die ausgewählte Zielressourcengruppe über mindestens eine Verfügbarkeitsgruppe verfügen.
    - Die Infrastrukturredundanzoption ist nicht erforderlich, wenn Sie keine dieser Verfügbarkeitskonfigurationen für die migrierten Computer benötigen.
    
1. Wählen Sie unter **Datenträgerverschlüsselungstyp** Folgendes aus:
    - Verschlüsselung ruhender Daten mit plattformseitig verwaltetem Schlüssel
    - Verschlüsselung ruhender Daten mit kundenseitig verwaltetem Schlüssel
    - Mehrfachverschlüsselung mit plattformseitig und kundenseitig verwalteten Schlüsseln

    > [!NOTE]
    > Um VMs mit CMK zu replizieren, müssen Sie unter der Zielressourcengruppe einen [Datenträgerverschlüsselungssatz erstellen](../../../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set). Ein Datenträgerverschlüsselungssatz-Objekt ordnet verwaltete Datenträger einer Key Vault-Instanz zu, die den für SSE zu verwendenden CMK enthält.
  
1. Wählen Sie unter **Azure-Hybridvorteil**

    - die Option **Nein** aus, falls Sie den Azure-Hybridvorteil nicht anwenden möchten. Wählen Sie **Weiter** aus.
    - Wählen Sie **Ja** aus, wenn Sie über Windows Server-Computer verfügen, die durch aktive Software Assurance- oder Windows Server-Abonnements abgedeckt sind, und den Vorteil auf die zu migrierenden Computer anwenden möchten. Wählen Sie **Weiter** aus.

    :::image type="content" source="../../../migrate/media/tutorial-migrate-vmware/target-settings.png" alt-text="Zieleinstellungen":::

1. Überprüfen Sie in **Compute** Name, Größe, Betriebssystem- Datenträger und Verfügbarkeitskonfiguration der VM (falls im vorherigen Schritt ausgewählt). Die VMs müssen die [Azure-Anforderungen](../../../migrate/migrate-support-matrix-physical-migration.md#azure-vm-requirements) erfüllen.

    - **VM-Größe**: Bei Verwendung von Bewertungsempfehlungen zeigt die Dropdownliste für die VM-Größe die empfohlene Größe. Andernfalls wählt Azure Migrate eine Größe basierend auf der höchsten Übereinstimmung im Azure-Abonnement aus. Alternativ können Sie unter **Azure-VM-Größe** manuell eine Größe auswählen.
    - **Betriebssystemdatenträger**: Geben Sie den Betriebssystemdatenträger (Startdatenträger) für die VM an. Der Betriebssystemdatenträger enthält den Bootloader und das Installationsprogramm des Betriebssystems.
    - **Verfügbarkeitszone**: Geben Sie die zu verwendende Verfügbarkeitszone an.
    - **Verfügbarkeitsgruppe**: Geben Sie die zu verwendende Verfügbarkeitsgruppe an.

    ![Computeeinstellungen](../../../migrate/media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

1. Geben Sie unter **Datenträger** an, ob die VM-Datenträger in Azure repliziert werden sollen, und wählen Sie den Datenträgertyp (SSD Standard/HDD Standard oder Managed Disks Premium) in Azure aus. Wählen Sie **Weiter** aus.
    - Verwenden Sie die Liste, die Sie zuvor erstellt haben, um die Datenträger auszuwählen, die mit jedem Server repliziert werden sollen. Schließen Sie andere Datenträger von der Replikation aus.
   

    ![Datenträgereinstellungen](../../../migrate/media/tutorial-migrate-physical-virtual-machines/disks.png)

1. Überprüfen Sie unter **Replikation prüfen und starten** die Einstellungen, und klicken Sie dann auf **Replizieren**, um die erste Replikation für die Server zu starten.

> [!NOTE]
> Sie können die Replikationseinstellungen vor Beginn der Replikation jederzeit unter **Verwalten** > **Aktuell replizierte Computer** aktualisieren. Die Einstellungen können nach dem Beginn der Replikation nicht mehr geändert werden.

## <a name="track-and-monitor"></a>Nachverfolgen und Überwachen

Die Replikation erfolgt in der folgenden Reihenfolge: 

- Wenn Sie **Replizieren** auswählen, beginnt die Durchführung des Auftrags _Replikation starten_. 
- Nachdem der Auftrag _Replikation starten_ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig auf den Replikatdatenträgern in Azure repliziert.
- Nachdem die erste Replikation abgeschlossen ist, konfigurieren Sie die Compute- und Netzwerkelemente für jeden virtuellen Computer. Cluster verfügen in der Regel über mehrere NICs, aber nur eine NIC ist für die Migration erforderlich (legen Sie für die anderen „Nicht erstellen“ fest).

Sie haben die Möglichkeit, den Auftragsstatus über die Portalbenachrichtigungen nachzuverfolgen.

Sie können den Replikationsstatus überwachen, indem Sie auf **Server werden repliziert** klicken (unter **Azure Migrate: Servermigration**).
![Überwachen der Replikation](../../../migrate/media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)


## <a name="migrate-vms"></a>Migrieren virtueller Computer

Nachdem die Computer repliziert wurden, sind sie für die Migration bereit. Führen Sie zum Migrieren Ihrer Server die folgenden Schritte aus 

1. Klicken Sie im Azure Migrate-Projekt unter **Server** > **Azure Migrate: Servermigration** die Option **Server werden repliziert** aus.

    ![Replizieren der Server](../../../migrate/media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

1. Um sicherzustellen, dass der migrierte Server mit dem Quellserver synchronisiert wird, beenden Sie die SQL Server-Ressource (in **Failovercluster-Manager** > **Rollen** > **Andere Ressourcen**), während Sie gleichzeitig sicherstellen, dass die Clusterdatenträger online sind.   
1. Wählen Sie unter **Aktuell replizierte Computer** den Servernamen aus, wählen Sie dann **Übersicht** aus, und stellen Sie sicher, dass der letzte synchronisierte Zeitstempel nach dem Beenden der SQL Server-Ressource auf den zu migrierenden Servern liegt, bevor Sie mit dem nächsten Schritt fortfahren. Dieser Vorgang sollte nur wenige Minuten dauern. 
1. Klicken Sie unter **Aktuell replizierte Computer** mit der rechten Maustaste auf die VM und dann auf **Migrieren**.
1. Wählen Sie unter **Migrieren** > **Virtuelle Computer herunterfahren und eine geplante Migration ohne Datenverlust durchführen?** die Option **Nein** >  und anschließend **OK** aus.
   
   > [!NOTE]
   > Bei der Migration physischer Server wird das automatische Herunterfahren des Quellcomputers nicht unterstützt. Es wird empfohlen, die Anwendung während des Migrationszeitfensters offline zu schalten. (Die Anwendungen dürfen keine Verbindungen akzeptieren.) Initiieren Sie anschließend die Migration. (Der Server muss weiterhin ausgeführt werden, damit Änderungen vor Abschluss der Migration synchronisiert werden können.)

1. Für den virtuellen Computer wird ein Migrationsauftrag gestartet. Verfolgen Sie den Auftrag anhand der Azure-Benachrichtigungen nach.
1. Nach Abschluss des Auftrags können Sie die VM auf der Seite **Virtuelle Computer** anzeigen und verwalten.

## <a name="reconfigure-cluster"></a>Neukonfigurieren des Clusters 

Konfigurieren Sie nach der Migration Ihrer virtuellen Computer den Cluster neu. Folgen Sie diesen Schritten: 

1. Fahren Sie die migrierten Server in Azure herunter.
1. Fügen Sie die migrierten Computer dem Back-End-Pool des Lastenausgleichs hinzu. Navigieren Sie zu **Load Balancer** > **Back-End-Pools**, wählen Sie den Back-End-Pool aus, und **fügen Sie die migrierten Computer hinzu**. 

1. Konfigurieren Sie die migrierten Datenträger der Server als freigegebene Datenträger neu, indem Sie das Skript `Create-SharedDisks.ps1` ausführen. Das Skript ist interaktiv, fordert zur Eingabe einer Liste von Computern auf und zeigt dann die verfügbaren Datenträger an, die extrahiert werden sollen (nur Datenträger für Daten). Sie werden einmal aufgefordert, auszuwählen, welche Computer die Laufwerke enthalten, die in freigegebene Datenträger umgewandelt werden sollen. Nach der Auswahl werden Sie erneut aufgefordert, einmal pro Computer die spezifischen Datenträger auszuwählen. 

   **Parameter** | **Typ** | **Beschreibung**
   --- | --- | ---
   ResourceGroupName | Obligatorisch. |  Geben Sie den Namen der Ressourcengruppe an, die die migrierten Server enthält.
   NumberofNodes | Optional | Geben Sie die Anzahl der Knoten in Ihrer Failoverclusterinstanz an. Mit diesem Parameter wird die richtige SKU für die freigegebenen Datenträger identifiziert, die erstellt werden sollen. Standardmäßig geht das Skript davon aus, dass die Anzahl der Knoten im Cluster 2 beträgt.   
   DiskNamePrefix | Optional | Geben Sie das Präfix an, das Sie den Namen Ihrer freigegebenen Datenträger hinzufügen möchten. 
   
   ```powershell
   ./Create-SharedDisks.ps1 -ResourceGroupName $resoucegroupname -NumberofNodes $nodesincluster -DiskNamePrefix $disknameprefix 
   ```

1. Fügen Sie die freigegebenen Datenträger den migrierten Servern an, indem Sie das Skript `Attach-SharedDisks.ps1` ausführen. 

   **Parameter** | **Typ** |**Beschreibung**
   --- | ---  | ---
   ResourceGroupName | Obligatorisch. | Geben Sie den Namen der Ressourcengruppe an, die die migrierten Server enthält.
   StartingLunNumber | Optional |Geben Sie die Start-LUN-Nummer an, der die freigegebenen Datenträger angefügt werden können. Standardmäßig versucht das Skript, freigegebene Datenträger LUNs ab 0 anzufügen.  
   
   ```powershell
   ./Attach-ShareDisks.ps1 -ResourceGroupName $resoucegroupname 
   ```

1. Starten Sie die migrierten Server in Azure, und melden Sie sich bei einem beliebigen Knoten an. 

1. Kopieren Sie die Datei `ClusterConfig.csv`, und führen Sie das Skript `Update-ClusterConfig.ps1` aus, indem Sie die CSV-Datei als Parameter übergeben. Dadurch wird sichergestellt, dass die Clusterressourcen mit der neuen Konfiguration aktualisiert werden und der Cluster in Azure funktioniert. 

   ```powershell
   ./Update-ClusterConfig.ps1 -ConfigFilePath $filepath
   ```

Ihre SQL Server-Failoverclusterinstanz ist bereit. 

## <a name="complete-the-migration"></a>Fertigstellen der Migration

1. Klicken Sie nach Abschluss der Migration mit der rechten Maustaste auf die VM und dann auf **Migration beenden**. Die folgenden Schritte werden ausgeführt:
    - Beendet die Replikation für den lokalen Computer.
    - Entfernt den Computer aus dem Zähler **Server werden repliziert.** in Azure Migrate: Servermigration.
    - Bereinigt die Replikationsstatusinformationen für den Computer.
1. Installieren Sie den [Windows](../../../virtual-machines/extensions/agent-windows.md)-Agent für die Azure-VM auf den migrierten Computern.
1. Führen Sie App-Anpassungen nach der Migration durch, z.B. die Aktualisierung von Datenbankverbindungszeichenfolgen und Webserverkonfigurationen.
1. Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
1. Leiten Sie den Datenverkehr auf die migrierte Instanz der Azure-VM um.
1. Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
1. Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
1. Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs. 

## <a name="post-migration-best-practices"></a>Bewährte Methoden nach der Migration

- Für SQL Server:
    -  Installieren Sie die [Erweiterung für SQL Server-IaaS-Agent](../../virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md), um Verwaltungsaufgaben zu automatisieren. 
    - [Optimieren](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md) Sie die Leistung von SQL Server auf Azure-VMs. 
    - Grundlegendes zu den [Preisen](../../virtual-machines/windows/pricing-guidance.md#free-licensed-sql-server-editions) für SQL Server in Azure. 
- Beachten Sie zur Steigerung der Resilienz Folgendes:
    - Schützen Sie Ihre Daten, indem Sie Azure-VMs mit dem [Azure Backup-Dienst](../../../backup/quick-backup-vm-portal.md) sichern. 
    - Sorgen Sie für die kontinuierliche Ausführung und Verfügbarkeit von Workloads, indem Sie Azure-VMs mithilfe von [Site Recovery](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md) in eine sekundäre Region replizieren.
- Beachten Sie zur Steigerung der Sicherheit Folgendes:
    - Sperren und beschränken Sie den Zugriff von eingehendem Datenverkehr mit der [Just-In-Time-Verwaltung bei Microsoft Defender für Cloud](../../../security-center/security-center-just-in-time.md).
    - Beschränken Sie den Netzwerkdatenverkehr mithilfe von [Netzwerksicherheitsgruppen](../../../virtual-network/network-security-groups-overview.md) auf Verwaltungsendpunkte.
    - Stellen Sie [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) bereit, um Datenträger und Daten vor Diebstahl und unbefugtem Zugriff zu schützen.
    - Erfahren Sie mehr über das [Schützen von IaaS-Ressourcen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), und besuchen Sie die Webseite [Microsoft Defender für Cloud](https://azure.microsoft.com/services/security-center/).
- Beachten Sie zur Überwachung und Verwaltung Folgendes:
    - Ziehen Sie die Bereitstellung von [Azure Cost Management](../../../cost-management-billing/cost-management-billing-overview.md) in Erwägung, um den Ressourceneinsatz und die Ausgaben zu überwachen.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Artikel zur [Cloudmigration](/azure/architecture/cloud-adoption/getting-started/migrate) des Frameworks für die Cloudeinführung (Cloud Adoption Framework) an.
