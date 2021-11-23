---
title: Vergleich von Azure Files und Azure NetApp Files | Microsoft-Dokumentation
description: Vergleich von Azure Files und Azure NetApp Files
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: jeffpatt
ms.openlocfilehash: 06c1c1e32c8e31f1762f3fbdc932612036c5a327
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519285"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Vergleich von Azure Files und Azure NetApp Files

Dieser Artikel enthält einen Vergleich von Azure Files und Azure NetApp Files. 

Die meisten Workloads, die Clouddateispeicher erfordern, funktionieren gut für Azure Files oder Azure NetApp Files. Überprüfen Sie die in diesem Artikel bereitgestellten Informationen, um die beste Lösung für Ihre Workload zu ermitteln. Weitere Informationen finden Sie in der Dokumentation zu [Azure Files](./index.yml) und [Azure NetApp Files](../../azure-netapp-files/index.yml) sowie im Video [Freigegebener Speicher für alle Dateiworkloads in Unternehmen](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s), in dem die Auswahl zwischen Azure Files und Azure NetApp Files behandelt wird.

## <a name="features"></a>Features

| Category | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| BESCHREIBUNG | [Azure Files](https://azure.microsoft.com/services/storage/files/) ist ein vollständig verwalteter, hochverfügbarer Dienst für Unternehmen, der für Workloads mit willkürlichem Zugriff und direkten Datenaktualisierungen optimiert ist.<br><br> Azure Files basiert auf derselben Azure Storage-Plattform wie andere Dienste wie Azure-Blobs. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) ist ein vollständig verwalteter, hochverfügbarer NAS-Dienst auf Unternehmensniveau, der die anspruchsvollsten, hochleistungsfähigen Workloads mit geringer Latenz verarbeiten kann, die erweiterte Datenverwaltungsfunktionen erfordern. Der Dienst ermöglicht die Migration von Workloads, die ohne ihn als „nicht migrierbar“ angesehen werden.<br><br>  ANF basiert auf dem Bare-Metal-System von NetApp mit einem ONTAP-Speicherbetriebssystem, das im Azure-Rechenzentrum ausgeführt wird, um eine konsistente Azure-Umgebung und eine Leistung zu erzielen, die einer lokalen Leistung entspricht. |
| Protokolle | Premium<br><ul><li>SMB 2.1, 3.0, 3.1.1</li><li>NFS 4.1</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2.1, 3.0, 3.1.1</li><li>REST</li></ul><br> Weitere Informationen finden Sie unter [Azure-Dateifreigabeprotokolle](./storage-files-planning.md#available-protocols). | Alle Ebenen<br><ul><li>SMB 2.x, 3.x</li><li>NFS 3.0, 4.1</li><li>Dual-Protokollzugriff (NFSv3/SMB)</li></ul><br> Weitere Informationen zum Erstellen von [NFS](../../azure-netapp-files/azure-netapp-files-create-volumes.md)-, [SMB](../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)- oder [Dual-Protokoll](../../azure-netapp-files/create-volumes-dual-protocol.md)-Volumes. |
| Regionale Verfügbarkeit | Premium<br><ul><li>mehr als 30 Regionen</li></ul><br>Standard<br><ul><li>Alle Regionen</li></ul><br> Weitere Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=storage). | Alle Ebenen<br><ul><li>mehr als 28 Regionen</li></ul><br> Weitere Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=storage). |
| Redundanz | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Weitere Informationen finden Sie unter [Redundanz](./storage-files-planning.md#redundancy). | Alle Ebenen<br><ul><li>Integrierte lokale Hochverfügbarkeit</li><li>[Regionsübergreifende Replikation](../../azure-netapp-files/cross-region-replication-introduction.md)</li></ul> |
| Vereinbarung zum Servicelevel (SLA)<br><br> Beachten Sie, dass SLAs für Azure Files und Azure NetApp Files unterschiedlich berechnet werden. | [SLA für AZure Files](https://azure.microsoft.com/support/legal/sla/storage/) | [SLA für Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Identitätsbasierte Authentifizierung und Autorisierung | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Beachten Sie, dass die identitätsbasierte Authentifizierung nur bei Verwendung des SMB-Protokolls unterstützt wird. Weitere Informationen dazu finden Sie unter [Häufig gestellte Fragen](./storage-files-faq.md#security-authentication-and-access-control). | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Duales Protokoll für NFS/SMB<ul><li>Integration von ADDS/LDAP</li></ul><br>NFSv3/NFSv4.1<ul><li>ADDS/LDAP-Integration mit erweiterten NFS-Gruppen [(Vorschau)](../../azure-netapp-files/configure-ldap-extended-groups.md)</li></ul><br> Weitere Informationen finden Sie unter [Häufig gestellte Fragen zu NFS in Azure NetApp Files](../../azure-netapp-files/faq-nfs.md) und [Häufig gestellte Fragen zu SMB in Azure NetApp Files](../../azure-netapp-files/faq-smb.md). |
| Verschlüsselung | Alle Protokolle<br><ul><li>Verschlüsselung im Ruhezustand (AES 256) mit vom Kunden oder von Microsoft verwalteten Schlüsseln</li></ul><br>SMB<br><ul><li>Kerberos-Verschlüsselung mit AES 256 oder RC4-HMAC</li><li>Verschlüsselung während der Übertragung</li></ul><br>REST<br><ul><li>Verschlüsselung während der Übertragung</li></ul><br> Weitere Informationen finden Sie unter [Sicherheit und Netzwerkbetrieb](files-nfs-protocol.md#security-and-networking). | Alle Protokolle<br><ul><li>Verschlüsselung im Ruhezustand (AES 256) mit von Microsoft verwalteten Schlüsseln </li></ul><br>SMB<ul><li>Verschlüsselung während der Übertragung mit AES-CCM (SMB 3.0) und AES-GCM (SMB 3.1.1)</li></ul><br>NFS 4.1<ul><li>Verschlüsselung während der Übertragung mithilfe von Kerberos mit AES 256</li></ul><br> Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Sicherheit](../../azure-netapp-files/faq-security.md). |
| Zugriffsoptionen | <ul><li>Internet</li><li>Sicherer VNet-Zugriff</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure-Dateisynchronisierung</li></ul><br> Weitere Informationen finden Sie unter [Überlegungen zum Netzwerkbetrieb](./storage-files-networking-overview.md). | <ul><li>Sicherer VNet-Zugriff</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[Globaler Dateicache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](../../hpc-cache/hpc-cache-overview.md)</li></ul><br> Weitere Informationen finden Sie unter [Überlegungen zum Netzwerkbetrieb](../../azure-netapp-files/azure-netapp-files-network-topologies.md). |
| Datenschutz  | <ul><li>Inkrementelle Momentaufnahmen</li><li>Selbstwiederherstellung der Datei/des Verzeichnisses durch den Benutzer</li><li>An neuem Speicherort wiederherstellen</li><li>Direkte Wiederherstellung</li><li>Vorläufiges Löschen auf Freigabeebene</li><li>Azure Backup-Integration</li></ul><br> Weitere Informationen finden Sie unter [Azure Files verbessert die Datenschutzfunktionen](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/). | <ul><li>Momentaufnahmen (255/Volume)</li><li>Selbstwiederherstellung der Datei/des Verzeichnisses durch den Benutzer</li><li>Wiederherstellen auf neuem Volume</li><li>Direkte Wiederherstellung</li><li>[Regionsübergreifende Replikation](../../azure-netapp-files/cross-region-replication-introduction.md) </li></ul><br> Weitere Informationen finden Sie unter [Funktionsweise von Azure NetApp Files-Momentaufnahmen](../../azure-netapp-files/snapshots-introduction.md). |
| Migrationstools  | <ul><li>Azure Data Box</li><li>Azure-Dateisynchronisierung</li><li>Speichermigrationsdienst</li><li>AzCopy</li><li>Robocopy</li></ul><br> Weitere Informationen finden Sie unter [Migrieren zu Azure-Dateifreigaben](./storage-files-migration-overview.md). | <ul><li>[Globaler Dateicache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Speichermigrationsdienst</li><li>AzCopy</li><li>Robocopy</li><li>Anwendungsbasiert (z. B. HSR, Data Guard, AOAG)</li></ul> |
| Ebenen | <ul><li>Premium</li><li>Für Transaktion optimiert</li><li>Heiß</li><li>Kalt</li></ul><br> Weitere Informationen finden Sie unter [Speicherebenen](./storage-files-planning.md#storage-tiers). | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> Alle Ebenen bieten unter MS eine minimale Wartezeit.<br><br> Weitere Informationen finden Sie unter [Dienstebenen](../../azure-netapp-files/azure-netapp-files-service-levels.md) und [Überlegungen zur Leistung](../../azure-netapp-files/azure-netapp-files-performance-considerations.md). |
| Preise | [Azure Files – Preise](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files – Preise](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Skalierbarkeit und Leistung

| Category | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| Mindestgröße für Freigabe/Volume | Premium<br><ul><li>100 GB</li></ul><br>Standard<br><ul><li>Keine Mindestanforderungen.</li></ul> | Alle Ebenen<br><ul><li>100 GiB (Mindestgröße des Kapazitätspools: 4 TiB)</li></ul> |
| Maximale Größe für Freigabe/Volume | ca. 100 TiB | Alle Ebenen<br><ul><li>100 TiB (500 TiB Limit für Kapazitätspool)</li></ul><br>Bis zu 12,5 PIB pro Azure NetApp-Konto |
| Maximale IOPS für Freigabe/Volume | Premium<br><ul><li>Bis zu 100.000</li></ul><br>Standard<br><ul><li>Bis zu 20.000</li></ul> | Ultra und Premium<br><ul><li>Bis zu 450.000 </li></ul><br>Standard<br><ul><li>Bis zu 320.000</li></ul> |
| Maximaler Durchsatz für Freigabe/Volume | Premium<br><ul><li>Bis zu 10 GiB/s</li></ul><br>Standard<br><ul><li>Bis zu 300 MiB/s</li></ul> | Ultra und Premium<br><ul><li>Bis zu 4,5 GiB/s</li></ul><br>Standard<br><ul><li>Bis zu 3,2 Gib/s</li></ul> |
| Maximale Dateigröße | 4 TiB | 16 TiB |
| Maximaler IOPS pro Datei | Premium<br><ul><li>Bis zu 8.000</li></ul><br>Standard<br><ul><li>1\.000</li></ul> | Alle Ebenen<br><ul><li>Volumelimit</li></ul> |
| Maximaler Durchsatz pro Datei | Premium<br><ul><li>300 MiB/s (bis zu 1 GiB/s bei SMB Multichannel)</li></ul><br>Standard<br><ul><li>60 MiB/s</li></ul> | Alle Ebenen<br><ul><li>Volumelimit</li></ul> |
| SMB Multichannel | Ja | Ja |
| Latency | Minimale Wartezeit im einstelligen Millisekundenbereich (2 ms bis 3 ms für kleine E/A-Vorgänge) | Minimale Wartezeit unter einer Millisekunde (unter 1 ms für zufällige E/A-Vorgänge)<br><br>Weitere Informationen finden Sie unter [Leistungsbenchmarks](../../azure-netapp-files/performance-benchmarks-linux.md). |

Weitere Informationen zur Skalierbarkeit und zu Leistungszielen finden Sie unter [Azure Files](./storage-files-scale-targets.md#azure-files-scale-targets) und [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-resource-limits.md).

## <a name="next-steps"></a>Nächste Schritte
* [Dokumentation zu Azure Files](./index.yml)
* [Azure NetApp Files-Dokumentation](../../azure-netapp-files/index.yml)
* [Video: Freigegebener Speicher für alle Dateiworkloads in Unternehmen](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s)
