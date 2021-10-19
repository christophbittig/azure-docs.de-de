---
title: Bewährte Methoden zum Hosten einer Kubernetes-Clientanwendung
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie eine Kubernetes-Clientanwendung hosten, die Azure Cache for Redis verwendet.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: shpathak
ms.openlocfilehash: 33628fc16dfc2693cad0f8cd13f555cc10e55f25
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808733"
---
# <a name="kubernetes-hosted-client-application"></a>Von Kubernetes gehostete Clientanwendung

## <a name="client-connections-from-multiple-pods"></a>Clientverbindungen von mehreren Pods

Wenn von mehreren Pods eine Verbindung mit einem Redis-Server hergestellt wird, stellen Sie sicher, dass die neuen Verbindungen von den Pods gestaffelt erstellt werden. Wenn innerhalb kurzer Zeit mehrere Pods gleichzeitig gestartet werden, führt dies zu einer plötzlichen Spitze in der Anzahl erstellter Clientverbindungen. Die hohe Anzahl von Verbindungen führt zu einer hohen Belastung des Redis-Servers und kann Timeouts verursachen.

Vermeiden Sie dasselbe Szenario, das durch gleichzeitiges Herunterfahren mehrerer Pods entsteht. Wenn das Herunterfahren nicht gestaffelt wird, kann dies zu einem erheblichen Abfall der Anzahl von Verbindungen und somit zur CPU-Auslastung führen.

## <a name="sufficient-pod-resources"></a>Ausreichende Podressourcen

Stellen Sie sicher, dass dem Pod, auf dem Ihre Clientanwendung ausgeführt wird, genügend CPU- und Arbeitsspeicherressourcen zur Verfügung stehen. Wenn die Clientanwendung bis an die Grenzen ihrer Ressourcen ausgeführt wird, kann dies zu Timeouts führen.

## <a name="sufficient-node-resources"></a>Ausreichende Knotenressourcen

Ein Pod, auf dem die Clientanwendung ausgeführt wird, kann von anderen auf demselben Knoten ausgeführten Pods beeinträchtigt werden. Dies kann zur Drosselung von Redis-Verbindungen oder E/A-Vorgänge führen. Stellen Sie daher immer sicher, dass der Knoten, auf dem Ihre Clientanwendungspods ausgeführt werden, über genügend Arbeitsspeicher- und CPU-Ressourcen sowie Netzwerkbandbreite verfügen. Wenn eine dieser Ressourcen nicht verfügbar ist, kann dies zu Konnektivitätsproblemen führen.

## <a name="linux-hosted-client-applications-and-tcp-settings"></a>Unter Linux gehostete Clientanwendungen und TCP-Einstellungen

Wenn Ihre Azure Cache for Redis-Clientanwendung in einem Linux-basierten Container ausgeführt wird, empfiehlt es sich, einige TCP-Einstellungen wie unter [TCP-Einstellungen für unter Linux gehostete Clientanwendungen](cache-best-practices-connection.md#tcp-settings-for-linux-hosted-client-applications) aufgeführt zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

- [Entwicklung](cache-best-practices-development.md)
- [Häufig gestellte Fragen zur Azure Cache for Redis-Entwicklung](cache-development-faq.yml)
