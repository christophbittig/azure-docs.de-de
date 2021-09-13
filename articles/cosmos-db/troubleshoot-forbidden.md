---
title: Behandeln von Azure Cosmos DB-Ausnahmen des Typs „Unzulässig“
description: Erfahren Sie mehr über die Diagnose und Behebung der Ausnahme „Unzulässig“.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: d1ea89443f1d4eaab359e23dfc201dc732ced75e
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108871"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnostizieren und Behandeln von Azure Cosmos DB-Ausnahmen des Typs „Unzulässig“
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
Vergewissern Sie sich, dass Ihre aktuellen [Firewalleinstellungen](how-to-configure-firewall.md) ordnungsgemäß sind und die IP-Adressen oder Netzwerke einschließen, über die Sie versuchen, eine Verbindung herzustellen.
Wenn Sie sie vor Kurzem aktualisiert haben, bedenken Sie, dass das Übernehmen von Änderungen **bis zu 15 Minuten** dauern kann.

## <a name="non-data-operations-are-not-allowed"></a>Andere Vorgänge als Datenvorgänge sind unzulässig
Dieses Szenario liegt vor, wenn [Nicht-Datenvorgänge im Konto nicht möglich sind](how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations). Bei diesem Szenario tauchen häufig Fehler wie die folgenden auf:

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>Lösung
Führen Sie den Vorgang über Azure Resource Manager, das Azure-Portal, die Azure CLI oder Azure PowerShell aus. Alternativ dazu können Sie die Ausführung von Nicht-Datenvorgängen wieder zulassen.

## <a name="next-steps"></a>Nächste Schritte
* Konfigurieren der [IP-Firewall](how-to-configure-firewall.md)
* Konfigurieren des Zugriffs über [virtuelle Netzwerke](how-to-configure-vnet-service-endpoint.md)
* Konfigurieren des Zugriffs über [private Endpunkte](how-to-configure-private-endpoints.md)
