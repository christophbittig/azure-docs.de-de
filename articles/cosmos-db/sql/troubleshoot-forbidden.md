---
title: Behandeln von Azure Cosmos DB-Ausnahmen des Typs „Unzulässig“
description: Erfahren Sie mehr über die Diagnose und Behebung der Ausnahme „Unzulässig“.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: dcef0eccc6ca314c51b436025fb10c25dbbe04fa
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658232"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnostizieren und Behandeln von Azure Cosmos DB-Ausnahmen des Typs „Unzulässig“
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Der HTTP-Statuscode 403 bedeutet, dass die Anforderung unzulässig ist und nicht abgeschlossen werden kann.

## <a name="firewall-blocking-requests"></a>Durch Firewall blockierte Anforderungen
Bei diesem Szenario tauchen häufig Fehler wie die folgenden auf:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Lösung
Vergewissern Sie sich, dass Ihre aktuellen [Firewalleinstellungen](../how-to-configure-firewall.md) ordnungsgemäß sind und die IP-Adressen oder Netzwerke einschließen, über die Sie versuchen, eine Verbindung herzustellen.
Wenn Sie sie vor Kurzem aktualisiert haben, bedenken Sie, dass das Übernehmen von Änderungen **bis zu 15 Minuten** dauern kann.

## <a name="partition-key-exceeding-storage"></a>Partitionsschlüssel überschreitet Speicher
Bei diesem Szenario tauchen häufig Fehler wie die folgenden auf:

```
Response status code does not indicate success: Forbidden (403); Substatus: 1014
```

```
Partition key reached maximum size of {...} GB
```

### <a name="solution"></a>Lösung
Dieser Fehler bedeutet, dass Ihr aktueller [Partitionierungsentwurf](../partitioning-overview.md#logical-partitions) und Ihre aktuelle Workload versuchen, für einen bestimmten Partitionsschlüsselwert mehr als die zulässige Datenmenge zu speichern. Es gibt keine Beschränkung für die Anzahl logischer Partitionen in Ihrem Container, aber die Datenmenge, die von den einzelnen logischen Partitionen gespeichert werden kann, ist begrenzt. Wenn Sie hierzu Fragen haben, wenden Sie sich an den Support.

## <a name="non-data-operations-are-not-allowed"></a>Andere Vorgänge als Datenvorgänge sind unzulässig
Dieses Szenario tritt auf, wenn [Nicht-Datenvorgänge im Konto nicht möglich sind](../how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations). Bei diesem Szenario tauchen häufig Fehler wie die folgenden auf:

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>Lösung
Führen Sie den Vorgang über Azure Resource Manager, das Azure-Portal, die Azure CLI oder Azure PowerShell aus. Alternativ dazu können Sie die Ausführung von Nicht-Datenvorgängen wieder zulassen.

## <a name="next-steps"></a>Nächste Schritte
* Konfigurieren der [IP-Firewall](../how-to-configure-firewall.md)
* Konfigurieren des Zugriffs über [virtuelle Netzwerke](../how-to-configure-vnet-service-endpoint.md)
* Konfigurieren des Zugriffs über [private Endpunkte](../how-to-configure-private-endpoints.md)
