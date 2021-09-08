---
title: Azure Cosmos DB SQL SDK-Konnektivitätsmodi
description: Erfahren Sie mehr über die unterschiedlichen Konnektivitätsmodi, die für die Azure Cosmos DB SQL SDKs verfügbar sind.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 645093cc63f351cc196e7d90812e6bc430259eed
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030917"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK-Konnektivitätsmodi
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die Art der Verbindungsherstellung zwischen einem Client und Azure Cosmos DB hat erhebliche Auswirkungen auf die Leistung, insbesondere im Hinblick auf die clientseitige Latenz. Azure Cosmos DB bietet ein einfaches und offenes RESTful-Programmiermodell über HTTPS, das Gatewaymodus genannt wird. Außerdem ist ein effizientes TCP-Protokoll enthalten, das ebenfalls RESTful in seinem Kommunikationsmodell ist und TLS für die anfängliche Authentifizierung und die Verschlüsselung des Datenverkehrs verwendet, den sogenannten direkten Modus.

## <a name="available-connectivity-modes"></a>Verfügbare Konnektivitätsmodi

Diese beiden Konnektivitätsmodi sind verfügbar:

  * Gatewaymodus
      
    Der Gatewaymodus wird auf allen SDK-Plattformen unterstützt. Wenn Ihre Anwendung in einem Unternehmensnetzwerk mit strengen Firewalleinschränkungen ausgeführt wird, ist der Gatewaymodus die beste Wahl, da er den HTTPS-Standardport und einen einzelnen DNS-Endpunkt verwendet. Im Gatewaymodus ist jedoch jeweils ein zusätzlicher Netzwerkhop erforderlich, wenn Daten in Azure Cosmos DB geschrieben oder daraus gelesen werden, was sich negativ auf die Leistung auswirkt. Der Gatewayverbindungsmodus wird auch empfohlen, wenn Sie Anwendungen in Umgebungen mit einer begrenzten Anzahl von Socketverbindungen ausführen.

    Beachten Sie bei Verwendung des SDK in Azure Functions – insbesondere beim [Verbrauchstarif](../azure-functions/consumption-plan.md) – die aktuellen [Grenzwerte für Verbindungen](../azure-functions/manage-connections.md).

  * Direkter Modus

    Der direkte Modus unterstützt die Konnektivität über das TCP-Protokoll und bietet eine bessere Leistung, da es weniger Netzwerkhops gibt. Die Anwendung verbindet sich direkt mit den Back-End-Replikaten. Der direkte Modus wird derzeit nur auf .NET- und Java-SDK-Plattformen unterstützt.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB-Konnektivitätsmodi" border="false":::

Diese Konnektivitätsmodi entscheiden im Wesentlichen über die Weiterleitung von Datenebenenanforderungen (Lese- und Schreibvorgänge für Dokumente) vom Clientcomputer an Partitionen im Azure Cosmos DB-Back-End. Der direkte Modus ist die bevorzugte Option für eine optimale Leistung. Er ermöglicht es dem Client, TCP-Verbindungen direkt mit Partitionen im Azure Cosmos DB-Back-End herzustellen und Anforderungen *direkt* und ohne Vermittler zu senden. Im Gatewaymodus werden Anforderungen vom Client an einen sogenannten „Gatewayserver“ im Azure Cosmos DB-Front-End weitergeleitet, der wiederum Ihre Anforderungen an die entsprechenden Partitionen im Azure Cosmos DB-Back-End verteilt.

## <a name="service-port-ranges"></a>Dienstportbereiche

Im direkten Modus müssen Sie zusätzlich zu den Gatewayports sicherstellen, dass der Portbereich zwischen 10000 und 20000 offen ist, da Azure Cosmos DB dynamische TCP-Ports verwendet. Wenn Sie den direkten Modus auf [privaten Endpunkten](./how-to-configure-private-endpoints.md) verwenden, sollte der gesamte Bereich der TCP-Ports von 0 bis 65535 geöffnet sein. Wenn diese Ports nicht geöffnet sind und Sie versuchen, das TCP zu verwenden, wird möglicherweise der 503-Fehler „Dienst nicht verfügbar“ angezeigt.

Die folgende Tabelle enthält eine Zusammenfassung der für verschiedene APIs verfügbaren Konnektivitätsmodi und der für die einzelnen APIs verwendeten Dienstports:

|Verbindungsmodus  |Unterstütztes Protokoll  |Unterstützte SDKs  |API/Dienstport  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Alle SDKs    |   SQL (443), MongoDB (10250, 10255, 10256), Tabelle (443), Cassandra (10350), Graph (443) <br> Port 10250 ist einer Instanz der Azure Cosmos DB-API für MongoDB ohne Georeplikation zugeordnet. Die Ports 10255 und 10256 hingegen sind der Instanz mit Georeplikation zugeordnet.   |
|Direkt    |     TCP    |  .NET SDK Java SDK    | Bei Verwendung von öffentlichen oder Dienstendpunkten: Ports im Bereich zwischen 10000 und 20000<br>Bei Verwendung von privaten Endpunkten: Ports im Bereich zwischen 0 und 65535 |

## <a name="next-steps"></a>Nächste Schritte

Für bestimmte Leistungsoptimierungen der SDK-Plattform:

* [Leistungstipps für Azure Cosmos DB und .NET SDK v2](performance-tips.md)

* [Leistungstipps für Azure Cosmos DB und .NET](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Leistungstipps für das Azure Cosmos DB Java SDK v4](performance-tips-java-sdk-v4-sql.md)

* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)