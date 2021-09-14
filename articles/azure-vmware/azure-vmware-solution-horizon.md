---
title: Bereitstellen von Horizon in Azure VMware Solution
description: Es wird beschrieben, wie Sie VMware Horizon in Azure VMware Solution bereitstellen.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: d99c8dc76dcab1866d0c536be7fc505c2eec0cb6
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356637"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Bereitstellen von Horizon in Azure VMware Solution 

>[!NOTE]
>In diesem Dokument geht es um das VMware Horizon-Produkt, das bisher als Horizon 7 bezeichnet wurde. Horizon ist eine andere Lösung als Horizon Cloud in Azure, obwohl es einige gemeinsame Komponenten gibt. Zu den wichtigsten Vorteilen von Azure VMware Solution gehören sowohl eine einfachere Methode für die Größenanpassung als auch die Integration der VMware Cloud Foundation-Verwaltung in das Azure-Portal.

[VMware Horizon](https://www.vmware.com/products/horizon.html)®, eine Plattform für virtuelle Desktops und Anwendungen, wird im Rechenzentrum ausgeführt und ermöglicht eine einfache und zentralisierte Verwaltung. Hiermit werden virtuelle Desktops und Anwendungen auf allen Geräten und an jedem Ort bereitgestellt. Mit Horizon können Sie Verbindungen mit virtuellen Windows- und Linux-Desktops, per Remotedesktopserver (RDS) gehosteten Anwendungen, Desktops und physischen Computern erstellen und vermitteln.

Hier geht es speziell um die Bereitstellung von Horizon unter Azure VMware Solution. Allgemeine Informationen zu VMware Horizon finden Sie in der Horizon-Produktdokumentation:

* [Was ist VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [Weitere Informationen zu VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Horizon-Referenzarchitektur](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Nach der Einführung von Horizon für Azure VMware Solution gibt es auf der Azure Platform nun zwei VDI-Lösungen (Virtuelle Desktopinfrastruktur). Das folgende Diagramm enthält eine allgemeine Zusammenfassung der wichtigsten Unterschiede.

:::image type="content" source="media/vmware-horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Diagramm, dass die Unterschiede zwischen Horizon unter Azure VMware Solution und Horizon Cloud unter Azure zeigt." border="false":::

Horizon 2006 und höhere Versionen der Horizon 8 Release Line unterstützen sowohl die lokale als auch die Azure VMware Solution-Bereitstellung. Es gibt einige Horizon-Features, die lokal unterstützt werden, aber nicht unter Azure VMware Solution. Weitere Produkte des Horizon-Ökosystems werden ebenfalls unterstützt. Weitere Informationen finden Sie im Artikel zur [Featureparität und Interoperabilität](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Bereitstellen von Horizon in einer Hybrid Cloud

Sie können Horizon in einer Hybrid Cloud-Umgebung bereitstellen, wenn Sie die Horizon Cloud Pod Architecture (CPA) verwenden, um lokale und Azure-Rechenzentren zu verbinden. Mit der CPA wird Ihre Bereitstellung hochskaliert, eine Hybrid Cloud erstellt und die Redundanz für die Geschäftskontinuität und Notfallwiederherstellung sichergestellt.  Weitere Informationen finden Sie im Artikel zur [Erweiterung vorhandener Horizon 7-Umgebungen](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>Die CPA ist keine gestreckte Bereitstellung. Jeder Horizon-Pod ist einzeln vorhanden. Alle Verbindungsserver, die zu den einzelnen Pods gehören, müssen an demselben Standort angeordnet sein und aus Netzwerksicht in derselben Broadcastdomäne ausgeführt werden.

Wie ein lokales oder privates Rechenzentrum können Sie Horizon in einer privaten Azure VMware Solution-Cloud bereitstellen. Die wichtigsten Unterschiede bei der Bereitstellung von Horizon in einer lokalen Umgebung und unter Azure VMware Solution sind in den folgenden Abschnitten beschrieben.

Die _private Azure-Cloud_ entspricht im Wesentlichen dem _VMware SDDC_. Dieser Begriff wird in der Horizon-Dokumentation normalerweise verwendet. Im restlichen Teil dieses Dokuments werden beide Begriffe austauschbar verwendet.

Der Horizon Cloud Connector ist für Horizon unter Azure VMware Solution erforderlich, um Abonnementlizenzen zu verwalten. Sie können den Cloud Connector in einer Azure Virtual Network-Instanz parallel zu Horizon-Verbindungsservern bereitstellen.

>[!IMPORTANT]
>Die Unterstützung der Horizon-Steuerungsebene für Horizon unter Azure VMware Solution ist noch nicht verfügbar. Achten Sie darauf, dass Sie die VHD-Version von Horizon Cloud Connector herunterladen.

## <a name="vcenter-cloud-admin-role"></a>vCenter-Cloudadministrator-Rolle

Da Azure VMware Solution ein SDDC-Dienst ist und der Lebenszyklus des SDDC unter Azure VMware Solution von Azure verwaltet wird, ist das vCenter-Berechtigungsmodell unter Azure VMware Solution standardmäßig eingeschränkt.

Kunden müssen die Rolle „Cloudadministrator“ verwenden, die über einen eingeschränkten Satz von vCenter-Berechtigungen verfügt. Das Horizon-Produkt wurde so modifiziert, dass es mit der Rolle „Cloudadministrator“ unter Azure VMware Solution verwendet werden kann. Beispielsweise wurden folgende Änderungen vorgenommen:

* Der Vorgang zur sofortigen Bereitstellung von Klonen wurde so angepasst, dass er unter Azure VMware Solution ausgeführt wird.

* Eine spezifische vSAN-Richtlinie (VMware_Horizon) wurde unter Azure VMware Solution für Horizon erstellt. Sie muss in den SDDCs, die für Horizon bereitgestellt werden, verfügbar sein und verwendet werden.

* Der inhaltsbasierte Lesecache von vSphere (Content-Based Read Cache, CBRC), der auch als „View Storage Accelerator“ bezeichnet wird, ist bei der Ausführung unter Azure VMware Solution deaktiviert.

>[!IMPORTANT]
>CBRC darf nicht wieder aktiviert werden.

>[!NOTE]
>Von Azure VMware Solution werden bestimmte Horizon-Einstellungen automatisch konfiguriert, wenn Sie Horizon 2006 (Horizon 8) und höher auf der Horizon 8-Zweigstelle bereitstellen und im Installationsprogramm des Horizon-Verbindungsservers die Option **Azure** auswählen.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Horizon in der Azure VMware Solution-Bereitstellungsarchitektur

Bei einem typischen Horizon-Architekturentwurf werden ein Pod und eine Blockstrategie genutzt. Ein Block ist eine einzelne vCenter-Instanz, und mehrere Blöcke bilden einen Pod. Ein Horizon-Pod ist eine Organisationseinheit, die durch die Horizon-Skalierungsgrenzen bestimmt wird. Da jeder Horizon-Pod über ein separates Verwaltungsportal verfügt, besteht ein häufiger Entwurfsansatz darin, die Anzahl von Pods zu verringern.

Jede Cloud weist ein eigenes Schema für die Netzwerkkonnektivität auf. In Kombination mit VMware SDDC-Netzwerken/NSX Edge gelten für die Azure VMware Solution-Netzwerkkonnektivität spezielle Anforderungen für die Bereitstellung von Horizon, die sich von den Anforderungen der lokalen Bereitstellung unterscheiden.

Jede private Azure-Cloud und jedes SDDC kann 4.000 Desktop- oder Anwendungssitzungen verarbeiten, wenn folgende Voraussetzungen erfüllt sind:

* Der Workloaddatenverkehr ist am LoginVSI Task Worker-Profil ausgerichtet.

* Es wird nur Protokolldatenverkehr berücksichtigt (keine Benutzerdaten).

* NSX Edge wird in der Konfiguration groß angelegt.

>[!NOTE]
>Es kann sein, dass sich Ihr Workloadprofil und Ihre Anforderungen hiervon unterscheiden, sodass die Ergebnisse je nach Ihrem Anwendungsfall variieren. Aufgrund der Benutzerdatenmengen kann es sein, dass sich die Skalierungslimits im Kontext Ihrer Workload verringern. Führen Sie eine entsprechende Dimensionierung und Planung für die Bereitstellung durch. Weitere Informationen finden Sie unter den Dimensionierungsrichtlinien im Abschnitt [Dimensionieren von Azure VMware Solution-Hosts für Horizon-Bereitstellungen](#size-azure-vmware-solution-hosts-for-horizon-deployments).

Aufgrund der Obergrenze für die private Azure-Cloud und das SDDC empfehlen wir Ihnen die Verwendung einer Bereitstellungsarchitektur, bei der die Horizon-Verbindungsserver und VMware Unified Access Gateways (UAGs) innerhalb des Azure Virtual Network ausgeführt werden. Bei dieser Vorgehensweise wird jede private Azure-Cloud und jedes SDDC in einen Block verwandelt. Dies führt wiederum dazu, dass die Skalierbarkeit von Horizon unter Azure VMware Solution maximiert wird.

Die Verbindung des Azure Virtual Network mit den privaten Azure-Clouds bzw. SDDCs sollte mit ExpressRoute FastPath konfiguriert werden. Im folgenden Diagramm ist eine grundlegende Bereitstellung eines Horizon-Pods dargestellt.

:::image type="content" source="media/vmware-horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Diagramm der typischen Bereitstellung eines Horizon-Pods mit ExpressRoute FastPath." border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Netzwerkkonnektivität für die Skalierung von Horizon in Azure VMware Solution

In diesem Abschnitt wird die allgemeine Netzwerkarchitektur für die Skalierung von Horizon unter Azure VMware Solution mit einigen gängigen Bereitstellungsbeispielen beschrieben. Der Schwerpunkt liegt hierbei insbesondere auf kritischen Netzwerkelementen. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Einzelner Horizon-Pod unter Azure VMware Solution

:::image type="content" source="media/vmware-horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Diagramm eines einzelnen Horizon-Pods unter Azure VMware Solution." border="false":::

Ein einzelner Horizon-Pod ist das einfachste Bereitstellungsszenario, weil Sie nur einen Horizon-Pod in der Region „USA, Osten“ bereitstellen.  Da jede private Cloud und jedes SDDC schätzungsweise 4.000 Desktopsitzungen verarbeiten kann, stellen Sie den Horizon-Pod mit der maximalen Größe bereit.  Sie können die Bereitstellung von bis zu drei privaten Clouds bzw. SDDCs planen.

Mit den virtuellen Computern (VMs) der Horizon-Infrastruktur, die im Azure Virtual Network bereitgestellt werden, können Sie die 12.000 Sitzungen pro Horizon-Pod erreichen. Die Verbindung zwischen jeder privaten Cloud bzw. dem SDDC und dem Azure Virtual Network hat den Typ „ExpressRoute Fast Path“.  Es ist kein Ost-West-Datenverkehr zwischen privaten Clouds erforderlich. 

Für dieses grundlegende Bereitstellungsbeispiel gelten die folgenden Annahmen:

* Sie verfügen nicht über einen lokalen Horizon-Pod, den Sie über die Cloud Pod Architecture (CPA) mit diesem neuen Pod verbinden möchten.

* Endbenutzer stellen eine Verbindung mit ihren virtuellen Desktops über das Internet her (nicht über ein lokales Rechenzentrum).

Sie verbinden Ihren AD-Domänencontroller im Azure Virtual Network per VPN oder über die ExpressRoute-Leitung mit Ihrer lokalen AD-Instanz.

Eine mögliche Variante dieses einfachen Beispiels ist die Unterstützung der Konnektivität für lokale Ressourcen. Beispielsweise können Benutzer auf Desktops zugreifen und Datenverkehr für Virtual Desktop-Anwendungen generieren oder per CPA eine Verbindung mit einem lokalen Horizon-Pod herstellen.

Diagramm: Unterstützung der Konnektivität für lokale Ressourcen Sie benötigen eine ExpressRoute-Leitung, um Ihr Unternehmensnetzwerk mit dem Azure Virtual Network zu verbinden.  Darüber hinaus müssen Sie Ihr Unternehmensnetzwerk per ExpressRoute Global Reach mit den einzelnen privaten Clouds und SDDCs verbinden.  Dies ermöglicht die Konnektivität zwischen dem SDDC und der ExpressRoute-Leitung sowie lokalen Ressourcen. 

:::image type="content" source="media/vmware-horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Diagramm der Verbindung eines Unternehmensnetzwerks mit einer Azure Virtual Network-Instanz." border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Mehrere Horizon-Pods unter Azure VMware Solution in mehreren Regionen

Ein weiteres Szenario ist die Skalierung von Horizon auf mehrere Pods.  Bei diesem Szenario stellen Sie zwei Horizon-Pods in zwei unterschiedlichen Regionen bereit und richten dafür per CPA einen Verbund ein. Dies ähnelt der Netzwerkkonfiguration aus dem vorherigen Beispiel, aber hier sind noch einige zusätzliche regionsübergreifende Links vorhanden. 

Sie verbinden das Azure Virtual Network in jeder Region mit den privaten Clouds/SDDCs in der anderen Region. So können Horizon-Verbindungsserver, die Teil des CPA-Verbunds sind, eine Verbindung mit allen verwalteten Desktops herstellen. Wenn Sie dieser Konfiguration zusätzliche private Clouds/SDDCs hinzufügen, können Sie auf insgesamt 24.000 Sitzungen skalieren. 

Dasselbe Prinzip gilt, wenn Sie zwei Horizon-Pods in derselben Region bereitstellen.  Stellen Sie hierbei sicher, dass Sie den zweiten Horizon-Pod in einem *separaten Azure Virtual Network* bereitstellen. Wie auch im Beispiel mit nur einem Pod, können Sie für Ihr Unternehmensnetzwerk und den lokalen Pod eine Verbindung mit mehreren Pods bzw. Regionen herstellen, indem Sie ExpressRoute und Global Reach verwenden. 

:::image type="content" source="media/vmware-horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Diagramm mehrerer Horizon-Pods unter Azure VMware Solution in mehreren Regionen." border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Dimensionieren von Azure VMware Solution-Hosts für Horizon-Bereitstellungen 

Die Dimensionierungsmethodik von Horizon auf einem Host, der unter Azure VMware Solution ausgeführt wird, ist einfacher als eine lokale Horizon-Instanz.  Dies liegt daran, dass der Azure VMware Solution-Host standardisiert ist.  Wenn die Größe des Hosts präzise festgelegt wird, kann die Anzahl von Hosts, die zur Unterstützung Ihrer VDI-Anforderungen benötigt werden, besser ermittelt werden.  Dies ist für die Bestimmung der Kosten pro Desktop von zentraler Bedeutung.

### <a name="sizing-tables"></a>Größentabellen

Bestimmte vCPU-/vRAM-Anforderungen für virtuelle Horizon-Desktops hängen vom jeweiligen Arbeitsauslastungsprofil des Kunden ab.   Ermitteln Sie gemeinsam mit Ihrem MSFT-und VMware-Vertriebsteam Ihre vCPU-/vRAM-Anforderungen für Ihre virtuellen Desktops. 

| vCPU pro VM | vRAM pro VM (GB) | Instanz | 100 VMs | 200 VMs | 300 VMs | 400 VMs | 500 VMs | 600 VMs | 700 VMs | 800 VMs | 900 VMs | 1\.000 VMs | 2\.000 VMs | 3\.000 VMs | 4\.000 VMs | 5\.000 VMs | 6\.000 VMs | 6\.400 VMs |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3,5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3,5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3,5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3,5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Eingaben für Horizon-Dimensionierung

Hier ist angegeben, welche Informationen Sie für Ihre geplante Workload sammeln müssen:

* Anzahl von gleichzeitigen Desktops

* Erforderliche vCPUs pro Desktop

* Erforderlicher vRAM pro Desktop

* Erforderlicher Speicher pro Desktop

Im Allgemeinen gilt für VDI-Bereitstellungen entweder eine CPU- oder RAM-Einschränkung, anhand der die Hostgröße bestimmt wird. Wir verwenden das folgende Beispiel für eine Workload vom Typ „LoginVSI-Wissensarbeiter“, für die ein Leistungstest durchgeführt wurde:

* Bereitstellung mit 2.000 gleichzeitigen Desktops

* 2 vCPUs pro Desktop

* 4 GB vRAM pro Desktop.

* 50 GB Speicherplatz pro Desktop

In diesem Beispiel ergibt sich für die Gesamtzahl der Hosts der Wert 18 und somit eine VMs-pro-Host-Dichte von 111.

>[!IMPORTANT]
>Die Kundenworkloads unterscheiden sich von diesem Beispiel für einen LoginVSI-Wissensarbeiter. Arbeiten Sie beim Planen Ihrer Bereitstellung mit Ihrem VMware EUC-Vertriebsteam zusammen, um Ihre spezifischen Anforderungen an die Dimensionierung und Leistung zu erfüllen. Stellen Sie sicher, dass Sie Ihre eigenen Leistungstests mit der tatsächlichen geplanten Workload durchführen, bevor Sie die Hostdimensionierung abschließen, und die entsprechenden Anpassungen vornehmen.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Lizenzierung für Horizon unter Azure VMware Solution 

Die Gesamtkosten für die Ausführung von Horizon unter Azure VMware Solution setzen sich aus vier Komponenten zusammen. 

### <a name="azure-vmware-solution-capacity-cost"></a>Kosten für Azure VMware Solution-Kapazität

Informationen zu den Preisen finden Sie auf der Seite [VMware-Lösung in Azure – Preise](https://azure.microsoft.com/pricing/details/azure-vmware/).

### <a name="horizon-licensing-cost"></a>Kosten für die Horizon-Lizenzierung

Für die Verwendung mit Azure VMware Solution sind zwei Lizenzen verfügbar (entweder „Gleichzeitiger Benutzer“ oder „Benannter Benutzer“):

* Lizenz für Horizon-Abonnement

* Lizenz für universelles Horizon-Abonnement

Falls Sie Horizon unter Azure VMware Solution nur auf absehbare Zeit bereitstellen möchten, sollten Sie die einfache Lizenz für ein Horizon-Abonnement nutzen, weil die Kosten hierfür niedriger sind.

Wählen Sie bei der Bereitstellung sowohl unter Azure VMware Solution als auch lokal die Lizenz für ein universelles Horizon-Abonnement als Notfallwiederherstellungs-Anwendungsfall aus. Da hierin jedoch eine vSphere-Lizenz für die lokale Bereitstellung enthalten ist, sind die Kosten höher.

Arbeiten Sie mit Ihrem VMware EUC-Vertriebsteam zusammen, um die Kosten für die Horizon-Lizenzierung zu ermitteln, die sich für Ihre Anforderungen ergeben.

### <a name="azure-instance-types"></a>Azure-Instanztypen

Informationen zu den Azure-VM-Größen, die für die Horizon-Infrastruktur erforderlich sind, finden Sie unter [Horizon-Installation auf Azure VMware Solution](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution).

## <a name="references"></a>References
[Systemanforderungen für den Horizon-Agent für Linux](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu VMware Horizon in Azure VMware Solution finden Sie in den [Häufig gestellten Fragen zu VMware Horizon](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf).
