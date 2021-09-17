---
title: Freigegebene Datenbank
description: Azure Synapse Analytics bietet ein Modell mit gemeinsam genutzten Metadaten, das es ermöglicht, über die zugehörige Engine für serverlose SQL-Pools und die SQL-Pool-Engine auf eine in einem serverlosen Apache Spark-Pool erstellte Datenbank zuzugreifen.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b671246d3ee199719dbb3da6bf7820a876791cf
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428288"
---
# <a name="azure-synapse-analytics-shared-database"></a>Gemeinsam genutzte Azure Synapse Analytics-Datenbank

Azure Synapse Analytics ermöglicht den verschiedenen Berechnungsengines von Arbeitsbereichen die gemeinsame Nutzung von Datenbanken und Tabellen. Derzeit werden die für die Apache Spark-Pools erstellten Datenbanken und Tabellen (Parquet- oder CSV-basiert) automatisch für die Engine für serverlose SQL-Pools freigegeben.

Eine mit einem Spark-Auftrag erstellte Datenbank wird mit dem gleichen Namen für alle aktuellen und zukünftigen Spark-Pools im Arbeitsbereich sichtbar (einschließlich der Engine für serverlose SQL-Pools). Sie können mithilfe des serverlosen SQL-Pools keine benutzerdefinierten Objekte (externe Tabellen, Ansichten, Prozeduren) direkt in dieser synchronisierten Datenbank hinzufügen.

Die Spark-Standarddatenbank `default` wird ebenfalls im Kontext des serverlosen SQL-Pools als Datenbank namens `default` angezeigt. Sie können keine Datenbank in Spark und dann eine andere Datenbank mit dem gleichen Namen im serverlosen SQL-Pool erstellen.

Da die Datenbanken asynchron mit dem serverlosen SQL-Pool synchronisiert werden, kommt es zu einer Verzögerung bei der Anzeige.

## <a name="manage-a-spark-created-database"></a>Verwalten einer von Spark erstellten Datenbank

Zum Verwalten der von Spark erstellten Datenbanken müssen Sie Apache Spark-Pools verwenden. Erstellen oder löschen Sie sie beispielsweise über einen Spark-Poolauftrag.

Objekte in synchronisierten Datenbanken können nicht über einen serverlosen SQL-Pool geändert werden.

>[!NOTE]
>Sie können nicht mehrere Datenbanken mit dem gleichen Namen in verschiedenen Pools erstellen. Wenn eine serverlose SQL-Pooldatenbank erstellt wird, können Sie keine Spark-Datenbank mit dem gleichen Namen erstellen. Ebenso können Sie keine serverlose SQL-Pooldatenbank mit dem gleichen Namen erstellen, wenn eine Datenbank in Spark erstellt wird.

## <a name="security-model"></a>Sicherheitsmodell

Die Spark-Datenbanken und -Tabellen sowie deren synchronisierte Darstellungen in der SQL-Engine werden auf der zugrunde liegenden Speicherebene geschützt.

Der Sicherheitsprinzipal, von dem eine Datenbank erstellt wird, gilt als Besitzer der Datenbank und verfügt über alle Rechte für die Datenbank und die zugehörigen Objekte. Der Synapse-Administrator und der Synapse SQL-Administrator haben ebenfalls standardmäßig alle Berechtigungen für synchronisierte Objekte in einem serverlosen SQL-Pool. Das Erstellen von benutzerdefinierten Objekten (einschließlich Benutzern) in synchronisierten SQL-Datenbanken ist nicht zulässig. 

Wenn Sie einem Sicherheitsprinzipal (etwa einem Benutzer, einer Azure AD-App oder einer Sicherheitsgruppe) Zugriff auf die zugrunde liegenden Daten gewähren möchten, die für externe Tabellen verwendet werden, müssen Sie Ihnen Berechtigungen vom Typ `read (R)` für Dateien (etwa die zugrunde liegenden Datendateien der Tabelle) und Berechtigungen vom Typ `execute (X)` für den Ordner, in dem die Dateien gespeichert sind, sowie für alle übergeordneten Ordner bis zum Stamm erteilen. Weitere Informationen zu diesen Berechtigungen finden Sie auf der Seite [Zugriffssteuerungslisten (ACLs)](/azure/storage/blobs/data-lake-storage-access-control). 

Beispielsweise müssen Sicherheitsprinzipale in `https://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/` über Berechtigungen vom Typ `X` für alle Ordner (ab `<fs>` bis `myparquettable`) sowie über Berechtigungen vom Typ `R` für `myparquettable` und Dateien in diesem Ordner verfügen, um eine Tabelle in einer Datenbank lesen zu können (synchronisiert oder Original).

Wenn ein Sicherheitsprinzipal Objekte in einer Datenbank erstellen oder löschen können muss, sind zusätzliche Berechtigungen vom Typ `W` für die Ordner und Dateien im Ordner `warehouse` erforderlich. Objekte in einer Datenbank können nicht über serverlose SQL-Pools, sondern nur über Spark geändert werden.

## <a name="examples"></a>Beispiele

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Erstellen einer Spark-Datenbank mit einem serverlosen SQL-Pool und Herstellen einer Verbindung mit dieser Datenbank

Erstellen Sie zunächst unter Verwendung eines Spark-Clusters, den Sie bereits in Ihrem Arbeitsbereich erstellt haben, eine neue Spark-Datenbank mit dem Namen `mytestdb`. Hierzu können Sie beispielsweise ein Spark-C#-Notebook mit der folgenden Anweisung vom Typ „.NET für Spark“ verwenden:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Nach einer kurzen Verzögerung wird die Datenbank aus dem serverlosen SQL-Pool angezeigt. Führen Sie beispielsweise die folgende Anweisung über den serverlosen SQL-Pool aus:

```sql
SELECT * FROM sys.databases;
```

Vergewissern Sie sich, dass `mytestdb` in den Ergebnissen enthalten ist.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu gemeinsam genutzten Metadaten von Azure Synapse Analytics finden Sie [hier](overview.md).
- Weitere Informationen zu gemeinsam genutzten Metadatentabellen von Azure Synapse Analytics finden Sie [hier](table.md).
