---
title: Überwachen von Daten in Azure Queue Storage – Referenz
description: Protokoll- und Metrikenreferenz für die Überwachung von Daten in Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 04/20/2021
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 58f2771cd8bf9704a098cdad5d72131b0a67bad1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804435"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Überwachen von Daten in Azure Queue Storage – Referenz

Ausführliche Informationen über das Erfassen und Analysieren von Überwachungsdaten für Azure Storage finden Sie unter [Überwachen von Azure Storage](monitor-queue-storage.md).

## <a name="metrics"></a>Metriken

In den nachstehenden Tabellen sind die für Azure Storage erfassten Plattformmetriken aufgelistet.

### <a name="capacity-metrics"></a>Kapazitätsmetriken

Kapazitätsmetriken werden täglich aktualisiert (bis zu 24 Stunden). Das Aggregationsintervall definiert das Zeitintervall, für das Metrikwerte dargestellt werden. Das unterstützte Aggregationsintervall für alle Kapazitätsmetriken beträgt eine Stunde (PT1H).

Azure Storage stellt in Azure Monitor folgende Kapazitätsmetriken bereit:

#### <a name="account-level-capacity-metrics"></a>Kapazitätsmetriken auf Kontoebene

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage-Metriken

Diese Tabelle enthält [Queue Storage-Metriken](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Metrik | BESCHREIBUNG |
| ------------------- | ----------------- |
| **QueueCapacity** | Der vom Speicherkonto beanspruchte Queue Storage. <br><br> Einheit: `Bytes` <br> Aggregationstyp: `Average` <br> Wertbeispiel: `1024` |
| **QueueCount** | Die Anzahl von Warteschlangen im Speicherkonto. <br><br> Einheit: `Count` <br> Aggregationstyp: `Average` <br> Wertbeispiel: `1024` |
| **QueueMessageCount** | Die Anzahl nicht abgelaufener Warteschlangennachrichten im Speicherkonto. <br><br> Einheit: `Count` <br> Aggregationstyp: `Average` <br> Wertbeispiel: `1024` |

### <a name="transaction-metrics"></a>Transaktionsmetriken

Transaktionsmetriken werden für jede Anforderung an ein Speicherkonto aus Azure Storage an Azure Monitor ausgegeben. Falls in Ihrem Speicherkonto keine Aktivität stattfindet, gibt es in diesem Zeitraum keine Daten zu Transaktionsmetriken. Alle Transaktionsmetriken stehen sowohl auf der Konto- als auch auf der Dienstebene von Queue Storage zur Verfügung. Das Aggregationsintervall definiert das Zeitintervall für die dargestellten Metrikwerte. Für alle Transaktionsmetriken werden die Aggregationsintervalle PT1H und PT1M unterstützt.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrikdimensionen

Azure Storage unterstützt folgende Dimensionen für Metriken in Azure Monitor:

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Ressourcenprotokolle (Vorschau)

> [!NOTE]
> Azure Storage-Protokolle in Azure Monitor befinden sich in der öffentlichen Vorschauphase und stehen in allen Regionen der öffentlichen und der US Government-Cloud für Vorschautests zur Verfügung. Diese Vorschauversion ermöglicht Protokolle für Blobs (einschließlich Azure Data Lake Storage Gen2), Dateien, Warteschlangen, Tabellen, Universell V1-Premium-Speicherkonten und Universell V2-Speicherkonten. Klassische Speicherkonten werden nicht unterstützt.

In der nachstehenden Tabelle sind die Eigenschaften für Azure Storage-Ressourcenprotokolle aufgelistet, wenn diese in Azure Monitor-Protokollen oder Azure Storage gesammelt werden. Diese Eigenschaften beschreiben den Vorgang, den Dienst und den Autorisierungstyp, der zum Ausführen des Vorgangs verwendet wurde.

### <a name="fields-that-describe-the-operation"></a>Den Vorgang beschreibende Felder

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Felder, die beschreiben, wie der Vorgang authentifiziert wurde

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Den Dienst beschreibende Felder

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung von Azure Queue Storage finden Sie unter [Überwachen von Azure Queue Storage](monitor-queue-storage.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
