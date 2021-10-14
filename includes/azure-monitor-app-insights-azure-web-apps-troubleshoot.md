---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.openlocfilehash: 327ab19750615c7912eeffdd520efa896314f1d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700141"
---
### <a name="connection-string-and-instrumentation-key"></a>Verbindungszeichenfolge und Instrumentierungsschlüssel

Bei Verwendung von Überwachung ohne Code ist nur die Verbindungszeichenfolge erforderlich. Es wird jedoch weiterhin empfohlen, den Instrumentierungsschlüssel festzulegen, um die Abwärtskompatibilität mit älteren Versionen des SDKs aufrechtzuerhalten, wenn eine manuelle Instrumentierung ausgeführt wird.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Unterschied zwischen Standardmetriken von Application Insights und Azure App Service-Metriken

Application Insights sammelt Telemetriedaten für die Anforderungen, die bis zur Anwendung gelangt sind. Wenn der Fehler in WebApps/WebServer aufgetreten ist und die Anforderung die Benutzeranwendung nicht erreicht hat, weist Application Insights keine Telemetriedaten dazu auf.

Die von Application Insights berechnete Dauer für `serverresponsetime` stimmt nicht unbedingt mit der von Web-Apps beobachteten Serverantwortzeit überein. Dies liegt daran, dass Application Insights nur die Dauer zählt, wenn die Anforderung tatsächlich die Benutzeranwendung erreicht. Wenn die Anforderung in WebServer hängenbleibt oder in die Warteschlange gestellt wird, ist diese Wartezeit in den Web-App-Metriken enthalten, in den Application Insights-Metriken jedoch nicht.


