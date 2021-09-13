---
title: Konfigurieren von Windows Server Failover Cluster in Azure VMware Solution vSAN
description: Hier erfahren Sie, wie Sie Windows Server Failover Cluster (WSFC) in Azure VMware Solution vSAN mit nativen freigegebenen Datenträgern einrichten.
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: fcde65b98b3774ee1ef9b15bfa6da3836aaa8a1b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351339"
---
# <a name="configure-windows-server-failover-cluster-on-azure-vmware-solution-vsan"></a>Konfigurieren von Windows Server Failover Cluster in Azure VMware Solution vSAN

In diesem Artikel erfahren Sie mehr über das Konfigurieren des [Failoverclustering in Windows Server](/windows-server/failover-clustering/failover-clustering-overview) in Azure VMware Solution vSAN mit nativen freigegebenen Datenträgern. 

>[!IMPORTANT]
>Die Implementierung in diesem Artikel dient dem Proof of Concept und Pilotzwecken. Es empfiehlt sich, eine CIB-Konfiguration (Cluster-in-a-Box) zu verwenden, bis Platzierungsrichtlinien verfügbar werden.

Windows Server Failover Cluster, früher als Microsoft Service Cluster Service (MSCS) bezeichnet, ist ein Feature des Windows Server-Betriebssystems (OS). WSFC ist eine unternehmenskritische Funktion, die für viele Anwendungen benötigt wird. WSFC ist beispielsweise für die folgenden Konfigurationen erforderlich:

- SQL-Server konfiguriert als:
  - Always On-Failoverclusterinstanz (FCI), für Hochverfügbarkeit auf Instanzebene
  - Always On-Verfügbarkeitsgruppe, für Hochverfügbarkeit auf Datenbankebene
- Windows-Dateidienste:
  - Allgemeine Dateifreigabe auf dem aktiven Clusterknoten
  - Dateiserver mit horizontaler Skalierung (Scale-Out File Server, SOFS), auf dem Dateien auf freigegebenen Clustervolumes (CSV) gespeichert werden
  - Direkte Speicherplätze (S2D): lokale Datenträger, die zum Erstellen von Speicherpools auf verschiedenen Clusterknoten verwendet werden

Sie können den WSFC-Cluster in verschiedenen Azure VMware Solution-Instanzen hosten, die als „Cluster-Across-Box“ (CAB) bezeichnet werden. Sie können den WSFC-Cluster auch auf einem einzelnen Azure VMware Solution-Knoten platzieren. Diese Konfiguration wird als „Cluster-in-a-Box“ (CIB) bezeichnet. Es wird nicht empfohlen, für eine Implementierung in der Produktion eine CIB-Lösung zu verwenden. Wenn der einzelne Azure VMware Solution-Knoten ausfällt, werden alle WSFC-Clusterknoten ausgeschaltet, sodass Downtimes für die Anwendung entstehen. Für Azure VMware Solution sind mindestens drei Knoten in einem privaten Cloudcluster erforderlich.

Dabei ist es wichtig, eine unterstützte WSFC-Konfiguration bereitzustellen. Die Lösung muss in vSphere und mit Azure VMware Solution unterstützt werden. Für VMware ist ein ausführliches Dokument zu WSFC in vSphere 6.7 erhältlich: [Setup for Failover Clustering and Microsoft Cluster Service](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf) (Einrichten von Failoverclustering und Microsoft Cluster Service).

Dieser Artikel bezieht sich auf WSFC unter Windows Server 2016 und Windows Server 2019. Ältere Windows Server-Versionen unterliegen leider nicht mehr dem [grundlegenden Support](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) und werden daher hier nicht berücksichtigt.

Sie müssen zunächst einen [WSFC erstellen](/windows-server/failover-clustering/create-failover-cluster). Verwenden Sie anschließend die in diesem Artikel enthaltenen Informationen, um eine WSFC-Bereitstellung in Azure VMware Solution anzugeben.

## <a name="prerequisites"></a>Voraussetzungen

- Azure VMware Solution-Umgebung
- Installationsmedien für das Microsoft Windows Server-Betriebssystem

## <a name="reference-architecture"></a>Referenzarchitektur

Azure VMware Solution bietet native Unterstützung für das virtualisierte WSFC. Der Dienst unterstützt SCSI3PR (SCSI-3 Persistent Reservations) auf der Ebene virtueller Datenträger. WSFC benötigt diese Unterstützung, um den Zugriff auf einen freigegebenen Datenträger zwischen Knoten zu vermitteln. Die Unterstützung von SCSI3PRs ermöglicht die Konfiguration von WSFC mit einer Datenträgerressource, die nativ für vSAN-Datenspeicher zwischen VMs freigegeben wird.

Die folgende Abbildung veranschaulicht die Architektur von virtuellen WSFC-Knoten in einer privaten Azure VMware Solution-Cloud. Sie zeigt, wo sich Azure VMware Solution, einschließlich der virtuellen WSFC-Server (roter Kasten), relativ zur umfassenderen Azure-Plattform befindet. In der Abbildung ist eine typische Hub-Spoke-Architektur dargestellt. Eine vergleichbare Konfiguration ist jedoch auch bei Verwendung von Azure Virtual WAN möglich. Beide bieten alle Vorteile, die auch andere Azure-Dienste bieten.

:::image type="content" source="media/windows-server-failover-cluster/windows-server-failover-architecture.svg" alt-text="Diagramm: Virtuelle WSFC-Knoten in einer privaten Azure VMware Solution-Cloud" border="false" lightbox="media/windows-server-failover-cluster/windows-server-failover-architecture.svg":::

## <a name="supported-configurations"></a>Unterstützte Konfigurationen

Dies sind die aktuell unterstützten Konfigurationen:

- Microsoft Windows Server 2012 oder höher
- Bis zu fünf Failoverclusteringknoten pro Cluster
- Bis zu vier PVSCSI-Adapter pro VM
- Bis zu 64 Datenträger pro PVSCSI-Adapter

## <a name="virtual-machine-configuration-requirements"></a>VM-Konfigurationsanforderungen

### <a name="wsfc-node-configuration-parameters"></a>Konfigurationsparameter für WSFC-Knoten

- Installieren Sie die neuesten VMware-Tools auf jedem WSFC-Knoten.
- Das Mischen von nicht freigegebenen und freigegebenen Datenträgern auf einem einzelnen virtuellen SCSI-Adapter ist nicht möglich. Wenn der Systemdatenträger (Laufwerk C:) beispielsweise an SCSI0:0 angefügt wurde, wird der erste freigegebene Datenträger an SCSI1:0 angefügt. Für einen VM-Knoten einer WSFC-Instanz gilt das gleiche Limit für virtuelle SCSI-Controller wie bei einer normalen VM, d. h. maximal vier (4) virtuelle SCSI-Controller.
- Die SCSI-IDs der virtuellen Datenträger müssen für alle VMs konsistent sein, die Knoten der gleichen WSFC-Instanz hosten.

| **Komponente** | **Anforderungen** |
| --- | --- |
| VM-Hardwareversion | 11 oder höher zur Unterstützung von Live vMotion |
| Virtuelle NIC | Paravirtualisierte VMXNET3-Netzwerkschnittstelle (NIC). Aktivieren Sie Windows Receive Side Scaling (RSS) im Gastbetriebssysteme für die virtuelle NIC. |
| Arbeitsspeicher | Verwenden Sie den vollständigen VM-Reservierungsspeicher für Knoten im WSFC-Cluster. |
| Erhöhen Sie das E/A-Timeout der einzelnen WSFC-Knoten. | Ändern Sie HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet in mindestens 60 Sekunden. (Wenn Sie den Cluster neu erstellen, wird dieser Wert möglicherweise auf den Standardwert zurückgesetzt, sodass Sie ihn erneut ändern müssen.) |
| Systemüberwachung des Windows-Clusters | Der Wert des SameSubnetThreshold-Parameters der Systemüberwachung des Windows-Clusters muss so geändert werden, dass mindestens 10 versäumte Heartbeats zulässig sind. Dies ist [die Standardeinstellung unter Windows Server 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Diese Empfehlung gilt für alle Anwendungen, die WSFC verwenden, einschließlich freigegebener und nicht freigegebener Datenträger. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>WSFC-Knoten: Konfigurationsparameter für Startdatenträger


| **Komponente** | **Anforderungen** |
| --- | --- |
| SCSI-Controllertyp | LSI Logic SAS |
| Datenträgermodus | Virtuell |
| Freigabe der SCSI-Schnittstelle | Keine |
| Ändern der erweiterten Einstellungen für einen virtuellen SCSI-Controller, der das Startgerät hostet | Fügen Sie auf jedem WSFC-Knoten die folgenden erweiterten Einstellungen hinzu:<br /> scsiX.returnNoConnectDuringAPD = "TRUE"<br />scsiX.returnBusyOnNoConnectStatus = "FALSE"<br />Dabei steht X für die Controller-ID der SCSI-Schnittstelle des Startgeräts. X ist standardmäßig auf „0“ festgelegt. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>WSFC-Knoten: Konfigurationsparameter für freigegebene Datenträger


| **Komponente** | **Anforderungen** |
| --- | --- |
| SCSI-Controllertyp | VMware Paravirtualize (PVSCSI) |
| Datenträgermodus | Unabhängig, persistent (Schritt 2 in der Abbildung unten). Durch die Verwendung dieser Einstellung stellen Sie sicher, dass alle Datenträger von Momentaufnahmen ausgeschlossen werden. Momentaufnahmen werden für WSFC-basierte VMs nicht unterstützt. |
| Freigabe der SCSI-Schnittstelle | Physisch (Schritt 1 in Abbildung unten) |
| Multi-writer-Flag | Nicht verwendet |
| Datenträgerformat | Vollständige (thick) Bereitstellung. (Eager Zeroed Thick (EZT) ist für vSAN nicht erforderlich.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Screenshot: Seite „Einstellungen bearbeiten“ für virtuelle Hardware":::

## <a name="non-supported-scenarios"></a>Nicht unterstützte Szenarien

Die folgenden Funktionen werden für WSFC in Azure VMware Solution nicht unterstützt:

- NFS-Datenspeicher
- Speicherplätze
- vSAN mit iSCSI-Dienst
- vSAN Stretched Cluster
- Erweiterte vMotion-Kompatibilität (Enhanced vMotion Compatibility, EVC)
- vSphere-Fehlertoleranz (FT)
- Momentaufnahmen
- Live (online) Storage vMotion
- N-Port ID Virtualization (NPIV)

Durch Änderungen an der Hardware von VMs im laufenden Betrieb („heiß“) kann der Heartbeat zwischen den WSFC-Knoten beeinträchtigt werden.

Die folgenden Aktivitäten werden nicht unterstützt und führen möglicherweise zu einem Failover des WSFC-Knotens:

- „Heißes“ Hinzufügen von Speicher
- „Heißes“ Vergrößern der CPU-Leistung
- Verwenden von Momentaufnahmen
- Vergrößern eines freigegebenen Datenträgers
- Anhalten und Fortsetzen des Status der VM
- Speicherüberbelegung, die zu ESXi-Austausch oder Memory-Ballooning der VM führt
- „Heiße“ lokale Erweiterung der VMDK-Datei, auch wenn sie nicht mit dem SCSI-Bus-Freigabecontroller verknüpft ist

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Konfigurieren von WSFC mit freigegebenen Datenträgern in Azure VMware Solution vSAN

1. Stellen Sie sicher, dass eine Active Directory-Umgebung verfügbar ist.
2. Erstellen Sie VMs im vSAN-Datenspeicher.
3. Schalten Sie alle VMs ein, konfigurieren Sie den Hostnamen und die IP-Adressen. Verknüpfen Sie alle VMs mit einer Active Directory-Domäne, und installieren Sie die neuesten verfügbaren Betriebssystemupdates.
4. Installieren Sie die neuesten VMware-Tools.
5. Aktivieren und konfigurieren Sie die Windows Server Failover Cluster-Funktion auf jeder VM.
6. Konfigurieren Sie einen Clusterzeugen für das Quorum (ein Dateifreigabezeuge funktioniert gut).
7. Schalten Sie alle Knoten des WSFC-Clusters aus.
8. Fügen Sie jedem VM-Teil des WSFC-Clusters einen oder mehrere (maximal vier) Paravirtual SCSI-Controller hinzu. Verwenden Sie die Einstellungen entsprechend den Angaben in den vorherigen Abschnitten.
9. Fügen Sie im ersten Clusterknoten über **Neues Gerät hinzufügen** > **Festplatte** alle erforderlichen freigegebenen Datenträger hinzu. Belassen Sie für die Datenträgerfreigabe die Option **Nicht angegeben** (Standardwert) und für den Datenträgermodus die Option **Unabhängig, persistent**. Fügen Sie ihn dann an die Controller an, die Sie in den vorherigen Schritten erstellt haben.
10. Konfigurieren Sie dann die übrigen WSFC-Knoten. Fügen Sie die im vorherigen Schritt erstellten Datenträger hinzu, indem Sie **Neues Gerät hinzufügen** > **Existing Hard Disk** (Vorhandene Festplatte) auswählen. Achten Sie darauf, dass Sie für alle WSFC-Knoten die gleichen SCSI-IDs der Datenträger beibehalten.
11. Schalten Sie den ersten WSFC-Knoten ein. Melden Sie sich an, und öffnen Sie die Datenträgerverwaltungskonsole (MMC). Stellen Sie sicher, dass die hinzugefügten freigegebenen Datenträger über das Betriebssystem verwaltet werden können und initialisiert werden. Formatieren Sie die Datenträger, und weisen Sie einen Laufwerkbuchstaben zu.
12. Schalten Sie die anderen WSFC-Knoten ein.
13. Fügen Sie den Datenträger über den Assistenten **Datenträger hinzufügen** dem WSFC-Cluster hinzu, und fügen Sie sie einem freigegebenen Clustervolume hinzu.
14. Testen Sie ein Failover mithilfe des Assistenten zum **Verschieben von Datenträgern**, und stellen Sie sicher, dass der WSFC-Cluster mit den freigegebenen Datenträgern ordnungsgemäß ausgeführt wird.
15. Führen Sie den Assistenten zur **Clustervalidierung** aus, um zu überprüfen, ob der Cluster und die zugehörigen Knoten ordnungsgemäß ausgeführt werden.

    Es ist wichtig, dass Sie die folgenden spezifischen Elemente des Tests zur Clustervalidierung berücksichtigen:

       - **Permanente Reservierung für Speicherplatz überprüfen:** Wenn Sie keine Speicherplätze mit dem Cluster verwenden (z. B. in Azure VMware Solution vSAN), ist dieser Test nicht anwendbar. Sie können alle Ergebnisse des Tests „Permanente Reservierung für Speicherplatz überprüfen“ einschließlich der entsprechenden Warnung ignorieren. Sie können diesen Test ausschließen, um Warnungen zu vermeiden.
        
      - **Netzwerkkommunikation überprüfen:** Beim Test zur Clustervalidierung wird eine Warnung ausgegeben, dass nur ein Netzwerkadapter pro Clusterknoten verfügbar ist. Diese Warnung kann ignoriert werden. Azure VMware Solution bietet die erforderliche Verfügbarkeit und Leistung, da die Knoten mit einem der NSX-T-Segmente verbunden sind. Behalten Sie dieses Element jedoch als Teil des Tests zur Clustervalidierung bei, da damit andere Aspekte der Netzwerkkommunikation überprüft werden.

16. Erstellen Sie eine DRS-Regel zum Platzieren der WSFC-VMs auf den gleichen Azure VMware Solution-Knoten. Dazu ist eine Host-zu-VM-Affinitätsregel erforderlich. Auf diese Weise werden Clusterknoten auf demselben Azure VMware Solution-Host ausgeführt. Auch dies dient Pilotzwecken, bis Platzierungsrichtlinien verfügbar sind.

    >[!NOTE]
    > Hierzu müssen Sie ein Supportanfrageticket erstellen. Der Azure-Support kann Ihnen dabei helfen.

## <a name="related-information"></a>Verwandte Informationen

- [Failoverclustering in Windows Server](/windows-server/failover-clustering/failover-clustering-overview)
- [Guidelines for Microsoft Clustering on vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959) (Richtlinien für Microsoft-Clustering in vSphere)
- [About Setup for Failover Clustering and Microsoft Cluster Service (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html) (Informationen zum Setup für Failoverclustering und Microsoft Cluster Service)
- [vSAN 6.7 U3 – WSFC with Shared Disks &amp; SCSI-3 Persistent Reservations (vmware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/) (vSAN 6.7 U3 – WSFC mit freigegebenen Datenträgern und SCSI-3 Persistent Reservations)
- [Einschränkungen für Azure VMware Solution](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Einrichten von WSFC in Azure VMware Solution vertraut gemacht haben, können Sie sich nun den folgenden Themen zuwenden:

- Einrichten der neuen WSFC-Instanz durch Hinzufügen weiterer Anwendungen, die WSFC-Funktionen erfordern, z. B. SQL Server und SAP ASCS
- Einrichten einer Sicherungslösung:
  - [Einrichten von Azure Backup Server für Azure VMware Solution](set-up-backup-server-for-azure-vmware-solution.md)
  - [Sicherungslösungen für virtuelle Azure VMware Solution-Computer](backup-azure-vmware-solution-virtual-machines.md)
