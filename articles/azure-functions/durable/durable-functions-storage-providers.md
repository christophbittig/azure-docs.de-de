---
title: 'Durable Functions-Speicheranbieter: Azure'
description: Informationen zu den verschiedenen Speicheranbietern für Durable Functions und deren Vergleich
author: cgillum
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: azfuncdf
ms.openlocfilehash: bbeb730b0e22bd555f06514870448a229fccfbc6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438099"
---
# <a name="durable-functions-storage-providers"></a>Durable Functions-Speicheranbieter

Durable Functions behält Funktionsparameter, Rückgabewerte und andere Zustände automatisch in einem dauerhaften Speicher bei, um eine zuverlässige Ausführung zu garantieren. Die Standardkonfiguration für Durable Functions speichert diesen Laufzeitstatus in einem Azure Storage-Konto (klassisch). Es ist jedoch möglich, Durable Functions v2.0 und höher so zu konfigurieren, dass ein alternativer dauerhafter Speicheranbieter verwendet wird.

Die Durable Functions-Erweiterung besteht aus Azure Functions-Triggern und -Bindungen, die intern vom [Durable Task-Framework](https://github.com/Azure/durabletask) unterstützt werden. DTFx unterstützt verschiedene Back-End-Speicheranbieter, einschließlich des Azure Storage-Anbieters, der von Durable Functions verwendet wird. Ab der Durable Functions-Version **v2.5.0** können Benutzer ihre Funktions-Apps so konfigurieren, dass ein anderer DTFx-Speicheranbieter als der Azure Storage-Anbieter verwendet wird.

> [!NOTE]
> Die Entscheidung, andere Speicheranbieter als Azure Storage zu verwenden, sollte sorgfältig bedacht werden. Die meisten Funktions-Apps, die in Azure ausgeführt werden, sollten den Azure Storage-Standardanbieter für Durable Functions verwenden. Bei der Entscheidung, ob ein alternativer Speicheranbieter verwendet werden soll, müssen jedoch wichtige Kompromisse bei den Kosten, der Skalierbarkeit und der Datenverwaltung abgewägt werden. In diesem Artikel werden viele dieser Kompromissen ausführlich beschrieben.
>
> Beachten Sie außerdem, dass es derzeit nicht möglich ist, Daten von einem Speicheranbieter zu einem anderen zu migrieren. Wenn Sie einen neuen Speicheranbieter verwenden möchten, sollten Sie eine neue App erstellen, die mit dem neuen Speicheranbieter konfiguriert ist.

Zwei alternative DTFx-Speicheranbieter wurden für die Verwendung mit Durable Functions entwickelt, und zwar der _Netherite_-Speicheranbieter und der _Microsoft SQL Server_-Speicheranbieter (MSSQL). Dieser Artikel beschreibt alle drei unterstützten Anbieter, vergleicht sie miteinander und stellt grundlegende Informationen zu den ersten Schritten bei der Verwendung bereit.

## <a name="azure-storage"></a>Azure Storage

Azure Storage ist der Standardspeicheranbieter für Durable Functions. Dieser nutzt Warteschlangen, Tabellen und Blobs, um den Orchestrierungs- und Entitätszustand dauerhaft zu speichern. Außerdem werden Blobs und Blobleases verwendet, um Partitionen zu verwalten. In vielen Fällen ist das Speicherkonto, das zum Speichern des Durable Functions-Laufzeitstatus verwendet wird, mit dem Standardspeicherkonto identisch, das von Azure Functions (`AzureWebJobsStorage`) verwendet wird. Es ist jedoch auch möglich, Durable Functions mit einem separaten Speicherkonto zu konfigurieren. Der Azure Storage-Anbieter ist in die Durable Functions-Erweiterung integriert und besitzt keine anderen Abhängigkeiten.

Die wichtigsten Vorteile des Azure Storage-Anbieters sind die folgenden:

* Es ist kein Setup erforderlich. Sie können das Speicherkonto verwenden, das von den Einrichtungsfunktionen der Funktions-App für Sie erstellt wurde.
* Kostengünstigstes serverloses Abrechnungsmodell: Azure Storage verfügt über ein nutzungsbasiertes Preismodell, das vollständig auf der Nutzung basiert ([weitere Informationen](durable-functions-billing.md#azure-storage-transactions)).
* Beste Toolunterstützung: Azure Storage bietet plattformübergreifende lokale Emulation und lässt sich in Visual Studio, Visual Studio Code und das Azure Functions Core Tools-Toolset integrieren.
* Der ausgereifteste Anbieter: Azure Storage ist das ursprüngliche und bewährteste Speicher-Back-End für Durable Functions.

Den Quellcode für die DTFx-Komponenten des Azure Storage-Speicheranbieters finden Sie im GitHub-Repository [Azure/durabletask](https://github.com/Azure/durabletask/tree/main/src/DurableTask.AzureStorage).

> [!NOTE]
> Für die Verwendung des Azure Storage-Anbieters sind universelle Azure Storage-Standardkonten erforderlich. Andere Speicherkontotypen werden nicht unterstützt. Es wird dringend empfohlen, allgemeine Legacyspeicherkonten der Version 1 zu verwenden, da die neueren v2-Speicherkonten für Durable Functions-Workloads um einiges teurer sein können. Weitere Informationen zu Azure Storage-Kontotypen finden Sie in der Dokumentation unter [Speicherkontoübersicht](../../storage/common/storage-account-overview.md).

## <a name="netherite-preview"></a><a name="netherite"></a>Netherite (Vorschau)

Das Netherite-Speicher-Back-End wurde von [Microsoft Research](https://www.microsoft.com/research) entworfen und entwickelt. Dabei werden, zusätzlich zu [Azure-Seiten-Blobs](../../storage/blobs/storage-blob-pageblob-overview.md), [Azure Event Hubs](../../event-hubs/event-hubs-about.md) sowie die [FASTER](https://www.microsoft.com/research/project/faster/)-Datenbanktechnologie verwendet. Das Design von Netherite ermöglicht im Vergleich zu anderen Anbietern eine Verarbeitung von Orchestrierungen und Entitäten mit deutlich höherem Durchsatz. In einigen Benchmarkszenarios wurde deutlich, dass der Durchsatz im Vergleich zum Azure Storage-Standardanbieter um mehr als eine Größenordnung zunimmt.

Die wichtigsten Vorteile des Netherite-Speicheranbieters sind die folgenden:

* Er bietet einen erheblich höheren Durchsatz bei geringeren Kosten im Vergleich zu anderen Speicheranbietern.
* Netherite unterstützt die Preis-/Leistungsoptimierung, wodurch Sie die Leistung bei Bedarf hochskalieren können.
* Es werden bis zu 32 Datenpartitionen mit Event Hubs Basic- und Standard-SKUs unterstützt.
* Netherite ist kostengünstiger als andere Anbieter im Hinblick auf Workloads mit hohem Durchsatz.

Weitere Informationen zu den technischen Details des Netherite-Speicheranbieters, einschließlich der ersten Schritte bei der Verwendung, finden Sie in der [Netherite-Dokumentation.](https://microsoft.github.io/durabletask-netherite) Den Quellcode für den Netherite-Speicheranbieter finden Sie im GitHub-Repository [microsoft/durabletask-netherite](https://github.com/microsoft/durabletask-netherite). Eine detailliertere Auswertung des Netherite-Speicheranbieters ist auch in der folgenden Forschungsarbeit verfügbar: [Serverless Workflows with Durable Functions and Netherite](https://arxiv.org/abs/2103.00033) (Serverlose Workflows mit Durable Functions und Netherite).

> [!NOTE]
> Der Name _Netherite_ stammt aus der Welt von [Minecraft](https://minecraft.fandom.com/wiki/Netherite).

## <a name="microsoft-sql-server-mssql-preview"></a><a name="mssql"></a>Microsoft SQL Server (MSSQL) (Vorschau)

Der Microsoft SQL Server-Speicheranbieter (MSSQL) speichert alle Zustände dauerhaft in einer Microsoft SQL Server-Datenbank. Er ist sowohl mit lokalen als auch in der Cloud gehosteten Bereitstellungen von SQL Server kompatibel, einschließlich mit [Azure SQL-Datenbank](../../azure-sql/database/sql-database-paas-overview.md).

Die wichtigsten Vorteile des MSSQL-Speicheranbieters sind die folgenden:

* Er unterstützt nicht verknüpfte Umgebungen: Es ist keine Azure-Konnektivität bei der Verwendung einer SQL Server-Installation erforderlich.
* Er ist umgebungs- und cloudübergreifend portierbar, einschließlich in von Azure gehosteten und lokalen Umgebungen und Clouds.
* Es wird eine starke Datenkonsistenz bereitgestellt, wodurch Sicherung bzw. Wiederherstellung sowie Failover ohne Datenverlust möglich sind.
* Er bietet native Unterstützung für benutzerdefinierte Datenverschlüsselung (ein Feature von SQL Server).
* Die Integration in vorhandene Datenbankanwendungen über integrierte gespeicherte Prozeduren ist ebenfalls enthalten.

Weitere Informationen zu den technischen Details des MSSQL-Speicheranbieters, einschließlich der ersten Schritte bei der Verwendung, finden Sie in der [Dokumentation für den Microsoft SQL-Anbieter](https://microsoft.github.io/durabletask-mssql). Den Quellcode für den MSSQL-Speicheranbieter finden Sie im GitHub-Repository [microsoft/durabletask-mssql](https://github.com/microsoft/durabletask-mssql).

## <a name="configuring-alternate-storage-providers"></a>Konfigurieren alternativer Speicheranbieter

Das Konfigurieren alternativer Speicheranbieter ist in der Regel ein zweistufiger Prozess:

1. Fügen Sie Ihrer Funktions-App das entsprechende NuGet-Paket hinzu (diese Anforderung gilt temporär für Apps, die Erweiterungsbündel verwenden).
1. Aktualisieren Sie die **host.json**-Datei, um anzugeben, welchen Speicheranbieter Sie verwenden möchten.

Wenn in „host.json“ explizit kein Speicheranbieter konfiguriert ist, wird standardmäßig der Azure Storage-Anbieter aktiviert.

### <a name="configuring-the-azure-storage-provider"></a>Konfigurieren des Azure Storage-Anbieters

Der Azure Storage-Anbieter ist der Standardspeicheranbieter und erfordert keine explizite Konfiguration, keine NuGet-Paketverweise oder Erweiterungspaketverweise. Die vollständigen **host.json**-Konfigurationsoptionen finden Sie [hier](durable-functions-bindings.md#hostjson-settings) unter dem Pfad `extensions/durableTask/storageProvider`.

### <a name="configuring-the-netherite-storage-provider"></a>Konfigurieren des Netherite-Speicheranbieters

Wenn Sie den Netherite-Speicheranbieter verwenden möchten, müssen Sie zuerst dem NuGet-Paket [Microsoft.Azure.DurableTask.Netherite.AzureFunctions](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Netherite.AzureFunctions) in Ihrer **csproj**-Datei (.NET-Apps) oder Ihrer **extensions.proje**-Datei (JavaScript-, Python- oder PowerShell-Apps) einen Verweis hinzufügen.

> [!NOTE]
> Der Netherite-Speicheranbieter wird in Apps, die [Erweiterungsbündel](../functions-bindings-register.md#extension-bundles) verwenden, noch nicht unterstützt.

Das folgende „host.json“-Beispiel zeigt die Mindestkonfiguration, die zum Aktivieren des Netherite-Speicheranbieters erforderlich ist.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "Netherite",
        "storageConnectionName": "AzureWebJobsStorage",
        "eventHubsConnectionName": "EventHubsConnection"
      }
    }
  }
}
```

Ausführlichere Setupanweisungen finden Sie in der [Netherite-Dokumentation zu den ersten Schritten](https://microsoft.github.io/durabletask-netherite/#/?id=getting-started).

### <a name="configuring-the-mssql-storage-provider"></a>Konfigurieren des MSSQL-Speicheranbieters

Wenn Sie den MSSQL-Speicheranbieter verwenden möchten, müssen Sie zuerst dem NuGet-Paket [Microsoft.DurableTask.SqlServer.AzureFunctions](https://www.nuget.org/packages/Microsoft.DurableTask.SqlServer.AzureFunctions) in Ihrer **csproj**-Datei (.NET-Apps) oder Ihrer **extensions.proje**-Datei (JavaScript-, Python- oder PowerShell-Apps) einen Verweis hinzufügen.

> [!NOTE]
> Der MSSQL-Speicheranbieter wird in Apps, die [Erweiterungsbündel](../functions-bindings-register.md#extension-bundles) verwenden, noch nicht unterstützt.

Das folgende Beispiel zeigt die Mindestkonfiguration, die zum Aktivieren des MSSQL-Speicheranbieters erforderlich ist.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "mssql",
        "connectionStringName": "SQLDB_Connection"
      }
    }
  }
}
```

Ausführlichere Setupanweisungen finden Sie in der [Dokumentation zu den ersten Schritten im MSSQL-Anbieter](https://microsoft.github.io/durabletask-mssql/#/quickstart).

## <a name="comparing-storage-providers"></a>Vergleichen von Speicheranbietern

Es gibt viele maßgebliche Kompromisse zwischen den verschiedenen unterstützten Speicheranbietern. In der folgenden Tabelle sind die jeweiligen Kompromisse aufgeführt, und Sie können anhand dieser Punkte dann entscheiden, welcher Speicheranbieter für Ihre Anforderungen am besten geeignet ist.

| Speicheranbieter | Azure Storage | Netherite | MSSQL |
|- |-              |-          |-      |
| Offizieller Supportstatus | ✅ Allgemein verfügbar (Generally Available, GA) | ⚠ Öffentliche Vorschau | ⚠ Öffentliche Vorschau |
| Externe Abhängigkeiten | Azure Storage-Konto (universell, v1) | Azure Event Hubs<br/>Azure Storage-Konto (universell) | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) oder Azure SQL-Datenbank |
| Lokale Entwicklungs- und Emulationsoptionen | [Azurite v3.12 und höher](../../storage/common/storage-use-azurite.md) (plattformübergreifend)<br/>[Azure Storage-Emulator](../../storage/common/storage-use-emulator.md) (nur Windows) | Emulation im Arbeitsspeicher ([weitere Informationen](https://microsoft.github.io/durabletask-netherite/#/emulation)) | SQL Server Developer Edition (unterstützt [Windows](/sql/database-engine/install-windows/install-sql-server)-, [Linux](/sql/linux/sql-server-linux-setup)- und [Docker](/sql/linux/sql-server-linux-docker-container-deployment)-Container) |
| Aufgabenhubkonfiguration | Explizit | Explizit | Standardmäßig implizit ([weitere Informationen](https://microsoft.github.io/durabletask-mssql/#/taskhubs)) |
| Maximaler Durchsatz | Moderat | Sehr hoch | Moderat |
| Maximale Orchestrierung/Aufskalierung von Entitäten (Knoten) | 16 | 32 | – |
| Maximales Aufskalieren von Aktivitäten (Knoten) | – | 32 | – |
| Verbrauchsplanunterstützung | ✅ Vollständig unterstützt | ❌ Nicht unterstützt | ❌ Nicht unterstützt |
| Unterstützung für elastische Premium-Pläne | ✅ Vollständig unterstützt | ⚠ Erfordert [Überwachung der Runtimeskalierung](../functions-networking-options.md#premium-plan-with-virtual-network-triggers) | ⚠ Erfordert [Überwachung der Runtimeskalierung](../functions-networking-options.md#premium-plan-with-virtual-network-triggers) |
| [KEDA 2.0](https://keda.sh/): Skalierungsunterstützung<br/>([Weitere Informationen](../functions-kubernetes-keda.md)) | ❌ Nicht unterstützt | ❌ Nicht unterstützt | ✅ Unterstützt die Verwendung des [MSSQL-Scalers](https://keda.sh/docs/scalers/mssql/) ([weitere Informationen](https://microsoft.github.io/durabletask-mssql/#/scaling)) |
| Unterstützung für [Erweiterungsbündel](../functions-bindings-register.md#extension-bundles) (empfohlen für Apps, die keine .NET-Apps sind) | ✅ Vollständig unterstützt | ❌ Nicht unterstützt | ❌ Nicht unterstützt |
| Für Preis/Leistung konfigurierbar? | ❌ Nein | ✅ Ja (Event Hubs-TUs und -CUs) | ✅ Ja (SQL-vCPUs) |
| Unterstützung von nicht verknüpften Umgebungen | ❌ Azure-Konnektivität erforderlich | ❌ Azure-Konnektivität erforderlich | ✅ Vollständig unterstützt |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Durable Functions-Leistung und -Skalierung](durable-functions-perf-and-scale.md)
