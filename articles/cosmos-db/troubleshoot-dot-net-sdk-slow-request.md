---
title: Problembehandlung von langsamen Azure Cosmos DB-Anforderungen mit dem .NET SDK
description: Hier erfahren Sie, wie Sie langsame Azure Cosmos DB-Anforderungen mit dem .NET SDK diagnostizieren und beheben können.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 06/15/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: abee066553c4ee338000efd39ecd3b0bfd11d171
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355741"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-slow-requests"></a>Diagnose und Problembehandlung von langsamen Azure Cosmos DB-Anforderungen mit dem .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Langsame Azure Cosmos DB-Anforderungen können aus verschiedenen Gründen auftreten, z. B. durch die Einschränkung von Anforderungen oder die Art, wie Ihre Anwendung konzipiert ist. Dieser Artikel erklärt die verschiedenen Grundursachen für dieses Problem. 

## <a name="request-rate-too-large-429-throttles"></a>Zu hohe Anforderungsrate (429 Einschränkungen)

Die Einschränkung von Anforderungen ist der häufigste Grund für langsame Anforderungen. Azure Cosmos DB schränkt Anforderungen ein, wenn sie die zugewiesenen RUs für die Datenbank oder den Container überschreiten. Das SDK verfügt über eine integrierte Logik zur Wiederholung dieser Anforderungen. Der Artikel zur Problembehandlung für [Zu hohe Anforderungsrate](troubleshoot-request-rate-too-large.md#how-to-investigate) erklärt, wie Sie überprüfen können, ob die Anforderungen eingeschränkt werden und wie Sie Ihr Konto skalieren können, um diese Probleme in Zukunft zu vermeiden.

## <a name="application-design"></a>Anwendungsentwurf

Wenn Ihre Anwendung nicht den Best Practices des SDK entspricht, kann es zu verschiedenen Problemen kommen, die zu langsamen oder fehlgeschlagenen Anforderungen führen. Befolgen Sie die [Best Practices zum .NET SDK](performance-tips-dotnet-sdk-v3-sql.md), um die beste Leistung zu erzielen.

Beachten Sie bei der Entwicklung Ihrer Anwendung die folgenden Punkte:
* Die Anwendung sollte sich in der gleichen Region befinden wie Ihr Azure Cosmos DB-Konto.
* Singleton-Instanz der SDK-Instanz. Das SDK hat mehrere Caches, die initialisiert werden müssen, was die ersten Anforderungen verlangsamen kann. 
* Verwenden Sie „Direkt/TCP“ als Konnektivitätsmodus.
* Vermeiden Sie eine hohe CPU-Auslastung. Achten Sie auf die maximale CPU-Auslastung und nicht auf den Durchschnitt, der bei den meisten Protokollierungssystemen voreingestellt ist. Für alles, was über etwa 40 % liegt, ist eine höhere Wartezeit möglich.


## <a name="capture-the-diagnostics"></a><a name="capture-diagnostics"></a>Erfassen der Diagnosen

Alle Antworten im SDK, einschließlich `CosmosException`, haben die Eigenschaft „Diagnose“. In dieser Eigenschaft werden alle Informationen zu der einzelnen Anforderung aufgezeichnet, einschließlich der Frage, ob es Wiederholungsversuche oder vorübergehende Fehlschläge gab. 

Die Diagnosen werden als Zeichenfolge zurückgegeben. Die Zeichenfolge ändert sich mit jeder Version, da sie verbessert wird, um die Problembehandlung für verschiedene Szenarios zu verbessern. Mit jeder Version des SDK wird die Formatierung der Zeichenfolge geändert. Analysieren Sie die Zeichenfolge nicht, um Breaking Changes zu vermeiden. Das folgende Codebeispiel zeigt, wie Diagnoseprotokolle mit dem .NET SDK gelesen werden:

```c#
try
{
    ItemResponse<Book> response = await this.Container.CreateItemAsync<Book>(item: testItem);
    if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan)
    {
        // Log the diagnostics and add any additional info necessary to correlate to other logs 
        Console.Write(response.Diagnostics.ToString());
    }
}catch(CosmosException cosmosException){
    // Log the full exception including the stack trace 
    Console.Write(cosmosException.ToString());
    // The Diagnostics can be logged separately if required.
    Console.Write(cosmosException.Diagnostics.ToString());
}

ResponseMessage response = await this.Container.CreateItemStreamAsync(partitionKey, stream);
if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan || IsFailureStatusCode(response.StatusCode))
{
    // Log the diagnostics and add any additional info necessary to correlate to other logs 
    Console.Write(response.Diagnostics.ToString());
}
```


## <a name="diagnostics-in-version-319-and-higher"></a>Diagnosen in Version 3.19 und höher
Die JSON-Struktur hat sich mit jeder Version des SDK geändert. Daher werden sie beim Analysieren unsicher. Der JSON-Code stellt eine Baumstruktur der Anforderungen dar, die das SDK durchlaufen. Dies deckt einige der wichtigsten Dinge ab, die zu beachten sind:

### <a name="cpu-history"></a><a name="cpu-history"></a>CPU-Verlauf
Eine hohe CPU-Auslastung ist die häufigste Ursache für langsame Anforderungen. Für optimale Latenz sollte die CPU-Auslastung ungefähr 40 Prozent betragen. Verwenden Sie 10 Sekunden als Intervall zur Überwachung der maximalen (nicht durchschnittlichen) CPU-Nutzung. CPU-Spitzen treten häufiger bei partitionsübergreifenden Abfragen auf, bei denen die Anforderungen möglicherweise mehrere Konnektivitäten für eine einzige Abfrage erfordern.

Wenn der Fehler `TransportException`-Informationen enthält, könnte er auch `CPU History`-Informationen enthalten:

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* Wenn die CPU-Auslastung über 70 % liegt, ist die Zeitüberschreitung wahrscheinlich auf eine CPU-Erschöpfung zurückzuführen. In diesem Fall besteht die Lösung darin, die Quelle der hohen CPU-Auslastung zu untersuchen und sie zu verringern oder den Computer auf eine größere Ressourcengröße zu skalieren.
* Wenn die CPU-Messungen nicht alle 10 Sekunden erfolgen, deuten die Lücken oder Messzeiten auf größere Zeitabstände zwischen den Messungen hin. In einem solchen Fall liegt die Ursache in einem Thread-Mangel. Die Lösung besteht darin, die Quelle(n) des Thread-Mangels zu untersuchen (möglicherweise gesperrte Threads) oder den/die Rechner auf eine größere Ressourcengröße zu skalieren.

#### <a name="solution"></a>Lösung:
Die Clientanwendung, die das SDK verwendet, sollte entsprechend skaliert werden.


### <a name="httpresponsestats"></a><a name="httpResponseStats"></a>HttpResponseStats
HttpResponseStats sind Anforderungen, die an [gateway](sql-sdk-connection-modes.md) gerichtet sind. Selbst im direkten Modus erhält das SDK alle Metadaten vom Gateway.

Wenn die Anforderung langsam ist, prüfen Sie zunächst, ob die oben genannten Vorschläge nicht zu Ergebnissen führen.

Wenn sie immer noch langsam ist, deuten verschiedene Muster auf unterschiedliche Probleme hin:

Einzelspeicherergebnis für eine einzelne Anforderung

| Anzahl von Anforderungen | Szenario | BESCHREIBUNG | 
|----------|-------------|-------------|
| Einzeln bis alle | Zeitüberschreitung bei Anforderungen oder HttpRequestExceptions | Weist auf [SNAT-Portüberlastung](troubleshoot-dot-net-sdk.md#snat) oder fehlende Ressourcen auf dem Computer zur rechtzeitigen Bearbeitung von Anforderungen hin. |
| Einzelne oder kleiner Prozentsatz (SLA wird nicht verletzt) | All | Ein einzelner oder kleiner Prozentsatz langsamer Anforderungen kann durch verschiedene vorübergehende Probleme verursacht werden und sollte erwartet werden. | 
| Alle | Alle | Weist auf ein Issue mit der Infrastruktur oder dem Netz hin. |
| SLA-Verletzung | Keine Änderungen an der Anwendung und SLA verworfen | Weist auf ein Issue mit dem Azure Cosmos DB-Dienst hin. |

```json
"HttpResponseStats": [
    {
        "StartTimeUTC": "2021-06-15T13:53:09.7961124Z",
        "EndTimeUTC": "2021-06-15T13:53:09.7961127Z",
        "RequestUri": "https://127.0.0.1:8081/dbs/347a8e44-a550-493e-88ee-29a19c070ecc/colls/4f72e752-fa91-455a-82c1-bf253a5a3c4e",
        "ResourceType": "Collection",
        "HttpMethod": "GET",
        "ActivityId": "e16e98ec-f2e3-430c-b9e9-7d99e58a4f72",
        "StatusCode": "OK"
    }
]
```

### <a name="storeresult"></a><a name="storeResult"></a>StoreResult
StoreResult stellt eine einzelne Anforderung an Azure Cosmos DB dar, die den direkten Modus mit TCP-Protokoll verwendet.

Wenn sie immer noch langsam ist, deuten verschiedene Muster auf unterschiedliche Probleme hin:

Einzelspeicherergebnis für eine einzelne Anforderung

| Anzahl von Anforderungen | Szenario | BESCHREIBUNG | 
|----------|-------------|-------------|
| Einzeln bis alle | StoreResult enthält TransportException | Weist auf [SNAT-Portüberlastung](troubleshoot-dot-net-sdk.md#snat) oder fehlende Ressourcen auf dem Computer zur rechtzeitigen Bearbeitung von Anforderungen hin. |
| Einzelne oder kleiner Prozentsatz (SLA wird nicht verletzt) | Alle | Ein einzelner oder kleiner Prozentsatz langsamer Anforderungen kann durch verschiedene vorübergehende Probleme verursacht werden und sollte erwartet werden. | 
| Alle | All | Weist auf ein Issue mit der Infrastruktur oder dem Netzwerk hin. |
| SLA-Verletzung | Anforderungen enthalten mehrere Fehlercodes wie 410, und IsValid ist „true“ | Weist auf ein Issue mit dem Azure Cosmos DB-Dienst hin. |
| SLA-Verletzung | Anforderungen enthalten mehrere Fehlercodes wie 410 und IsValid ist „false“ | Weist auf ein Issue mit dem Computer hin. |
| SLA-Verletzung | StorePhysicalAddress ist identisch ohne Fehlerstatuscode | Wahrscheinlich ein Issue mit dem Cosmos DB-Dienst. |
| SLA-Verletzung | StorePhysicalAddress hat die gleiche Partitions-ID, aber unterschiedliche Replikat-IDs ohne Fehlerstatuscode | Wahrscheinlich ein Issue mit dem Cosmos DB-Dienst. |
| SLA-Verletzung | StorePhysicalAddress sind zufällig und ohne Fehlerstatuscode | Weist auf ein Issue mit dem Computer hin. |

Mehrere StoreResults für eine einzelne Anforderung:

* Konsistenz bei starker und begrenzter Veraltung hat immer mindestens zwei Speicherergebnisse
* Überprüfen Sie den Statuscode der einzelnen StoreResults. Das SDK führt bei mehreren verschiedenen [vorübergehenden Fehlschlägen](troubleshoot-dot-net-sdk-request-timeout.md) automatisch Wiederholungsversuche durch. Das SDK wird ständig verbessert, um mehr Szenarios abzudecken. 

### <a name="rntbdrequeststats"></a><a name="rntbdRequestStats"></a>RntbdRequestStats 
Zeigt die Zeit für die verschiedenen Phasen des Sendens und Empfangens einer Anforderung in der Datentransportebene an.

* ChannelAcquisitionStarted: Die Zeit für das Abrufen oder Erstellen einer neuen Verbindung. Neue Verbindungen können für zahlreiche verschiedene Regionen erstellt werden. Zum Beispiel wurde eine Verbindung unerwartet geschlossen oder es wurden zu viele Anforderungen über die bestehenden Verbindungen gesendet, sodass eine neue Verbindung erstellt wird. 
* Die Pipeline-Time ist groß, was auf eine möglicherweise große Anforderung hinweist.
* Die Transit-Time ist groß, was auf ein Netzproblem hindeutet. Vergleichen Sie diese Zahl mit `BELatencyInMs`. Wenn BELatencylnMs klein ist, wurde die Zeit im Netz und nicht im Azure Cosmos DB-Dienst verbraucht.
* Ist die Received-Time groß, deutet dies auf ein Thread-Mangel-Issue hin. Dies ist die Zeit zwischen dem Erhalt der Antwort und der Rückgabe des Ergebnisses.

```json
"StoreResult": {
    "ActivityId": "a3d325c1-f4e9-405b-820c-bab4d329ee4c",
    "StatusCode": "Created",
    "SubStatusCode": "Unknown",
    "LSN": 1766,
    "PartitionKeyRangeId": "0",
    "GlobalCommittedLSN": -1,
    "ItemLSN": -1,
    "UsingLocalLSN": false,
    "QuorumAckedLSN": 1765,
    "SessionToken": "-1#1766",
    "CurrentWriteQuorum": 1,
    "CurrentReplicaSetSize": 1,
    "NumberOfReadRegions": 0,
    "IsClientCpuOverloaded": false,
    "IsValid": true,
    "StorePhysicalAddress": "rntbd://127.0.0.1:10253/apps/DocDbApp/services/DocDbServer92/partitions/a4cb49a8-38c8-11e6-8106-8cdcd42c33be/replicas/1p/",
    "RequestCharge": 11.05,
    "BELatencyInMs": "7.954",
    "RntbdRequestStats": [
        {
            "EventName": "Created",
            "StartTime": "2021-06-15T13:53:10.1302477Z",
            "DurationInMicroSec": "6383"
        },
        {
            "EventName": "ChannelAcquisitionStarted",
            "StartTime": "2021-06-15T13:53:10.1366314Z",
            "DurationInMicroSec": "96511"
        },
        {
            "EventName": "Pipelined",
            "StartTime": "2021-06-15T13:53:10.2331431Z",
            "DurationInMicroSec": "50834"
        },
        {
            "EventName": "Transit Time",
            "StartTime": "2021-06-15T13:53:10.2839774Z",
            "DurationInMicroSec": "17677"
        },
        {
            "EventName": "Received",
            "StartTime": "2021-06-15T13:53:10.3016546Z",
            "DurationInMicroSec": "7079"
        },
        {
            "EventName": "Completed",
            "StartTime": "2021-06-15T13:53:10.3087338Z",
            "DurationInMicroSec": "0"
        }
    ],
    "TransportException": null
}
```

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Fehlerrate verstößt gegen die Azure Cosmos DB SLA
Wenden Sie sich an den [Azure-Support](https://aka.ms/azure-support).

## <a name="next-steps"></a>Nächste Schritte
* [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
* Weitere Informationen zu Leistungsrichtlinien für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET Version 2](performance-tips.md)
