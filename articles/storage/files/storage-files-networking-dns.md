---
title: Konfigurieren der DNS-Weiterleitung für Azure Files | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die DNS-Weiterleitung für Azure Files konfigurieren.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8923b578f22f15dbf84b6e2ca33dfe14df38d4b
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687135"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Konfigurieren der DNS-Weiterleitung für Azure Files
Mit Azure Files können Sie private Endpunkte für die Speicherkonten erstellen, die Ihre Dateifreigaben enthalten. Private Endpunkte können für viele unterschiedliche Anwendungsarten genutzt werden. Sie sind aber besonders nützlich, um aus Ihrem lokalen Netzwerk eine Verbindung mit Ihren Azure-Dateifreigaben herzustellen, indem eine VPN- oder ExpressRoute-Verbindung mit privatem Peering verwendet wird. 

Damit die Verbindungen mit Ihrem Speicherkonto durch Ihren Netzwerktunnel verlaufen, muss der vollqualifizierte Domänenname (FQDN) Ihres Speicherkontos in die private IP-Adresse Ihres privaten Endpunkts aufgelöst werden. Hierfür müssen Sie das Speicherendpunkt-Suffix (`core.windows.net` für Regionen der öffentlichen Cloud) an den privaten DNS-Dienst von Azure weiterleiten, der aus Ihrem virtuellen Netzwerk zugänglich ist. In diesem Leitfaden wird veranschaulicht, wie Sie die DNS-Weiterleitung so einrichten und konfigurieren, dass die richtige Auflösung in die IP-Adresse des privaten Endpunkts für Ihr Speicherkonto durchgeführt wird.

Wir empfehlen Ihnen dringend, die Anleitungen [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md) und [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md) zu lesen, bevor Sie die in diesem Artikel beschriebenen Schritte ausführen.

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |

## <a name="overview"></a>Übersicht
Azure Files verfügt über zwei Arten von Endpunkten für den Zugriff auf Azure-Dateifreigaben: 
- Öffentliche Endpunkte mit einer öffentlichen IP-Adresse, auf die von jedem Ort der Welt aus zugegriffen werden kann.
- Private Endpunkte, die in einem virtuellen Netzwerk vorhanden sind und eine private IP-Adresse im Adressraum des virtuellen Netzwerks aufweisen.

Öffentliche und private Endpunkte befinden sich unter dem Azure-Speicherkonto. Ein Speicherkonto ist ein Verwaltungskonstrukt, das einen gemeinsam genutzten Pool mit Speicherplatz darstellt, in dem Sie mehrere Dateifreigaben sowie weitere Speicherressourcen wie Blobcontainer oder Warteschlangen bereitstellen können.

Jedes Speicherkonto verfügt über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN). Für die Regionen der öffentlichen Cloud basiert dieser FQDN auf dem Muster `storageaccount.file.core.windows.net`, wobei `storageaccount` für den Namen des Speicherkontos steht. Wenn Sie für diesen Namen Anforderungen senden, z. B. zum Bereitstellen der Freigabe auf Ihrer Arbeitsstation, wird von Ihrem Betriebssystem eine DNS-Suche durchgeführt, um den vollqualifizierten Domänennamen in eine IP-Adresse aufzulösen.

Standardmäßig wird `storageaccount.file.core.windows.net` in die IP-Adresse des öffentlichen Endpunkts aufgelöst. Der öffentliche Endpunkt für ein Speicherkonto wird in einem Azure Storage-Cluster gehostet, in dem viele andere öffentliche Endpunkte der Speicherkonten gehostet werden. Beim Erstellen eines privaten Endpunkts wird eine private DNS-Zone mit dem virtuellen Netzwerk verknüpft, dem der Endpunkt hinzugefügt wurde. Über einen CNAME-Eintrag wird `storageaccount.file.core.windows.net` einem A-Eintrag für die private IP-Adresse des privaten Endpunkts Ihres Speicherkontos zugeordnet. Auf diese Weise können Sie den FQDN `storageaccount.file.core.windows.net` im virtuellen Netzwerk verwenden und erreichen, dass er in die IP-Adresse des privaten Endpunkts aufgelöst wird.

Das übergeordnete Ziel besteht darin, aus der lokalen Umgebung auf die Azure-Dateifreigaben, die unter dem Speicherkonto gehostet werden, über einen Netzwerktunnel zuzugreifen, z. B. eine VPN- oder ExpressRoute-Verbindung. Daher müssen Sie Ihre lokalen DNS-Server so konfigurieren, dass Anforderungen für den Azure Files-Dienst an den privaten DNS-Dienst von Azure weitergeleitet werden. Hierfür müssen Sie die *bedingte Weiterleitung* von `*.core.windows.net` (bzw. das entsprechende Speicherendpunkt-Suffix für die nationalen Clouds „US Government“, „Deutschland“ oder „China“) an den DNS-Server einrichten, der in Ihrem virtuellen Azure-Netzwerk gehostet wird. Dieser DNS-Server leitet die Anforderung dann rekursiv an den privaten DNS-Dienst von Azure weiter, der den vollqualifizierten Domänennamen des Speicherkontos an die richtige private IP-Adresse weiterleitet.

Für die Konfiguration der DNS-Weiterleitung für Azure Files wird ein virtueller Computer zum Hosten eines DNS-Servers benötigt, damit die Anforderungen weitergeleitet werden können. Dies ist aber ein einmaliger Schritt für alle Azure-Dateifreigaben, die in Ihrem virtuellen Netzwerk gehostet werden. Dies ist darüber hinaus keine Anforderung, die nur für Azure Files gilt. Für alle Azure-Dienste mit Unterstützung von privaten Endpunkten, auf die Sie aus der lokalen Umgebung zugreifen möchten, kann die in diesem Leitfaden konfigurierte DNS-Weiterleitung genutzt werden: Azure Blob Storage, SQL Azure, Cosmos DB usw. 

In diesem Leitfaden werden die Schritte zum Konfigurieren der DNS-Weiterleitung für den Azure Storage-Endpunkt beschrieben. Anforderungen zur DNS-Namensauflösung werden also nicht nur für Azure Files weitergeleitet, sondern auch für alle anderen Azure-Speicherdienste (Azure-Blobspeicher, -Tabellenspeicher, -Warteschlangenspeicher usw.) erfolgt die Weiterleitung an den privaten DNS-Dienst von Azure. Bei Bedarf können zudem weitere Endpunkte für andere Azure-Dienste hinzugefügt werden. Darüber hinaus wird auch die DNS-Weiterleitung an Ihre lokalen DNS-Server so konfiguriert, dass Cloudressourcen in Ihrem virtuellen Netzwerk (z. B. ein DFS-N-Server) die Namen lokaler Computer auflösen können. 

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie die DNS-Weiterleitung zu Azure Files einrichten können, müssen Sie die folgenden Schritte ausführen:

- Ein Speicherkonto mit einer Azure-Dateifreigabe, die Sie einbinden möchten. Informationen zum Erstellen eines Speicherkontos und einer Azure-Dateifreigabe finden Sie unter [Erstellen einer Azure-Dateifreigabe](storage-how-to-create-file-share.md).
- Ein privater Endpunkt für das Speicherkonto. Informationen zum Erstellen eines privaten Endpunkts für Azure Files finden Sie unter [Erstellen eines privaten Endpunkts](storage-files-networking-endpoints.md#create-a-private-endpoint).
- Die [aktuelle Version](/powershell/azure/install-az-ps) des Azure PowerShell-Moduls.

> [!Important]  
> In diesem Leitfaden wird davon ausgegangen, dass Sie den DNS-Server unter Windows Server in Ihrer lokalen Umgebung verwenden. Alle Schritte, die in diesem Leitfaden beschrieben werden, gelten nicht nur für den DNS-Server von Windows, sondern für alle DNS-Server.

## <a name="configuring-dns-forwarding"></a>Konfigurieren der DNS-Weiterleitung
Falls Sie in Ihrem virtuellen Azure-Netzwerk bereits DNS-Server eingerichtet haben oder einfach basierend auf der Methodik Ihrer Organisation eigene virtuelle Computer als DNS-Server bereitstellen möchten, können Sie das DNS mit den integrierten PowerShell-Cmdlets für DNS-Server konfigurieren.

Erstellen Sie auf Ihren lokalen DNS-Servern mit `Add-DnsServerConditionalForwarderZone` eine bedingte Weiterleitung. Diese bedingte Weiterleitung muss bei Ihnen auf allen lokalen DNS-Servern bereitgestellt werden, damit die Weiterleitung von Datenverkehr an Azure korrekt durchgeführt werden kann. Denken Sie daran, `<azure-dns-server-ip>` durch die entsprechenden IP-Adressen für Ihre Umgebung zu ersetzen.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Auf den DNS-Servern in Ihrem virtuellen Azure-Netzwerk müssen Sie ebenfalls eine Weiterleitung einrichten, damit Anforderungen für die DNS-Zone des Speicherkontos an den privaten DNS-Dienst von Azure geleitet werden, der über die reservierte IP-Adresse `168.63.129.16` verfügt. (Denken Sie an die Angaben unter `$storageAccountEndpoint`, falls Sie die Befehle in einer anderen PowerShell-Sitzung ausführen.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="confirm-dns-forwarders"></a>Bestätigen von DNS-Weiterleitungen
Bevor Sie testen, ob die Anwendung der DNS-Weiterleitungen erfolgreich war, empfehlen wir Ihnen, den DNS-Cache auf Ihrer lokalen Arbeitsstation mit `Clear-DnsClientCache` zu leeren. Verwenden Sie `Resolve-DnsName` oder `nslookup`, um zu testen, ob Sie den vollqualifizierten Domänennamen Ihres Speicherkontos richtig auflösen können.

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud you're deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Wenn die Namensauflösung erfolgreich ist, sollte die aufgelöste IP-Adresse mit der IP-Adresse Ihres Speicherkontos übereinstimmen.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Wenn Sie eine SMB-Dateifreigabe einbinden, können Sie auch den folgenden `Test-NetConnection`-Befehl verwenden, um zu überprüfen, ob eine TCP-Verbindung mit Ihrem Speicherkonto erfolgreich hergestellt werden kann.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Weitere Informationen
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md)
- [Konfigurieren von Azure Files-Netzwerkendpunkten](storage-files-networking-endpoints.md)
