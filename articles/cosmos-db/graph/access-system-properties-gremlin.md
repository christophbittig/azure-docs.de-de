---
title: Zugreifen auf Systemdokumenteigenschaften über den Azure Cosmos DB-Graph
description: Hier erfahren Sie, wie Sie Cosmos DB-Systemdokumenteigenschaften über die Gremlin-API lesen und schreiben.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/16/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 8a0574d5622ae0ceceb52be72ccd8c5d99a18529
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614805"
---
# <a name="system-document-properties"></a>Systemdokumenteigenschaften
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Azure Cosmos DB verfügt für jedes Dokument über [Systemeigenschaften](/rest/api/cosmos-db/databases), z. B. ```_ts```, ```_self```, ```_attachments```, ```_rid``` und ```_etag```. Darüber hinaus fügt die Gremlin-Engine die Eigenschaften ```inVPartition``` und ```outVPartition``` für Kanten hinzu. Standardmäßig sind diese Eigenschaften für einen Durchlauf verfügbar. Es ist aber möglich, einzelne oder auch alle Eigenschaften in den Gremlin-Durchlauf einzubeziehen.

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>ETag

Diese Eigenschaft wird für die Steuerung der optimistischen Nebenläufigkeit genutzt. Wenn eine Anwendung einen Vorgang in mehrere separate Durchläufe unterteilen muss, können mithilfe der Eigenschaft „ETag“ Datenverluste in gleichzeitigen Schreibvorgängen verhindert werden.

```console
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Gültigkeitsdauer (TTL)

Wenn für die Sammlung der Dokumentablauf aktiviert ist und für Dokumente die `ttl`-Eigenschaft festgelegt ist, ist diese Eigenschaft im Gremlin-Durchlauf als reguläre Scheitelpunkt- oder Kanteneigenschaft verfügbar. `ProjectionStrategy` ist nicht erforderlich, um die Eigenschaft für die Gültigkeitsdauer verfügbar machen zu können.

* Verwenden Sie den folgenden Befehl, um die Gültigkeitsdauer für einen neuen Scheitelpunkt festzulegen:

  ```console
  g.addV(<ID>).property('ttl', <expirationTime>)
  ```

  Beispielsweise wird ein Scheitelpunkt, der mit dem folgenden Durchlauf erstellt wurde, nach *123 Sekunden* automatisch gelöscht:

  ```console
  g.addV('vertex-one').property('ttl', 123)
  ```

* Verwenden Sie den folgenden Befehl, um die Gültigkeitsdauer für einen vorhandenen Scheitelpunkt festzulegen:

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).property('ttl', <expirationTime>)
  ```

* Durch das Anwenden der Eigenschaft für die Gültigkeitsdauer auf Scheitelpunkte wird sie nicht automatisch auf Kanten angewendet. Kanten sind unabhängige Datensätze im Datenbankspeicher. Verwenden Sie den folgenden Befehl, um die Gültigkeitsdauer für Scheitelpunkte und alle ein- und ausgehenden Kanten des Scheitelpunkts festzulegen:

  ```console
  g.V().hasId(<ID>).has('pk', <pk>).as('v').bothE().hasNot('ttl').property('ttl', <expirationTime>)
  ```

Sie können TTL für den Container auf -1 oder auf **Ein (kein Standardwert)** vom Azure-Portal festlegen. Dann ist die TTL für jedes Element unendlich, es sei denn, für das Element wurde der TTL-Wert explizit festgelegt.

## <a name="next-steps"></a>Nächste Schritte
* [Wie stellt die SQL-API Parallelität bereit?](../faq.yml#how-does-the-sql-api-provide-concurrency-)
* [Gültigkeitsdauer (TTL)](../time-to-live.md) in Azure Cosmos DB
