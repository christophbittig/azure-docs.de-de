---
title: Die besten Methoden zum Skalieren
titleSuffix: Azure Cache for Redis
description: Erfahren Sie, wie Sie Azure Cache for Redis skalieren können.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: b0d43e062db591600d1cbcc0cdb18b7ce6b8ebac
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323503"
---
# <a name="scaling"></a>Skalierung

## <a name="scaling-under-load"></a>Skalieren unter Belastung

Wenn Sie einen Cache unter Belastung skalieren, konfigurieren Sie die Einstellung „maxmemory-reserved“, um die Reaktionsfähigkeit des Systems zu verbessern. Weitere Informationen finden Sie unter [Konfigurieren der Einstellung „maxmemory-reserved“](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting).

## <a name="scaling-clusters"></a>Skalieren von Clustern

Versuchen Sie, die Daten im Cache so weit wie möglich zu reduzieren, bevor Sie Ihren gruppierten Cache ab- oder aufskalieren. Durch das Reduzieren von Daten wird sichergestellt, dass kleinere Datenmengen verschoben werden müssen, wodurch sich der Zeitaufwand für den Skalierungsvorgang verringert. Weitere Informationen zum Skalierungszeitpunkt finden Sie unter [Zeitpunkt für die Skalierung](cache-how-to-scale.md#when-to-scale).

## <a name="scale-before-load-is-too-high"></a>Skalieren vor Erreichen einer zu hohen Auslastung

Beginnen Sie mit der Skalierung, bevor die Server- oder Speicherauslastung zu hoch wird. Wenn sie zu hoch ist, weist dies auf die Auslastung des Redis-Servers hin. Ein ausgelasteter Redis-Server verfügt nicht über genügend Ressourcen, um Daten zu skalieren und neu zu verteilen.

## <a name="cache-sizes"></a>Cachegrößen

Wenn Sie TLS verwenden und über eine hohe Anzahl von Verbindungen verfügen, sollten Sie eine Aufskalierung in Betracht ziehen, um die Last auf mehr Kerne zu verteilen. Einige Cachegrößen werden auf VMs mit mindestens vier Kernen gehostet.

Verteilen Sie die Workloads der TLS-Verschlüsselung/-Entschlüsselung sowie der TLS-Verbindung/-Verbindungstrennung auf mehrere Kerne, um die CPU-Gesamtauslastung der Cache-VMs zu senken. Weitere Informationen finden Sie unter [Details zu VM-Größen und -Kernen.](./cache-planning-faq.yml#azure-cache-for-redis-performance)

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren der Einstellung „maxmemory-reserved“](cache-best-practices-memory-management.md#configure-your-maxmemory-reserved-setting)
- [Skalieren einer Azure Cache for Redis-Instanz](cache-how-to-scale.md)
