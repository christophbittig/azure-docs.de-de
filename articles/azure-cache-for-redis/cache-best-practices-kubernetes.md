---
title: Bewährte Methoden zum Hosten einer Kubernetes-Clientanwendung
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie eine Kubernetes-Clientanwendung hosten, die Azure Cache for Redis verwendet.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 5d61b7047262c86471dba988bd0f80cb4fdac8ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663628"
---
# <a name="kubernetes-hosted-client-application"></a>Von Kubernetes gehostete Clientanwendung

## <a name="multiple-pods"></a>Mehrere Pods

Wenn von mehreren Pods eine Verbindung mit einem Redis-Server hergestellt wird, stellen Sie sicher, dass die neuen Verbindungen von den Pods gestaffelt erstellt werden. Wenn innerhalb kurzer Zeit mehrere Pods gleichzeitig gestartet werden, führt dies zu einer plötzlichen Spitze in der Anzahl erstellter Clientverbindungen. Die hohe Anzahl von Verbindungen führt zu einer hohen Belastung des Redis-Servers und kann Timeouts verursachen.

Vermeiden Sie dasselbe Szenario, das durch gleichzeitiges Herunterfahren mehrerer Pods entsteht. Wenn das Herunterfahren nicht gestaffelt wird, kann dies zu einem erheblichen Abfall der Anzahl von Verbindungen und somit zur CPU-Auslastung führen.

## <a name="sufficient-resources"></a>Ausreichende Ressourcen

Stellen Sie sicher, dass der Pod, der eine Verbindung mit dem Redis-Server herstellt, auf einem Kubernetes-Knoten gehostet wird, der über ausreichend Arbeitsspeicher, CPU und Netzwerkbandbreite verfügt.  

## <a name="noisy-neighbor-problem"></a>Noisy Neighbor-Problem

Verhindern Sie das *Noisy Neighbor*-Problem. Ein Pod, auf dem der Client ausgeführt wird, kann von anderen auf demselben Knoten ausgeführten Pods beeinträchtigt werden. Dies kann zur Drosselung von Redis-Verbindungen oder E/A-Vorgänge führen.

## <a name="next-steps"></a>Nächste Schritte

- [Entwicklung](cache-best-practices-development.md)
- [Häufig gestellte Fragen zur Azure Cache for Redis-Entwicklung](cache-development-faq.yml)
