---
title: Azure Stack Edge Mini R – Übersicht | Microsoft-Dokumentation
description: Informationen zu Azure Stack Edge Mini R, einer Speicherlösung für militärische Anwendungen, die ein tragbares physisches Gerät mit einem Akku für die Datenübertragung über WLAN in Azure nutzt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/04/2021
ms.author: alkohli
ms.openlocfilehash: df6838d1616d27c12d7176df0041a35d0d773f83
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545113"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Was ist Azure Stack Edge Mini R?

Azure Stack Edge Mini R ist ein ultraportables, robustes Edgecomputinggerät für die Verwendung in rauen Umgebungen. Azure Stack Edge Mini R wird als HaaS-Lösung (Hardware-as-a-Service) ausgeliefert. Microsoft liefert Ihnen ein von der Cloud verwaltetes Gerät, das als Netzwerkspeichergateway fungiert und über eine integrierte Bildverarbeitungseinheit (Vision Processing Unit, VPU) verfügt, die beschleunigte KI-Rückschlüsse ermöglicht.

Dieser Artikel bietet Ihnen einen Überblick über die Azure Stack Edge Mini R-Lösung, ihre wichtigsten Funktionen und die Szenarien, in denen Sie dieses Gerät bereitstellen können.


## <a name="key-capabilities"></a>Wichtige Funktionen

Azure Stack Edge Mini R weist die folgenden Merkmale auf:

|Funktion |Beschreibung  |
|---------|---------|
|Robuste Hardware| Robuste Hardware für den Einsatz in widrigen Umgebungen|
|Sehr einfacher Transport| Sehr einfacher Transport, Formfaktor mit Akkubetrieb.|
|Von der Cloud verwaltet|Das Gerät und der Dienst werden über das Azure-Portal verwaltet.|
|Workloads für Edgecomputing|Ermöglicht das Analysieren, Verarbeiten und Filtern von Daten.<br>Unterstützt VMs und Workloads in Containern. <ul><li>Informationen zu VM-Workloads finden Sie in der [VM-Übersicht auf Azure Stack Edge](azure-stack-edge-gpu-virtual-machine-overview.md).</li> <li>Informationen Containerworkloads finden Sie in der [Übersicht über Kubernetes auf Azure Stack Edge](azure-stack-edge-gpu-kubernetes-overview.md).</li></ul>  |
|Beschleunigte KI-Rückschlüsse| Ausgestattet mit der Intel Movidius Myriad X-VPU. |
|Verdrahtet und drahtlos | Ermöglicht verdrahtete und drahtlose Datenübertragungen.|
|Datenzugriff     | Direkter Datenzugriff aus Azure Storage-Blobs und Azure Files über Cloud-APIs zur zusätzlichen Datenverarbeitung in der Cloud. Lokaler Cache auf dem Gerät wird für schnellen Zugriff auf zuletzt verwendete Dateien verwendet.|
|Modus „Getrennt“|  Gerät und Dienst können optional über Azure Stack Hub verwaltet werden. Bereitstellen, Ausführen und Verwalten von Anwendungen im Offlinemodus. <br> Der Modus „Getrennt“ unterstützt Szenarien für das Hochladen von Daten im Offlinemodus.|
|Unterstützte Dateiübertragungsprotokolle      |Unterstützt für die Datenerfassung die Standardprotokolle SMB, NFS und REST. <br> Weitere Informationen zu unterstützten Versionen finden Sie unter [Systemanforderungen für Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md).|
|Datenaktualisierung     | Möglichkeit, lokale Dateien mit den neuesten Informationen aus der Cloud zu aktualisieren. <br> Weitere Informationen finden Sie unter [Aktualisieren einer Freigabe auf Ihrem Azure Stack Edge-Gerät](azure-stack-edge-gpu-manage-shares.md#refresh-shares).|
|Doppelte Verschlüsselung    | Das sich selbst verschlüsselnde Laufwerk stellt die erste Verschlüsselungsebene dar. VPN bietet die zweite Verschlüsselungsebene. BitLocker-Unterstützung zur lokalen Verschlüsselung von Daten und zur sicheren Datenübertragung in die Cloud über *HTTPS*. <br> Weitere Informationen finden Sie unter [Konfigurieren von VPN auf Ihrem Azure Stack Edge Pro R-Gerät](azure-stack-edge-mini-r-configure-vpn-powershell.md).|
|Bandbreiteneinschränkung| Drosselung, um die Bandbreitennutzung während der Spitzenzeiten einzuschränken. <br> Weitere Informationen finden Sie unter [Verwalten von Bandbreitenzeitplänen auf Ihrem Azure Stack Edge-Gerät](azure-stack-edge-gpu-manage-bandwidth-schedules.md).|
|Einfaches Bestellen| Massenbestellung und Nachverfolgung des Geräts über Azure Edge Hardware Center (Vorschau) <br> Weitere Informationen finden Sie unter [Bestellen eines Geräts über das Azure Edge Hardware Center](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).|

## <a name="use-cases"></a>Anwendungsfälle

Dies sind die verschiedenen Szenarien, in denen Azure Stack Edge Mini R für schnelle Machine Learning-Rückschlüsse (ML) am Edge sowie für die Vorverarbeitung von Daten verwendet werden kann, bevor diese an Azure gesendet werden.

- **Rückschluss mit Azure Machine Learning**: Mit Azure Stack Edge Mini R können Sie ML-Modelle ausführen, um schnell Ergebnisse zu erzielen, auf die Sie reagieren können, bevor die Daten in die Cloud übertragen werden. Das gesamte Dataset kann optional übertragen werden, um Ihre ML-Modelle weiter zu trainieren und zu verbessern. Weitere Informationen zur Verwendung der Azure ML-Modelle mit Hardwarebeschleunigung auf dem Azure Stack Edge Mini R-Gerät finden Sie unter [Bereitstellen von Azure ML-Modellen mit Hardwarebeschleunigung auf Azure Stack Edge Mini R](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Vorverarbeiten von Daten**: Transformieren Sie Daten mithilfe von Computeoptionen wie Containern oder virtuellen Computern, ehe Sie sie an Azure senden, um ein besser verwertbares Dataset zu erstellen. Die Vorverarbeitung ermöglicht Folgendes:

    - Aggregieren von Daten.
    - Ändern von Daten, um beispielsweise persönliche Daten zu entfernen.
    - Teilmengen von Daten zur Optimierung von Speicher und Bandbreite oder für die weitere Analyse.
    - Analysieren von und Reagieren auf IoT-Ereignisse.

- **Übertragen von Daten über das Netzwerk an Azure**: Verwenden Sie Azure Stack Edge Mini R, um Daten einfach und schnell in Azure zu übertragen. Dadurch werden weitere Compute- und Analysenvorgänge oder eine Archivierung ermöglicht.

## <a name="components"></a>Komponenten

Die Azure Stack Edge Mini R-Lösung umfasst eine Azure Stack Edge Pro R-Ressource, ein robustes, physisches, sehr einfach zu transportierendes Azure Stack Edge Mini R-Gerät und eine lokale Webbenutzeroberfläche.

* **Physisches Azure Stack Edge Mini R-Gerät:** Von Microsoft geliefertes ultraportables, robustes Compute- und Speichergerät. Das Gerät verfügt über einen eingebauten Akku und wiegt etwas mehr als 3 kg.

    ![Azure Stack Edge Mini R-Gerät](media/azure-stack-edge-mini-r-overview/perspective-view-1.png)

    [!INCLUDE [azure-stack-edge-gateway-edge-hardware-center-overview](../../includes/azure-stack-edge-gateway-edge-hardware-center-overview.md)]    

    Weitere Informationen finden Sie unter [Neue Ressource erstellen](azure-stack-edge-mini-r-deploy-prep.md#create-a-new-resource).

* **Azure Stack Edge-Ressource:** Eine Ressource im Azure-Portal, mit der Sie ein robustes Azure Stack Edge Mini R-Gerät über eine zentrale Webbenutzeroberfläche verwalten können, auf die von verschiedenen geografischen Standorten aus zugegriffen werden kann. Verwenden Sie die Azure Stack Edge-Ressource, um Ressourcen zu erstellen und zu verwalten, Geräte und Warnungen anzuzeigen und zu verwalten sowie Freigaben zu verwalten.  

* **Lokale Webbenutzeroberfläche von Azure Stack Edge Mini R:** Eine browserbasierte lokale Benutzeroberfläche auf Ihrem Azure Stack Edge Mini R-Gerät, die in erster Linie der anfänglichen Konfiguration des Geräts dient. Über die lokale Webbenutzeroberfläche können Sie zudem Diagnosen ausführen, das Azure Stack Edge Pro-Gerät herunterfahren und neu starten, Kopierprotokolle anzeigen und den Microsoft-Support kontaktieren, um eine Serviceanfrage zu stellen.

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]

## <a name="region-availability"></a>Regionale Verfügbarkeit

Das physische Azure Stack Edge Mini R-Gerät, die Azure-Ressource und das Zielspeicherkonto, an das Sie Daten übertragen, müssen sich nicht in derselben Region befinden.

- **Ressourcenverfügbarkeit**: Eine Liste aller Regionen, in denen die Azure Stack Edge-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Geräteverfügbarkeit:** Eine Liste aller Länder/Regionen, in denen das Azure Stack Edge Mini R-Gerät verfügbar ist, finden Sie auf der Seite [Azure Stack Edge – Preise](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeMiniR) auf der Registerkarte „Azure Stack Edge Mini R“ im Abschnitt „Verfügbarkeit“.

- **Zielspeicherkonten**: Die Speicherkonten, in denen die Daten gespeichert werden, sind in allen Azure-Regionen verfügbar. Die Regionen, in denen die Speicherkonten Azure Stack Edge Mini R-Daten speichern, sollten sich in der Nähe des Gerätestandorts befinden, um optimale Leistung zu erzielen. Ein weit entfernt vom Gerät befindliches Speicherkonto führt zu langen Wartezeiten und einer langsameren Leistung.

Der Azure Stack Edge-Dienst ist ein nicht regionaler Dienst. Weitere Informationen finden Sie unter [Regionen und Verfügbarkeitszonen in Azure](../availability-zones/az-overview.md). Beim Azure Stack Edge-Dienst besteht keine Abhängigkeit von einer bestimmten Azure-Region. Deshalb ist er resilienter gegen zonenweite oder regionsweite Ausfälle.

Überlegungen zur Auswahl einer Region für den Azure Stack Edge-Dienst, das Gerät und die Datenspeicherung finden Sie unter [Auswählen einer Region für Azure Stack Edge](azure-stack-edge-gpu-regions.md).

## <a name="next-steps"></a>Nächste Schritte

- Prüfen Sie die [Systemanforderungen für Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md).