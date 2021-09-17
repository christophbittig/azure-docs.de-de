---
title: Schritte zur Optimierung nach der Migration mit der Azure Cosmos DB-API für MongoDB
description: Dieses Dokument enthält Optimierungstechniken nach der Migration von MongoDB zur Azure Cosmos DB-API für MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: 6dbcb5e65c99c2009602d660e3cd0cc9e22efdbf
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123031566"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Schritte zur Optimierung nach der Migration bei Verwendung der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Lesen Sie den gesamten Leitfaden, bevor Sie die Schritte nach der Migration ausführen.
>

Dieser Leitfaden zur MongoDB-Migrationsnachbereitung ist Teil der Reihe zur MongoDB-Migration. Die wichtigen MongoDB-Migrationsschritte umfassen die [Migrationsvorbereitung](pre-migration-steps.md), die Migration und die Migrationsnachbereitung, wie nachfolgend dargestellt.

![Diagramm der Migrationsschritte.](./media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-post-migration"></a>Übersicht über die Migrationsnachbereitung

Nach der Migration der in einer MongoDB-Datenbank gespeicherten Daten zur Azure Cosmos DB-API für MongoDB können Sie eine Verbindung mit Azure Cosmos DB herstellen und die Daten verwalten. Diese Anleitung enthält die Schritte, die Sie nach der Migration in Erwägung ziehen sollten. Informationen zu den Migrationsschritten finden Sie im [Tutorial zum Migrieren von MongoDB zur Azure Cosmos DB-API für MongoDB](../../dms/tutorial-mongodb-cosmos-db.md).

Führen Sie diese Schritte aus, um eine Migrationsnachbereitung durchzuführen.

- [Optimieren der Indizierungsrichtlinie](#optimize-the-indexing-policy)
- [Konfigurieren der globalen Verteilung für die Azure Cosmos DB-API für MongoDB](#globally-distribute-your-data)
- [Festlegen der Konsistenzebene](#set-consistency-level)
- [Verbinden (Übernahme) Ihrer Anwendung](#connect-or-cutover-your-application)
- [Optimieren für optimale Leistung](#tune-for-optimal-performance)

> [!NOTE]
> Der einzige obligatorische Schritt nach der Migration ist die Änderung der Verbindungszeichenfolge in Ihrer Anwendung, damit diese auf Ihr neues Azure Cosmos DB-Konto verweist. Alle anderen Schritte nach der Migration sind empfohlene Optimierungen, um die Leistung der Datenebene zu verbessern. *Wenn* Sie jedoch die Anwendungsübernahme sofort ohne die anderen Schritte ausführen, wird die Anwendung sofort die Auswirkungen einer nicht optimalen Indizierung und Konsistenz spüren. Insbesondere wenn Sie vor dem Konfigurieren Ihrer Indizes eine Übernahme vornehmen, kann es bei der Anwendung zu einem sofortigen Preis-/Leistungsabfall kommen. Beachten Sie, dass dies behoben werden kann. [Sobald der Index optimiert wurde](#optimize-the-indexing-policy), erwarten wir, dass Azure Cosmos DB die Statuslösung hinsichtlich Preis/Leistung häufig  übertrifft.
>

## <a name="pre-requisites"></a>Voraussetzungen

In diesem Leitfaden wird davon ausgegangen, dass Sie den Fortschritt Ihrer Migration mit einer Art von Tracking-Artefakt, wie z. B. einem Arbeitsblatt, aufzeichnen. Wenn Sie es noch nicht getan haben, empfiehlt es sich, den [Leitfaden zur Migrationsvorbereitung](pre-migration-steps.md) zu lesen. Dort erhalten Sie Anleitungen zum Erstellen eines *Datenbestandsmigrationsarbeitsblatts* zum Ermitteln Ihrer vorhandenen MongoDB-Ressourcen und zum Planen der Migration.

## <a name="optimize-the-indexing-policy"></a>Optimieren der Indizierungsrichtlinie

Um den Preis und die Leistung zu optimieren, empfehlen wir, das Datenbestandsmigrationsarbeitsblatt schrittweise durchzugehen und eine Indexkonfiguration für jede Ressource zu entwerfen. 
1. Es wird empfohlen, dass Sie [Ihre Indizes während der Migrationsvorbereitung planen](pre-migration-steps.md#post-migration). Fügen Sie Ihrem Datenbestandsmigrationsarbeitsblatt eine Spalte für Indexeinstellungen hinzu. 
   * Die Azure Cosmos DB-API für die MongoDB-Serverversion 3.6 und höher indiziert nur das _ID-Feld automatisch. Dieses Feld kann nicht gelöscht werden. Sie erzwingt automatisch die Eindeutigkeit des _ID-Felds durch einen Shard-Schlüssel. Um zusätzliche Felder zu indizieren, verwenden Sie die MongoDB-Indexverwaltungsbefehle. Diese Standardindizierungsrichtlinie unterscheidet sich von der Azure Cosmos DB-SQL-API, die standardmäßig alle Felder indiziert.

   * Für die Azure Cosmos DB-API für MongoDB-Serverversion 3.2 werden alle Datenfelder während der Datenmigration zu Azure Cosmos DB standardmäßig automatisch indiziert. In vielen Fällen ist diese standardmäßige Indizierungsrichtlinie akzeptabel. Im Allgemeinen können Sie durch das Entfernen von Indizes Schreibanforderungen optimieren, während die Standardindizierungsrichtlinie (d. h. die automatische Indizierung) Leseanforderungen optimiert.

   * Die von Azure Cosmos DB bereitgestellten Indizierungsfunktionen umfassen das Hinzufügen von zusammengesetzten Indizes, eindeutigen Indizes und Time-to-Live-Indizes (TTL-Indizes). Die Indexverwaltungsschnittstelle ist dem Befehl createIndex() zugeordnet. Weitere Informationen finden Sie unter Indizierung in Azure Cosmos DB und Indizierung in der Azure Cosmos DB-API für MongoDB.
2. Wenden Sie diese Indexeinstellungen während der Migrationsnachbearbeitung an.
   * MongoDB-Sammlungen mit eindeutigen Indizes werden von [Azure Database Migration Service](../../dms/tutorial-mongodb-cosmos-db.md) automatisch migriert. Die eindeutigen Indizes müssen jedoch vor der Migration erstellt werden. Wenn sich in den Sammlungen bereits Daten befinden, wird die Erstellung eindeutiger Indizes von Azure Cosmos DB nicht unterstützt. Weitere Informationen finden Sie unter [Eindeutige Schlüssel in Azure Cosmos DB](../unique-keys.md).

## <a name="globally-distribute-your-data"></a>Globales Verteilen Ihrer Daten

Azure Cosmos DB ist in allen [Azure-Regionen](https://azure.microsoft.com/regions/#services) weltweit verfügbar. 
1. Befolgen Sie die Anweisungen in dem Artikel [Globales Verteilen von Daten in der Azure Cosmos DB-API für MongoDB](tutorial-global-distribution-mongodb.md), um Ihre Daten global zu verteilen. Nachdem Sie die Standardkonsistenzebene für Ihr Azure Cosmos DB-Konto ausgewählt haben, können Sie diesem eine oder mehrere Azure-Regionen zuordnen (je nachdem, welche Anforderungen an eine globale Verteilung bestehen). Für Hochverfügbarkeit und Geschäftskontinuität empfehlen wir immer eine Ausführung in mindestens zwei Regionen. Sehen Sie sich die Tipps zum [Optimieren der Kosten für Bereitstellungen in mehreren Regionen in Azure Cosmos DB](../optimize-cost-regions.md) an.

## <a name="set-consistency-level"></a>Festlegen der Konsistenzebene

Azure Cosmos DB bietet 5 klar definierte [Konsistenzebenen](../consistency-levels.md). Informationen zur Zuordnung zwischen den Konsistenzebenen von MongoDB und Azure Cosmos DB finden Sie unter [Konsistenzebenen und Azure Cosmos DB-APIs](../consistency-levels.md). Die Standardkonsistenzebene ist „Sitzung“. Das Ändern der Konsistenzebene ist optional, kann Ihre App jedoch optimieren. So ändern Sie die Konsistenzebene im Azure-Portal

1. Wechseln Sie zum Blatt **Standardkonsistenz** unter „Einstellungen“.
2. Wählen Sie Ihre [Konsistenzebene](../consistency-levels.md) aus.

Die meisten Benutzer behalten als Konsistenzebene die Standardeinstellung „Sitzung“ bei. Sie sollten jedoch die [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](../consistency-levels.md) beachten.

## <a name="connect-or-cutover-your-application"></a>Verbinden oder Übernahme Ihrer Anwendung

Durch die Verarbeitung der Übernahme oder des Verbindens Ihrer Anwendung können Sie Ihre Anwendung auf die Verwendung von Azure Cosmos DB umzustellen, sobald die Migration abgeschlossen ist. Führen Sie diese Schritte aus:

1. Melden Sie sich in einem neuen Fenster beim [Azure-Portal](https://www.portal.azure.com/) an.
2. Öffnen Sie im [Azure-Portal](https://www.portal.azure.com/) im linken Bereich das Menü **Alle Ressourcen**, und suchen Sie das Azure Cosmos DB-Konto, zu dem Sie Ihre Daten migriert haben.
3. Öffnen Sie das Blatt **Verbindungszeichenfolge**. Der rechte Bereich enthält alle Informationen, die Sie zum erfolgreichen Verbinden des Kontos benötigen.
4. Verwenden Sie die Verbindungsinformationen in der Konfiguration Ihrer Anwendung (oder an anderen relevanten Stellen) für die Verbindung Ihrer App zur Azure Cosmos DB-API für MongoDB.
:::image type="content" source="./media/post-migration-optimization/connection-string.png" alt-text="Screenshot: Einstellungen für eine Verbindungszeichenfolge":::

Weitere Informationen finden Sie auf der Seite [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md).

## <a name="tune-for-optimal-performance"></a>Optimieren für optimale Leistung

Eine praktische Tatsache bei der [Indizierung](#optimize-the-indexing-policy), [globalen Verteilung](#globally-distribute-your-data) und [Konsistenz](#set-consistency-level) ist: Sie können alle leicht konfiguriert und neu konfiguriert werden, so wie Sie es für richtig halten. Nachdem Sie die Anwendungsübernahme abgeschlossen haben, empfehlen wir Ihnen, die Leistung Ihrer Anwendung zu überwachen und diese Einstellungen nach Bedarf anzupassen, um die Anforderungen Ihrer Anwendung zu erfüllen.

## <a name="next-steps"></a>Nächste Schritte

* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen?
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md). 
    * Sollten Ihnen die typischen Anforderungsraten Ihres aktuellen Datenbankworkloads kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe des Azure Cosmos DB Kapazitätsplaners](estimate-ru-capacity-planner.md)
* [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md)
* [Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto mithilfe von Studio 3T](connect-using-mongochef.md)
* [Globales Verteilen von Lesevorgängen mit der Azure Cosmos DB-API für MongoDB](readpreference-global-distribution.md)
* [Ablauf von Daten mit der Azure Cosmos DB-API für MongoDB](mongodb-time-to-live.md)
* [Konsistenzebenen in Azure Cosmos DB](../consistency-levels.md)
* [Indizierung in Azure Cosmos DB](../index-overview.md)
* [Anforderungseinheiten in Azure Cosmos DB](../request-units.md)