---
title: MABS (Azure Backup Server) V3 UR1-Schutzmatrix
description: Dieser Artikel bietet eine Unterstützungsmatrix aller Workloads, Datentypen und Installationen, die von Azure Backup Server geschützt werden.
ms.date: 07/27/2021
ms.topic: conceptual
ms.openlocfilehash: c3ad2fe2b74179f3797f8986e6a16c9c36d1b2a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446788"
---
# <a name="mabs-azure-backup-server-v3-ur1-and-later-protection-matrix"></a>MABS (Azure Backup Server) V3 UR1-Schutzmatrix (und höher)

In diesem Artikel sind verschiedene Server und Workloads aufgeführt, die Sie mit Azure Backup Server schützen können. In der folgenden Matrix sind die Elemente aufgelistet, die mit Azure Backup Server geschützt werden können.

Verwenden Sie die folgende Matrix für MABS v3 UR1 (und höher):

* Workloads – Der Workloadtyp der Technologie.

* Version – Unterstützte MABS-Version für die Workloads.

* MABS-Installation – Der Computer/Standort, auf dem bzw. an dem Sie MABS installieren möchten.

* Schutz und Wiederherstellung – Listen Sie die detaillierten Informationen zu den Workloads auf, wie z. B. unterstützter Speichercontainer oder unterstützte Bereitstellung.

>[!NOTE]
>Die Unterstützung für den 32-Bit-Schutz-Agent ist mit MABS v3 UR1 (und höher) veraltet. Informationen dazu finden Sie unter [Eingestellte Unterstützung für den 32-Bit-Schutz-Agent](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Schutzunterstützungsmatrix

In den folgenden Abschnitten wird die Schutzunterstützungsmatrix für MABS detailliert beschrieben:

* [Anwendungssicherung](#applications-backup)
* [VM-Sicherung](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Anwendungssicherung

| **Workload**               | **Version**                                                  | **Azure Backup Server V2-Installation**                       | **Unterstützte Azure Backup Server-Instanz** | **Schutz und Wiederherstellung**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Clientcomputer (64-Bit) | Windows 10                                                  | Physischer Server  <br><br>    Virtueller Hyper-V-Computer   <br><br>   Virtueller VMware-Computer | V3 UR1 und V3 UR2                            | Volume, Freigabe, Ordner, Dateien, deduplizierte Volumes   <br><br>   Geschützte Volumes müssen mit NTFS formatiert sein. FAT und FAT32 werden nicht unterstützt.  <br><br>    Volumes müssen mindestens 1 GB groß sein. Azure Backup Server verwendet den Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS) für die Daten der Momentaufnahme, und die Momentaufnahme funktioniert nur, wenn das Volume mindestens 1GB groß ist. |
| Server (64 Bit)          | Windows Server 2022, 2019, 2016, 2012 R2, 2012                    | Virtueller Azure-Computer (wenn die Workload als virtueller Azure-Computer ausgeführt wird)  <br><br>    Physischer Server  <br><br>    Virtueller Hyper-V-Computer <br><br>     Virtueller VMware-Computer  <br><br>    Azure Stack | V3 UR1 und V3 UR2                            | Volume, Freigabe, Ordner, Datei <br><br>    deduplizierte Volumes (nur NTFS) <br><br>Beim Schutz eines deduplizierten WS 2016 NTFS-Volumes mit MABS v3 auf Windows Server 2019 können die Wiederherstellungen betroffen sein. Es gibt einen Fix für Wiederherstellungen ohne Deduplizierung, die in neuere Versionen von MABS integriert wird. Wenden Sie sich an den MABS-Support, wenn Sie diesen Fix für MABS v3 UR1 benötigen.<br><br> Beim Schutz eines deduplizierten WS 2019 NTFS-Volumes mit MABS v3 auf Windows Server 2016 werden die Sicherungen und Wiederherstellungen nicht dedupliziert. Das bedeutet, dass die Sicherungen mehr Platz auf dem MABS-Server verbrauchen als das ursprüngliche deduplizierte NTFS-Volume.   <br><br>   Systemstatus und Bare-Metal (wird nicht unterstützt, wenn die Workload als virtueller Azure-Computer ausgeführt wird) |
| Server (64 Bit)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (Sie müssen [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616) installieren.) | Physischer Server  <br><br>    Virtueller Hyper-V-Computer  <br><br>      Virtueller VMware-Computer  <br><br>   Azure Stack | V3 UR1 und V3 UR2                           | Volume, Freigabe, Ordner, Datei, Systemstatus/Bare-Metal        |
| SQL Server                | SQL Server 2019, 2017, 2016 und [unterstützte SPs](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 und unterstützte [SPs](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Physischer Server  <br><br>     Virtueller Hyper-V-Computer   <br><br>     Virtueller VMware-Computer  <br><br>   Virtueller Azure-Computer (wenn die Workload als virtueller Azure-Computer ausgeführt wird)  <br><br>     Azure Stack | V3 UR1 und V3 UR2                            | Alle Bereitstellungsszenarien: Datenbank       <br><br>    MABS v3 UR2 und höher unterstützt die Sicherung von SQL-Datenbanken, die auf dem freigegebenen Clustervolume (CSV) gespeichert sind.     <br><br>  MABS v3 UR1 unterstützt die Sicherung von SQL-Datenbanken über ReFS-Volumes.     <br><br>     MABS unterstützt keine auf Windows Server 2012-Dateiservern mit horizontaler Skalierung gehostete SQL Server-Datenbanken. <br><br>   MABS kann weder Verfügbarkeitsgruppen noch verteilte Verfügbarkeitsgruppen von SQL Server schützen, bei denen sich der Rollenname im Failovercluster von der benannten Verfügbarkeitsgruppe in SQL unterscheidet.       |
| Exchange                   | Exchange 2019, 2016                                         | Physischer Server   <br><br>   Virtueller Hyper-V-Computer  <br><br>      Virtueller VMware-Computer  <br><br>   Azure Stack  <br><br>    Virtueller Azure-Computer (wenn die Workload als virtueller Azure-Computer ausgeführt wird) | V3 UR1 und V3 UR2                            | Schutz (alle Bereitstellungsszenarien): Eigenständiger Exchange-Server, Datenbank unter einer Database Availability Group (DAG)  <br><br>    Wiederherstellen (alle Bereitstellungsszenarien): Postfach, Postfachdatenbanken unter einer DAG    <br><br>  Die Sicherung von Exchange über ReFS wird mit MABS v3 UR1 unterstützt. |
| SharePoint                 | SharePoint 2019, 2016 mit neuesten SPs                       | Physischer Server  <br><br>    Virtueller Hyper-V-Computer <br><br>    Virtueller VMware-Computer  <br><br>   Virtueller Azure-Computer (wenn die Workload als virtueller Azure-Computer ausgeführt wird)   <br><br>   Azure Stack | V3 UR1 und V3 UR2                            | Schutz (alle Bereitstellungsszenarien): Farm, Inhalt von Front-End-Webserver  <br><br>    Wiederherstellen (alle Bereitstellungsszenarien): Farm, Datenbank, Webanwendung, Datei oder Listenelement, SharePoint-Suche, Front-End-Webserver  <br><br>    Der Schutz von SharePoint-Farmen, die für die Inhaltsdatenbanken das Always On-Feature von SQL Server 2012 verwenden, wird nicht unterstützt. |

## <a name="vm-backup"></a>VM-Sicherung

| **Workload**                                                 | **Version**                                             | **Azure Backup Server V2-Installation**                      | **Unterstützte Azure Backup Server-Instanz** | **Schutz und Wiederherstellung**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V-Host – MABS-Schutz-Agent auf Hyper-V-Hostserver, Cluster oder VM | Windows Server 2022, 2019, 2016, 2012 R2, 2012               | Physischer Server  <br><br>    Virtueller Hyper-V-Computer <br><br>    Virtueller VMware-Computer | V3 UR1 und V3 UR2                                 | Schützen: Virtuelle Computer, freigegebene Clustervolumes (Cluster Shared Volumes, CSVs)  <br><br>    Wiederherstellung: VM, die Wiederherstellung von Dateien und Ordnern auf Elementebene ist nur für Windows, Volumes und virtuelle Datenträger verfügbar |
| Azure Stack HCI  |       V1 und 20H2      |    Physischer Server        <br><br>     Virtueller Hyper-V-/Azure Stack HCI-Computer     <br><br>    Virtueller VMware-Computer     |    V3 UR2 und höher   | Schützen: Virtuelle Computer, freigegebene Clustervolumes (Cluster Shared Volumes, CSVs)      <br><br>     Wiederherstellen: Virtueller Computer, Wiederherstellung von Dateien und Ordnern auf Elementebene ist nur für Windows, Volumes und virtuelle Datenträger verfügbar |
| Virtuelle VMware-Computer                                                  | VMware-Server 5.5, 6.0 oder 6.5, 6.7 (lizenzierte Version) | Virtueller Hyper-V-Computer  <br><br>   Virtueller VMware-Computer         | V3 UR1                             | Schützen:  VMware-Ms in freigegebenen Clustervolumen (CSVs), NFS und im SAN-Speicher   <br><br>     Wiederherstellung: VM, die Wiederherstellung von Dateien und Ordnern auf Elementebene ist nur für Windows, Volumes und virtuelle Datenträger verfügbar <br><br>    VMware-vApps werden nicht unterstützt. |
| VMware-VMs      |   VMware-Server 7.0, 6.7, 6.5 oder 6.0 (lizenzierte Version)     |    Virtueller Hyper-V-Computer     <br><br>    Virtueller VMware-Computer    |   V3 UR2 und höher    |   Schützen: VMware-VMs auf freigegebenen Clustervolumes (CSV), NFS und SAN-Speicher     <br><br>    Wiederherstellen: Virtueller Computer, Wiederherstellung von Dateien und Ordnern auf Elementebene ist nur für Windows, Volumes und virtuelle Datenträger verfügbar     <br><br>    VMware vApp wird nicht unterstützt.  |

>[!NOTE]
> MABS unterstützt nicht die Sicherung von virtuellen Computern mit Pass-Through-Datenträgern oder VMs, die eine Remote-VHD (virtuelle Festplatte) verwenden. In diesen Szenarios wird empfohlen, dass Sie eine Sicherung auf Gastebene mit MABS verwenden und einen Agent zum Sichern der Daten auf der VM installieren.

## <a name="linux"></a>Linux

| **Workload** | **Version**                               | **Azure Backup Server V2-Installation**                      | **Unterstützte Azure Backup Server-Instanz** | **Schutz und Wiederherstellung**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux, ausgeführt als [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md)- oder [VMware](backup-azure-backup-server-vmware.md)-Gast | Physischer Server, lokale Hyper-V-VM, Windows-VM in VMware | V3 UR1 und V3 UR2                             | Hyper-V muss unter Windows Server 2012 R2, Windows Server 2016 oder Windows Server 2019 ausgeführt werden. Schützen:  Gesamter virtueller Computer   <br><br>   Wiederherstellen: Gesamter virtueller Computer   <br><br>    Es werden nur dateikonsistente Momentaufnahmen unterstützt.    <br><br>   Eine vollständige Liste der unterstützten Linux-Verteilungen und -Versionen finden Sie im Artikel [Linux auf von Azure unterstützten Verteilungen](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute-Unterstützung

Sie können Ihre Daten über Azure ExpressRoute mit öffentlichem Peering (verfügbar für alte Verbindungen) und Microsoft-Peering sichern. Die Sicherung über privates Peering wird nicht unterstützt.

Bei öffentlichem Peering: Stellen Sie den Zugriff auf die folgenden Domänen/Adressen sicher:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-Adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

Mit Microsoft-Peering: Wählen Sie die folgenden Dienste, Regionen und relevanten Communitywerte aus:

* Azure Active Directory (12076:5060)
* Microsoft Azure-Region (entsprechend dem Standort Ihres Recovery Services-Tresors)
* Azure Storage (entsprechend dem Standort Ihres Recovery Services-Tresors)

Weitere Informationen finden Sie unter [ExpressRoute-Routinganforderungen](../expressroute/expressroute-routing.md).

>[!NOTE]
>Öffentliches Peering gilt für neue Leitungen als veraltet.

## <a name="cluster-support"></a>Unterstützung für Cluster

Azure Backup Server kann Daten in den folgenden gruppierten Anwendungen schützen:

* Dateiserver

* SQL Server

* Hyper-V: Wenn Sie einen Hyper-V-Cluster mithilfe von horizontal skaliertem MABS-Schutz-Agent schützen, können Sie keinen sekundären Schutz für die geschützten Hyper-V-Workloads hinzufügen.

* Exchange Server – Azure Backup Server kann nicht freigegebene Datenträgercluster für unterstützte Exchange Server-Versionen schützen (fortlaufende Clusterreplikation). Auch der Schutz von Exchange Server in einer Konfiguration für fortlaufende lokale Replikation ist mit Azure Backup Server möglich.

* SQL Server – Azure Backup Server unterstützt nicht das Sichern von SQL Server-Datenbanken, die auf freigegebenen Clustervolumes (CSVs) gehostet werden.

>[!NOTE]
>MABS unterstützt nur den Schutz von Hyper-V-VMs auf freigegebenen Volumes in Clustern. Der Schutz anderer auf freigegebenen Clustervolumes gehosteten Arbeitsauslastungen wird nicht unterstützt.

Azure Backup Server kann Workloads schützen, die sich in derselben Domäne wie der MABS-Server, in einer untergeordneten oder in einer vertrauenswürdigen Domäne befinden. Wenn Sie Datenquellen in nicht vertrauenswürdigen Domänen oder Arbeitsgruppen schützen möchten, verwenden Sie NTLM, Zertifikatauthentifizierung für einen einzelnen Server oder Zertifikatauthentifizierung nur für einen Cluster.

## <a name="data-protection-issues"></a>Probleme beim Schutz von Daten

* MABS kann VMs mit freigegebenen Laufwerken (die möglicherweise an andere VMs angefügt sind) nicht sichern, da Hyper-V-VSS Writer keine Volumes sichern kann, die von freigegebenen VHDs gesichert werden.

* Wenn Sie einen freigegebenen Ordner schützen, enthält der Pfad zum freigegebenen Ordner den logischen Pfad im Volume. Wenn Sie den freigegebenen Ordner verschieben, tritt beim Schutz ein Fehler auf. Gehen Sie wie folgt vor, wenn Sie einen geschützten freigegebenen Ordner verschieben müssen: Entfernen Sie ihn aus der Schutzgruppe, und fügen Sie ihn nach dem Verschieben dann wieder dem Schutz hinzu.  Außerdem schlägt der Schutz von Daten fehl, wenn Sie den Pfad einer geschützten Datenquelle auf einem Volume ändern, das ein verschlüsselndes Dateisystem verwendet, und der neue Dateipfad 5120 Zeichen überschreitet.

* Sie können die Domäne eines geschützten Computers nicht ändern, ohne den Schutz nicht zu unterbrechen. Darüber hinaus können Sie die Domäne eines geschützten Computers nicht ändern und die vorhandenen Replikate und Wiederherstellungspunkte dem Computer zuordnen, wenn dieser wieder geschützt wird. Wenn Sie die Domäne eines geschützten Computers ändern müssen, entfernen Sie zunächst die Datenquellen auf dem Computer aus dem Schutz. Schützen Sie dann nach dem Ändern der Domäne die Datenquelle auf dem Computer.

* Sie können den Namen eines geschützten Computers nicht ändern und den Schutz fortsetzen, ohne den Schutz zu unterbrechen. Außerdem können Sie den Namen eines geschützten Computers nicht ändern und die vorhandenen Replikate und Wiederherstellungspunkte dem Computer zuordnen, wenn dieser wieder geschützt wird. Wenn Sie den Namen eines geschützten Computers ändern müssen, dann entfernen Sie zunächst die Datenquellen auf dem Computer aus dem Schutz. Schützen Sie dann die Datenquelle auf dem Computer, nachdem sie einen neuen Namen hat.

* MABS identifiziert die Zeitzone eines geschützten Computers während der Installation des Schutz-Agents automatisch. Wenn ein geschützter Computer nach dem Konfigurieren des Schutzes in eine andere Zeitzone verschoben wird, müssen Sie die Computerzeit in der Systemsteuerung ändern. Aktualisieren Sie dann die Zeitzone in der MABS-Datenbank.

* MABS kann Workloads in derselben Domäne wie der MABS-Server oder in untergeordneten/vertrauenswürdigen Domänen schützen. Sie können auch die folgenden Workloads in Arbeitsgruppen und nicht vertrauenswürdigen Domänen mithilfe von NTLM oder der Zertifikatauthentifizierung schützen:

  * SQL Server
  * Dateiserver
  * Hyper-V

  Diese Arbeitslasten können auf einem einzelnen Server oder in einer Clusterkonfiguration ausgeführt werden. Ausführliche Informationen zur Unterstützung und zur erforderlichen Authentifizierung, um eine Workload zu schützen, die sich nicht in einer vertrauenswürdigen Domäne befindet, finden Sie unter [Vorbereiten von Computern in Arbeitsgruppen und nicht vertrauenswürdigen Domänen](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019&preserve-view=true#supported-scenarios).

## <a name="unsupported-data-types"></a>Nicht unterstützte Datentypen

MABS unterstützt die folgenden Datentypen derzeit nicht:

* Feste Links

* Analysepunkte, einschließlich DFS-Links und Verknüpfungspunkte

* Bereitstellungspunkt-Metadaten – Eine Schutzgruppe kann Daten mit Bereitstellungspunkten enthalten. In diesem Fall schützt DPM das bereitgestellte Volume, das Ziel des Bereitstellungspunkts ist, aber nicht die Metadaten des Bereitstellungspunkts. Wenn Sie Daten mit Bereitstellungspunkten wiederherstellen, müssen Sie die Hierarchie der Bereitstellungspunkte manuell neu erstellen.

* Daten in bereitgestellten Volumes innerhalb von bereitgestellten Volumes

* Papierkorb

* Auslagerungsdateien

* Ordner "System Volume Information". Sie müssen den Systemstatus des Computers als Mitglied der Schutzgruppe auswählen, um die Systeminformationen eines Computers zu schützen.

* Nicht-NTFS-Volumes

* Dateien, die feste Links oder symbolische Verknüpfungen von Windows Vista enthalten.

* Daten in Dateifreigaben, die Benutzerprofil-Datenträger hosten

* Dateien mit einer der folgenden Kombinationen von Attributen:

  * Verschlüsselung und Analyse

  * Verschlüsselung und Einzelinstanz-Speicherung (Single Instance Storage, SIS)

  * Verschlüsselung und Unterscheidung nach Groß-/Kleinschreibung

  * Verschlüsselung und geringe Datendichte

  * Unterscheidung nach Groß-/Kleinschreibung und SIS

  * Komprimierung und SIS

## <a name="next-steps"></a>Nächste Schritte

* [Unterstützungsmatrix für die Sicherung mit Microsoft Azure Backup Server oder System Center DPM](backup-support-matrix-mabs-dpm.md)