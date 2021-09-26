---
title: Verwenden privater Endpunkte
titleSuffix: Azure Storage
description: Übersicht über private Endpunkte für den sicheren Zugriff auf Speicherkonten über virtuelle Netzwerke.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: a52460db452d519c51fb7a1b191766b21da67f88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592278"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Verwenden privater Endpunkte für Azure Storage

Sie können [private Endpunkte](../../private-link/private-endpoint-overview.md) für Ihre Azure Storage-Konten verwenden, um Clients in einem virtuellen Netzwerk (VNET) den sicheren Zugriff auf Daten über [Private Link](../../private-link/private-link-overview.md) zu ermöglichen. Der private Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihren Speicherkontodienst. Der Netzwerkdatenverkehr zwischen den Clients im VNET und dem Speicherkonto wird über das VNET und eine private Verbindung im Microsoft-Backbone-Netzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt.

Die Verwendung privater Endpunkte für Ihr Speicherkonto bietet Ihnen folgende Möglichkeiten:

- Schützen Ihres Speicherkontos, indem Sie die Speicherfirewall so konfigurieren, dass alle Verbindungen am öffentlichen Endpunkt für den Speicherdienst blockiert werden.
- Erhöhen der Sicherheit für das virtuelle Netzwerk (VNET), indem Sie die Exfiltration von Daten aus dem VNET blockieren.
- Sicheres Verbinden mit Speicherkonten aus lokalen Netzwerken, die eine Verbindung mit dem VNET über [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoutes](../../expressroute/expressroute-locations.md) mit privatem Peering herstellen.

## <a name="conceptual-overview"></a>Konzeptionelle Übersicht

![Übersicht über private Endpunkte für Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Ein privater Endpunkt ist eine spezielle Netzwerkschnittstelle für einen Azure-Dienst in Ihrem [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) (VNET). Wenn Sie einen privaten Endpunkt für Ihr Speicherkonto erstellen, wird eine sichere Verbindung zwischen Clients in Ihrem VNET und Ihrem Speicher bereitgestellt. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres VNET zugewiesen. Für die Verbindung zwischen dem privaten Endpunkt und dem Speicherdienst wird eine sichere private Verbindung verwendet.

Anwendungen im VNET können eine nahtlose Verbindung mit dem Speicherdienst über den privaten Endpunkt herstellen, und zwar **mit denselben Verbindungszeichenfolgen und Autorisierungsmechanismen, die auch sonst verwendet würden**. Private Endpunkte können mit allen vom Speicherkonto unterstützten Protokollen verwendet werden, einschließlich REST und SMB.

Private Endpunkte können in Subnetzen erstellt werden, die [Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md) verwenden. Clients in einem Subnetz können somit eine Verbindung mit einem Speicherkonto über einen privaten Endpunkt herstellen, während für den Zugriff auf andere Konten Dienstendpunkte verwendet werden.

Wenn Sie einen privaten Endpunkt für einen Speicherdienst in Ihrem VNET erstellen, wird an den Speicherkontobesitzer eine Einwilligungsanforderung zur Genehmigung gesendet. Wenn der Benutzer, der die Erstellung des privaten Endpunkts anfordert, auch ein Besitzer des Speicherkontos ist, wird diese Einwilligungsanforderung automatisch genehmigt.

Speicherkontobesitzer können Einwilligungsanforderungen und die privaten Endpunkte im [Azure-Portal](https://portal.azure.com) über die Registerkarte *Private Endpunkte* für das Speicherkonto verwalten.

> [!TIP]
> Wenn der Zugriff auf Ihr Speicherkonto nur über den privaten Endpunkt erfolgen soll, konfigurieren Sie die Speicherfirewall so, dass der Zugriff über den öffentlichen Endpunkt verweigert oder gesteuert wird.

Sie können Ihr Speicherkonto schützen, indem nur Verbindungen über das VNET akzeptiert werden. Dazu [konfigurieren Sie die Speicherfirewall](storage-network-security.md#change-the-default-network-access-rule) so, dass der Zugriff über den öffentlichen Endpunkt standardmäßig verweigert wird. Sie brauchen keine Firewallregel, um Datenverkehr aus einem VNET mit einem privaten Endpunkt zuzulassen, da die Speicherfirewall nur den Zugriff über den öffentlichen Endpunkt steuert. Private Endpunkte verwenden stattdessen den Einwilligungsflow, um Subnetzen den Zugriff auf den Speicherdienst zu gewähren.

> [!NOTE]
> Beim Kopieren von Blobs zwischen Speicherkonten benötigt der Client Netzwerkzugriff auf beide Konten. Wenn Sie also einen privaten Link für ein einziges Konto (Quelle oder Ziel) verwenden möchten, stellen Sie sicher, dass der Client über Netzwerkzugriff auf das andere Konto verfügt. Weitere Informationen zu anderen Möglichkeiten zum Konfigurieren des Netzwerkzugriffs finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](storage-network-security.md?toc=/azure/storage/blobs/toc.json).

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

Informationen zum Erstellen eines privaten Endpunkts über das Azure-Portal finden Sie [unter Privat mit einem Speicherkonto verbinden über die Speicherkonto-Erfahrung im Azure-Portal](../../private-link/tutorial-private-endpoint-storage-portal.md).

Wie Sie einen privaten Endpunkt mithilfe von PowerShell oder der Azure-CLI erstellen, erfahren Sie in einem dieser Artikel. Bei beiden ist eine Azure-Web-App der Zieldienst, die Schritte zum Erstellen einer privaten Verknüpfung sind jedoch die gleichen für ein Azure-Storage-Konto.

- [Erstellen eines privaten Endpunkts mit Azure CLI](../../private-link/create-private-endpoint-cli.md)

- [Erstellen eines privaten Endpunkts mit Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

Wenn Sie einen privaten Endpunkt erstellen, müssen Sie das Speicherkonto und den Speicherdienst angeben, mit dem eine Verbindung hergestellt wird.

Sie benötigen einen separaten privaten Endpunkt für jede Speicherressource, auf die Sie zugreifen müssen. Dies sind [Blobs](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [Dateien](../files/storage-files-introduction.md), [Warteschlangen](../queues/storage-queues-introduction.md), [Tabellen](../tables/table-storage-overview.md) oder [statische Websites](../blobs/storage-blob-static-website.md). Für den privaten Endpunkt sind diese Speicherdienste als **untergeordnete Zielressourcen** des zugeordneten Speicherkontos definiert.

Wenn Sie einen privaten Endpunkt für die Speicherressource „Data Lake Storage Gen2“ erstellen, sollten Sie auch einen für die Ressource „Blob Storage“ erstellen. Der Grund: Vorgänge, deren Ziel der Endpunkt „Data Lake Storage Gen2“ ist, werden möglicherweise an den Endpunkt „Blob“ umgeleitet. Durch das Erstellen eines privaten Endpunkts für beide Ressourcen sorgen Sie dafür, dass Vorgänge erfolgreich abgeschlossen werden können.

> [!TIP]
> Erstellen Sie einen separaten privaten Endpunkt für die sekundäre Instanz des Speicherdiensts, um die Leseleistung für RA-GRS-Konten zu verbessern.
> Erstellen Sie unbedingt ein Speicherkonto (Standard oder Premium) vom Typ „Universell v2 (GPv2)“.

Für Lesezugriff auf die sekundäre Region mit einem Speicherkonto, das für georedundanten Speicher konfiguriert ist, benötigen Sie separate private Endpunkte sowohl für die primäre als auch die sekundäre Instanz des Diensts. Sie müssen für ein **Failover** keinen privaten Endpunkt für die sekundäre Instanz erstellen. Der private Endpunkt stellt nach einem Failover automatisch eine Verbindung mit der neuen primären Instanz her. Weitere Informationen zu den Speicherredundanzoptionen finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>Herstellen einer Verbindung mit einem privaten Endpunkt

Clients in einem VNET, die den privaten Endpunkt verwenden, sollten dieselbe Verbindungszeichenfolge für das Speicherkonto verwenden wie Clients, die eine Verbindung mit dem öffentlichen Endpunkt herstellen. Das automatische Weiterleiten der Verbindungen vom VNET zum Speicherkonto über eine private Verbindung basiert auf der DNS-Auflösung.

> [!IMPORTANT]
> Verwenden Sie zum Herstellen einer Verbindung mit dem Speicherkonto mithilfe privater Endpunkte dieselbe Verbindungszeichenfolge, die Sie auch sonst verwenden würden. Stellen Sie keine Verbindung mit dem Speicherkonto über die URL der Unterdomäne `privatelink` her.

Wir erstellen standardmäßig eine [private DNS-Zone](../../dns/private-dns-overview.md), die an das VNET angehängt ist, mit den erforderlichen Updates für die privaten Endpunkte. Wenn Sie jedoch einen eigenen DNS-Server verwenden, müssen Sie möglicherweise zusätzliche Änderungen an Ihrer DNS-Konfiguration vornehmen. Im folgenden Abschnitt zu [DNS-Änderungen](#dns-changes-for-private-endpoints) werden die für private Endpunkte erforderlichen Updates beschrieben.

## <a name="dns-changes-for-private-endpoints"></a>DNS-Änderungen für private Endpunkte

Wenn Sie einen privaten Endpunkt erstellen, wird der DNS-CNAME-Ressourceneintrag für das Speicherkonto auf einen Alias in einer Unterdomäne mit dem Präfix `privatelink` aktualisiert. Standardmäßig erstellen wir außerdem eine [private DNS-Zone](../../dns/private-dns-overview.md), die der Unterdomäne `privatelink` entspricht, mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte.

Wenn Sie die Speicherendpunkt-URL von außerhalb des VNET mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt des Speicherdiensts aufgelöst. Bei Auflösung aus dem VNET, das den privaten Endpunkt hostet, wird die Speicherendpunkt-URL in die IP-Adresse des privaten Endpunkts aufgelöst.

Beim oben gezeigten Beispiel lauten die DNS-Ressourceneinträge für das Speicherkonto „StorageAccountA“ bei Auflösung von außerhalb des VNET, das den privaten Endpunkt hostet, wie folgt:

| Name                                                  | type  | Wert                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| `StorageAccountA.blob.core.windows.net`             | CNAME | `StorageAccountA.privatelink.blob.core.windows.net` |
| `StorageAccountA.privatelink.blob.core.windows.net` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | Ein     | \<storage service public IP address\>                 |

Wie bereits erwähnt, können Sie den Zugriff für Clients außerhalb des VNET über den öffentlichen Endpunkt mithilfe der Speicherfirewall verweigern oder steuern.

Die DNS-Ressourceneinträge für „StorageAccountA“ lauten nach dem Auflösen durch einen Client im VNET, das den privaten Endpunkt hostet, wie folgt:

| Name  | type | Wert |
| :--- | :---: | :--- |
| `StorageAccountA.blob.core.windows.net` | CNAME | `StorageAccountA.privatelink.blob.core.windows.net` |
| `StorageAccountA.privatelink.blob.core.windows.net` | Ein | `10.1.1.5` |

Diese Vorgehensweise ermöglicht den Zugriff auf das Speicherkonto **mithilfe derselben Verbindungszeichenfolge** für Clients in dem VNET, das die privaten Endpunkte hostet, als auch Clients außerhalb des VNET.

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den FQDN für den Speicherkontoendpunkt in die IP-Adresse des privaten Endpunkts aufzulösen. Sie sollten den DNS-Server so konfigurieren, dass die Unterdomäne der privaten Verbindung an die private DNS-Zone für das VNET delegiert wird, oder konfigurieren Sie die A-Einträge für `StorageAccountA.privatelink.blob.core.windows.net` mit der IP-Adresse des privaten Endpunkts.

> [!TIP]
> Wenn Sie einen benutzerdefinierten oder lokalen DNS-Server verwenden, sollten Sie ihn so konfigurieren, dass der Speicherkontoname in der Unterdomäne `privatelink` in die IP-Adresse des privaten Endpunkts aufgelöst wird. Hierzu können Sie die Unterdomäne `privatelink` an die private DNS-Zone des VNET delegieren oder die DNS-Zone auf dem DNS-Server konfigurieren und die DNS-A-Einträge hinzufügen.

Die empfohlenen DNS-Zonennamen der privaten Endpunkte für die Speicherdienste und die verknüpften untergeordneten Zielressourcen der Endpunkte lauten wie folgt:

| Speicherdienst        | Zielunterressource | Zonenname                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Blob-Dienst           | Blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | DFS                 | `privatelink.dfs.core.windows.net`   |
| Dateidienst           | file                | `privatelink.file.core.windows.net`  |
| Warteschlangendienst          | queue               | `privatelink.queue.core.windows.net` |
| Tabellenspeicherdienst          | table               | `privatelink.table.core.windows.net` |
| Statische Websites        | Web-                 | `privatelink.web.core.windows.net`   |

Weitere Informationen zum Konfigurieren des eigenen DNS-Servers für die Unterstützung privater Endpunkte finden Sie in den folgenden Artikeln:

- [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-Konfiguration für private Endpunkte](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Preise

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Bekannte Probleme

Beachten Sie die folgenden bekannten Probleme im Zusammenhang mit privaten Endpunkten für Azure Storage:

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Einschränkungen beim Speicherzugriff für Clients in VNETs mit privaten Endpunkten

Für Clients in VNETs mit vorhandenen privaten Endpunkten bestehen Einschränkungen beim Zugriff auf andere Speicherkonten mit privaten Endpunkten. Nehmen wir zum Beispiel an, ein VNet N1 verfügt über einen privaten Endpunkt für ein Speicherkonto A1 für die Blob-Speicherung. Wenn nun das Speicherkonto A2 über einen privaten Endpunkt im VNET N2 für Blobspeicher verfügt, müssen Clients im VNET N1 auch auf Blobspeicher im Konto A2 über einen privaten Endpunkt zugreifen. Hat das Speicherkonto A2 keine privaten Endpunkte für Blobspeicher, können Clients im VNET N1 ohne privaten Endpunkt auf Blobspeicher in diesem Konto zugreifen.

Diese Einschränkung ist die Folge der DNS-Änderungen, die vorgenommen werden, wenn Konto A2 einen privaten Endpunkt erstellt.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Netzwerksicherheitsgruppen-Regeln für Subnetze mit privaten Endpunkten

Derzeit können keine [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md)-Regeln und benutzerdefinierten Routen für private Endpunkte konfiguriert werden. Netzwerksicherheitsgruppen-Regeln, die auf das Subnetz angewendet werden, das den privaten Endpunkt hostet, werden nicht auf den privaten Endpunkt angewendet. Sie werden nur auf andere Endpunkte angewendet (z. B. Netzwerkschnittstellencontroller). Eine eingeschränkte Umgehung dieses Problems ist das Implementieren Ihrer Zugriffsregeln für private Endpunkte in den Quellsubnetzen, obwohl dieser Ansatz möglicherweise einen höheren Verwaltungsaufwand erfordert.

### <a name="copying-blobs-between-storage-accounts"></a>Kopieren von Blobs zwischen Speicherkonten

Sie können Blobs zwischen Speicherkonten über private Endpunkte nur kopieren, wenn Sie die Azure-REST-API oder Tools verwenden, bei denen die REST-API verwendet wird. Zu diesen Tools gehören AzCopy, Storage-Explorer, Azure PowerShell, die Azure-Befehlszeilenschnittstelle und die Azure Blob Storage-SDKs.

Nur private Endpunkte mit der Blob Storage-Ressource als Ziel werden unterstützt. Private Endpunkte mit Data Lake Storage Gen2 oder der Dateiressource als Ziel werden noch nicht unterstützt. Außerdem wird das Kopieren zwischen Speicherkonten über das NFS-Protokoll (Network File System) noch nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](storage-network-security.md)
- [Sicherheitsempfehlungen für Blob Storage](../blobs/security-recommendations.md)
