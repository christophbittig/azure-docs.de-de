---
title: Unterstützung für Network File System 3.0 in Azure Blob Storage | Microsoft-Dokumentation
description: Blob Storage unterstützt jetzt das NFS 3.0-Protokoll (Network File System). Diese Unterstützung ermöglicht es Linux-Clients, einen Container aus einem virtuellen Azure-Computer (VM) oder einem lokalen Computer in Blob Storage einzubinden.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: f350b4fad79a80a1bba0572c5ed906aa4aff1168
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005454"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage

Blob Storage unterstützt jetzt das NFS 3.0-Protokoll (Network File System). Diese Unterstützung bietet Linux-Dateisystemkompatibilität bei Objektspeicherskalierung und -preisen und ermöglicht es Linux-Clients, einen Container aus einem virtuellen Azure-Computer (VM) oder einem lokalen Computer in Blob Storage einzubinden. 

Es war immer eine Herausforderung, umfangreiche Legacy-Workloads wie High Performance Computing (HPC) in der Cloud auszuführen. Ein Grund hierfür ist, dass Anwendungen oft herkömmliche Dateiprotokolle wie NFS oder SMB (Server Message Block) für den Zugriff auf Daten verwenden. Außerdem haben sich native Cloudspeicherdienste auf Objektspeicher mit einem flachen Namespace und umfangreichen Metadaten statt auf Dateisysteme konzentriert, die einen hierarchischen Namespace und effiziente Metadatenvorgänge bereitstellen. 

Blob Storage unterstützt jetzt einen hierarchischen Namespace, und in Kombination mit der Unterstützung des NFS 3.0-Protokolls erleichtert Azure das Ausführen von Legacy-Anwendungen zusätzlich zum großen Cloudobjektspeicher. 

## <a name="applications-and-workloads-suited-for-this-feature"></a>Für dieses Feature geeignete Anwendungen und Workloads

Das NFS 3.0-Protokoll Feature eignet sich am besten für die Verarbeitung von hohem Durchsatz, hohe Skalierbarkeit sowie Workloads mit vielen Lesevorgängen wie Medienverarbeitung, Risikosimulationen und Genomiksequenzierung. Sie sollten die Nutzung dieses Features für jeden anderen Workloadtyp in Betracht ziehen, der mehrere Leser und viele Threads verwendet, die eine hohe Bandbreite erfordern. 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3.0 und der hierarchische Namespace

Die NFS 3.0-Protokollunterstützung erfordert, dass Blobs in einem hierarchischen Namespace organisiert werden. Sie können beim Erstellen eines Speicherkontos einen hierarchischen Namespace aktivieren. Die Möglichkeit zur Verwendung eines hierarchischen Namespaces wurde durch Azure Data Lake Storage Gen2 eingeführt. Er organisiert Objekte (Dateien) genauso in eine Hierarchie von Verzeichnissen und Unterverzeichnissen, wie das Dateisystem auf Ihrem Computer organisiert ist.  Der hierarchische Namespace wird linear skaliert und beeinträchtigt weder die Datenkapazität noch die Leistung. Verschiedene Protokolle werden aus dem hierarchischen Namespace erweitert. Das NFS 3.0-Protokoll ist eines dieser verfügbaren Protokolle.   

> [!div class="mx-imgBorder"]
> ![hierarchischer Namespace](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>Als Blockblobs gespeicherte Daten

Wenn Sie die NFS 3.0-Protokollunterstützung aktivieren, werden alle Daten in Ihrem Speicherkonto als Blockblobs gespeichert. Blockblobs sind optimiert, damit große Mengen von Daten mit vielen Lesevorgängen effizient verarbeitet werden. Blockblobs werden aus Blöcken zusammengesetzt. Jeder Block wird durch eine Block-ID identifiziert. Ein Blockblob kann bis zu 50.000 Blöcke enthalten. Jeder Block in einem Blockblob kann eine andere Größe haben – bis zur maximalen Größe, die für die von Ihrem Konto verwendete Dienstversion zulässig ist.

Wenn Ihre Anwendung eine Anforderung mithilfe des NFS 3.0-Protokolls sendet, wird diese Anforderung in eine Kombination aus Blockblobvorgängen übersetzt. Beispielsweise werden NFS 3.0-Anforderungen zum Lesen von Remoteprozeduraufrufen (Remote Procedure Calls, RPCs) in einen [Get Blob](/rest/api/storageservices/get-blob)-Vorgang (Blob abrufen) übersetzt. NFS 3.0-Anforderungen zum Schreiben von Remoteprozeduraufrufen werden in eine Kombination aus [Get Block List](/rest/api/storageservices/get-block-list) (Blockierungsliste abrufen), [Put Block](/rest/api/storageservices/put-block) (Block festlegen) und [Put Block List](/rest/api/storageservices/put-block-list) (Blockierungsliste festlegen) übersetzt.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Allgemeiner Workflow: Einbinden eines Speicherkontocontainers

Ihre Linux-Clients können einen Container aus einem virtuellen Azure-Computer (VM) oder einem lokalen Computer in Blob Storage einbinden. Zum Einbinden eines Speicherkontocontainers müssen Sie diese Schritte ausführen.

1. Erstellen eines virtuellen Azure-Netzwerks (VNet).

2. Konfigurieren der Netzwerksicherheit.

3. Erstellen und Konfigurieren des Speicherkontos, das nur Datenverkehr aus dem VNet akzeptiert.

4. Erstellen eines Containers im Speicherkonto.

5. Einbinden des Containers.

Eine Schrittanleitung finden Sie unter [Einbinden von Blob-Speicher unter Linux mithilfe des NFS 3.0-Protokolls (Network File System)](network-file-system-protocol-support-how-to.md).

## <a name="network-security"></a>Netzwerksicherheit

Der Datenverkehr muss aus einem VNet stammen. Ein VNet ermöglicht Clients eine sichere Verbindung mit Ihrem Speicherkonto. Die einzige Möglichkeit zum Sichern der Daten in Ihrem Konto besteht darin, ein VNet und andere Netzwerksicherheitseinstellungen zu verwenden. Alle anderen Tools, die zum Sichern von Daten verwendet werden, wie Kontoschlüsselautorisierung, Azure Active Directory (AD) und Zugriffssteuerungslisten (Access Control Lists, ACLs), werden in Konten, für die die Unterstützung des NFS 3.0-Protokolls aktiviert ist, noch nicht unterstützt. 

Weitere Informationen finden Sie unter [Netzwerksicherheitsempfehlungen für Blob-Speicher](security-recommendations.md#networking).

### <a name="supported-network-connections"></a>Unterstützte Netzwerkverbindungen

Ein Client kann über einen öffentlichen oder einen [privaten Endpunkt](../common/storage-private-endpoints.md) sowie von jeder der folgenden Netzwerkadressen aus eine Verbindung herstellen:

- Das VNet, das Sie für Ihr Speicherkonto konfigurieren. 

  In diesem Artikel bezeichnen wir dieses VNet als das *primäre VNet*. Weitere Informationen finden Sie unter [Gewähren des Zugriffs aus einem virtuellen Netzwerk](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Ein Peer-VNet, das sich in derselben Region wie das primäre VNet befindet.

  Sie müssen Ihr Speicherkonto so konfigurieren, dass es den Zugriff auf dieses Peer-VNet zulässt. Weitere Informationen finden Sie unter [Gewähren des Zugriffs aus einem virtuellen Netzwerk](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Ein lokales Netzwerk, das mit Ihrem primären VNet über ein [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) oder ein [ExpressRoute-Gateway](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) verbunden ist. 

  Weitere Informationen finden Sie unter [Konfigurieren des Zugriffs aus lokalen Netzwerken](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Ein lokales Netzwerk, das mit einem Peer-Netzwerk verbunden ist.

  Dies kann mithilfe eines [VPN Gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md) oder eines [ExpressRoute-Gateways](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) zusammen mit [Gatewaytransit](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit) erfolgen. 

> [!IMPORTANT]
> Wenn Sie eine Verbindung aus einem lokalen Netzwerk heraus herstellen, stellen Sie sicher, dass Ihr Client die ausgehende Kommunikation über die Ports 111 und 2048 zulässt. Das NFS 3.0-Protokoll verwendet diese Ports.

<a id="azure-storage-features-not-yet-supported"></a>

## <a name="known-issues-and-limitations"></a>Einschränkungen und bekannte Probleme

Im Artikel [Bekannte Probleme](network-file-system-protocol-known-issues.md) finden Sie eine vollständige Liste der Probleme und Einschränkungen im aktuellen Release der NFS 3.0-Unterstützung.

## <a name="pricing"></a>Preise

Auf der Seite [Azure Blob Storage-Preise](https://azure.microsoft.com/pricing/details/storage/blobs/) finden Sie die Kosten für Datenspeicher und Transaktionen. 

## <a name="see-also"></a>Weitere Informationen

- [Einbinden von Azure Blob Storage mithilfe des NFS 3.0-Protokolls (Network File System)](network-file-system-protocol-support-how-to.md)
- [Überlegungen zur NFS 3.0-Leistung (Network File System) in Azure Blob Storage](network-file-system-protocol-support-performance.md)
- [Vergleichen des Zugriffs auf Azure Files, Blob Storage und Azure NetApp Files mit NFS](../common/nfs-comparison.md)
