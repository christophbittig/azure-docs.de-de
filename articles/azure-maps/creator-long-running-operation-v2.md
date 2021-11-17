---
title: 'Azure Maps: API V2 für zeitintensive Vorgänge'
description: Hier erfahren Sie mehr über die zeitintensive asynchrone V2-Hintergrundverarbeitung in Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: ba9ec841d4a1118a342032d81cbb534212b26786
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312770"
---
# <a name="creator-long-running-operation-api-v2"></a>Creator-API V2 für zeitintensive Vorgänge

Einige APIs in Azure Maps verwenden ein [asynchrones Anforderung-Antwort-Muster](/azure/architecture/patterns/async-request-reply). Dieses Muster ermöglicht Azure Maps die Bereitstellung hochverfügbarer und reaktionsfähiger Dienste. In diesem Artikel wird die für Azure Maps spezifische Implementierung der zeitintensiven asynchronen Hintergrundverarbeitung erläutert.

## <a name="submit-a-request"></a>Anforderung senden

Eine Clientanwendung startet einen zeitintensiven Vorgang durch den synchronen Aufruf einer HTTP-API. Dieser Befehl erfolgt in der Regel in Form einer HTTP POST-Anforderung. Bei erfolgreicher Erstellung einer asynchronen Workload gibt die API den HTTP-Statuscode `202` zurück, der angibt, dass die Anforderung akzeptiert wurde. Die Antwort enthält einen `Location`-Header, der auf einen Endpunkt verweist, den der Client abfragen kann, um den Status des zeitintensiven Vorgangs zu überprüfen.

### <a name="example-of-a-success-response"></a>Beispiel für eine erfolgreiche Antwort

```HTTP
Status: 202 Accepted
Operation-Location: https://atlas.microsoft.com/service/operations/{operationId} 

```

Wenn die Überprüfung für den Aufruf nicht erfolgreich war, gibt die API stattdessen die HTTP-Antwort `400` für eine ungültige Anforderung zurück. Der Antworttext liefert dem Client weitere Informationen darüber, warum die Anforderung ungültig war.

### <a name="monitor-the-operation-status"></a>Überwachen des Vorgangsstatus

Der in den akzeptierten Antwortheadern angegebene Standortendpunkt kann abgerufen werden, um den Status des zeitintensiven Vorgangs überprüfen. Der Antworttext der Vorgangsstatusanforderung enthält immer die Eigenschaften `status` und `created`. Die Eigenschaft `status` zeigt den aktuellen Status eines zeitintensiven Vorgangs an. Mögliche Werte sind `"NotStarted"`, `"Running"`, `"Succeeded"` und `"Failed"`. Die Eigenschaft `created` gibt die Zeit an, zu der die anfängliche Anforderung gesendet wurde, um den zeitintensiven Vorgang zu starten. Wenn der Zustand `"NotStarted"` oder `"Running"` lautet, enthält die Antwort auch einen Header vom Typ `Retry-After`. Mithilfe des Headers vom Typ `Retry-After` (gemessen in Sekunden) kann ermittelt werden, wann der nächste Abfrageaufruf für die Vorgangsstatus-API erfolgen soll.

### <a name="example-of-running-a-status-response"></a>Beispiel für das Ausführen einer Statusantwort

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handle-operation-completion"></a>Behandeln des Vorgangsabschlusses

Nach Abschluss des zeitintensiven Vorgangs lautet der Status der Antwort entweder `"Succeeded"` oder `"Failed"`. Alle Antworten geben einen HTTP-Code „200 OK“ zurück. Wenn eine neue Ressource aus einem zeitintensiven Vorgang erstellt wurde, enthält die Antwort auch einen `Resource-Location`-Header, der auf Metadaten zur Ressource verweist. Bei einem Fehler enthält die Antwort die eine `error`-Eigenschaft im Textkörper. Die Fehlerdaten entsprechen der OData-Fehlerspezifikation.

### <a name="example-of-success-response"></a>Beispiel für eine Erfolgsantwort

```HTTP
Status: 200 OK
Resource-Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "2021-05-06T07:55:19.5256829+00:00",
    "status": "Succeeded"
}
```

### <a name="example-of-failure-response"></a>Beispiel für eine Fehlerantwort

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "created": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```
