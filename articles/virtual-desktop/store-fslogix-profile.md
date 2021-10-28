---
title: Speicher für FSLogix-Profilcontainer mit Azure Virtual Desktop – Azure
description: Optionen zum Speichern Ihres FSLogix-Profils für Azure Virtual Desktop auf Azure Storage
author: Heidilohr
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 78dae3bbf31d4f22047288861afeebb11ee4648f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232094"
---
# <a name="storage-options-for-fslogix-profile-containers-in-azure-virtual-desktop"></a>Speicheroptionen für FSLogix-Profilcontainer in Azure Virtual Desktop

Azure bietet mehrere Speicherlösungen für die Speicherung Ihres FSLogix-Profilcontainers. In diesem Artikel werden die Speicherlösungen verglichen, die Azure für Azure Virtual Desktop FSLogix-Benutzerprofilcontainer bietet. Für die meisten unserer Kunden wird empfohlen, die FSLogix-Profilcontainer in Azure Files zu speichern.

Azure Virtual Desktop stellt FSLogix-Profilcontainer als empfohlene Lösung für Benutzerprofile bereit. FSLogix ist für das Roaming von Profilen in Remotecomputingumgebungen wie z B. Azure Virtual Desktop konzipiert. Bei der Anmeldung wird dieser Container dynamisch an die Computingumgebung angefügt. Hierzu werden eine nativ unterstützte virtuelle Festplatte (Virtual Hard Disk, VHD) und eine virtuelle Hyper-V-Festplatte (VHDX) verwendet. Das Benutzerprofil ist sofort verfügbar und wird im System genau so angezeigt wie ein natives Benutzerprofil.

In den folgenden Tabellen werden die Speicherlösungen verglichen, die in Azure Storage für Benutzerprofile in einem Azure Virtual Desktop-FSLogix-Profilcontainer zur Verfügung stehen.

## <a name="azure-platform-details"></a>Details zur Azure-Plattform

|Features|Azure Files|Azure NetApp Files|Speicherplätze direkt|
|--------|-----------|------------------|---------------------|
|Anwendungsfall|Allgemeiner Zweck|Ultra-Leistung oder Migration aus einer lokalen NetApp-Umgebung|Plattformübergreifend|
|Plattformdienst|Ja, native Azure-Lösung|Ja, native Azure-Lösung|Nein, selbst verwaltet|
|Regionale Verfügbarkeit|Alle Regionen|[Bestimmte Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Alle Regionen|
|Redundanz|Lokal redundant/zonenredundant/georedundant/geozonenredundant|Lokal redundant|Lokal redundant/zonenredundant/georedundant|
|Tarife und Leistung| Standard (Transaktionsoptimiert)<br>Premium<br>Bis zu 100.000 IOPS pro Freigabe mit 10 GBit/s pro Freigabe bei einer ungefähren Wartezeit von 3 ms|Standard<br>Premium<br>Ultra<br>Bis zu 4,5 GBit/s bei einer ungefähren Wartezeit von 1 ms IOPS- und Leistungsdetails finden Sie unter [Überlegungen zur Leistung von Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md) und in [Häufig gestellte Fragen (FAQ)](../azure-netapp-files/faq-performance.md#how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops).|HDD Standard: bis zu 500 IOPS pro Datenträger<br>SSD Standard: bis zu 4.000 IOPS pro Datenträger<br>SSD Premium: bis zu 20.000 IOPS pro Datenträger<br>Für direkte Speicherplätze werden Premium-Datenträger empfohlen.|
|Capacity|100 TiB pro Freigabe, bis zu 5 PiB pro universellem Konto |100 TiB pro Volume, bis zu 12,5 PiB pro Abonnement|Maximal 32 TiB pro Datenträger|
|Erforderliche Infrastruktur|Mindestgröße für Freigaben: 1 GiB|Mindestgröße für Kapazitätspools: 4 TiB. Mindestgröße für Volumes: 100 GiB|Zwei virtuelle Computer in Azure IaaS (+ Cloudzeuge) oder mindestens drei virtuelle Computer ohne und Kosten für Datenträger|
|Protokolle|SMB 3.0/2.1, NFSv4.1 (Vorschau), REST|NFSv3, NFSv4.1 (Vorschauversion), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Details zur Azure-Verwaltung

|Features|Azure Files|Azure NetApp Files|Speicherplätze direkt|
|--------|-----------|------------------|---------------------|
|Zugriff|Cloud, lokal und hybrid (Azure-Dateisynchronisierung)|Cloud, lokal (über ExpressRoute)|Cloud, lokal|
|Backup|Azure Backup-Momentaufnahmeintegration|Azure NetApp Files-Momentaufnahmen|Azure Backup-Momentaufnahmeintegration|
|Sicherheit und Compliance|[Alle von Azure unterstützten Zertifikate](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO abgeschlossen|[Alle von Azure unterstützten Zertifikate](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-Integration|[Natives Active Directory und Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services und Active Directory (nativ)](../azure-netapp-files/faq-smb.md#does-azure-netapp-files-support-azure-active-directory)|Nur Unterstützung von Active Directory (nativ) oder Azure Active Directory Domain Services|

Nachdem Sie sich für eine Speichermethode entschieden haben, können Sie sich unter [Azure Virtual Desktop – Preise](https://azure.microsoft.com/pricing/details/virtual-desktop/) über unsere Tarife informieren.

## <a name="azure-files-tiers"></a>Ebenen in Azure Files

Azure Files bietet zwei unterschiedliche Speicherebenen: „Premium“ und „Standard“. Mit diesen Ebenen können Sie Leistung und Kosten Ihrer Dateifreigaben anpassen, um die Anforderungen Ihres Szenarios zu erfüllen.

- Premium-Dateifreigaben basieren auf Solid State Drives (SSDs) und werden in Speicherkonten vom Typ FileStorage bereitgestellt. Premium-Dateifreigaben bieten eine konsistente hohe Leistung und niedrige Latenz für ein- und ausgabeintensive Workloads (E/A). 

- Standard-Dateifreigaben basieren auf Festplattenlaufwerken (Hard Disk Drives, HDDs) und werden in Speicherkonten vom Typ „Universell Version 2 (GPv2)“ bereitgestellt. Standard-Dateifreigaben bieten eine zuverlässige Leistung für E/A-Workloads, die weniger anfällig für Leistungsschwankungen sind, z. B. universelle Dateifreigaben und Dev/Test-Umgebungen. Standard-Dateifreigaben sind nur in einem nutzungsbasierten Abrechnungsmodell verfügbar.

In der folgenden Tabelle sind unsere Empfehlungen aufgeführt, welche Leistungsstufe basierend auf der Workload verwendet werden sollte. Diese Empfehlungen helfen Ihnen bei der Auswahl der Leistungsstufe, die Ihren Leistungszielen, Ihrem Budget und regionalen Aspekten gerecht wird. Diese Empfehlungen basieren auf den Beispielszenarien aus [Remotedesktop-Workloads](/windows-server/remote/remote-desktop-services/remote-desktop-workloads). 

| Workloadtyp | Empfohlene Dateiebene |
|--------|-----------|
| Leicht (weniger als 200 Benutzer) | Standarddateifreigaben |
| Leicht (mehr als 200 Benutzer) | Premium-Dateifreigaben oder Standard mit mehreren Dateifreigaben |
|Medium|Premium-Dateifreigaben|
|Schwer|Premium-Dateifreigaben|
|Power|Premium-Dateifreigaben|

Weitere Informationen zur Leistung von Azure Files finden Sie unter [Skalierbarkeitsziele für Azure Files](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets). Weitere Informationen zu den Preisen finden Sie unter [Azure Files – Preise](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu FSLogix-Profilcontainern, Benutzerprofil-Datenträgern und anderen Benutzerprofiltechnologien finden Sie in der Tabelle unter [FSLogix-Profilcontainer und Azure Files](fslogix-containers-azure-files.md).

Wenn Sie zum Erstellen Ihrer eigenen FSLogix-Profilcontainer bereit sind, beginnen Sie mit einem der folgenden Tutorials:

- [Erste Schritte mit FSLogix-Profilcontainern für Azure Files in Azure Virtual Desktop](create-file-share.md)
- [Erstellen eines FSLogix-Profilcontainers für einen Hostpool mit Azure NetApp Files](create-fslogix-profile-container.md)
- Die Anweisungen unter [Bereitstellen eines Scale-Out-Dateiservers mit direkten Speicherplätzen und zwei Knoten für die Speicherung von Benutzerprofil-Datenträgern](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) gelten auch, wenn Sie anstelle eines Benutzerprofil-Datenträgers einen FSLogix-Profilcontainer verwenden.

Sie können auch ganz von vorn beginnen und Ihre eigene Azure Virtual Desktop-Lösung einrichten, wie unter [Tutorial: Erstellen eines Mandanten in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) beschrieben.
