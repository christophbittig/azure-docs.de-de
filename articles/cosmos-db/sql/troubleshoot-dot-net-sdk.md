---
title: Diagnostizieren und Behandeln von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
description: Verwenden Sie Features wie clientseitige Protokollierung und andere Tools von Drittanbietern, um bei Verwenden des .NET SDK Probleme im Zusammenhang mit Azure Cosmos DB zu erkennen, zu diagnostizieren und zu beheben.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 03/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 26c16583dbbbc49d2f1be51d0cbf1a6e26e70299
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115418"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostizieren und Behandeln von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Dieser Artikel behandelt allgemeine Probleme, Problemumgehungen, Diagnoseschritte und Tools bei der Verwendung des [.NET SDK](sql-api-sdk-dotnet.md) mit Azure Cosmos DB-SQL-API-Konten.
Das .NET SDK bietet eine clientseitige logische Darstellung für den Zugriff auf die Azure Cosmos DB-SQL-API. Dieser Artikel beschreibt hilfreiche Tools und Vorgehensweisen für Problemfälle.

## <a name="checklist-for-troubleshooting-issues"></a>Checkliste für die Problembehandlung

Gehen Sie die folgende Prüfliste durch, bevor Sie Ihre Anwendung in die Produktion überführen. Mithilfe der Prüfliste werden einige häufige Probleme vermieden, die möglicherweise auftreten. Sie können ein Problem auch schnell diagnostizieren, sobald es auftritt:

*    Verwenden Sie das neueste [SDK](sql-api-sdk-dotnet-standard.md). SDKs in der Vorschauversion sollten nicht für die Produktion verwendet werden. Dadurch wird verhindert, dass bekannte Probleme, die bereits behoben wurden, erneut auftreten.
*    Überprüfen Sie die [Leistungstipps](performance-tips.md), und implementieren Sie die Empfehlungen. Dadurch können Skalierungs-, Latenz- und andere Leistungsprobleme vermieden werden.
*    Aktivieren Sie die SDK-Protokollierung, um die Problembehandlung zu unterstützen. Die Aktivierung der Protokollierung kann die Leistung beeinträchtigen, weshalb es am besten ist, sie nur für die Problembehandlung zu aktivieren. Sie können die folgenden Protokolle aktivieren:
*    [Protokollmetriken](../monitor-cosmos-db.md) im Azure-Portal. Metriken im Portal zeigen die Azure Cosmos DB-Telemetriedaten, die hilfreich sind, um festzustellen, ob das Problem im Zusammenhang mit Azure Cosmos DB steht oder ob es von der Clientseite kommt.
*    Protokollieren Sie die [DiagnosticsString](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)-Eigenschaft im V2 SDK oder die [Diagnostics](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)-Eigenschaft im V3 SDK in den Antworten der Punktoperationen.
*    Protokollieren Sie in allen Abfrageantworten die [SQL-Abfragemetriken](sql-api-query-metrics.md). 
*    Führen Sie das Setup für die [SDK-Protokollierung]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) aus.

Sehen Sie sich den Abschnitt [Häufig auftretende Probleme und Problemumgehungen](#common-issues-workarounds) in diesem Artikel an.

Überprüfen Sie den [Problemabschnitt auf GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues), der aktiv überwacht wird. Überprüfen Sie, ob Sie ein ähnliches Problem finden, für das bereits eine Problemumgehung dokumentiert wurde. Wenn Sie keine Lösung gefunden haben, legen Sie ein GitHub-Problem an. Bei dringenden Problemen können Sie ein Supportticket öffnen.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Häufig auftretende Probleme und Problemumgehungen

### <a name="general-suggestions"></a>Allgemeine Empfehlungen
* Führen Sie Ihre App nach Möglichkeit in der gleichen Azure-Region wie Ihr Azure Cosmos DB-Konto aus. 
* Aufgrund fehlender Ressourcen auf Ihrem Clientcomputer kann es zu Verbindungs-/Verfügbarkeitsproblemen kommen. Wir empfehlen, die CPU-Auslastung auf Knoten zu überwachen, auf denen der Azure Cosmos DB-Client ausgeführt wird, und hoch bzw. zentral zu skalieren, wenn ihre Last hoch ist.

### <a name="check-the-portal-metrics"></a>Überprüfen der Metriken im Portal
Das Überprüfen der [Metriken im Portal](../monitor-cosmos-db.md) hilft festzustellen, ob es sich um ein Problem auf Clientseite handelt oder ob es ein Problem mit dem Dienst gibt. Wenn die Metriken beispielsweise viele Anforderungen mit Ratenbegrenzungen aufweisen (HTTP-Statuscode 429), bedeutet dies, dass die Anforderung gedrosselt wird. Siehe dann den Abschnitt [Anforderungsrate zu hoch](troubleshoot-request-rate-too-large.md). 

## <a name="retry-logic"></a>Wiederholungslogik <a id="retry-logics"></a>
Das Cosmos DB SDK versucht bei jedem E/A-Fehler, den fehlgeschlagenen Vorgang zu wiederholen, sofern eine Wiederholung im SDK möglich ist. Es ist eine bewährte Methode, einen Wiederholungsversuch für jeden Fehler durchzuführen, doch müssen insbesondere Schreibfehler behandelt/wiederholt werden. Es wird empfohlen, das neueste SDK zu verwenden, da die Wiederholungslogik kontinuierlich verbessert wird.

1. E/A-Fehler bei Lese- und Abfragevorgängen werden vom SDK wiederholt, ohne dass sie dem Endbenutzer angezeigt werden.
2. Schreibvorgänge (Erstellen, Aktualisieren/Einfügen, Ersetzen, Löschen) sind nicht idempotent, und daher kann das SDK die fehlgeschlagenen Schreibvorgänge nicht immer blind wiederholen. Es ist erforderlich, dass die Anwendungslogik des Benutzers den Fehler behandelt und den Wiederholungsversuch durchführt.
3. Unter [Beheben von Problemen bei der Verfügbarkeit von SDKs](troubleshoot-sdk-availability.md) werden Wiederholungen für Cosmos DB-Konten in mehreren Regionen erläutert.

### <a name="retry-design"></a>Wiederholungsentwurf

Die Anwendung sollte so konzipiert sein, dass sie bei jeder Ausnahme einen Wiederholungsversuch ausführt, sofern es sich nicht um ein bekanntes Problem handelt, bei dem Wiederholungen nicht helfen. Anforderungstimeouts vom Typ 408 können beispielsweise vorübergehend sein, sodass Wiederholungsversuche möglicherweise erfolgreich sind. Bei diesen Fehlern sollte die Anwendung den Vorgang wiederholen. Bei Fehlern vom Typ 400 sollte die Anwendung dagegen keinen Wiederholungsversuch ausführen, da bei diesen Fehlern in der Regel ein Problem mit der Anforderung vorliegt, das zuerst gelöst werden muss. Wenn Sie für einen Fehler vom Typ 400 einen Wiederholungsversuch ausführen, wird das Problem nicht behoben, und derselbe Fehler tritt erneut auf. In der folgenden Tabelle sind bekannte Fehler aufgeführt, und in der Spalte „Wiederholbar“ ist angegeben, für welche Fehler Wiederholungsversuche ausgeführt sollten.

## <a name="common-error-status-codes"></a>Allgemeine Fehlerstatuscodes <a id="error-codes"></a>

| Statuscode | Wiederholbar | BESCHREIBUNG | 
|----------|-------------|-------------|
| 400 | Nein | Ungültige Anforderung (d. h. ungültiger JSON-Code, falsche Header, falscher Partitionsschlüssel im Header)| 
| 401 | Nein | [Nicht autorisiert](troubleshoot-unauthorized.md) | 
| 403 | Nein | [Verboten](troubleshoot-forbidden.md) |
| 404 | Nein | [Ressource nicht gefunden](troubleshoot-not-found.md) |
| 408 | Ja | [Timeout bei der Anforderung](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Nein | Konflikt: Die ID, die bei einem Schreibvorgang für eine Ressource angegeben wurde, wird bereits von einer vorhandenen Ressource verwendet. Verwenden Sie eine andere ID für die Ressource, um dieses Problem zu beheben. Die ID muss in allen Dokumenten mit demselben Partitionsschlüsselwert eindeutig sein. |
| 410 | Ja | Frühere Ausnahmen (vorübergehender Fehler, der nicht gegen die SLA verstößt) |
| 412 | Nein | Fehler bei Vorbedingung: Bei dem Vorgang wurde ein ETag angegeben, das sich von der auf dem Server verfügbaren Version unterscheidet. Es liegt also ein Fehler mit optimistischer Nebenläufigkeit vor. Wiederholen Sie die Anforderung nach dem Lesen der neuesten Version der Ressource und dem Aktualisieren des eTag für die Anforderung.
| 413 | Nein | [Anforderungsentität zu groß](../concepts-limits.md#per-item-limits) |
| 429 | Ja | Bei einem Fehler vom Typ 429 Vorgang kann der Vorgang sicher wiederholt werden. Sie können diesen Fehler vermeiden, indem Sie dem Link für [Zu viele Anforderungen](troubleshoot-request-rate-too-large.md) folgen.|
| 449 | Ja | Vorübergehender Fehler, der nur bei Schreibvorgängen auftritt und bei dem der Vorgang sicher wiederholt werden kann. Dieser Fehler kann auf ein Entwurfsproblem hinweisen, bei dem zu viele gleichzeitige Vorgänge versuchen, dasselbe Objekt in Cosmos DB zu aktualisieren. |
| 500 | Ja | Der Vorgang war aufgrund eines unerwarteten Dienstfehlers fehlerhaft. Senden Sie eine [Azure-Supportanfrage](https://aka.ms/azure-support), um den Support zu kontaktieren. |
| 503 | Ja | [Dienst nicht verfügbar](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT-Portauslastung (PAT)

Wenn Ihre App auf einem [virtuellen Azure-Computer ohne öffentliche IP-Adresse](../../load-balancer/load-balancer-outbound-connections.md) bereitgestellt wird, werden standardmäßig [Azure SNAT-Ports](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports) verwendet, um Verbindungen mit beliebigen Endpunkten außerhalb Ihres virtuellen Computers herzustellen. Die Anzahl zulässiger Verbindungen des virtuellen Computers mit dem Azure Cosmos DB-Endpunkt wird durch die [Azure SNAT-Konfiguration](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports) eingeschränkt. Diese Situation kann zu einer Bandbreiteneinschränkung der Verbindung, zum Beenden der Verbindung oder den oben erwähnten [Timeouts von Anforderungen](troubleshoot-dot-net-sdk-request-timeout.md) führen.

 Azure SNAT-Ports werden nur verwendet, wenn Ihr virtueller Computer eine private IP-Adresse besitzt und eine Verbindung mit einer öffentlichen IP-Adresse hergestellt wird. Es gibt zwei Problemumgehungen, um die Azure-SNAT-Einschränkung zu vermeiden (vorausgesetzt, dass Sie bereits eine einzelne Clientinstanz für die gesamte Anwendung verwenden):

* Fügen Sie Ihren Azure Cosmos DB-Dienstendpunkt dem Subnetz Ihres virtuellen Netzwerks von Azure Virtual Machines hinzu. Weitere Informationen finden Sie unter [Azure Virtual Network-Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md). 

    Wenn der Dienstendpunkt aktiviert ist, werden die Anforderungen nicht mehr von einer öffentlichen IP-Adresse an Azure Cosmos DB gesendet. Stattdessen wird die Identität des virtuellen Netzwerks und des Subnetzes gesendet. Diese Änderung kann zu Firewallproblemen führen, wenn nur öffentliche IP-Adressen zulässig sind. Wenn Sie eine Firewall verwenden und den Dienstendpunkt aktivieren, fügen Sie der Firewall mithilfe von [VNET-ACLs](/previous-versions/azure/virtual-network/virtual-networks-acl) ein Subnetz hinzu.
* Weisen Sie Ihrem [virtuellen Azure-Computer eine öffentliche IP-Adresse](../../load-balancer/troubleshoot-outbound-connection.md#assignilpip) zu.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Hohe Netzwerklatenz
Eine hohe Netzwerklatenz kann mithilfe der [DiagnosticsString](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)-Eigenschaft im V2 SDK oder der [Diagnostics](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)-Eigenschaft im V3 SDK ermittelt werden.

Wenn keine [Timeouts](troubleshoot-dot-net-sdk-request-timeout.md) vorliegen und die Diagnose einzelne Anforderungen zeigt, in denen die hohe Latenz ersichtlich ist.

# <a name="v3-sdk"></a>[V3 SDK](#tab/diagnostics-v3)

Die Diagnose kann aus jeder beliebigen `ResponseMessage`, `ItemResponse`, `FeedResponse` oder `CosmosException` durch die Eigenschaft `Diagnostics` abgerufen werden:

```csharp
ItemResponse<MyItem> response = await container.CreateItemAsync<MyItem>(item);
Console.WriteLine(response.Diagnostics.ToString());
```

Netzwerkinteraktionen in der Diagnose sind beispielsweise:

```json
{
    "name": "Microsoft.Azure.Documents.ServerStoreModel Transport Request",
    "id": "0e026cca-15d3-4cf6-bb07-48be02e1e82e",
    "component": "Transport",
    "start time": "12: 58: 20: 032",
    "duration in milliseconds": 1638.5957
}
```

Gibt an, wo der Wert `duration in milliseconds` die Latenz zeigt.

# <a name="v2-sdk"></a>[V2 SDK](#tab/diagnostics-v2)

Die Diagnose ist verfügbar, wenn der Client im [direkten Modus](sql-sdk-connection-modes.md) über die Eigenschaft `RequestDiagnosticsString` konfiguriert wird:

```csharp
ResourceResponse<Document> response = await client.ReadDocumentAsync(documentLink, new RequestOptions() { PartitionKey = new PartitionKey(partitionKey) });
Console.WriteLine(response.RequestDiagnosticsString);
```

Und die Latenz wäre die Differenz zwischen `ResponseTime` und `RequestStartTime`:

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```
--- 

Diese Latenz kann mehrere Ursachen haben:

* Die Anwendung wird nicht in der gleichen Region ausgeführt, in der sich auch Ihr Azure Cosmos DB-Konto befindet.
* Die [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations)- oder [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion)-Konfiguration ist falsch und versucht, eine Verbindung zu einer anderen Region herzustellen, in der die Anwendung derzeit ausgeführt wird.
* Durch hohen Datenverkehr kann es zu einem Engpass in der Netzwerkschnittstelle kommen. Wenn die Anwendung auf virtuellen Azure-Computern ausgeführt wird, gibt es mögliche Problemumgehungen:
    * Erwägen Sie die Erstellung eines [virtuellen Computers, auf dem der beschleunigte Netzwerkbetrieb aktiviert ist](../../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Aktivieren Sie den [beschleunigten Netzwerkbetrieb auf einem vorhandenen virtuellen Computer](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Verwenden Sie ggf. einen [leistungsfähigeren virtuellen Computer](../../virtual-machines/sizes.md).

### <a name="common-query-issues"></a>Häufige Probleme bei Abfragen

Anhand der [Metriken zu Abfragen](sql-api-query-metrics.md) können Sie bestimmen, wo die Abfrage die meiste Zeit verbringt. Sie können erkennen, wie viel Zeit im Back-End und auf dem Client verbracht wird. Weitere Informationen zur Behandlung von [Problemen bei der Abfrageleistung](troubleshoot-query-performance.md).

* Wenn die Back-End-Abfrage schnell zurückgegeben wird und viel Zeit auf den Client verbringt, überprüfen Sie die Last auf dem Computer. Es ist wahrscheinlich, dass nicht genügend Ressourcen vorhanden sind und das SDK darauf wartet, dass Ressourcen für die Bearbeitung der Antwort verfügbar werden.
* Wenn die Back-End-Abfrage langsam ist, versuchen Sie, [die Abfrage zu optimieren](troubleshoot-query-performance.md), und sehen Sie sich die aktuelle [Indizierungsrichtlinie](../index-overview.md) an.

    > [!NOTE]
    > Für eine bessere Leistung wird die Windows-64-Bit-Hostverarbeitung empfohlen. Das SQL SDK enthält die native Datei „ServiceInterop.dll“, um Abfragen lokal zu analysieren und zu optimieren. „ServiceInterop.dll“ wird nur auf der Windows x64-Plattform unterstützt. Bei Linux und anderen nicht unterstützten Plattformen, bei denen die Datei „ServiceInterop.dll“ nicht verfügbar ist, erfolgt ein zusätzlicher Netzwerkaufruf an das Gateway, um die optimierte Abfrage zu erhalten.

Wenn der Fehler `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` auftritt und Sie Windows verwenden, sollten Sie ein Upgrade auf die neueste Windows-Version ausführen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Leistungsrichtlinien für [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET V2](performance-tips.md)
* Weitere Informationen über [Reactor-basierte Java-SDKs](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
