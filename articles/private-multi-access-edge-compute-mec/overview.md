---
title: Privates Multi-Access Edge Computing in Azure
description: Hier erfahren Sie mehr über die Lösung für privates Multi-Access Edge Computing (MEC) in Azure, die ein Portfolio aus Compute-, Netzwerk- und Anwendungsdiensten von Microsoft vereint, die über die Cloud verwaltet werden.
author: niravi-msft
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: kumud
ms.openlocfilehash: 6d85a42b3ff23f4bead8abd3187b9a899ebb12c3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277940"
---
# <a name="what-is-azure-private-multi-access-edge-compute"></a>Was ist privates Multi-Access Edge Computing in Azure?

Privates Multi-Access Edge Computing in Azure (MEC) ist eine Lösung, die ein Portfolio aus Compute-, Netzwerk- und Anwendungsdiensten von Microsoft vereint, die über die Cloud verwaltet werden. Diese Lösung ermöglicht Operators und Systemintegratoren die Bereitstellung von latenzarmer Hochleistungskonnektivität sowie von IoT-Anwendungen am Edge des Unternehmens für die nächste Welle der digitalen Transformation von Unternehmen. 

Das private MEC in Azure ist eine Weiterentwicklung von Private Edge Zone und erweitert den Umfang der Möglichkeiten von nur einer Plattform und einem Dienst auf eine Lösung, die mehrere Plattformen und Funktionen nutzt. Zu diesen Funktionen gehören Edgedienste und -anwendungen, Edge-Netzwerkfunktionen, Edgecomputingoptionen sowie Edgefunk und -geräte. Durch die Verarbeitung von Daten näher am Endgerät helfen diese Funktionen dabei, latenz- und durchsatzabhängige Benutzerszenarios wie Videoanalysen, Echtzeitrobotik und gemischte IoT-Anwendungsfälle auf globaler Ebene zu verbessern. Kunden und Partner können von einer vollständigen Sammlung von Azure-Diensten und -Ökosystemtechnologiekomponenten profitieren, um Lösungen schnell und einfach zu erstellen, bereitzustellen und zu verwalten. 

## <a name="benefits-to-customers-and-partners"></a>Vorteile für Kunden und Partner
- Unternehmenskunden:
    - Zugriff auf ein wachsendes Portfolio mit Azure-Diensten, ISVs und einem Entwicklerökosystem
    - Auswahl vertrauenswürdiger branchenspezifischer Lösungen
    - Vollständig integrierter Technologiestapel ab einem Speicherbedarf von 1U

- Telekommunikations- und Systemintegrationspartner:
    - Einfaches Beschaffen, Bereitstellen, Verwalten und Monetarisieren von 5G und Edge für den Kunden
    - Zusammengestellte Auswahl in einem Ökosystem aus Produkten, Clouddiensten und Anwendungen
    - Zugriff auf das Azure-Entwicklerökosystem

- ISVs von Anwendungen:
    - Plattform mit sämtlichen Features zum Entwickeln von Anwendungen mit äußerst geringer Latenz 
    - Konsistente Umgebung für Entwickler mit bekannten Tools und Ressourcen
    - Skalieren der Verteilung über den Microsoft Marketplace

## <a name="microsoft-capabilities"></a>Microsoft-Funktionen
Das private MEC in Azure schließt einige Funktionen von Microsoft ein. Hierzu gehören eine Kombination aus Netzwerkfunktionsprodukten, Verwaltungsdiensten sowie Hardwareinfrastruktur und -diensten. 

### <a name="azure-network-functions-offered-via-marketplace"></a>Über den Marketplace angebotene Azure-Netzwerkfunktionen

**Metaswitch Fusion Core**: Fusion Core ist eine vollständig containerisierte 5G-Kernlösung, die alle Netzwerkfunktionen unterstützt, die für die Konnektivität zwischen IoT-Geräten erforderlich sind, die über 4G- oder 5G-Funk mit dem Datennetzwerk verbunden sind. Diese Lösung bietet unter anderem die folgenden wichtigen Vorteile:
 - Einfache Bereitstellung über das Azure Marketplace-Portal in Azure Stack Edge
 - Hohe Leistung mit 25 Gbit/s Durchsatz bei einem ungewöhnlich geringen Speicherbedarf von vier physischen Kernen
 - Unterstützung für eigenständige 4G- und 5G-Zugriffstypen
 - Integrierte Tools für unternehmensorientierte Dashboards für Dienstüberprüfung und Key Performance Indicators 
 
Fusion Core ermöglicht ISVs die Bereitstellung von Anwendungen auf demselben Azure Stack Edge-Knoten für IoT Edge-Anwendungen wie Livevideoanalysen. 

**Affirmed Private Network Service**: Affirmed Private Network Service ist eine Azure Marketplace-Instanz, die einen verwalteten privaten Netzwerkdienst für Betreiber von Mobilfunknetzen und Anbieter von verwalteten Diensten anbietet, die 4G- und 5G-Angebote für verwaltete Dienste für Unternehmen bereitstellen möchten. Mit APNS können Operator für Unternehmen ein privates Mobilfunknetz auf Netzbetreiberniveau bereitstellen, in dem diese unternehmenskritische Anwendungen ausführen und betreiben können, die eine geringe Latenz, eine hohe Bandbreite und End-to-End-Sicherheit erfordern. APNS verfügt über eine Integration von Betreibern von Mobilfunknetzen und bietet vollständige Mobilität zwischen privaten Netzwerken und solchen von öffentlichen Betreibern. Dank Automatisierung und vereinfachter Vorgänge bietet APNS Skalierbarkeit für Tausende von Edgestandorten von Unternehmen und nutzt Azure, um mehr Sicherheit in privaten Netzwerken und Unternehmensanwendungen zu gewährleisten. Mit APNS können Sie flexibel den gesamten mobilen Kern am Edge des Netzwerks oder in der Cloud oder in einem Hybridmodus mit Steuerungsebene in der Cloud und Benutzerebene am Edge des Unternehmens bereitstellen. 

### <a name="azure-management-services"></a>Verwaltungsdienste in Azure

**Azure Network Function Manager (NFM)** : Azure NFM ermöglicht die Bereitstellung von Netzwerkfunktionen am Edge mit konsistenten Azure-Tools und -Schnittstellen. Verwenden Sie diesen Dienst, um Paketkern- und SD-WAN-Netzwerkfunktionen in Azure Stack Edge bereitzustellen. Weitere Informationen finden Sie unter [Was ist Azure Network Function Manager (Vorschau)?](../network-function-manager/overview.md).

**Kubernetes mit Arc-Unterstützung**: Mit Kubernetes mit Azure Arc-Unterstützung können Sie Kubernetes-Cluster anfügen und konfigurieren, die sich entweder innerhalb oder außerhalb von Azure befinden. Sie können mit richtlinienbasierten Bereitstellungen im großen Stil überwachen und verwalten sowie konsistente Sicherheitskonfigurationen im großen Stil anwenden. Kubernetes mit Azure Arc-Unterstützung funktioniert mit jedem Kubernetes-Cluster, der eine CNCF-Zertifizierung (Cloud Native Computing Foundation) besitzt. Weitere Informationen finden Sie unter [Azure Arc](https://azure.microsoft.com/services/azure-arc/).

### <a name="azure-stack-hardware-and-services"></a>Azure Stack-Hardware und -Dienste
**Azure Stack Edge**: Azure Stack Edge bietet ein Portfolio von Geräten, mit denen Computeressourcen, Speicher und Intelligence am Edge bereitgestellt werden – genau dort, wo die Daten erstellt werden. Die Geräte sind mit 1U-Racks kompatible Appliances mit ein bis zwei NVIDIA T4-GPUs. Mit Azure IoT Edge können Sie Container über IoT Hub bereitstellen und verwalten und mit Azure IoT-Lösungen in Azure Stack Edge integrieren. Die Azure Stack Edge Pro-SKU ist für die Ausführung von Netzwerkfunktionen am Edge zertifiziert. Weitere Informationen finden Sie unter [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/).

**Azure Stack HCI**: Die Azure Stack HCI ist ein neues HCI-Betriebssystem (hyperkonvergente Infrastruktur), das als Azure-Dienst bereitgestellt wird und die neuesten Sicherheits-, Leistungs- und Featureupdates zur Verfügung stellt. Verwenden Sie Ihre bereits vorhandenen Tools und Prozesse, um Windows- und Linux-VMs (virtuelle Computer) in Ihrem Rechenzentrum oder am Edge bereitzustellen und auszuführen. Erweitern Sie Ihr Rechenzentrum mit Azure Backup, Azure Monitor und Microsoft Defender für Cloud in die Cloud. Weitere Informationen finden Sie unter [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

### <a name="application-services"></a>Anwendungsdienste

**Azure IoT Edge-Runtime**: Die Azure IoT Edge-Runtime ermöglicht die Verwaltung und Bereitstellung von Cloudworkloads auf verschiedenen Edgecomputinggeräten mit denselben Tools und demselben Sicherheitsstatus wie bei cloudnativen Workloads. Weitere Informationen finden Sie unter [Azure IoT Edge-Runtime](/windows/ai/windows-ml-container/iot-edge-runtime).

**Azure IoT Hub** Azure IoT Edge-Runtime: Die Azure IoT Edge-Runtime ermöglicht die Verwaltung und Bereitstellung von Cloudworkloads auf verschiedenen Edgecomputinggeräten mit denselben Tools und demselben Sicherheitsstatus wie bei cloudnativen Workloads. Weitere Informationen finden Sie unter [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/).

**Azure IoT Central**: Azure IoT Central ist eine verwaltete Anwendungsplattform, die Device-Management- und Data-Ingestion-as-a-Service mit einem vorhersagbaren Preismodell und integrierter globaler Skalierung ermöglicht. Weitere Informationen finden Sie unter [Azure IoT Central](https://azure.microsoft.com/services/iot-central/).

**Azure Digital Twins**: Azure Digital Twins ermöglicht es, Gerätesensoren in ihrem Geschäftskontext unter Berücksichtigung von räumlichen Beziehungen, Nutzungsmustern und anderen Geschäftskontexten zu modellieren. Hierbei wird eine Gerätegruppe in ein digitales Replikat einer physischen Ressource oder Umgebung verwandelt. Weitere Informationen finden Sie unter [Azure Digital Twins](https://azure.microsoft.com/services/digital-twins/).

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Metaswitch Fusion Core](metaswitch-fusion-core-overview.md)
- Weitere Informationen zu [Affirmed Private Network Service](affirmed-private-network-service-overview.md)
