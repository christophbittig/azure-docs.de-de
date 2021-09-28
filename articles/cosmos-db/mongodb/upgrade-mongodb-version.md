---
title: Aktualisieren der Mongo-Version Ihrer Azure Cosmos DB-API für das MongoDB-Konto
description: Informationen, wie Sie die MongoDB Wire Protocol-Version für Ihre vorhandene Azure Cosmos DB-API für MongoDB-Konten nahtlos aktualisieren können
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 2880bc5fc9c367a5ab3cb02db3e3d5901861d789
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439378"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Upgrade der API-Version Ihrer Azure Cosmos DB-API für das MongoDB-Konto
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

In diesem Artikel wird beschrieben, wie Sie ein Upgrade der API-Version Ihrer Azure Cosmos DB-API für das MongoDB-Konto durchführen. Nach dem Upgrade können Sie die neuesten Funktionen in der Azure Cosmos DB-API für MongoDB verwenden. Der Upgradeprozess unterbricht die Verfügbarkeit Ihres Kontos nicht und verbraucht zu keinem Zeitpunkt RU/s oder verringert die Kapazität der Datenbank. Dieser Prozess wirkt sich nicht auf vorhandene Daten oder Indizes aus. 

Beim Upgrade auf eine neue API-Version beginnen Sie mit den Entwicklungs-/Testworkloads, bevor Sie ein Upgrade der Produktionsworkloads durchführen. Es ist wichtig, ein Upgrade Ihrer Clients auf eine Version durchzuführen, die mit der für das Upgrade angestrebten API-Version kompatibel ist, bevor Sie das Upgrade Ihrer Azure Cosmos DB-API für das MongoDB-Konto ausführen.

>[!Note]
> Zurzeit kann nur für qualifizierende Konten, die Serverversion 3.2 verwenden, ein Upgrade auf Version 3.6 oder 4.0 durchgeführt werden. Wenn Ihr Konto die Upgradeoption nicht anzeigt, [erstellen Sie ein Supportticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Upgrade auf 4.0 oder 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Vorteile des Upgrades auf Version 4.0

Die folgenden neuen Features sind in Version 4.0 enthalten:
- Unterstützung für Transaktionen mit mehreren Dokumenten in Sammlungen ohne Shards
- Neue Aggregationsoperatoren
- Verbesserte Leistung bei Überprüfungen
- Schnellerer, effizienterer Speicher

### <a name="benefits-of-upgrading-to-version-36"></a>Vorteile des Upgrades auf Version 3.6

Die folgenden neuen Features sind in Version 3.6 enthalten:
- Verbesserte Leistung und Stabilität
- Unterstützung für neue Datenbankbefehle
- Standardunterstützung für Aggregationspipeline und neue Aggregationsphasen
- Unterstützung für das Ändern von Datenströmen
- Unterstützung für zusammengesetzte Indizes
- Partitionsübergreifende Unterstützung für die folgenden Vorgänge: Aktualisieren, Löschen, Zählen und Sortieren
- Verbesserte Leistung für die folgenden zusammengesetzten Operationen: „$count“, „$skip“, „$limit“ und „$group“
- Platzhalterindizierung wird jetzt unterstützt

### <a name="changes-from-version-32"></a>Änderungen gegenüber Version 3.2

- Das Feature für die [serverseitige Wiederholung (Server-Side Retry, SSR)](prevent-rate-limiting-errors.md) ist standardmäßig aktiviert, sodass Anforderungen von der Clientanwendung keine 16500-Fehler zurückgeben. Stattdessen werden Anforderungen so lange fortgesetzt, bis sie abgeschlossen sind oder das Zeitlimit von 60 Sekunden erreicht wird.
- Das Pro-Anforderungstimeout ist auf 60 Sekunden festgelegt.
- Bei MongoDB-Sammlungen, die in der neuen Wire Protocol-Version erstellt wurden, wird nur die Eigenschaft `_id` standardmäßig indiziert.

### <a name="action-required-when-upgrading-from-32"></a>Erforderliche Aktion beim Upgrade von 3.2

Beim Upgrade von 3.2 wird das Endpunktsuffix des Datenbankkontos auf das folgende Format aktualisiert:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Beim Upgrade von Version 3.2 müssen Sie den vorhandenen Endpunkt in Ihren Anwendungen und Treibern ersetzen, die eine Verbindung mit diesem Datenbankkonto herstellen. **Nur Verbindungen, die den neuen Endpunkt verwenden, können auf die Features in der neuen API-Version zugreifen**. Der vorherige 3.2-Endpunkt sollte das Suffix `.documents.azure.com` haben.

Beim Upgrade von 3.2 auf neuere Versionen sind jetzt [zusammengesetzte Indizes](mongodb-indexing.md) erforderlich, um Sortiervorgänge für mehrere Felder durchzuführen, um eine stabile, hohe Leistung für diese Abfragen sicherzustellen. Stellen Sie sicher, dass diese zusammengesetzten Indizes erstellt werden, damit die Sortierungen für mehrere Felde erfolgreich sind. 

>[!Note]
> Dieser Endpunkt weist möglicherweise geringfügige Unterschiede auf, wenn Ihr Konto in einer unabhängigen, behördlichen oder eingeschränkten Azure-Cloud erstellt wurde.

## <a name="how-to-upgrade"></a>Ausführen eines Upgrades

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem Konto für die Azure Cosmos DB-API für MongoDB. Öffnen Sie den Bereich **Übersicht** und überprüfen Sie, ob die aktuelle **Serverversion** 3.2 or 3.6 ist.

    :::image type="content" source="./media/upgrade-mongodb-version/check-current-version.png" alt-text="Überprüfen Sie die aktuelle Version Ihres MongoDB-Kontos im Azure-Portal." border="true":::

1. Öffnen Sie im Menü auf der linken Seite den Bereich `Features`. Dieser Bereich zeigt die Features auf Kontoebene an, die für Ihr Datenbankkonto zur Verfügung stehen.

1. Wählen Sie die Zeile `Upgrade MongoDB server version` aus. Wenn diese Option nicht angezeigt wird, ist Ihr Konto für dieses Upgrade möglicherweise nicht qualifiziert. Wenn das zutrifft, erstellen Sie [ein Supportticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

    :::image type="content" source="./media/upgrade-mongodb-version/upgrade-server-version.png" alt-text="Öffnen Sie das Blatt „Features“, und aktualisieren Sie Ihr Konto." border="true":::

1. Überprüfen Sie die für das Upgrade angezeigten Informationen. Wählen Sie `Set server version to 4.0` (oder je nach aktueller Version 3.6) aus.

    :::image type="content" source="./media/upgrade-mongodb-version/select-upgrade.png" alt-text="Befolgen Sie den Upgradeleitfaden, und wählen Sie „Upgrade“ aus." border="true":::

1. Nachdem Sie das Upgrade gestartet haben, ist das **Feature**-Menü abgeblendet, und der Status wird auf *Ausstehend* gesetzt. Für das Upgrade werden etwa 15 Minuten benötigt. Dieser Prozess wirkt sich nicht auf die vorhandenen Funktionen oder Vorgänge Ihres Datenbankkontos aus. Nach Abschluss des Vorgangs wird im Status **Aktualisieren der MongoDB-Serverversion** die aktualisierte Version angezeigt. Wenn beim Verarbeiten Ihrer Anforderung ein Problem aufgetreten ist, [wenden Sie sich an den Support](https://azure.microsoft.com/support/create-ticket/).

1. Nach dem Upgrade Ihres Kontos sind einige Überlegungen zu beachten:

    1. Wenn Sie ein Upgrade von 3.2 durchgeführt haben, kehren Sie zum Bereich **Übersicht** zurück, und kopieren Sie die neue Verbindungszeichenfolge für die Verwendung in Ihrer Anwendung. Die alte Verbindungszeichenfolge, mit der 3.2 ausgeführt wird, wird nicht unterbrochen. Zur Gewährleistung eines konsistenten Verhaltens müssen alle Ihre Anwendungen den neuen Endpunkt verwenden.

    1. Wenn Sie ein Upgrade von 3.6 durchgeführt haben, wird die vorhandene Verbindungszeichenfolge auf die angegebene Version aktualisiert und sollte weiterhin verwendet werden.

## <a name="how-to-downgrade"></a>Ausführen eines Downgrades

Anhand der gleichen Schritte im Abschnitt „Ausführen eines Upgrades“ können Sie auch ein Downgrade Ihres Kontos von 4.0 auf 3.6 durchführen.

Wenn Sie ein Upgrade von 3.2 auf 4.0 oder 3.6 durchgeführt haben und nun ein Downgrade auf 3.2 vornehmen möchten, können Sie einfach mit dem Host `accountname.documents.azure.com`, auf dem auch nach dem Upgrade noch Version 3.2 aktiv bleibt, zurück zur vorherigen Verbindungszeichenfolge (3.2) wechseln.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die unterstützten und nicht unterstützten [Features von MongoDB, Version 4.0](feature-support-40.md).
- Informieren Sie sich über die unterstützten und nicht unterstützten [Features von MongoDB, Version 3.6](feature-support-36.md).
- Weitere Informationen finden Sie unter [Features der Mongo-Version 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    - Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md). 
    - Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)
