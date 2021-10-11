---
title: Microsoft Azure Stack Edge Pro mit GPU – Übersicht | Microsoft-Dokumentation
description: In diesem Thema erfahren Sie mehr über Azure Stack Edge Pro mit GPU, eine Speicherlösung, die ein physisches Gerät für die netzwerkbasierte Übertragung in Azure verwendet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/05/2021
ms.author: alkohli
ms.openlocfilehash: 551933814b034c3ac6370e4fe25ffe93fc616a4a
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544980"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>Was ist Azure Stack Edge Pro mit GPU?

Azure Stack Edge Pro mit GPU ist ein KI-fähiges Edgecomputinggerät mit Netzwerkfunktionen für die Datenübertragung. Dieser Artikel bietet Ihnen einen Überblick über die Azure Stack Edge Pro-Lösung. Dabei werden die Vorteile der Lösung, ihre wichtigsten Funktionen und die Szenarien beschrieben, in denen Sie das Gerät bereitstellen können.

Azure Stack Edge Pro mit GPU ist eine HaaS-Lösung (Hardware-as-a-Service). Microsoft liefert Ihnen ein von der Cloud verwaltetes Gerät, das als Netzwerkspeichergateway fungiert und über einen Grafikprozessor (Graphical Processing Unit, GPU) verfügt, der beschleunigte KI-Rückschlüsse ermöglicht. 

## <a name="use-cases"></a>Anwendungsfälle

Im Anschluss sehen Sie die verschiedenen Szenarien, in denen Azure Stack Edge Pro GPU für schnelle ML-Rückschlüsse (Machine Learning) am Edge sowie für die Vorverarbeitung von Daten verwendet werden kann, bevor diese an Azure gesendet werden:

- **Rückschlüsse mit Azure Machine Learning:** Mit Azure Stack Edge Pro GPU können Sie ML-Modelle ausführen, um schnell Ergebnisse zu erzielen, auf die Sie reagieren können, bevor die Daten in die Cloud übertragen werden. Das gesamte Dataset kann optional übertragen werden, um Ihre ML-Modelle weiter zu trainieren und zu verbessern. Weitere Informationen zur Verwendung der Azure ML-Modelle mit Hardwarebeschleunigung auf dem Azure Stack Edge Pro GPU-Gerät finden Sie unter [Bereitstellen von ML-Modellen für Field Programmable Gate Arrays (FPGAs) mit Azure Machine Learning](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Vorverarbeiten von Daten**: Transformieren Sie Daten mithilfe von Computeoptionen wie Containerworkloads oder virtuellen Computern, ehe Sie die Daten an Azure senden, um ein besser verwertbares Dataset zu erstellen. Die Vorverarbeitung ermöglicht Folgendes: 

    - Aggregieren von Daten.
    - Ändern von Daten, um beispielsweise persönliche Daten zu entfernen.
    - Teilmengen von Daten zur Optimierung von Speicher und Bandbreite oder für die weitere Analyse.
    - Analysieren von und Reagieren auf IoT-Ereignisse. 

- **Übertragen von Daten über das Netzwerk an Azure:** Verwenden Sie Azure Stack Edge Pro GPU, um Daten einfach und schnell an Azure zu übertragen. Dadurch werden weitere Berechnungen und Analysen oder eine Archivierung ermöglicht. 

## <a name="key-capabilities"></a>Wichtige Funktionen

Azure Stack Edge Pro GPU bietet die folgenden Funktionen:

|Funktion |BESCHREIBUNG  |
|---------|---------|
|Beschleunigte KI-Rückschlüsse| Ermöglicht durch die integrierte GPU (eine oder zwei, je nach Modell) <br> Weitere Informationen finden Sie unter [GPU-Freigabe auf Ihrem Azure Stack Edge-Gerät](azure-stack-edge-gpu-sharing.md).|
|Edgecomputing      |Unterstützung von VMs und Containerworkloads zur Analyse, Verarbeitung und Filterung von Daten. <ul><li>Informationen zu VM-Workloads finden Sie in der [VM-Übersicht auf Azure Stack Edge](azure-stack-edge-gpu-virtual-machine-overview.md).</li> <li>Informationen Containerworkloads finden Sie in der [Übersicht über Kubernetes auf Azure Stack Edge](azure-stack-edge-gpu-kubernetes-overview.md).</li></ul> |
|Datenzugriff     | Direkter Datenzugriff aus Azure Storage-Blobs und Azure Files über Cloud-APIs zur zusätzlichen Datenverarbeitung in der Cloud. Lokaler Cache auf dem Gerät wird für schnellen Zugriff auf zuletzt verwendete Dateien verwendet.|
|Von der Cloud verwaltet     |Das Gerät und der Dienst werden über das Azure-Portal verwaltet.|
|Hochladen von Daten im Offlinemodus     | Der Modus „Getrennt“ unterstützt Szenarien für das Hochladen von Daten im Offlinemodus.|
|Unterstützte Dateiübertragungsprotokolle      | Unterstützung der standardmäßigen SMB-, NFS- und REST-Protokolle für die Datenerfassung. <br> Weitere Informationen zu unterstützten Versionen finden Sie unter [Systemanforderungen für Azure Stack Edge Pro GPU](azure-stack-edge-system-requirements.md).|
|Datenaktualisierung     | Möglichkeit, lokale Dateien mit den neuesten Informationen aus der Cloud zu aktualisieren. <br> Weitere Informationen finden Sie unter [Aktualisieren einer Freigabe auf Ihrem Azure Stack Edge-Gerät](azure-stack-edge-gpu-manage-shares.md#refresh-shares).|
|Verschlüsselung    | BitLocker-Unterstützung zur lokalen Verschlüsselung von Daten und zur sicheren Datenübertragung in die Cloud über *HTTPS*.|
|Bandbreiteneinschränkung| Drosselung, um die Bandbreitennutzung während der Spitzenzeiten einzuschränken. <br> Weitere Informationen finden Sie unter [Verwalten von Bandbreitenzeitplänen auf Ihrem Azure Stack Edge-Gerät](azure-stack-edge-gpu-manage-bandwidth-schedules.md).|
|Einfaches Bestellen| Massenbestellung und Nachverfolgung des Geräts über Azure Edge Hardware Center (Vorschau) <br> Weitere Informationen finden Sie unter [Bestellen eines Geräts über das Azure Edge Hardware Center](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).|
|Spezialisierte Netzwerkfunktionen|Verwenden Sie den Marketplace von Azure Network Function Manager, um Netzwerkfunktionen wie mobile Paketkerne, SD-WAN-Edge und VPN-Dienste schnell auf einem Azure Stack Edge-Gerät bereitzustellen, das in Ihrer lokalen Umgebung ausgeführt wird. Weitere Informationen finden Sie unter [Was ist der Azure Network Function Manager (Vorschau)?](../network-function-manager/overview.md).|

<!--|ExpressRoute | Added security through ExpressRoute. Use peering configuration where traffic from local devices to the cloud storage endpoints travels over the ExpressRoute. For more information, see [ExpressRoute overview](../expressroute/expressroute-introduction.md).|-->


## <a name="components"></a>Komponenten

Die Azure Stack Edge Pro GPU-Lösung umfasst die Azure Stack Edge-Ressource, ein physisches Azure Stack Edge Pro GPU-Gerät und eine lokale Webbenutzeroberfläche.

* **Physisches Azure Stack Edge Pro GPU-Gerät:** ein von Microsoft bereitgestellter 1U-Rackserver, der für das Senden von Daten an Azure konfiguriert werden kann

    [!INCLUDE [azure-stack-edge-gateway-edge-hardware-center-overview](../../includes/azure-stack-edge-gateway-edge-hardware-center-overview.md)]    

    Weitere Informationen finden Sie unter [Neue Ressource erstellen](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
    
* **Azure Stack Edge-Ressource:** eine Ressource im Azure-Portal, mit der Sie ein Azure Stack Edge Pro GPU-Gerät über eine zentrale Webbenutzeroberfläche verwalten können, auf die von verschiedenen geografischen Standorten aus zugegriffen werden kann. Verwenden Sie die Azure Stack Edge-Ressource, um Ressourcen zu erstellen und zu verwalten, Geräte und Warnungen anzuzeigen und zu verwalten sowie Freigaben zu verwalten.  
   

* **Lokale Webbenutzeroberfläche von Azure Stack Edge Pro GPU:** eine browserbasierte lokale Benutzeroberfläche auf Ihrem Azure Stack Edge Pro GPU-Gerät, die in erster Linie der anfänglichen Konfiguration des Geräts dient. Über die lokale Webbenutzeroberfläche können Sie zudem Diagnosen ausführen, das Azure Stack Edge Pro GPU-Gerät herunterfahren und neu starten, Kopierprotokolle anzeigen und den Microsoft-Support kontaktieren, um eine Serviceanfrage zu stellen.

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]
    
    Informationen zur Verwendung der webbasierten Benutzeroberfläche finden Sie unter [Verwalten des Zugriffs, der Energieeinstellungen und des Konnektivitätsmodus für Ihr Azure Stack Edge Pro-Gerät](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Regionale Verfügbarkeit

Das physische Azure Stack Edge Pro GPU-Gerät, die Azure-Ressource und das Zielspeicherkonto, an das Sie Daten übertragen, müssen sich nicht in derselben Region befinden.

- **Ressourcenverfügbarkeit:** Für dieses Release ist die Ressource in den Regionen „USA, Osten“, „Europa, Westen“, und „Asien, Südosten“ verfügbar.

- **Geräteverfügbarkeit:** Eine Liste aller Länder/Regionen, in denen das Azure Stack Edge Pro GPU-Gerät verfügbar ist, finden Sie unter [Azure Stack Edge-Preise](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro) im Abschnitt **Verfügbarkeit** auf der Registerkarte **Azure Stack Edge Pro**.
    
- **Zielspeicherkonten**: Die Speicherkonten, in denen die Daten gespeichert werden, sind in allen Azure-Regionen verfügbar. Für eine optimale Leistung sollten sich die Regionen, in denen die Speicherkonten Azure Stack Edge Pro GPU-Daten speichern, in der Nähe des Gerätestandorts befinden. Ein weit entfernt vom Gerät befindliches Speicherkonto führt zu langen Wartezeiten und einer langsameren Leistung.

Der Azure Stack Edge-Dienst ist ein nicht regionaler Dienst. Weitere Informationen finden Sie unter [Regionen und Verfügbarkeitszonen in Azure](../availability-zones/az-overview.md). Beim Azure Stack Edge-Dienst besteht keine Abhängigkeit von einer bestimmten Azure-Region. Deshalb ist er resilienter gegen zonenweite oder regionsweite Ausfälle.

Überlegungen zur Auswahl einer Region für den Azure Stack Edge-Dienst, das Gerät und die Datenspeicherung finden Sie unter [Auswählen einer Region für Azure Stack Edge](azure-stack-edge-gpu-regions.md).

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie die [Systemanforderungen für Azure Stack Edge Pro mit GPU](azure-stack-edge-gpu-system-requirements.md).

- Machen Sie sich mit den [Einschränkungen von Azure Stack Edge Pro GPU](azure-stack-edge-limits.md) vertraut.

- Stellen Sie [Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md) im Azure-Portal bereit.
