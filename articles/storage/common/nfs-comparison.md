---
title: Vergleich des NFS-Zugriffs auf Azure Files, Azure Blob Storage und Azure NetApp Files
description: Hier finden Sie einen Vergleich des NFS-Zugriffs auf Azure Files, Azure Blob Storage und Azure NetApp Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: rogarana
ms.openlocfilehash: 3dcc0a93473e5f0b14f8921114172dbf6506d8aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636741"
---
# <a name="compare-access-to-azure-files-blob-storage-and-azure-netapp-files-with-nfs"></a>Vergleich des Zugriffs auf Azure Files, Azure Blob Storage und Azure NetApp Files mit NFS

Dieser Artikel enthält einen Vergleich der einzelnen Angebote und erläutert die Unterschiede beim Zugriff über das [Network File System-Protokoll (NFS)](https://en.wikipedia.org/wiki/Network_File_System). Dieser Vergleich gilt nicht, wenn Sie eine andere Zugriffsmethode verwenden.

Einen allgemeineren Vergleich finden Sie in [diesem Artikel](storage-introduction.md) zu Azure Blob Storage und Azure Files oder in [diesem Artikel](../files/storage-files-netapp-comparison.md) zu Azure Files und Azure NetApp Files.

## <a name="comparison"></a>Vergleich

|Category  |Azure Blob Storage  |Azure Files  |Azure NetApp Files  |
|---------|---------|---------|---------|
|Anwendungsfälle     |Blob Storage eignet sich am besten für umfangreiche Workloads mit vielen Lesevorgängen und sequenziellem Zugriff, bei denen Daten einmal erfasst und danach nur minimal geändert werden.<br></br>Blob Storage bietet die niedrigsten Gesamtkosten, wenn nur wenig oder gar keine Wartung notwendig ist.<br></br>Beispielszenarien: umfangreiche analytische Daten, durchsatzabhängiges High Performance Computing, Sicherung und Archivierung, autonomes Fahren, Medienrendering oder Genomsequenzierung         |Azure Files ist ein hoch verfügbarer Dienst, der sich am besten für Workloads mit wahlfreiem Zugriff eignet.<br></br>Azure Files bietet für NFS-Freigaben vollständige Unterstützung für POSIX-Dateisysteme und kann mit dem integrierten CSI-Treiber problemlos neben VM-basierten Plattformen auf Containerplattformen wie Azure Container Instances (ACI) und Azure Kubernetes Service (AKS) verwendet werden.<br></br>Beispielszenarien: freigegebene Dateien, Datenbanken, Basisverzeichnisse, herkömmliche Anwendungen, ERP, CMS, NAS-Migrationen, die keine erweiterte Verwaltung erfordern, und benutzerdefinierte Anwendungen, für die horizontal skalierbarer Dateispeicher erforderlich ist         |Azure NetApp Files ist ein vollständig verwalteter Dateidienst in der Cloud, der von NetApp unterstützt wird und erweiterte Verwaltungsfunktionen bietet.<br></br>NetApp Files eignet sich für Workloads, die wahlfreien Zugriff erfordern, und bietet umfassende Protokollunterstützung sowie Datenschutzfunktionen.<br></br>Beispielszenarien: Lokale NAS-Migration für Unternehmen, die umfassende Verwaltungsfunktionen erfordert, wartezeitempfindliche Workloads wie SAP HANA, wartezeitempfindliches oder IOPS-intensives High Performance Computing oder Workloads, die gleichzeitigen Zugriff mit mehreren Protokollen erfordern         |
|Verfügbare Protokolle     |NFS 3.0<br></br>REST<br></br>Data Lake Storage Gen2         |SMB<br><br>NFS 4.1 (Vorschau)<br></br> (Keine Interoperabilität zwischen den Protokollen)         |NFS 3.0 und 4.1<br></br>SMB         |
|Wichtige Features     | Integriert in HPC-Cache für Workloads mit geringer Wartezeit <br> </br> Integrierte Verwaltung, einschließlich Lebenszyklus, unveränderliche Blobs, Datenfailover und Metadatenindex         | Zonenredundant für Hochverfügbarkeit <br></br> Konsistente Wartezeit im einstelligen Millisekundenbereich <br></br>Vorhersagbare Leistung und Kosten entsprechend der Kapazität         |Extrem niedrige Wartezeit (unter einer Millisekunde)<br></br>Umfangreiche NetApp ONTAP-Verwaltungsfunktionen wie SnapMirror in der Cloud<br></br>Konsistente Hybrid Cloud-Umgebung         |
|Leistung (pro Volume)     |Bis zu 20.000 IOPS, bis zu 100 GiB/s Durchsatz         |Bis zu 100.000 IOPS, bis zu 80 GiB/s Durchsatz         |Bis zu 460.000 IOPS, bis zu 36 GiB/s Durchsatz         |
|Skalieren     | Bis zu 2 PiB für ein einzelnes Volume <br></br> Bis zu ca. 4,75 TiB für eine einzelne Datei<br></br>Keine Mindestkapazitätsanforderungen         |Bis zu 100 TiB für eine einzelne Dateifreigabe<br></br>Bis zu 4 TiB für eine einzelne Datei<br></br>100 GiB Mindestkapazität         |Bis zu 100 TiB für ein einzelnes Volume<br></br>Bis zu 16 TiB für eine einzelne Datei<br></br>Konsistente Hybrid Cloud-Umgebung         |
|Preise     |[Preise für Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)         |[Preise für Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)         |[Preise für Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)         |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Zugriff auf Blob Storage mit NFS finden Sie unter [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage (Vorschau)](../blobs/network-file-system-protocol-support.md).
- Informationen zum Zugriff auf Azure Files mit NFS finden Sie unter [NFS-Dateifreigaben in Azure Files](../files/files-nfs-protocol.md).
- Informationen zum Zugriff auf Azure NetApp Files mit NFS finden Sie unter [Schnellstart: Einrichten von Azure NetApp Files und Erstellen eines NFS-Volumes](../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).
