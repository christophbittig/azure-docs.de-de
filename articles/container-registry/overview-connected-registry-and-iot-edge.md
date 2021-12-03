---
title: Verwenden der verbundenen Registrierung mit Azure IoT Edge
description: Übersicht über die verbundene Azure Container Registry in hierarchischen IoT Edge-Szenarien
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 08/24/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f5be0a18c2185dffa685291121c8a730d60ef52
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100315"
---
# <a name="using-connected-registry-with-azure-iot-edge"></a>Verwenden der verbundenen Registrierung mit Azure IoT Edge

In diesem Artikel erfahren Sie mehr über die Verwendung einer [verbundenen Azure-Registrierung](intro-connected-registry.md) in hierarchischen [IoT Edge](../iot-edge/about-iot-edge.md)-Szenarien. Die verbundene Containerregistrierung kann als IoT Edge-Modul bereitgestellt werden und eine wichtige Rolle bei der Bereitstellung von Containerimages spielen, die von den Geräten in der Hierarchie benötigt werden.

## <a name="what-is-a-hierarchical-iot-edge-deployment"></a>Was ist eine hierarchische IoT Edge-Bereitstellung?

Azure IoT Edge ermöglicht die Bereitstellung von IoT Edge-Geräten in Netzwerken, die in hierarchischen Ebenen organisiert sind. Jede Ebene einer Hierarchie entspricht einem [Gatewaygerät](../iot-edge/iot-edge-as-gateway.md), mit dem Nachrichten und Anforderungen von den Geräten der direkt untergeordneten Ebene verarbeitet werden. Sie können eine Hierarchie mit Geräten so strukturieren, dass nur die oberste Ebene über Konnektivität mit der Cloud verfügt und die untergeordneten Ebenen nur mit der Ebene direkt darüber bzw. darunter kommunizieren können. Diese Netzwerkebenen sind die Grundlage der meisten Unternehmensnetzwerke, die auf der [ISA-95-Norm](https://en.wikipedia.org/wiki/ANSI/ISA-95) basieren.

Informationen zum Erstellen einer Hierarchie von IoT Edge-Geräten finden Sie unter [Tutorial: Erstellen einer Hierarchie für IoT Edge-Geräte][tutorial-nested-iot-edge].

## <a name="how-do-i-use-connected-registry-in-hierarchical-iot-edge-scenarios"></a>Wie verwende ich die verbundene Registrierung in hierarchischen IoT Edge-Szenarien?

Die folgende Abbildung zeigt, wie die verbundene Registrierung verwendet werden kann, um die hierarchische Bereitstellung von IoT Edge zu unterstützen. Einfarbige graue Linien zeigen den tatsächlichen Netzwerkdatenfluss an, während die gestrichelten Linien die logische Kommunikation zwischen Komponenten und den verbundenen Registrierungen anzeigen.

![Verbundene Registrierung und hierarchische IoT Edge-Bereitstellungen](media/overview-connected-registry-and-iot-edge/connected-registry-iot-edge-overview.svg)

### <a name="top-layer"></a>Obere Ebene

Die oberste Ebene der Beispielarchitektur, *Ebene 5: Unternehmensnetzwerk*, wird von der IT-Abteilung verwaltet und kann auf die Containerregistrierung für Contoso in der Azure-Cloud zugreifen. Die verbundene Registrierung wird als IoT Edge-Modul auf der IoT Edge-VM bereitgestellt und kann direkt mit der Cloudregistrierung kommunizieren, um Images und Artefakte per Pull und Push zu übertragen. 

Die verbundene Registrierung wird im standardmäßigen [ReadWrite-Modus](intro-connected-registry.md#modes) als „In Bearbeitung“ angezeigt. Clients dieser verbundenen Registrierung können Images und Artefakte von ihr pullen und an sie pushen. Per Push übertragene Images werden mit der Cloudregistrierung synchronisiert. Wenn Push-Vorgänge auf dieser Ebene nicht erforderlich sind, kann die verbundene Registrierung so geändert werden, dass sie im [ReadOnly-Modus](intro-connected-registry.md#modes) ausgeführt wird.

Schritte zum Bereitstellen der verbundenen Registrierung als IoT Edge-Modul auf dieser Ebene finden Sie unter [Schnellstart: Bereitstellen einer verbundenen Registrierung auf einem IoT Edge-Gerät][quickstart-deploy-connected-registry-iot-edge-cli].

### <a name="nested-layers"></a>Geschachtelte Ebenen

Die nächsttiefere Ebene, *Ebene 4: Standortgeschäftsplanung und Logistik*, ist so konfiguriert, dass sie nur mit Ebene 5 kommuniziert. Bei der Bereitstellung der IoT Edge-VM auf Ebene 4 muss sie daher stattdessen die Modulimages von der verbundenen Registrierung auf Ebene 5 pullen. 

Sie können auch eine verbundene Registrierung bereitstellen, die im ReadOnly-Modus ausgeführt wird, um die unten aufgeführten Ebenen bereitzustellen. Dies wird mit der IoT Edge-VM auf *Ebene 3: Industrielle Sicherheitszone* veranschaulicht. Dieser virtuelle Computer muss die Modulimages aus der verbundenen Registrierung auf *Ebene 4* pullen. Wenn Clients auf niedrigeren Ebenen bereitgestellt werden müssen, kann eine verbundene Registrierung im ReadOnly-Modus auf Ebene 3 bereitgestellt werden usw.

In dieser Architektur werden die verbundenen Registrierungen, die auf jeder Ebene bereitgestellt werden, so konfiguriert, dass sie die Images mit der verbundenen Registrierung auf der darüber liegenden Ebene synchronisieren. Die verbundenen Registrierungen werden als IoT Edge-Module bereitgestellt und nutzen die IoT Edge-Mechanismen für die Bereitstellung und das Netzwerkrouting.

Schritte zum Bereitstellen der verbundenen Registrierung auf geschachtelten IoT Edge-Geräten finden Sie unter [Schnellstart: Bereitstellen der verbundenen Registrierung auf geschachtelten IoT Edge-Geräten][tutorial-deploy-connected-registry-nested-iot-edge-cli].

## <a name="next-steps"></a>Nächste Schritte

In dieser Übersicht haben Sie die Verwendung der verbundenen Registrierung in hierarchischen IoT Edge-Szenarien kennengelernt. Fahren Sie mit den folgenden Artikeln fort, um zu erfahren, wie Sie eine verbundene Registrierung für Ihr IoT Edge-Gerät konfigurieren und bereitstellen.

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen einer verbundenen Registrierung mithilfe der CLI][quickstart-connected-registry-cli]

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen einer verbundenen Registrierung über das Portal][quickstart-connected-registry-portal]

> [!div class="nextstepaction"]
> [Schnellstart: Bereitstellen einer verbundenen Registrierung auf einem IoT Edge-Gerät][quickstart-deploy-connected-registry-iot-edge-cli]

> [!div class="nextstepaction"]
> [Tutorial: Bereitstellen einer verbundenen Registrierung auf geschachtelten IoT Edge-Geräten][tutorial-deploy-connected-registry-nested-iot-edge-cli]

<!-- LINKS - internal -->
[quickstart-connected-registry-cli]:quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]:quickstart-connected-registry-portal.md
[quickstart-deploy-connected-registry-iot-edge-cli]:quickstart-deploy-connected-registry-iot-edge-cli.md
[tutorial-nested-iot-edge]:../iot-edge/tutorial-nested-iot-edge.md
[tutorial-deploy-connected-registry-nested-iot-edge-cli]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
