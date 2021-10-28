---
title: 'SAP HANA: ANF-Konfiguration für virtuelle Azure-Computer | Microsoft-Dokumentation'
description: 'Azure NetApp Files: Speicherempfehlungen für SAP HANA.'
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, Momentaufnahme
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/08/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb96e0da4e9e724220492821f1418f3d587b63e8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222910"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>NFS v4.1-Volumes unter Azure NetApp Files für SAP HANA

Azure NetApp Files verfügt über native NFS-Freigaben, die für die Volumes **/hana/shared**, **/hana/data** und **/hana/log** verwendet werden können. Zur Nutzung von ANF-basierten NFS-Freigaben für die Volumes **/hana/data** und **/hana/log** wird das v4.1 NFS-Protokoll benötigt. Das NFS-Protokoll v3 wird nicht für die Nutzung der Volumes **/hana/data** und **/hana/log** unterstützt, wenn die Freigaben auf ANF basieren. 


> [!IMPORTANT]
> Die Verwendung des NFS v3-Protokolls, das für Azure NetApp Files implementiert wurde, wird für **/hana/data** und **/hana/log** **nicht** unterstützt. Die Verwendung von NFS 4.1 ist für die Volumes **/hana/data** und **/hana/log** aus funktionaler Sicht obligatorisch. Während für das Volume **/hana/shared** das Protokoll NFS v3 oder NFS v4.1 aus funktionaler Sicht verwendet werden kann.

## <a name="important-considerations"></a>Wichtige Hinweise

Wenn Sie Azure NetApp Files für die Hochverfügbarkeitsarchitektur von SAP NetWeaver und SAP HANA in Betracht ziehen, beziehen Sie die folgenden wichtigen Überlegungen mit ein:

- Die Mindestgröße eines Kapazitätspools beträgt 4 TiB.  
- Die Mindestvolumegröße ist 100 GiB.
- Azure NetApp Files und alle virtuellen Computer, auf denen Azure NetApp Files-Volumes eingebunden sind, müssen sich im selben virtuellen Azure-Netzwerk oder in [über Peering gekoppelten virtuellen Netzwerken](../../../virtual-network/virtual-network-peering-overview.md) in derselben Region befinden.
- Um eine niedrige Latenz zu erzielen, ist es wichtig, dass die virtuellen Computer in unmittelbarer Nähe des Azure NetApp-Speichers bereitgestellt werden.  
- Das ausgewählte virtuelle Netzwerk muss über ein an Azure NetApp Files delegiertes Subnetz verfügen.
- Stellen Sie sicher, dass die Latenz zwischen dem Datenbankserver und dem ANF-Volume gemessen wird und unter 1 Millisekunde liegt.
- Der Durchsatz eines Azure NetApp-Volumes ist eine Funktion des Volumekontingents und der Dienstebene, wie in [Dienstebenen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) beschrieben. Stellen Sie bei der Größenanpassung der HANA Azure NetApp-Volumes sicher, dass der sich ergebende Durchsatz die HANA-Systemanforderungen erfüllt. Alternativ können Sie einen [manuellen QoS-Kapazitätspool](../../../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md#manual-qos-type) verwenden, in dem Volumenkapazität und -durchsatz unabhängig konfiguriert und skaliert werden können (SAP HANA-spezifische Beispiele finden Sie in [diesem Dokument](../../../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md#manual-qos-type)).
- Versuchen Sie, Volumes zu „konsolidieren“, um mit einem größeren Volume höhere Leistung zu erzielen, verwenden Sie z. B. ein Volume für „/sapmnt“, „/usr/sap/trans“..., wenn möglich.  
- Azure NetApp Files bietet [Exportrichtlinien](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): Sie können die zulässigen Clients und den Zugriffstyp (Lesen und Schreiben, schreibgeschützt usw.) steuern. 
- Azure NetApp Files wertet derzeit noch keine Zonen aus. Das Azure NetApp Files-Feature wird bisher nicht in allen Verfügbarkeitszonen in einer Azure-Region bereitgestellt. Achten Sie auf mögliche Latenzauswirkungen in einigen Azure-Regionen.   
- Die Benutzer-ID für „<b>sid</b>adm“ und die Gruppen-ID für `sapsys` auf den virtuellen Computern müssen mit der Konfiguration in Azure NetApp Files übereinstimmen. 

> [!IMPORTANT]
> Bei SAP HANA-Workloads ist eine niedrige Latenz sehr wichtig. Arbeiten Sie mit Ihrem Microsoft-Vertreter zusammen, um sicherzustellen, dass die virtuellen Computer und die Azure NetApp Files-Volumes in unmittelbarer Nähe zueinander bereitgestellt werden.  

> [!IMPORTANT]
> Wenn die Benutzer-ID für „<b>sid</b>adm“ und die Gruppen-ID für `sapsys` auf dem virtuellen Computern und in der Azure NetApp-Konfiguration nicht übereinstimmen, würden die Berechtigungen für Dateien auf Azure NetApp-Volumes, die in die VM eingebunden sind, als `nobody` angezeigt. Stellen Sie beim [Onboarding eines neuen Systems](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) in Azure NetApp Files sicher, dass Sie die richtige Benutzer-ID für „<b>sid</b>adm“ und die Gruppen-ID für `sapsys` angeben.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Größenanpassung für eine HANA-Datenbank in Azure NetApp Files

Der Durchsatz eines Azure NetApp-Volumes ist eine Funktion der Volumegröße und der Dienstebene, wie in [Dienstebenen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) beschrieben. 

Es ist wichtig, die Leistungsbeziehung und die Größe zu kennen, und zu wissen, dass es physikalische Grenzwerte für einen Speicherendpunkt des Diensts gibt. Jeder Speicherendpunkt wird bei der Volumeerstellung dynamisch in das [Azure NetApp Files delegierte Subnetz](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) eingefügt und erhält eine IP-Adresse. Azure NetApp Files-Volumes können – je nach verfügbarer Kapazität und Bereitstellungslogik – einen gemeinsamen Speicherendpunkt nutzen.

Die folgende Tabelle zeigt, dass es sinnvoll sein kann, ein großes „Standard“-Volume zum Speichern von Sicherungen zu erstellen, und dass es nicht sinnvoll ist, ein „Ultra“-Volume zu erstellen, das größer als 12 TB ist, da die maximale physikalische Bandbreitenkapazität eines einzelnen Volume überschritten würde. 

Der maximale Schreibdurchsatz für ein Volume und eine einzelne Linux-Sitzung liegt zwischen 1,2 und 1,4 GB/s. Wenn Sie mehr Durchsatz für /hana/data benötigen, können Sie die SAP HANA-Partitionierung für Datenvolumes verwenden, um für die E/A-Aktivität während des erneuten Ladens von Daten oder HANA-Sicherungspunkten über mehrere HANA-Datendateien ein Striping vorzunehmen, die sich auf mehreren NFS-Freigaben befinden. Zur Verbesserung des Lesedurchsatzes kann die Einbindungsoption „NFS nconnect“ verwendet werden. Weitere Informationen zur Leistung von Azure NetApp Files unter Linux und zu „nconnect“ finden Sie unter [Einbindungsoptionen für NFS unter Linux: bewährte Methoden für Azure NetApp Files](../../../azure-netapp-files/performance-linux-mount-options.md). Weitere Informationen zum Striping von HANA-Datenvolumen finden Sie in diesen Artikeln:

- [HANA-Administratorhandbuch](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog zu SAP HANA-Datenvolumepartitionierung](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP Note 2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP-Hinweis 2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Size  | Durchsatz Standard | Durchsatz Premium | Durchsatz Ultra |
| --- | --- | --- | --- |
| 1 TB | 16 MB/s | 64 MB/s | 128 MB/s |
| 2 TB | 32 MB/s | 128 MB/s | 256 MB/s |
| 4 TB | 64 MB/s | 256 MB/s | 512 MB/s |
| 10 TB | 160 MB/s | 640 MB/s | 1\.280 MB/s |
| 15 TB | 240 MB/s | 960 MB/s | 1\.400 MB/s<sup>1</sup> |
| 20 TB | 320 MB/s | 1\.280 MB/s | 1\.400 MB/s<sup>1</sup> |
| 40 TB | 640 MB/s | 1\.400 MB/s<sup>1</sup> | 1\.400 MB/s<sup>1</sup> |

<sup>1</sup>: Durchsatzlimits für Schreibvorgänge oder für Lesevorgänge in einzelnen Sitzungen (für den Fall, dass die NFS-Bereitstellungsoption „nconnect“ nicht verwendet wird) 

Es ist wichtig zu wissen, dass die Daten im Speicher-Back-End in die gleichen SSDs geschrieben werden. Das Leistungskontingent aus dem Kapazitätspool wurde erstellt, um die Umgebung verwalten zu können.
Die Speicher-KPIs sind für alle HANA-Datenbankgrößen gleich. In fast allen Fällen spiegelt diese Annahme nicht die Realität und die Kundenerwartung wider. Die Größe von HANA-Systemen bedeutet nicht notwendigerweise, dass ein kleines System einen niedrigen und ein großes System einen hohen Speicherdurchsatz erfordert. Im Allgemeinen sind jedoch für größere HANA-Datenbankinstanzen höhere Durchsatzanforderungen zu erwarten. Infolge der Dimensionierungsregeln von SAP für die zugrunde liegende Hardware bieten solch größere HANA-Instanzen auch mehr CPU-Ressourcen und eine höhere Parallelität in Aufgaben wie dem Laden von Daten nach einem Neustart einer Instanz. Daher sollten die Volumegrößen den Erwartungen und Anforderungen der Kunden angepasst werden. Sie sollten nicht nur durch reine Kapazitätsanforderungen bestimmt werden.

Beim Entwerfen der Infrastruktur für SAP in Azure müssen Sie einige Mindestanforderungen von SAP an den Speicherdurchsatz (für Produktionssysteme) beachten, aus denen sich folgende Merkmale für den Mindestdurchsatz ergeben:

| Volumetyp und E/A-Typ | Minimaler KPI-Bedarf von SAP | Premium-Servicelevel | Ultra-Servicelevel |
| --- | --- | --- | --- |
| Protokollvolume – Schreiben | 250 MB/s | 4 TB | 2 TB |
| Datenvolume – Schreiben | 250 MB/s | 4 TB | 2 TB |
| Datenvolume – Lesen | 400 MB/s | 6,3 TB | 3,2 TB |

Da alle drei KPIs erforderlich sind, muss das **/hana/data**-Volume auf die größere Kapazität skaliert werden, um die minimalen Leseanforderungen zu erfüllen. Bei Verwendung manueller QoS-Kapazitätspools können Größe und Durchsatz der Volumes unabhängig voneinander definiert werden. Da sowohl die Kapazität als auch der Durchsatz aus demselben Kapazitätspool entnommen werden, müssen die Dienstebene und die Größe des Pools ausreichend groß sein, um die Gesamtleistung zu erbringen (ein Beispiel finden Sie [hier](../../../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md#manual-qos-type)).

Bei HANA-Systemen, für die keine hohe Bandbreite erforderlich ist, kann der Durchsatz des ANF-Volumes entweder durch eine kleinere Volumegröße oder bei manuellem QoS durch direktes Anpassen des Durchsatzes verringert werden. Für den Fall, dass ein HANA-System mehr Durchsatz erfordert, kann das Volume angepasst werden, indem die Größe der Kapazität online geändert wird. Für Sicherungsvolumes sind keine KPIs definiert. Der Durchsatz des Sicherungsvolumes ist jedoch für eine gut funktionierende Umgebung entscheidend. Die Leistung von Protokoll- und Datenvolume muss auf die Kundenerwartungen zugeschnitten werden.

> [!IMPORTANT]
> Unabhängig von der Kapazität, die Sie auf einem einzelnen NFS-Volume bereitstellen, wird erwartet, dass der Durchsatz im Bereich von 1,2 bis 1,4 GB/Sek. Bandbreite von einem Consumer in einer einzelnen Sitzung genutzt wird. Dies hat mit der zugrundeliegenden Architektur des ANF-Angebots und den damit verbundenen Einschränkungen der Linux-Sitzungen bezüglich NFS zu tun. Die Leistungs- und Durchsatzzahlen, die im Artikel [Ergebnisse des Leistungsbenchmarktests für Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) beschrieben werden, wurden für ein gemeinsam genutztes NFS-Volume mit mehreren Client-VMs und daher mit mehreren Sitzungen durchgeführt. Dieses Szenario unterscheidet sich von dem Szenario, das wird in SAP messen. Dabei messen wir den Durchsatz von einer einzelnen VM für ein NFS-Volume, Auf ANF gehostet.

Um die SAP-Mindestanforderungen für den Durchsatz für Daten und Protokolle und die Richtlinien für **/hana/shared** zu erfüllen, werden folgende Größen empfohlen:

| Volume | Size<br /> Storage Premium-Tarif | Size<br /> Storage Ultra-Tarif | Unterstütztes NFS-Protokoll |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v4.1 |
| /hana/shared hochskalieren | Min(1 TB, 1 x RAM)  | Min(1 TB, 1 x RAM) | v3 oder v4.1 |
| /hana/shared aufskalieren | 1 x RAM des Workerknotens<br /> pro vier Workerknoten  | 1 x RAM des Workerknotens<br /> pro vier Workerknoten  | v3 oder v4.1 |
| /hana/logbackup | 3 × RAM  | 3 × RAM | v3 oder v4.1 |
| /hana/backup | 2 × RAM  | 2 × RAM | v3 oder v4.1 |

Für alle Volumes wird NFS v4.1 dringend empfohlen.

Die Größen der Sicherungsvolumes sind Schätzungen. Genaue Anforderungen müssen auf der Grundlage von Workload und Vorgangsprozessen definiert werden. Bei Sicherungen können Sie viele Volumes für verschiedene SAP HANA-Instanzen zu einem größeren Volume (oder zwei) konsolidieren, das einen niedrigeren ANF-Servicelevel haben könnte.

> [!NOTE]
> Die in diesem Dokument angegebenen Empfehlungen für die Azure NetApp Files-Größenanpassung zielen darauf ab, die Mindestanforderungen zu erfüllen, die SAP an seine Infrastrukturanbieter stellt. In realen Kundenbereitstellungen und Workloadszenarien sind sie möglicherweise nicht ausreichend. Verwenden Sie diese Empfehlungen also als Ausgangspunkt, und nehmen Sie Anpassungen auf Grundlage der Anforderungen der spezifischen Workload vor.  

Daher könnten Sie für die ANF-Volumes einen ähnlichen wie den bereits für Disk Storage Ultra angegebenen Durchsatz bereitstellen. Beachten Sie auch die Größen, die für die Volumes verschiedener VM-SKUs bereits in den Tabellen zu Ultra Disks aufgeführt sind.

> [!TIP]
> Sie können die Größe der Azure NetApp Files-Volumes dynamisch anpassen, ohne die Bereitstellung der Volumes aufheben (`unmount`) oder die virtuellen Computer oder SAP HANA beenden zu müssen. Damit kann Ihre Anwendung sowohl den erwarteten als auch unvorhergesehenen Durchsatzanforderungen flexibel gerecht werden.

Die Dokumentation zur Bereitstellung einer SAP HANA-Konfiguration für horizontale Skalierung mit Standbyknoten über NFS v4.1-Volumes, die in ANF gehostet werden, ist in [Horizontale SAP HANA-Skalierung mit Standbyknoten auf Azure-VMs mit Azure NetApp Files auf SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md) veröffentlicht.


## <a name="availability"></a>Verfügbarkeit
Updates und Upgrades von ANF-Systemen werden ohne Beeinträchtigung der Kundenumgebung angewendet. Der definierte [SLA ist 99,99 %](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Volumes, IP-Adressen und Kapazitätspools
Bei ANF ist es wichtig zu verstehen, wie die zugrunde liegende Infrastruktur erstellt wird. Ein Kapazitätspool ist nur ein Konstrukt, das basierend auf der Dienstebene des Kapazitätspools ein Kapazitäts- und Leistungsbudget und eine Abrechnungseinheit bereitstellt. Ein Kapazitätspool hat keine physische Beziehung zur zugrunde liegenden Infrastruktur. Wenn Sie ein Volume im den Dienst erstellen, wird ein Speicherendpunkt erstellt. Diesem Speicherendpunkt wird eine einzelne IP-Adresse zugewiesen, um Datenzugriff auf das Volume zu ermöglichen. Wenn Sie mehrere Volumes erstellen, werden alle Volumes auf die zugrunde liegende Bare-Metal-Flotte verteilt, die an diesen Speicherendpunkt gebunden ist. Die ANF verfügt über eine Logik, die Kundenworkloads automatisch verteilt, sobald die Volumes oder/und die Kapazität des konfigurierten Speichers eine interne vordefinierte Stufe erreichen. Sie werden solche Fälle bemerken, da automatisch ein neuer Speicherendpunkt mit einer neuen IP-Adresse erstellt wird, um auf die Volumes zuzugreifen. Der ANF-Dienst bietet keine Kundenkontrolle über diese Verteilungslogik.

## <a name="log-volume-and-log-backup-volume"></a>Protokollvolume und Protokollsicherungsvolume
Das „Protokollvolume“ ( **/hana/log**) wird zum Schreiben des Onlinewiederholungsprotokolls verwendet. Daher befinden sich in diesem Volume geöffnete Dateien, und es ist nicht sinnvoll, Momentaufnahmen dieses Volumes zu erstellen. Eine Onlinewiederholungsprotokoll-Datei wird im Protokollsicherungsvolume archiviert oder gesichert, sobald sie voll ist oder eine Wiederholungsprotokollsicherung ausgeführt wird. Um eine angemessene Sicherungsleistung zu gewährleisten, erfordert das Protokollsicherungsvolume einen guten Durchsatz. Um die Speicherkosten zu optimieren, kann es sinnvoll sein, das Protokollsicherungsvolume mehrerer HANA-Instanzen zu konsolidieren. Dann würden mehrere HANA-Instanzen das gleiche Volume nutzen und ihre Sicherungen in verschiedene Verzeichnisse schreiben. Mithilfe einer solchen Konsolidierung können Sie einen höheren Durchsatz erzielen, da Sie das Volume etwas vergrößern müssen. 

Das gleiche gilt für das Volume, das Sie für das Schreiben vollständiger HANA-Datenbanksicherungen verwenden.  
 

## <a name="backup"></a>Backup
Neben Streamingsicherungen und dem Azure Backup-Dienst, der SAP HANA-Datenbanken wie im Artikel [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](../../../backup/sap-hana-db-about.md) beschrieben sichert, eröffnet Azure NetApp Files die Möglichkeit, speicherbasierte Momentaufnahmensicherungen auszuführen. 

SAP HANA unterstützt:

- Unterstützung von speicherbasierter Momentaufnahmesicherung für Einzelcontainersysteme mit SAP HANA 1.0 SPS7 und höher 
- Unterstützung von speicherbasierter Momentaufnahmesicherung für MDC-HANA-Umgebungen (Multi Database Container) mit einem einzelnen Mandanten mit SAP HANA 2.0 SPS1 und höher
- Unterstützung von speicherbasierter Momentaufnahmesicherung für MDC-HANA-Umgebungen (Multi Database Container) mit mehreren Mandanten mit SAP HANA 2.0 SPS4 und höher


Das Erstellen speicherbasierter Momentaufnahmensicherungen ist ein einfaches Verfahren in vier Schritten: 
1. Erstellen einer (internen) HANA-Datenbankmomentaufnahme – eine Aktivität, die von Ihnen oder Tools ausgeführt wird. 
1. SAP HANA schreibt Daten in die Datendateien, um einen konsistenten Zustand im Speicher zu schaffen – HANA führt diesen Schritt aus, wenn eine HANA-Momentaufnahme erstellt wird.
1. Erstellen einer Momentaufnahme auf dem **/hana/data**-Volume im Speicher – ein Schritt, der von Ihnen oder Tools ausgeführt wird. Es ist nicht erforderlich, eine Momentaufnahme auf dem **/hana/log**-Volume auszuführen.
1. Löschen der (internen) HANA-Datenbankmomentaufnahme und Fortsetzen des normalen Betriebs – ein Schritt, der von Ihnen oder Tools ausgeführt wird.

> [!WARNING]
> Wenn der letzte Schritt fehlt oder nicht durchgeführt werden kann, wirkt sich dies schwerwiegend auf den Arbeitsspeicherbedarf von SAP HANA aus und kann zu einem Anhalten von SAP HANA führen.

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![ANF-Momentaufnahmensicherung für SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![ANF-Momentaufnahmensicherung für SAP HANA – Teil 2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Der Vorgang der Momentaufnahmensicherung kann auf unterschiedliche Weise mithilfe verschiedener Tools verwaltet werden. Ein Beispiel hierfür ist das auf GitHub [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) verfügbare Python-Skript „ntaphana_azure. py“. Dies ist ein Beispielcode, der „wie vorliegend“ ohne jegliche Wartung oder Unterstützung bereitgestellt wird.



> [!CAUTION]
> Eine Momentaufnahme an sich ist keine geschützte Sicherung, da sie sich im gleichen physischen Speicher befindet wie das Volume, von dem Sie gerade diese Momentaufnahme erstellt haben. Es ist obligatorisch, mindestens eine Momentaufnahme pro Tag an einem anderen Speicherort zu „sichern“. Dies kann in derselben Umgebung, in einer Azure-Remoteregion oder in Azure Blob Storage durchgeführt werden.

Verfügbare Lösungen für die auf Speichermomentaufnahmen basierende anwendungskonsistente Sicherung:

- Das Microsoft-[Tool für konsistente Momentaufnahmen in Azure-Anwendungen (AzAcSnap)](../../../azure-netapp-files/azacsnap-introduction.md) ist ein Befehlszeilentool, das den Datenschutz für Datenbanken von Drittanbietern ermöglicht, indem es die gesamte Orchestrierung abwickelt, die erforderlich ist, damit die Anwendungsdaten konsistent sind, bevor eine Speichermomentaufnahme erstellt wird, nach der die Daten in einem Betriebszustand zurückgegeben werden. AzAcSnap unterstützt momentaufnahmebasierte Sicherungen für HANA (große Instanzen) sowie Azure NetApp Files. Weitere Informationen finden Sie unter „Worum handelt es sich beim Tool für konsistente Momentaufnahmen in Azure-Anwendungen?“ 
- Für Benutzer von Commvault-Sicherungsprodukten ist Commvault IntelliSnap V.11.21 und höher eine weitere Option. Diese oder höhere Versionen von Commvault bieten Unterstützung von Momentaufnahmen für Azure NetApp Files. Im Artikel [Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) finden Sie weitere Informationen.



### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Sichern der Momentaufnahme mit Azure Blob Storage
Die Sicherung in Azure Blob Storage ist eine kostengünstige und schnelle Methode zum Speichern von Momentaufnahmensicherungen auf ANF basierenden HANA-Datenbankspeichers. Zum Speichern der Momentaufnahmen in Azure Blob Storage wird das Tool AzCopy bevorzugt. Laden Sie die neueste Version dieses Tools herunter, und installieren Sie sie z. B. im Verzeichnis „bin“, wo das Python-Skript von GitHub installiert ist.
Laden Sie das aktuellste AzCopy-Tool herunter:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

Das fortschrittlichste Feature ist die Option SYNC. Wenn Sie die Option SYNC verwenden, werden Quell- und Zielverzeichnis von azcopy synchronisiert. Die Verwendung des Parameters **--delete-destination** ist wichtig. Ohne diesen Parameter werden Dateien am Zielstandort von azcopy nicht gelöscht, und die Speicherplatzauslastung auf der Zielseite würde zunehmen. Erstellen Sie einen Blockblobcontainer in Ihrem Azure-Speicherkonto. Erstellen Sie dann den SAS-Schlüssel für den Blobcontainer, und synchronisieren Sie den Momentaufnahmenordner mit dem Azure Blob-Container.

Dies ist z. B. sinnvoll, wenn eine tägliche Momentaufnahme mit dem Azure-Blobcontainer synchronisiert werden soll, um die Daten zu schützen. Wenn nur eine Momentaufnahme beibehalten werden soll, kann der folgende Befehl verwendet werden.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie diesen Artikel:

- [Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](./sap-hana-availability-overview.md)
