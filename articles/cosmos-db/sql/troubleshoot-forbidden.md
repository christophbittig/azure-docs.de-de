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
ms.openlocfilehash: 9a5f1590e73388429f613a1c6b078f5c315397d6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238942"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnostizieren und Behandeln von Azure Cosmos DB-Ausnahmen des Typs „Unzulässig“
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Der HTTP-Statuscode 403 bedeutet, dass die Anforderung unzulässig ist und nicht abgeschlossen werden kann.

## <a name="firewall-blocking-requests"></a>Durch Firewall blockierte Anforderungen

Anforderungen auf Datenebene können über die folgenden drei Wege zu Cosmos DB gelangen:

- Öffentliches Internet (IPv4)
- Dienstendpunkt
- Privater Endpunkt

Wenn eine Anforderung auf Datenebene mit dem Fehler „403 Forbidden“ blockiert wird, gibt die Fehlermeldung an, über welchen der drei Pfade die Anforderung an Cosmos DB übermittelt wurde.

- `Request originated from client IP {...} through public internet.`
- `Request originated from client VNET through service endpoint.`
- `Request originated from client VNET through private endpoint.`

### <a name="solution"></a>Projektmappe

Sie müssen verstehen, welcher Pfad **erwartungsgemäß** die Anforderung an Cosmos DB übermittelt.
   - Wenn die Fehlermeldung zeigt, dass die Anforderung nicht über den erwarteten Pfad an Cosmos DB gesendet wurde, liegt das Problem wahrscheinlich bei der clientseitigen Einrichtung. Überprüfen Sie Ihre clientseitige Einrichtung mithilfe der folgenden Dokumentationen.
      - Öffentliches Internet: [Konfigurieren der IP-Firewall in Azure Cosmos DB](../how-to-configure-firewall.md)
      - Dienstendpunkt: [Konfigurieren des Zugriffs auf Azure Cosmos DB über virtuelle Netzwerke (VNet)](../how-to-configure-vnet-service-endpoint.md). Wenn Sie beispielsweise erwarten, einen Dienstendpunkt zu verwenden, aber die Anforderung über das öffentliche Internet an Cosmos DB übermittelt wurde, wurde vom Subnetz, das der Client ausgeführt hat, möglicherweise nicht der Dienstendpunkt für Cosmos DB aktiviert.
      - Privater Endpunkt: [Konfigurieren von Azure Private Link für ein Azure Cosmos-Konto](../how-to-configure-private-endpoints.md). Wenn Sie beispielsweise erwarten, einen privaten Endpunkt zu verwenden, aber die Anforderung über das öffentliche Internet an Cosmos DB übermittelt wurde, wurde das DNS auf dem virtuellen Computer möglicherweise nicht für die Auflösung des Kontoendpunkts an die private IP-Adresse konfiguriert, sodass stattdessen die öffentliche IP-Adresse des Kontos verwendet wurde.
   - Wenn die Anforderungen über den erwarteten Pfad an Cosmos DB übermittelt wurde, wurde sie blockiert, da die Quellnetzwerkidentität nicht für das Konto konfiguriert wurde. Überprüfen Sie die Einstellungen des Kontos abhängig vom Pfad, über den die Anforderung an Cosmos DB übermittelt wurde.
      - Öffentliches Internet: Überprüfen Sie den [öffentlichen Netzwerkzugriff](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation) des Kontos sowie die Konfigurationen für den IP-Adressbereichfilter.
      - Dienstendpunkt: Überprüfen Sie den [öffentlichen Netzwerkzugriff](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation) des Kontos sowie die Konfigurationen für den VNet-Filter.
      - Privater Endpunkt: Überprüfen Sie die Konfiguration des privaten Kontoendpunkts und die private DNS-Konfiguration des Clients. Möglicherweise ist der Grund dafür, dass über einen privaten Endpunkt, der für ein anderes Konto eingerichtet ist, auf das Konto zugegriffen wird.

Wenn Sie die Firewallkonfigurationen des Kontos vor Kurzem aktualisiert haben, bedenken Sie, dass es **bis zu 15 Minuten** dauern kann, bis die Änderungen übernommen werden.

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
