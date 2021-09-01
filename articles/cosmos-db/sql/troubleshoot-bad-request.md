---
title: Behandeln von Azure Cosmos DB-Ausnahmen des Typs „Bad Request“ (ungültige Anfrage)
description: Erfahren Sie, wie Sie ungültige Anforderungsausnahmen diagnostizieren und beheben können, wie z. B. Eingabeinhalt oder Partitionsschlüssel ist ungültig, der Partitionsschlüssel stimmt in Azure Cosmos DB nicht überein.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: ea7c05bc2361c266103908342e4ef9fb3a6160a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115395"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Diagnostizieren und Beheben fehlerhafter Anforderungsausnahmen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Der HTTP-Statuscode 400 stellt dar, dass die Anforderung ungültige Daten enthält, oder es fehlen erforderliche Parameter.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>Fehlende ID-Eigenschaft
Bei diesem Szenario tauchen häufig Fehler wie die folgenden auf:

*Der Eingabeinhalt ist ungültig, weil die erforderlichen Eigenschaften (id) fehlen*

Eine Antwort mit diesem Fehler bedeutet, dass das JSON-Dokument, das an den Dienst gesendet wird, nicht die erforderliche ID-Eigenschaft hat.

### <a name="solution"></a>Lösung
Geben Sie eine `id`-Eigenschaft mit einem Zeichenfolgenwert an, der gemäß der [REST-Spezifikation](/rest/api/cosmos-db/documents) als Teil des Dokuments angezeigt wird, die SDKs generieren die Werte für diese Eigenschaft nicht automatisch.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Ungültiger Partitionsschlüsselstyp
Bei diesem Szenario tauchen häufig Fehler wie die folgenden auf:

*Partitions Schlüssel... ist ungültig.*

Eine Antwort mit diesem Fehler bedeutet, dass der Partitionsschlüsselwert einen ungültigen Typ hat.

### <a name="solution"></a>Lösung
Der Wert des Partitionsschlüssels muss eine Zeichenfolge oder eine Zahl sein. Stellen Sie sicher, dass der Wert den erwarteten Typen entspricht.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Falscher Wert für den Partitionsschlüssel
Bei diesem Szenario tauchen häufig Fehler wie die folgenden auf:

*Der aus dem Dokument extrahierte Partitionsschlüssel stimmt nicht mit dem im Header angegebenen Wert überein*

Eine Antwort mit diesem Fehler bedeutet, dass Sie einen Vorgang ausführen und einen Partitionsschlüsselwert übergeben, der nicht mit dem Textwert des Dokuments für die erwartete Eigenschaft identisch ist. Wenn der Partitionsschlüsselpfad `/myPartitionKey` ist, verfügt das Dokument über eine Eigenschaft namens `myPartitionKey` mit einem Wert, der nicht mit dem Wert identisch ist, der beim Aufrufen der SDK-Methode als Partitionsschlüsselwert angegeben wurde.

### <a name="solution"></a>Lösung
Senden Sie den Wertparameter für den Partitionsschlüssel, der mit dem Dokument Eigenschaftswert übereinstimmt.

## <a name="next-steps"></a>Nächste Schritte
* [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
* Weitere Informationen zu Leistungsrichtlinien für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET Version 2](performance-tips.md)
* [Diagnostizieren und Behandeln](troubleshoot-java-sdk-v4-sql.md) von Problemen bei Verwendung des Java v4 SDK für Azure Cosmos DB.
* Weitere Informationen zu Leistungsrichtlinien für das [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).
