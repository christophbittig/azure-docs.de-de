---
title: Problembehandlung der Ausnahme „Azure Cosmos DB nicht verfügbar“ beim Java v4 SDK
description: Erfahren Sie mehr über das Diagnostizieren und Beheben der Ausnahme „Azure Cosmos DB nicht verfügbar“ beim Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 611870b7de3f1a60683e89653726f1453dc85cfc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115410"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnose und Problembehandlung der Ausnahme „Azure Cosmos DB nicht verfügbar“ beim Java v4 SDK
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]
Das Java v4 SDK war nicht in der Lage, eine Verbindung mit Azure Cosmos DB herzustellen.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Die folgende Liste enthält bekannte Gründe und Lösungen für Ausnahmen, dass ein Dienst nicht verfügbar ist.

### <a name="the-required-ports-are-being-blocked"></a>Die erforderlichen Ports werden blockiert
Vergewissern Sie sich, dass alle [erforderlichen Ports](sql-sdk-connection-modes.md#service-port-ranges) aktiviert sind.

### <a name="client-initialization-failure"></a>Fehler bei der Clientinitialisierung
Die folgende Ausnahme wird ausgelöst, wenn das SDK nicht mit der Cosmos DB-Instanz kommunizieren kann. Dies verweist normalerweise darauf hin, dass die Anforderungen durch ein Sicherheitsprotokoll, z. B. eine Firewallregel, blockiert werden.

```java
 java.lang.RuntimeException: Client initialization failed. Check if the endpoint is reachable and if your auth token is valid
```

Um zu überprüfen, ob das SDK mit dem Cosmos DB-Konto kommunizieren kann, führen Sie den folgenden Befehl an der Stelle aus, an der die Anwendung gehostet wird. Wenn ein Fehler auftritt, ist dies ein Hinweis darauf, dass eine Firewallregel oder ein anderes Sicherheitsfeature die Anforderung blockiert. Wenn der Vorgang erfolgreich ist, sollte das SDK dazu in der Lage sein, mit dem Cosmos DB-Konto zu kommunizieren.
```
telnet myCosmosDbAccountName.documents.azure.com 443
```

### <a name="client-side-transient-connectivity-issues"></a>Clientseitige vorübergehende Konnektivitätsprobleme
Ausnahmen vom Typ „Dienst nicht verfügbar“ können auftreten, wenn vorübergehende Konnektivitätsprobleme auftreten, die Timeouts verursachen. In der Regel enthält die Stapelüberwachung im Zusammenhang mit diesem Szenario einen `ServiceUnavailableException`-Fehler mit Diagnosedetails. Beispiel:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Befolgen Sie die [Schritte zur Problembehandlung für Anforderungstimeouts](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps), um das Problem zu beheben.

### <a name="service-outage"></a>Dienstunterbrechung
Überprüfen Sie den [Azure-Status](https://status.azure.com/status), um festzustellen, ob ein Problem vorliegt.


## <a name="next-steps"></a>Nächste Schritte
* [Diagnostizieren und Behandeln](troubleshoot-java-sdk-v4-sql.md) von Problemen bei Verwendung des Java v4 SDK für Azure Cosmos DB.
* Weitere Informationen zu Leistungsrichtlinien für das [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).