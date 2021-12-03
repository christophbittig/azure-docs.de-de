---
title: Azure Files – Überlegungen zum Netzwerkbetrieb | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die Netzwerkoptionen für Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d3f3bb466156b08ae90561d5e02e5b5ddf469755
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518506"
---
# <a name="azure-files-networking-considerations"></a>Azure Files – Überlegungen zum Netzwerkbetrieb 
Sie können auf zwei Arten eine Verbindung mit einer Azure-Dateifreigabe herstellen:

- Direkter Zugriff auf die Freigabe über das SMB-Protokoll (Server Message Block), das NFS-Protokoll (Network File System) oder das REST-Protokoll „File“. Dieses Zugriffsmuster kommt primär dann zum Einsatz, wenn so wenige lokale Server wie möglich einbezogen werden sollen.
- Sie erstellen mithilfe der Azure-Dateisynchronisierung einen Cache der Azure-Dateifreigabe auf einem lokalen Server (oder in einer Azure-VM) und greifen auf dem lokalen Server über ein Protokoll Ihrer Wahl (SMB, NFS, FTPS usw.) auf die gewünschten Daten der Dateifreigabe zu. Dieses Zugriffsmuster ist praktisch, weil es die Vorteile einer lokalen Leistung mit serverlos anfügbaren Diensten im Cloudmaßstab – z. B. Azure Backup – kombiniert.

In diesem Artikel wird beschrieben, wie Sie das Netzwerk konfigurieren, wenn Ihr Anwendungsfall einen direkten Zugriff auf die Azure-Dateifreigabe statt der Nutzung der Azure-Dateisynchronisierung erfordert. Weitere Informationen zu Überlegungen zum Netzwerkbetrieb für eine Bereitstellung mit der Azure-Dateisynchronisierung finden Sie unter [Azure Files – Überlegungen zum Netzwerkbetrieb](../file-sync/file-sync-networking-overview.md).

Die Netzwerkkonfiguration für Azure-Dateifreigaben wird im Azure-Speicherkonto vorgenommen. Ein Speicherkonto ist ein Verwaltungskonstrukt, das einen gemeinsam genutzten Pool mit Speicherplatz darstellt, in dem Sie mehrere Dateifreigaben sowie weitere Speicherressourcen wie Blobcontainer oder Warteschlangen bereitstellen können. Speicherkonten machen verschiedene Einstellungen verfügbar, die zum Schutz des Netzwerkzugriffs auf Ihre Dateifreigaben beitragen. Hierzu zählen Netzwerkendpunkte, Speicherkonto-Firewalleinstellungen sowie Verschlüsselung während der Übertragung. 

> [!Important]  
> Azure Files unterstützt mehrere Netzwerkroutingoptionen. Die Standardoption, das Microsoft-Routing, kann bei allen Azure Files-Konfigurationen verwendet werden. Die Internetroutingoption bietet keine Unterstützung für Szenarien mit AD-Domänenbeitritt oder Azure-Dateisynchronisierung.

Wir empfehlen Ihnen, vor diesem konzeptionellen Leitfaden den Artikel [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md) zu lesen.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Dieses Video ist eine Anleitung und Demo dazu, wie Azure-Dateifreigaben in fünf einfachen Schritte für Information-Worker und Apps auf sichere Weise direkt verfügbar gemacht werden können. In den folgenden Abschnitten finden Sie Links und zusätzlichen Kontext für die Dokumentation, auf die im Video verwiesen wird.
   :::column-end:::
:::row-end:::

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Ja](../media/icons/yes-icon.png) |

## <a name="accessing-your-azure-file-shares"></a>Zugreifen auf Ihre Azure-Dateifreigaben
Auf SMB-Azure-Dateifreigaben kann sofort über den öffentlichen Endpunkt des Speicherkontos mit SMB 3.1.1 und SMB 3.0 zugegriffen werden. Dies bedeutet, dass authentifizierte Anforderungen (etwa Anforderungen, die durch die Anmeldeidentität eines Benutzer autorisiert wurden) auf sichere Weise verwendet werden können – ganz gleich, ob ihr Ursprung innerhalb oder außerhalb von Azure liegt. Auf NFS-Azure-Dateifreigaben ist der Zugriff über den öffentlichen Endpunkt des Speicherkontos nur möglich, wenn der öffentliche Endpunkt auf virtuelle Azure-Netzwerke beschränkt ist.

Bei vielen Umgebungen können Sie auf die Azure-Dateifreigaben zusätzliche Netzwerkkonfigurationen anwenden:

- Im Zusammenhang mit SMB-Dateifreigaben blockieren viele Organisationen und Internetdienstanbieter (Internet Service Providers, ISPs) Port 445, der von SMB für die Kommunikation verwendet wird. Diese Praxis geht auf ältere Sicherheitsempfehlungen im Zusammenhang mit veralteten Versionen des SMB-Protokolls zurück, die nicht internetsicher waren. Auch wenn SMB 3.x ein internetsicheres Protokoll ist, können Organisations- oder ISP-Richtlinien möglicherweise nicht geändert werden. 

- In Bezug auf NFS-Dateifreigaben begrenzt der eingeschränkte Zugriff auf öffentliche Endpunkte nur Bereitstellungen innerhalb von Azure.

- In einigen Organisationen muss Datenverkehr zu Azure einem deterministischen Pfad folgen.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneln von Datenverkehr über ein virtuelles privates Netzwerk oder über ExpressRoute
Wenn Sie einen Netzwerktunnel zwischen Ihrem lokalen Netzwerk und Azure einrichten, entsteht eine Peeringbeziehung zwischen Ihrem lokalen Netzwerk und mindestens einem virtuellen Netzwerk in Azure. Ein [virtuelles Netzwerk](../../virtual-network/virtual-networks-overview.md) (VNET) ähnelt einem herkömmlichen Netzwerk in Ihrer lokalen Umgebung. Ähnlich wie ein Azure-Speicherkonto oder eine Azure-VM ist ein VNET eine Azure-Ressource, die in einer Ressourcengruppe bereitgestellt wird. 

Azure Files unterstützt folgende Mechanismen, um Datenverkehr zwischen Ihren lokalen Arbeitsstationen und Servern und SMB-/NFS-Dateifreigaben in Azure zu tunneln:

- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Ein VPN-Gateway ist eine spezielle Art von Gateway für virtuelle Netzwerke, das verwendet wird, um verschlüsselten Datenverkehr zwischen einem virtuellen Azure-Netzwerk und einem anderen Standort (beispielsweise einer lokalen Umgebung) über das Internet zu senden. Azure VPN Gateway ist eine Azure-Ressource, die neben einem Speicherkonto oder anderen Azure-Ressourcen in einer Ressourcengruppe bereitgestellt werden kann. VPN-Gateways machen zwei Arten von Verbindungen verfügbar:
    - [Point-to-Site-VPN](../../vpn-gateway/point-to-site-about.md)-Gatewayverbindungen (P2S-VPN) sind VPN-Verbindungen zwischen Azure und einem einzelnen Client. Diese Lösung ist primär für Geräte nützlich, die nicht zum lokalen Netzwerk Ihrer Organisation gehören, z. B. im Fall von Telearbeitern, die ihre Azure-Dateifreigabe von Zuhause, im Café oder im Hotel einbinden möchten. Um eine P2S-VPN-Verbindung mit Azure Files zu verwenden, muss für jeden Client, der eine Verbindung herstellen möchte, eine P2S-VPN-Verbindung konfiguriert werden. Informationen zur Vereinfachung der Bereitstellung einer P2S-VPN-Verbindung finden Sie unter [Konfigurieren eines P2S-VPN (Point-to-Site) unter Windows zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-windows.md) bzw. unter [Konfigurieren eines P2S-VPN (Point-to-Site) unter Linux zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Site-to-Site-VPN](../../vpn-gateway/design.md#s2smulti)-Verbindungen (S2S-VPN) sind VPN-Verbindungen zwischen Azure und dem Netzwerk Ihrer Organisation. Mit dieser Lösung können Sie einmalig eine VPN-Verbindung für einen VPN-Server oder ein im Netzwerk Ihrer Organisation gehostetes Gerät konfigurieren und müssen diese Einrichtung nicht für jedes Clientgerät wiederholen, das auf Ihre Azure-Dateifreigabe zugreifen muss. Informationen zur Vereinfachung der Bereitstellung einer S2S-VPN-Verbindung finden Sie unter [Konfigurieren eines Site-to-Site-VPN zur Verwendung mit Azure Files](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md) ermöglicht die Erstellung einer definierten Route zwischen Azure und Ihrem lokalen Netzwerk, die nicht über das Internet läuft. Da ExpressRoute einen dedizierten Pfad zwischen Ihrem lokalen Rechenzentrum und Azure bereitstellt, ist dieser Dienst sehr nützlich, wenn die Netzwerkleistung ein wichtiger Aspekt ist. ExpressRoute ist auch dann eine gute Option, wenn die Richtlinie Ihrer Organisation oder gesetzliche Vorschriften einen deterministischen Pfad zu den Ressourcen in der Cloud erfordern.

Unabhängig von der Tunnelingmethode, die Sie für den Zugriff auf Ihre Azure-Dateifreigaben verwenden, benötigen Sie einen Mechanismus, der sicherstellt, dass der für Ihr Speicherkonto bestimmte Datenverkehr über den Tunnel und nicht über Ihre reguläre Internetverbindung geleitet wird. Es ist zwar technisch möglich, Datenverkehr an den öffentlichen Endpunkt des Speicherkontos weiterzuleiten, dies erfordert jedoch die Hartcodierung aller IP-Adressen für die Azure-Speichercluster in einer Region, da Speicherkonten jederzeit zwischen Speicherclustern verschoben werden können. Darüber hinaus müssen in diesem Fall ständig die IP-Adresszuordnungen aktualisiert werden, da immer wieder neue Cluster hinzukommen.

Wir empfehlen daher die Verwendung privater Endpunkte, anstatt die IP-Adressen Ihrer Speicherkonten in VPN-Routingregeln hartzucodieren. Durch die privaten Endpunkte erhält Ihr Speicherkonto eine IP-Adresse aus dem Adressraum eines virtuellen Azure-Netzwerks. Da durch die Einrichtung eines Tunnels mit Azure eine Peeringbeziehung zwischen Ihrem lokalen Netzwerk und mindestens einem virtuellen Netzwerk entsteht, sorgt diese Methode dauerhaft für eine korrekte Weiterleitung.

### <a name="private-endpoints"></a>Private Endpunkte
Neben dem standardmäßigen öffentlichen Endpunkt für ein Speicherkonto ermöglicht Azure Files die Verwendung privater Endpunkte. Ein privater Endpunkt ist ein Endpunkt, auf den nur innerhalb eines virtuellen Azure-Netzwerks zugegriffen werden kann. Wenn Sie einen privaten Endpunkt für Ihr Speicherkonto erstellen, erhält Ihr Speicherkonto eine private IP-Adresse aus dem Adressraum Ihres virtuellen Netzwerks. Dies ist vergleichbar mit einem lokalen Dateiserver oder NAS-Gerät, der bzw. das eine IP-Adresse aus dem dedizierten Adressraum Ihres lokalen Netzwerks erhält. 

Ein privater Endpunkt ist einem bestimmten Subnetz des virtuellen Azure-Netzwerks zugeordnet. Ein Speicherkonto kann über private Endpunkte in mehreren virtuellen Netzwerken verfügen.

Die Verwendung privater Endpunkte mit Azure Files ermöglicht Folgendes:
- Herstellen einer sicheren Verbindung mit Ihren Azure-Dateifreigaben aus lokalen Netzwerken über eine VPN- oder ExpressRoute-Verbindung mit privatem Peering
- Schützen Ihrer Azure-Dateifreigaben, indem Sie die Speicherkontofirewall so konfigurieren, dass alle Verbindungen am öffentlichen Endpunkt blockiert werden. Durch die Erstellung eines privaten Endpunkts werden Verbindungen mit dem öffentlichen Endpunkt nicht standardmäßig blockiert.
- Erhöhen der Sicherheit für das virtuelle Netzwerk durch die Möglichkeit zum Blockieren der Exfiltration von Daten aus dem virtuellen Netzwerk (und Peeringgrenzen)

Informationen zum Erstellen eines privaten Endpunkts finden Sie unter [Konfigurieren von privaten Endpunkten für Azure Files](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Private Endpunkte und DNS
Wenn Sie einen privaten Endpunkt erstellen, wird standardmäßig auch eine private DNS-Zone erstellt, die der Unterdomäne `privatelink` entspricht, oder es wird eine vorhandene private DNS-Zone entsprechend aktualisiert. Streng genommen muss keine private DNS-Zone erstellt werden, um einen privaten Endpunkt für Ihr Speicherkonto zu verwenden. Es wird jedoch im Allgemeinen dringend empfohlen und ist explizit erforderlich, wenn Sie Ihre Azure-Dateifreigabe mit einem Active Directory Benutzerprinzipal einbinden oder über die FileREST-API zugreifen.

> [!Note]  
> In diesem Artikel wird das Speicherkonto-DNS-Suffix für die öffentlichen Azure-Regionen (`core.windows.net`) verwendet. Dieser Kommentar gilt auch für Azure Sovereign Clouds wie etwa die Azure US Government-Cloud und die Azure China-Cloud. Verwenden Sie einfach die entsprechenden Suffixe für Ihre Umgebung. 

In Ihrer privaten DNS-Zone werden ein A-Eintrag für `storageaccount.privatelink.file.core.windows.net` und ein CNAME-Eintrag für den regulären Namen des Speicherkontos im Format `storageaccount.file.core.windows.net` erstellt. Da Ihre private Azure-DNS-Zone mit dem virtuellen Netzwerk verbunden ist, das den privaten Endpunkt enthält, können Sie sich die DNS-Konfiguration ansehen, indem Sie das Cmdlet `Resolve-DnsName` über PowerShell auf einem virtuellen Azure-Computer aufrufen (oder `nslookup` unter Windows und Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

In diesem Beispiel wird das Speicherkonto `storageaccount.file.core.windows.net` in die private IP-Adresse des privaten Endpunkts (`192.168.0.4`) aufgelöst.

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


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Wenn Sie den gleichen Befehl lokal ausführen, sehen Sie, dass der gleiche Speicherkontoname stattdessen in die öffentliche IP-Adresse des Speicherkontos aufgelöst wird. `storageaccount.file.core.windows.net` ist ein CNAME-Eintrag für `storageaccount.privatelink.file.core.windows.net`, was wiederum ein CNAME-Eintrag für den Azure-Speichercluster ist, von dem das Speicherkonto gehostet wird:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Dies zeigt, dass das Speicherkonto sowohl den öffentlichen Endpunkt als auch einen oder mehrere private Endpunkte verfügbar machen kann. Um sicherzustellen, dass der Speicherkontoname in die private IP-Adresse des privaten Endpunkts aufgelöst wird, müssen Sie die Konfiguration auf Ihren lokalen DNS-Servern ändern. Hierzu gibt es verschiedene Möglichkeiten:

- Ändern der Datei „hosts“ auf Ihren Clients, damit `storageaccount.file.core.windows.net` in die private IP-Adresse des gewünschten privaten Endpunkts aufgelöst wird: Hiervon wird in Produktionsumgebungen dringend abgeraten, da Sie diese Änderungen für jeden Client vornehmen müssen, von dem Ihre Azure-Dateifreigaben eingebunden werden sollen, und Änderungen am Speicherkonto oder am privaten Endpunkt nicht automatisch behandelt werden.
- Erstellen eines A-Eintrags für `storageaccount.file.core.windows.net` auf Ihren lokalen DNS-Servern: Diese Lösung hat den Vorteil, dass Clients in Ihrer lokalen Umgebung das Speicherkonto automatisch auflösen können, ohne dass jeder Client einzeln konfiguriert werden muss. Sie ist jedoch ähnlich fehleranfällig wie das Ändern der Hostdatei, da Änderungen nicht berücksichtigt werden. Für einige Umgebungen ist diese Lösung trotz ihrer Fehleranfälligkeit die beste Wahl.
- Weiterleiten der Zone `core.windows.net` von Ihren lokalen DNS-Servern an Ihre private Azure-DNS-Zone: Der private Azure-DNS-Host ist über eine spezielle IP-Adresse (`168.63.129.16`) erreichbar, auf die nur innerhalb von virtuellen Netzwerken zugegriffen werden kann, die mit der privaten Azure-DNS-Zone verknüpft sind. Um diese Einschränkung zu umgehen, können Sie zusätzliche DNS-Server in Ihrem virtuellen Netzwerk ausführen, die `core.windows.net` an die private Azure-DNS-Zone weiterleiten. Zur Vereinfachung dieser Einrichtung stehen PowerShell-Cmdlets zur Verfügung, die automatisch DNS-Server in Ihrem virtuellen Azure-Netzwerk bereitstellen und wie gewünscht konfigurieren. Informationen zum Einrichten der DNS-Weiterleitung finden Sie unter [Konfigurieren von DNS mit Azure Files](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Speicherkonto-Firewalleinstellungen
Eine Firewall ist eine Netzwerkrichtlinie, die steuert, von welchen Anforderungen auf den öffentlichen Endpunkt für ein Speicherkonto zugegriffen werden darf. Mithilfe der Speicherkontofirewall können Sie den Zugriff auf den öffentlichen Endpunkt des Speicherkontos auf bestimmte IP-Adressen oder -Bereiche oder auf ein virtuelles Netzwerk beschränken. Im Allgemeinen beschränken die meisten Firewallrichtlinien für Speicherkonten den Netzwerkzugriff auf ein virtuelles Netzwerk (oder auf mehrere). 

Der Speicherkontozugriff kann auf zwei Arten auf ein virtuelles Netzwerk beschränkt werden:
- Erstellen eines oder mehrerer privater Endpunkte für das Speicherkonto und Beschränken des gesamten Zugriffs auf den öffentlichen Endpunkt: Dadurch wird sichergestellt, dass nur von Datenverkehr aus den gewünschten virtuellen Netzwerken auf die Azure-Dateifreigaben im Speicherkonto zugegriffen werden kann.
- Beschränken des öffentlichen Endpunkts auf ein einzelnes virtuelles Netzwerk (oder auf mehrere): Hierzu werden sogenannte *Dienstendpunkte* des virtuellen Netzwerks verwendet. Wenn Sie den Datenverkehr für ein Speicherkonto über einen Dienstendpunkt beschränken, erfolgt der Zugriff auf das Speicherkonto weiterhin über die öffentliche IP-Adresse.

> [!NOTE]
> NFS-Dateifreigaben können nur über virtuelle Netzwerke auf den öffentlichen Endpunkt des Speicherkontos zugreifen. Sie können frei auf die privaten Endpunkte des Speicherkontos zugreifen.

Weitere Informationen zum Konfigurieren der Speicherkontofirewall finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Weitere Informationen
- [Azure Files: Übersicht](storage-files-introduction.md)
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
