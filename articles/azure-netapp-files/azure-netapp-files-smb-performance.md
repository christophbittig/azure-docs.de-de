---
title: Best Practices für leistungsfähiges SMB in Azure NetApp Files | Microsoft-Dokumentation
description: Informationen und Best Practices bezüglich der Leistung von SMB für Azure NetApp Files
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: b-juche
ms.openlocfilehash: dd909362950d961b4dcf944ab608c5103840e9a7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239994"
---
# <a name="smb-performance-best-practices-for-azure-netapp-files"></a>Best Practices für leistungsfähiges SMB in Azure NetApp Files

Dieser Artikel enthält Informationen und Best Practices bezüglich der Leistung von SMB für Azure NetApp Files.

## <a name="smb-multichannel"></a>SMB Multichannel

SMB Multichannel ist in SMB-Freigaben standardmäßig aktiviert. Bei allen SMB-Freigaben, die vor vorhandenen SMB-Volumes erstellt wurden, wurde das Feature aktiviert, und bei allen neu erstellten Volumes ist das Feature zum Zeitpunkt der Erstellung ebenfalls aktiviert. 

Alle SMB-Verbindungen, die vor der Featureaktivierung erstellt wurden, müssen zurückgesetzt werden, um die Vorteile der SMB Multichannel-Funktionalität nutzen zu können. Zum Zurücksetzen können Sie die Verbindung mit der SMB-Freigabe trennen und wiederherstellen.

Windows unterstützt SMB Multichannel seit Windows 2012, um eine optimale Leistung zu erzielen.  Weitere Informationen finden Sie unter [Bereitstellen von SMB Multichannel](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) und [Grundlagen von SMB Multichannel](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0). 

### <a name="benefits-of-smb-multichannel"></a>Vorteile von SMB Multichannel

Das SMB Multichannel-Feature ermöglicht einem SMB3-Client das Einrichten eines Pools von Verbindungen über eine einzelne Netzwerkschnittstellenkarte (NIC) oder über mehrere NICs und deren Verwendung zum Senden von Anforderungen für eine einzelne SMB-Sitzung. Im Gegensatz dazu erfordern SMB1 und SMB2, dass der Client eine Verbindung herstellt und den gesamten SMB-Datenverkehr für eine Sitzung über diese Verbindung sendet. Diese Einzelverbindung schränkt die allgemeine Protokollleistung ein, die mit einem einzelnen Client erreicht werden kann.

### <a name="performance-for-smb-multichannel"></a>Leistung von SMB Multichannel

Die folgenden Tests und Diagramme veranschaulichen die Leistung von SMB Multichannel bei Einzelinstanzworkloads.

#### <a name="random-io"></a>Zufällige E/A  

Auf dem Client war SMB Multichannel deaktiviert, während die reinen 4-KiB-Lese- und -Schreibtests mit FIO und einem 40-GiB-Arbeitssatz durchgeführt wurden.  Die SMB-Freigabe wurde zwischen den einzelnen Tests getrennt, wobei die Anzahl der SMB-Clientverbindungen pro RSS-Netzwerkschnittstellen in Schritten zwischen den Einstellungen `1`, `4`, `8`, `16` und `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` erhöht wurde. Die Tests zeigen, dass die Standardeinstellung `4` für E/A-intensive Workloads ausreichend ist. Die Auswirkungen der Erhöhung auf `8` und `16` sind vernachlässigbar. 

Der Befehl `netstat -na | findstr 445` belegte, dass bei den Schritten von `1` auf `4` auf `8` und auf `16` zusätzliche Verbindungen hergestellt wurden.  Bei jedem Test wurden vier CPU-Kerne vollständig für SMB genutzt, was auch von der Statistik `Per Processor Network Activity Cycles` von perfmon bestätigt wurde (nicht in diesem Artikel enthalten).

![Diagramm: Vergleich mit zufälligen E/A-Zugriffen für SMB Multichannel](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Der virtuelle Azure-Computer wirkt sich nicht auf die Speicher-E/A-Limits von SMB (und NFS) aus.  Wie im folgenden Diagramm dargestellt, ist beim Instanztyp „D32ds“ die Rate für Speicher-IOPS mit Zwischenspeicherung auf 308.000 und für Speicher-IOPS ohne Zwischenspeicherung auf 51.200 beschränkt.  Das obige Diagramm zeigt jedoch deutlich mehr E/A-Vorgänge über SMB.

![Diagramm: Vergleichstest mit zufälligen E/A-Zugriffen](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

#### <a name="sequential-io"></a>Sequenzielle E/A 

Auch mit sequenziellen 64-KiB-E/A-Zugriffen wurden Tests durchgeführt, die den zuvor beschriebenen Tests mit zufälligen E/A-Zugriffen ähneln. Obwohl die Zunahme der Anzahl der Clientverbindungen pro RSS-Netzwerkschnittstelle über 4 hinaus keine merkliche Auswirkung auf zufällige E/A hatte, gilt dies nicht für die sequenzielle E/A. Wie im folgenden Diagramm dargestellt, führt jede Erhöhung zu einer entsprechenden Steigerung des Lesedurchsatzes. Der Schreibdurchsatz blieb aufgrund der Einschränkungen der Netzwerkbandbreite durch Azure für jeden Instanztyp und jede Instanzgröße gleich. 

![Diagramm: Testvergleich zum Durchsatz](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure legt für jeden Typ/jede Größe von virtuellen Computern Limits für die Netzwerkrate fest. Das Ratenlimit wird nur auf ausgehenden Datenverkehr angewandt. Die Anzahl der auf einem virtuellen Computer vorhandenen NICs hat keinen Einfluss auf die Gesamtbandbreite, die für den Computer verfügbar ist.  Für den Instanztyp D32ds wird beispielsweise ein Netzwerklimit von 16.000 MBit/s (2.000 MiB/s) erzwungen.  Wie das Diagramm für sequenzielle E/A oben zeigt, wirkt sich das Limit auf den ausgehenden Datenverkehr (Schreibvorgänge), aber nicht auf Multichannel-Lesevorgänge aus.

![Diagramm: Vergleichstest mit sequenziellen E/A-Zugriffen](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="smb-signing"></a>SMB-Signierung

Das SMB-Protokoll stellt die Grundlage für die Datei- und Druckfreigabe sowie andere Netzwerkvorgänge wie die Windows-Remoteverwaltung dar. Das SMB-Protokoll unterstützt das digitale Signieren von SMB-Paketen, um Man-in-the-Middle-Angriffe zu vermeiden, die SMB-Pakete während der Übermittlung ändern. 

SMB Signing wird für alle SMB-Protokollversionen unterstützt, die von Azure NetApp Files unterstützt werden. 

### <a name="performance-impact-of-smb-signing"></a>Leistungsauswirkungen der SMB-Signierung  

SMB Signing wirkt sich negativ auf die SMB-Leistung aus. Neben anderen möglichen Ursachen von Leistungsminderungen beansprucht das digitale Signieren jedes Pakets eine zusätzliche clientseitige CPU, wie die folgende perfmon-Ausgabe zeigt. In diesem Fall ist Kern 0 für SMB verantwortlich, also auch für SMB Signing.  Ein Vergleich mit den Zahlen für sequenzielle Lesevorgänge ohne Multichannel im vorherigen Abschnitt zeigt, dass SMB Signing den Gesamtdurchsatz von 875 MiB/s auf ungefähr 250 MiB/s senkt. 

![Diagramm: Auswirkung von SMB Signing auf die Leistung](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="performance-for-a-single-instance-with-a-1-tb-dataset"></a>Leistung für eine einzelne Instanz mit einem 1-TB-Dataset

Um Ihnen einen ausführlicheren Einblick in Workloads mit einer Mischung aus Lese- und Schreibvorgängen zu geben, ist in den folgenden beiden Diagrammen die Leistung eines einzelnen Cloudvolumes (50 TB) mit dem Servicelevel „Ultra“ und einem Dataset von 1 TB und SMB Multichannel (4) dargestellt. Es wurde eine optimale E/A-Tiefe (IODepth) von 16 verwendet. Zudem wurden Parameter für flexible E/A-Zugriffe (Flexible IO, FIO) genutzt, um die vollständige Verwendung der Netzwerkbandbreite (`numjobs=16`) sicherzustellen.

Im folgenden Diagramm sind die Ergebnisse für 4.000 zufällige E/A-Zugriffe bei einer einzelnen VM-Instanz und einer Mischung aus Lese- und Schreibvorgängen in 10-Prozent-Intervallen dargestellt:

![Diagramm: Windows 2019-Standardversion – _D32ds_v4 4K: Test mit zufälligen E/A-Zugriffen](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

Im folgenden Diagramm sind die Ergebnisse für sequenzielle E/A-Zugriffe dargestellt:

![Diagramm: Windows 2019-Standardversion – _D32ds_v4 64K: Sequenzieller Durchsatz](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="performance-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Leistung beim horizontalen Aufskalieren mit fünf VMs bei einem 1-TB-Dataset

Bei diesen Tests mit fünf virtuellen Computern wird dieselbe Testumgebung wie bei der einzelnen VM verwendet, und jeder Prozess schreibt in seine eigene Datei.

Im folgenden Diagramm sind die Ergebnisse für zufällige E/A-Zugriffe dargestellt:

![Diagramm: Windows 2019-Standardversion – _D32ds_v4 4K: Test mit zufälligen E/A-Zugriffen bei fünf Instanzen](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

Im folgenden Diagramm sind die Ergebnisse für sequenzielle E/A-Zugriffe dargestellt:

![Diagramm: Windows 2019-Standardversion – _D32ds_v4 64K: Sequenzieller Durchsatz bei fünf Instanzen](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-to-monitor-hyper-v-ethernet-adapters"></a>Überwachen von Hyper-V-Ethernet-Adaptern  

Eine Strategie beim Testen mit FIO ist das Festlegen von `numjobs=16`. Hierbei wird für jeden Auftrag das Forken in 16 spezifische Instanzen durchgeführt, um für den Microsoft Hyper-V-Netzwerkadapter eine Maximierung zu erzielen.

Sie können auf den einzelnen Adaptern im Windows-Systemmonitor die Aktivität überprüfen, indem Sie **Systemmonitor > Leistungsindikatoren hinzufügen > Netzwerkschnittstelle > Microsoft Hyper-V-Netzwerkadapter** auswählen.

![Screenshot: Oberfläche zum Hinzufügen von Leistungsindikatoren für Systemmonitor](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Wenn auf Ihren Volumes Datenverkehr verarbeitet wird, können Sie in Windows-Systemmonitor Ihre Adapter überwachen. Wenn Sie nicht alle 16 virtuellen Adapter verwenden, erzielen Sie ggf. keine Maximierung Ihrer Netzwerkbandbreitenkapazität.

![Screenshot: Systemmonitor-Ausgabe](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="smb-encryption"></a>SMB-Verschlüsselung

Dieser Abschnitt erklärt die SMB-Verschlüsselung (SMB 3.0 und SMB 3.1.1). 

[SMB-Verschlüsselung](/windows-server/storage/file-server/smb-security) sorgt für End-to-End-Verschlüsselung von SMB-Daten und schützt Daten vor Lauschangriffen in nicht vertrauenswürdigen Netzwerken. Die SMB-Verschlüsselung wird ab SMB 3.0 unterstützt. 

Wenn eine Anforderung an den Speicher gesendet wird, verschlüsselt der Client die Anforderung, und der Speicher entschlüsselt sie wieder. Antworten werden analog dazu vom Server verschlüsselt und vom Client entschlüsselt.

Die SMB-Verschlüsselung wird von Windows 10, Windows 2012 und höheren Versionen unterstützt.

### <a name="smb-encryption-and-azure-netapp-files"></a>SMB-Verschlüsselung und Azure NetApp Files

Die SMB-Verschlüsselung wird bei Azure NetApp Files auf Freigabeebene aktiviert. Von SMB 3.0 wird der AES-CCM-Algorithmus eingesetzt, von SMB 3.1.1 der AES-GCM-Algorithmus.

Die SMB-Verschlüsselung ist nicht erforderlich. Sie wird daher nur für eine bestimmte Freigabe aktiviert, wenn der Benutzer dies bei Azure NetApp Files anfordert. Azure NetApp Files-Freigaben werden niemals für das Internet verfügbar gemacht. Der Zugriff ist nur innerhalb eines bestimmten VNET per VPN oder ExpressRoute möglich. Dadurch sind Azure NetApp Files-Freigaben von Natur aus sicher. Die Entscheidung, die SMB-Verschlüsselung zu aktivieren, liegt allein beim Benutzer. Beachten Sie die zu erwartenden Leistungseinbußen, bevor Sie dieses Feature aktivieren.

### <a name="impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>Auswirkungen der SMB-Verschlüsselung auf Clientworkloads

Die SMB-Verschlüsselung wirkt sich zwar sowohl auf den Client (CPU-Mehraufwand für das Verschlüsseln und Entschlüsseln von Nachrichten) als auch auf den Speicher (Verringerung des Durchsatzes) aus, in der folgenden Tabelle werden allerdings nur die Auswirkungen auf den Speicher veranschaulicht. Es empfiehlt sich, die verschlüsselungsbedingten Leistungsauswirkungen mit Ihren eigenen Anwendungen zu testen, bevor Sie Workloads in der Produktion bereitstellen.

|     E/A-Profil       |     Auswirkung        |
|-  |-  |
|     Lese- und Schreibworkloads      |     10 bis 15 Prozent        |
|     Metadatenintensiv        |     5 %    |

## <a name="accelerated-networking"></a>Beschleunigter Netzwerkbetrieb 

Um die maximale Leistung zu erzielen, empfiehlt es sich, nach Möglichkeit [Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-powershell.md) auf Ihren VMs zu konfigurieren. Beachten Sie dabei Folgendes:  

* Das Azure-Portal aktiviert „Beschleunigter Netzwerkbetrieb“ standardmäßig für virtuelle Computer, die dieses Feature unterstützen.  Bei anderen Bereitstellungsmethoden, wie z. B. Ansible und ähnliche Konfigurationstools, erfolgt dies jedoch möglicherweise nicht.  Wenn „Beschleunigter Netzwerkbetrieb“ nicht aktiviert wird, kann die Leistung eines Computers beeinträchtigt werden.  
* Wenn „Beschleunigter Netzwerkbetrieb“ für die Netzwerkschnittstelle eines virtuellen Computers nicht aktiviert ist (aufgrund des Instanztyps oder der Größe), bleibt dieses Feature auch bei größeren Instanztypen deaktiviert. In diesen Fällen ist ein manueller Eingriff erforderlich.

## <a name="rss"></a>RSS 

Azure NetApp Files unterstützt die empfangsseitige Skalierung (Receive-Side Scaling, RSS).

Wenn SMB Multichannel aktiviert ist, stellt ein SMB3-Client mehrere TCP-Verbindungen mit dem SMB-Server von Azure NetApp Files über eine Netzwerkschnittstellenkarte (Network Interface Card, NIC) her, die Einzel-RSS-fähig ist. 

Wenn Sie wissen möchten, ob die NICs Ihres virtuellen Azure-Computers RSS unterstützen, führen Sie den Befehl `Get-SmbClientNetworkInterface` wie folgt aus, und überprüfen Sie das Feld `RSS Capable`: 

![Screenshot: RSS-Ausgabe für virtuellen Azure-Computer](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)


## <a name="multiple-nics-on-smb-clients"></a>Mehrere NICs auf SMB-Clients

Sie sollten nicht mehrere NICs auf Ihrem Client für SMB konfigurieren. Der SMB-Client passt die Anzahl der NICs an die vom SMB-Server zurückgegebene Anzahl an.  Auf jedes Speichervolume kann nur von genau einem einzigen Speicherendpunkt aus zugegriffen werden.  Das bedeutet, dass für jede SMB-Beziehung nur eine NIC verwendet wird.  

Wie die Ausgabe von `Get-SmbClientNetworkInterace` unten zeigt, verfügt der virtuelle Computer über zwei Netzwerkschnittstellen: 15 und 12.  Wie unter dem folgenden Befehl `Get-SmbMultichannelConnection` zu sehen ist, wird trotz zweier RSS-fähiger NICs nur die Schnittstelle 12 in Verbindung mit der SMB-Freigabe verwendet. Schnittstelle 15 wird nicht verwendet.

![Screenshot: Ausgabe für RSS-fähige NICs](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="next-steps"></a>Nächste Schritte  

- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- Weitere Informationen zur Verwendung von SMB-Dateifreigaben mit Azure NetApp Files finden Sie unter [Azure NetApp Files: Managed Enterprise File Shares for SMB Workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) (Azure NetApp Files: verwaltete Unternehmensdateifreigaben für SMB-Workloads).
