---
title: 'Konzepte: Speicher'
description: Erfahren Sie mehr über Speicherkapazität, Speicherrichtlinien, Fehlertoleranz und Speicherintegration in Azure VMware Solution Private Clouds.
ms.topic: conceptual
ms.custom: contperf-fy21q4
ms.date: 08/31/2021
ms.openlocfilehash: fb6397752893640bed426e668e833126537d028a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255405"
---
# <a name="azure-vmware-solution-storage-concepts"></a>Speicherkonzepte von Azure VMware Solution

Private Azure VMware Solution-Clouds bieten nativen, clusterweiten Speicher mit VMware vSAN. Der lokale Speicher jedes Hosts in einem Cluster wird in einem vSAN-Datenspeicher verwendet, und Verschlüsselung ruhender Daten ist verfügbar und standardmäßig aktiviert. Sie können Azure Storage-Ressourcen zum Erweitern der Speicherfunktionen Ihrer privaten Clouds verwenden.

## <a name="vsan-clusters"></a>vSAN-Cluster

Der lokale Speicher in jedem Clusterhost wird als Teil eines vSAN-Datenspeichers verwendet. Alle Datenträgergruppen verwenden eine NVMe-Cacheebene von 1,6 TB mit SSD-basierter Rohkapazität von 15,4 TB pro Host. Die Größe der Rohkapazitätsebene eines Clusters entspricht der Kapazität pro Host multipliziert mit der Anzahl der Hosts. So bietet beispielsweise ein Cluster mit vier Hosts 61,6 TB Rohkapazität in der vSAN-Kapazitätsschicht.

Der lokale Speicher in Clusterhosts wird im clusterweiten vSAN-Datenspeicher verwendet. Alle Datenspeicher werden im Rahmen einer privaten Cloudbereitstellung erstellt und können sofort verwendet werden. Der Benutzer **cloudadmin** sowie alle Benutzer, mit der Rolle CloudAdmin, können mit diesen vSAN-Berechtigungen Datenspeicher verwalten:

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

>[!IMPORTANT]
>Der Name von Datenspeichern oder Clustern kann nicht geändert werden. Bei der Bereitstellung von einem anderen Ort als dem Portal (AzureCLI oder PowerShell) können Sie einen anderen Clusternamen als "Cluster-n" mit n > 1 wählen.

## <a name="storage-policies-and-fault-tolerance"></a>Speicherrichtlinien und Fehlertoleranz

Diese Standardspeicherrichtlinie ist auf RAID-1 (Mirroring), FTT-1 und Thick Provisioning eingestellt. Wenn Sie die Speicherrichtlinie nicht anpassen oder eine neue Richtlinie anwenden, wächst der Cluster mit dieser Konfiguration. Informationen zum Festlegen der Speicherrichtlinie finden Sie unter [Konfigurieren der Speicherrichtlinie](configure-storage-policy.md).

In einem Drei-Host-Cluster gleicht FTT-1 den Ausfall eines einzelnen Hosts aus. Microsoft reguliert Ausfälle regelmäßig und tauscht die Hardware aus, sobald Ereignisse aus Sicht der Architektur erkannt werden.

:::image type="content" source="media/concepts/vsphere-vm-storage-policies.png" alt-text="Screenshot, der die VM-Speicherrichtlinien des vSphere-Clients zeigt.":::


|Bereitstellungstyp  |Beschreibung  |
|---------|---------|
|**Thick**      | Reservierter oder vorab zugewiesener Speicherplatz. Dies schützt Systeme, indem sie auch dann funktionieren, wenn der vSAN-Datenspeicher voll ist, da der Speicherplatz bereits reserviert ist. Ein Beispiel: Sie erstellen einen virtuellen 10-GB-Datenträger mit Thick Provisioning. In diesem Fall wird die gesamte Speicherkapazität des virtuellen Datenträgers im physischen Speicher des virtuellen Datenträgers vorab zugewiesen, und die Kapazität nutzt den gesamten ihr zugewiesenen Speicherplatz im Datenspeicher. Andere virtuelle Maschinen (VMs) können den Speicherplatz des Datenspeichers nicht mitbenutzen.         |
|**Dünn**      | Verbraucht den Speicherplatz, den er zunächst benötigt und wächst auf den im Datenspeicher verwendeten Speicherplatzbedarf an. Über den Standard (Thick Provisioning) hinaus können Sie VMs mit FTT-1 Thin Provisioning erstellen. Verwenden Sie für die Dedupe-Einrichtung Thin Provisioning für Ihre VM-Vorlage.         |

>[!TIP]
>Sollten Sie sich nicht sicher sein, ob der Cluster auf vier oder mehr anwachsen wird, dann verwenden Sie die Standardrichtlinie.  Falls Sie sicher sind, dass Ihr Cluster wachsen wird, dann empfehlen wir, anstatt den Cluster nach dem ersten Einsatz zu erweitern, die zusätzlichen Hosts während des Einsatzes einzusetzen. Während die VMs im Cluster bereitgestellt werden, ändern Sie die Speicherrichtlinie der Festplatte in den VM-Einstellungen entweder auf RAID-5 FTT-1 oder RAID-6 FTT-2. 
>
>:::image type="content" source="media/concepts/vsphere-vm-storage-policies-2.png" alt-text="Screenshot: Hervorgehobene Optionen „RAID-5 FTT-1“ und „RAID-6 Ftt-2“":::


## <a name="data-at-rest-encryption"></a>Verschlüsselung ruhender Daten

vSAN-Datenspeicher verwenden standardmäßig die Verschlüsselung ruhender Daten mithilfe von Schlüsseln, die in Azure Key Vault gespeichert sind. Die Verschlüsselungslösung ist KMS-basiert und unterstützt vCenter-Vorgänge für die Schlüsselverwaltung.  Wenn ein Host aus einem Cluster entfernt wird, werden die Daten auf SSDs sofort ungültig.

## <a name="azure-storage-integration"></a>Azure-Speicherintegration

Sie können Azure-Speicherdienste in Workloads verwenden, die in Ihrer privaten Cloud ausgeführt werden. Die Azure-Speicherdienste umfassen Speicherkonten, Table Storage und Blob Storage. Die Verbindung von Workloads mit Azure-Speicherdiensten durchläuft nicht das Internet. Diese Konnektivität bietet mehr Sicherheit und ermöglicht Ihnen die Verwendung von SLA-basierten Azure Storage-Diensten in den Workloads Ihrer privaten Clouds.

## <a name="alerts-and-monitoring"></a>Benachrichtigungen und Überwachung

Wenn die Kapazitätsauslastung 75 % überschreitet, gibt Microsoft Warnungen aus. Darüber hinaus können Sie Metriken zum Kapazitätsverbrauch überwachen, die in Azure Monitor integriert sind. Weitere Informationen finden Sie unter [Konfigurieren von Azure-Warnungen in Azure VMware Solution](configure-alerts-for-azure-vmware-solution.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Speicherkonzepten von Azure VMware Solution vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- [Anfügen von Datenträgerpools an Azure VMware Solution-Hosts (Vorschau)](attach-disk-pools-to-azure-vmware-solution-hosts.md): Sie können Datenträger als beständigen Speicher für Azure VMware Solution verwenden, um die Kosten und die Leistung zu optimieren.

- [Konfigurieren der Speicherrichtlinie](configure-storage-policy.md): Jeder in einem vSAN-Datenspeicher bereitgestellten VM wird mindestens eine VM-Speicherrichtlinie zugewiesen. Sie können eine VM-Speicherrichtlinie zuweisen, wenn Sie eine VM erstmals bereitstellen oder wenn Sie andere VM-Vorgänge ausführen (z. B. Klonen oder Migrieren).

- [Skalieren von Clustern in der privaten Cloud][tutorial-scale-private-cloud]: Sie können die Cluster und Hosts in einer privaten Cloud gemäß dem Bedarf Ihrer Anwendungsworkload skalieren. Leistungs- und Verfügbarkeitseinschränkungen für bestimmte Dienste sollten von Fall zu Fall behandelt werden.

- [Azure NetApp Files mit Azure VMware Solution](netapp-files-with-azure-vmware-solution.md): Sie können Azure NetApp für die Migration und die Ausführung besonders anspruchsvoller Unternehmensdateiworkloads in der Cloud (Datenbanken, SAP und High Performance Computing-Anwendungen) ohne Codeänderungen verwenden. 

- [Rollenbasierte Zugriffssteuerung von vSphere für Azure VMware Solution](concepts-identity.md): Mit vCenter können Sie VM-Workloads und NSX-T Manager verwenden, um die private Cloud zu verwalten und zu erweitern. Zugriffs- und Identitätsverwaltung übernehmen die Rolle CloudAdmin für vCenter und eingeschränkte Administratorrechte für NSX-T Manager.


<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
