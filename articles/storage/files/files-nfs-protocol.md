---
title: NFS-Dateifreigaben in Azure Files
description: Erfahren Sie mehr über Dateifreigaben, die in Azure Files mit dem NFS-Protokoll (Network File System) gehostet werden.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 31aec4e32178b2f0ca7905c6d584df298dd2059a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524339"
---
# <a name="nfs-file-shares-in-azure-files"></a>NFS-Dateifreigaben in Azure Files
Azure Files unterstützt zwei Branchenstandardprotokolle für die Einbindung von Azure-Dateifreigaben: das [SMB-Protokoll (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) und das [NFS-Protokoll (Network File System)](https://en.wikipedia.org/wiki/Network_File_System). In Azure Files können Sie das Dateisystemprotokoll auswählen, das sich für Ihre Workload am besten eignet. Bei Azure-Dateifreigaben ist der Zugriff auf eine einzelne Azure-Dateifreigabe über das SMB- oder das NFS-Protokoll nicht möglich. Sie können jedoch SMB- und NFS-Dateifreigaben innerhalb desselben Speicherkontos erstellen. Für alle Dateifreigaben ermöglicht Azure Files Dateifreigaben in Unternehmensqualität, die entsprechend Ihren Speicheranforderungen hochskaliert werden können und auf die Tausende von Clients gleichzeitig zugreifen können.

In diesem Artikel werden NFS-Azure-Dateifreigaben behandelt. Informationen zu SMB-Azure-Dateifreigaben finden Sie unter [SMB-Dateifreigaben in Azure Files](files-smb-protocol.md).

> [!IMPORTANT]
>  Bevor Sie NFS-Dateifreigaben für die Produktion verwenden, finden Sie im Artikel [Problembehandlung für Azure NFS-Dateifreigaben](storage-troubleshooting-files-nfs.md) eine Liste bekannter Probleme.

## <a name="common-scenarios"></a>Häufige Szenarien
NFS-Dateifreigaben werden häufig in den folgenden Szenarien verwendet:

- Zusatzspeicher für Linux/UNIX-basierte Anwendungen, z. B. branchenspezifische Anwendungen, die mit Linux- oder POSIX-Dateisystem-APIs geschrieben wurden (auch wenn keine POSIX-Konformität erforderlich ist)
- für Workloads, die POSIX-kompatible Dateifreigaben erfordern, Szenarios, in denen die Groß-/Kleinschreibung wichtig ist oder bei Berechtigungen im UNIX-Format
- Entwicklung neuer Anwendungen und Dienste, insbesondere wenn die Anwendungen oder Dienste wahlfreie E/A und hierarchische Speicherung erfordern 

## <a name="features"></a>Features
- Vollständig POSIX-kompatibles Dateisystem
- Unterstützung fester Links
- Unterstützung von symbolischen Verknüpfungen
- NFS-Dateifreigaben unterstützen derzeit lediglich die meisten Features der [4.1-Protokollspezifikation](https://tools.ietf.org/html/rfc5661). Einige Features wie Delegierungen und Rückrufe aller Art, Kerberos-Authentifizierung und Verschlüsselung während der Übertragung werden nicht unterstützt.


## <a name="security-and-networking"></a>Sicherheit und Netzwerk
Alle in Azure Files gespeicherten Daten werden im Ruhezustand mithilfe der Azure-Speicherdienstverschlüsselung (Storage Service Encryption, SSE) verschlüsselt. Die Speicherdienstverschlüsselung funktioniert ähnlich wie BitLocker unter Windows: Daten werden unterhalb der Dateisystemebene verschlüsselt. Da Daten durch die Codierung auf dem Datenträger unterhalb des Dateisystems der Azure-Dateifreigabe verschlüsselt werden, benötigen Sie keinen Zugriff auf den zugrunde liegenden Schlüssel auf dem Client, um aus der Azure-Dateifreigabe zu lesen oder in diese zu schreiben. Die Verschlüsselung ruhender Daten wird auf SMB- und NFS-Protokolle angewendet.

Für die Verschlüsselung während der Übertragung bietet Azure eine Verschlüsselungsschicht für alle Daten, die zwischen Azure-Rechenzentren übertragen werden: [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). Dadurch ist eine Verschlüsselung möglich, wenn Daten zwischen Azure-Rechenzentren übertragen werden. Anders als Azure Files-Dateien mit dem SMB-Protokoll bieten Dateifreigaben, die das NFS-Protokoll verwenden, keine benutzerbasierte Authentifizierung. Die Authentifizierung für NFS-Freigaben basiert auf den konfigurierten Netzwerksicherheitsregeln. Aus diesem Grund müssen Sie entweder Dienstendpunkte oder private Endpunkte verwenden, um sicherzustellen, dass nur sichere Verbindungen mit der NFS-Freigabe hergestellt werden. Wenn Sie lokal auf Freigaben zugreifen möchten, müssen Sie zusätzlich zu einem privaten Endpunkt ein VPN oder eine ExpressRoute-Verbindung einrichten. Anforderungen, die nicht von den folgenden Quellen ausgehen, werden abgelehnt:

- [Anforderungen von einem privaten Endpunkt](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [Anforderungen von einem Point-to-Site-VPN](../../vpn-gateway/point-to-site-about.md)
    - [Standort-zu-Standort](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Anforderungen von einem eingeschränkten öffentlichen Endpunkt](storage-files-networking-overview.md#storage-account-firewall-settings)

Weitere Informationen zu den verfügbaren Netzwerkoptionen finden Sie im Artikel zu [Azure Files-Netzwerken](storage-files-networking-overview.md).

## <a name="support-for-azure-storage-features"></a>Unterstützung für Azure Storage-Features

In der folgenden Tabelle sind die aktuellen Unterstützungsebenen für Azure Storage-Features in Konten angegeben, für die das NFS 4.1-Feature aktiviert ist. 

Der Status der Elemente in dieser Tabelle kann sich im Laufe der Zeit ändern, da die Unterstützung laufend erweitert wird.

| Storage-Feature | Unterstützt für NFS-Freigaben |
|-----------------|---------|
| [REST-API auf Dateiverwaltungsebene](/rest/api/storagerp/file-shares) | ✔️ |
| [REST-API auf Dateidatenebene](/rest/api/storageservices/file-service-rest-api)| ⛔ |
| Verschlüsselung ruhender Daten|   ✔️ |
| Verschlüsselung während der Übertragung| ⛔ |
| [LRS- oder ZRS-Redundanztypen](storage-files-planning.md#redundancy)|  ✔️ |
| [Private Endpunkte](storage-files-networking-overview.md#private-endpoints) | ✔️  |
| Einbindung von Unterverzeichnissen|  ✔️ |
| [Gewähren des Netzwerkzugriffs auf bestimmte virtuelle Azure-Netzwerke](storage-files-networking-endpoints.md#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)|  ✔️  |
| [Gewähren des Netzwerkzugriffs auf bestimmte IP-Adressen](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#grant-access-from-an-internet-ip-range)| ⛔ |
| [Premium-Tarif](storage-files-planning.md#storage-tiers) |  ✔️  |
| [Standardebenen (heiß, kalt und transaktionsoptimiert)](storage-files-planning.md#storage-tiers)| ⛔ |
| [POSIX-Berechtigungen](https://en.wikipedia.org/wiki/File-system_permissions#Notation_of_traditional_Unix_permissions)|  ✔️  |
| Root Squash|  ✔️  |
| [Identitätsbasierte Authentifizierung](storage-files-active-directory-overview.md) | ⛔ |
| [Vorläufiges Löschen von Azure-Dateifreigaben](storage-files-prevent-file-share-deletion.md) | ⛔  |
| [Azure-Dateisynchronisierung](../file-sync/file-sync-introduction.md)| ⛔ |
| [Sichern von Azure-Dateifreigaben](../../backup/azure-file-share-backup-overview.md)| ⛔ |
| [Momentaufnahmen von Azure-Dateifreigaben](storage-snapshots-files.md)| ⛔ |
| [GRS- oder GZRS-Redundanztypen](storage-files-planning.md#redundancy)| ⛔ |
| [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)| ⛔ |
| Azure Storage-Explorer| ⛔ |
| Erstellen von NFS-Freigaben für vorhandene Speicherkonten*| ⛔ |
| Unterstützung für mehr als 16 Gruppen| ⛔ |

## <a name="regional-availability"></a>Regionale Verfügbarkeit

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="performance"></a>Leistung
NFS-Dateifreigaben in Azure werden nur für Premium-Dateifreigaben angeboten, bei denen Daten auf SSD-Datenträgern gespeichert werden. IOPS und Durchsatz von NFS-Freigaben werden mit der bereitgestellten Kapazität skaliert. Informationen zu den Formeln für IOPS, E/A-Bursting und Durchsatz finden Sie im Abschnitt zum [bereitgestellten Modell](understanding-billing.md#provisioned-model) im Artikel mit grundlegenden Informationen zur Abrechnung. Die durchschnittliche E/A-Latenz liegt bei geringer E/A-Größe im unteren einstelligen Millisekundenbereich, während die durchschnittliche Metadatenlatenz im hohen einstelligen Millisekundenbereich liegt. Bei metadatenlastigen Vorgänge wie „untar“ und Workloads wie WordPress kann es aufgrund der hohen Anzahl von Öffnungs- und Schließvorgängen zu zusätzlichen Wartezeiten kommen.

## <a name="workloads"></a>Arbeitsauslastungen
> [!IMPORTANT]
> Bevor Sie NFS-Dateifreigaben für die Produktion verwenden, finden Sie im Artikel [Problembehandlung für Azure NFS-Dateifreigaben](storage-troubleshooting-files-nfs.md) eine Liste bekannter Probleme.

NFS wurde für eine gute Zusammenarbeit mit Workloads wie SAP-Anwendungsebene, Datenbanksicherungen, Datenbankreplikation, Messagingwarteschlangen, Homeverzeichnissen für universelle Dateiserver und Inhaltsrepositorys für Anwendungsworkloads überprüft.

Bei den folgenden Workloads sind Probleme bekannt. Eine Liste bekannter Probleme finden Sie im Artikel zur [Problembehandlung bei NFS-Dateifreigaben in Azure](storage-troubleshooting-files-nfs.md):
- Bei Oracle Database besteht eine Inkompatibilität mit dem dNFS-Feature.


## <a name="next-steps"></a>Nächste Schritte
- [Erstellen einer NFS-Dateifreigabe](storage-files-how-to-create-nfs-shares.md)
- [Vergleichen des Zugriffs auf Azure Files, Blob Storage und Azure NetApp Files mit NFS](../common/nfs-comparison.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
