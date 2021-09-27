---
title: Konfigurieren von Cloudmetriken und Protokollen für selbstgehostete Gateways für Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Cloudmetriken und Protokolle für selbstgehostete Gateways für Azure API Management konfigurieren.
services: api-management
documentationcenter: ''
author: dlepow
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: danlep
ms.openlocfilehash: 5661218fd9e1849eb3133496d8e2640753eb2fa1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659928"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Konfigurieren von Cloudmetriken und Protokollen für selbstgehostete Gateways für Azure API Management

Dieser Artikel enthält Details zum Konfigurieren von Cloudmetriken und Protokollen für das [selbstgehostete Gateway](./self-hosted-gateway-overview.md).

Das selbstgehostete Gateway muss einem API Management-Dienst zugeordnet werden und erfordert eine ausgehende TCP/IP-Verbindung mit Azure über Port 443. Das Gateway nutzt die ausgehende Verbindung, um Telemetriedaten an Azure zu senden (sofern dies konfiguriert ist). 

## <a name="metrics"></a>Metriken
Standardmäßig gibt das selbstverwaltete Gateway eine Reihe von Metriken über [Azure Monitor](https://azure.microsoft.com/services/monitor/) aus (wie das verwaltete Gateway [in der Cloud](api-management-howto-use-azure-monitor.md)). 

Das Feature kann über den Schlüssel `telemetry.metrics.cloud` in der ConfigMap der Gatewaybereitstellung aktiviert oder deaktiviert werden. Nachfolgend sind die verfügbaren Konfigurationen gezeigt:

| Feld  | Standard | BESCHREIBUNG |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | Aktivieren der Protokollierung über Azure Monitor. Mögliche Werte: `true` oder `false`. |


Es folgt eine Beispielkonfiguration:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

Das selbstgehostete Gateway gibt derzeit die folgenden Metriken über Azure Monitor aus:

| Metrik  | BESCHREIBUNG |
| ------------- | ------------- |
| Requests  | Anzahl der API-Anforderungen innerhalb des Zeitraums |
| Dauer der Gatewayanforderungen | Anzahl von Millisekunden zwischen dem Zeitpunkt, zu dem das Gateway die Anforderung empfangen hat, und dem Zeitpunkt, zu dem die Antwort vollständig gesendet wurde |
| Dauer der Back-End-Anforderungen | Anzahl von Millisekunden für alle Back-End-E/A-Vorgänge (Verbindungsherstellung, Senden und Empfangen von Bytes)  |

## <a name="logs"></a>Protokolle

Das selbstgehostete Gateway sendet aktuell keine [Diagnoseprotokolle](./api-management-howto-use-azure-monitor.md#activity-logs) an die Cloud. Sie können jedoch [Protokolle auf dem lokalen Datenträger konfigurieren und persistent speichern](how-to-configure-local-metrics-logs.md), auf dem das selbstgehostete Gateway bereitgestellt wird. 

Wenn ein Gateway in [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) bereitgestellt ist, können Sie [Azure Monitor für Container](../azure-monitor/containers/container-insights-overview.md) aktivieren, um Protokolle aus Ihren Containern zu sammeln und in Log Analytics anzuzeigen. 


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum selbstgehosteten Gateway finden Sie unter [Azure API Management: Übersicht über das selbstgehostete Gateway](self-hosted-gateway-overview.md).
* Weitere Informationen zum [Konfigurieren und lokalen Speichern von Protokollen in persistentem Speicher](how-to-configure-local-metrics-logs.md)
