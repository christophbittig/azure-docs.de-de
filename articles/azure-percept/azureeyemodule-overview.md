---
title: Azure Percept Vision-KI-Modul
description: Eine Übersicht über „azureeyemodule“ – das Modul zum Ausführen der KI-Vision-Workload im Azure Percept DK.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: overview
ms.date: 08/09/2021
ms.custom: template-overview
ms.openlocfilehash: b09c5eb0dc2fa977c544d7da8178408dadcd08a6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222373"
---
# <a name="azure-percept-vision-ai-module"></a>Azure Percept Vision-KI-Modul

„azureeyemodule“ ist der Name des Edgemoduls zum Ausführen der KI-Vision-Workload im Azure Percept DK. Es gehört zu den Edgemodulen der Azure IoT-Suite und wird im Rahmen der [Einrichtung](./quickstart-percept-dk-set-up.md) für das Azure Percept DK bereitgestellt. Dieser Artikel enthält eine Übersicht über das Modul und seine Architektur.

## <a name="architecture"></a>Aufbau

:::image type="content" source="media/azureeyemodule-overview/azureeyemodule-architecture.png" alt-text="Diagramm: Architektur von „azureeyemodule“":::

Bei der Azure Percept-Workload für das Azure Percept DK handelt es sich um eine C++-Anwendung, die im Docker-Container „azureeyemodule“ ausgeführt wird. Sie verwendet OpenCV GAPI für die Bildverarbeitung und Modellausführung. „azureeyemodule“ wird unter dem Mariner-Betriebssystem als Teil der Module der Azure IoT-Suite ausgeführt, die auf Basis des Azure Percept DK ausgeführt werden.

Die Azure Percept-Workload ist zum Erfassen von Bildern sowie zum Ausgeben von Bildern und Nachrichten konzipiert. Die Ausgabebilder können mit Zeichnungen wie Begrenzungsfeldern, Segmentierungsmasken, Verknüpfungen, Bezeichnungen usw. markiert werden. Bei den Ausgabenachrichten handelt es sich um einen JSON-Datenstrom mit Rückschlussergebnissen, der von Downstreamtasks erfasst und verwendet werden kann.
Die Ergebnisse werden als RTSP-Datenstrom bereitgestellt, der am Port 8554 des Geräts verfügbar ist. Die Ergebnisse werden auch an ein anderes Modul gesendet, das auf dem Gerät ausgeführt wird. Dadurch wird der RTSP-Datenstrom im Rahmen eines am Port 3000 ausgeführten HTTP-Servers bereitgestellt. Sie können in beiden Fällen allerdings nur im lokalen Netzwerk angezeigt werden.

> [!CAUTION]
> Es gibt *keine* Verschlüsselung oder Authentifizierung für die RTSP-Feeds. Jeder Benutzer im lokalen Netzwerk kann genau das anzeigen, was Azure Percept Vision sieht, indem er die richtige Adresse in einen Webbrowser oder RTSP-basierten Media Player eingibt.

Die Azure Percept-Workload bietet mehrere praktische Features für Endbenutzer:
- Eine codefreie Lösung für gängige Anwendungsfälle mit maschinellem Sehen wie Objektklassifizierung und allgemeine Objekterkennung
- Eine erweiterte Lösung, bei der ein Entwickler sein eigenes (ggf. kaskadiertes) trainiertes Modell auf das Gerät übertragen und dort ausführen sowie möglicherweise Ergebnisse an ein anderes, selbst erstelltes IoT-Modul übergeben kann, das auf dem Gerät ausgeführt wird
- Eine Schleife zum erneuten Trainieren, um regelmäßig Bilder vom Gerät zu erfassen, das Modell in der Cloud neu zu trainieren und anschließend das neu trainierte Modell wieder an das Gerät zu pushen. Dabei bietet das Gerät die Möglichkeit, Modelle im laufenden Betrieb zu aktualisieren und auszutauschen.

## <a name="ai-workload-details"></a>Details zur KI-Workload
Die Workloadanwendung steht im [GitHub-Repository für die erweiterte Entwicklung mit Azure Percept](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app) als Open Source zur Verfügung und setzt sich aus zahlreichen kleinen C++-Modulen zusammen. Die wichtigsten davon sind:
- [main.cpp](https://github.com/microsoft/azure-percept-advanced-development/blob/main/azureeyemodule/app/main.cpp): Richtet alles ein und führt dann die Hauptschleife aus.
- [iot](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/iot): Dieser Ordner enthält Module für die Behandlung ein- und ausgehender Nachrichten des Azure IoT Edge-Hubs sowie die Methode zum Aktualisieren von Zwillingen.
- [model](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/model): Dieser Ordner enthält Module für eine Klassenhierarchie von Modellen für maschinelles Sehen.
- [kernels](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/kernels): Dieser Ordner enthält Module für G-API-Kernel, Vorgänge und C++-Wrapperfunktionen.

Entwickler können mithilfe dieser Workloadanwendung benutzerdefinierte Module erstellen oder das aktuelle Modul „azureeyemodule“ anpassen. 

## <a name="next-steps"></a>Nächste Schritte

- Nachdem Sie nun mehr über „azureeyemodule“ und die Azure Percept-Workload erfahren haben, können Sie versuchen, ein eigenes Modell oder eine eigene Pipeline zu verwenden. Folgen Sie hierzu einem der [Tutorials](https://github.com/microsoft/azure-percept-advanced-development/blob/main/tutorials/README.md).
- Alternativ können Sie den **Lerntransfer** mit einem unserer vorgefertigten [Notebooks für maschinelles Lernen](https://github.com/microsoft/azure-percept-advanced-development/tree/main/machine-learning-notebooks) ausprobieren.

