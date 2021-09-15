---
title: Einführung in die Azure Cosmos DB-API für MongoDB
description: Erfahren Sie, wie Sie Azure Cosmos DB verwenden können, um große Datenmengen mithilfe der Azure Cosmos DB-API für MongoDB zu speichern und abzufragen.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.openlocfilehash: bce5b5874b12638d1f2d628e23812676053006a4
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035202"
---
# <a name="azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Mit der Azure Cosmos DB-API für MongoDB können Sie Cosmos DB ganz einfach wie eine MongoDB-Datenbank verwenden. Sie können auf Ihre MongoDB-Erfahrung zurückgreifen und weiter Ihre bevorzugten MongoDB-Treiber, -SDKs und -Tools verwenden, indem Sie Ihre Anwendung auf die Verbindungszeichenfolge des API für MongoDB-Kontos verweisen.

## <a name="why-choose-the-api-for-mongodb"></a>Gründe für die Verwendung der API für MongoDB

Da die API für MongoDB auf [Azure Cosmos DB](../introduction.md) basiert, bietet sie im Vergleich zu Dienstangeboten wie MongoDB Atlas zahlreiche zusätzliche Vorteile:

* **Sofortige Skalierbarkeit:** Wenn das Feature [Autoskalierung](../provision-throughput-autoscale.md) aktiviert ist, lässt sich Ihre Datenbank ganz ohne Aufwärmphase hoch- und herunterskalieren.
* **Automatisches und transparentes Sharding:** Die API für MongoDB übernimmt die Verwaltung der gesamten Infrastruktur für Sie. Im Gegensatz zu anderen MongoDB-Angeboten wie MongoDB Atlas, bei denen Sie das Sharding für die horizontale Skalierung selbst angeben und verwalten müssen, sind hier das Sharding und die Anzahl von Shards mit eingeschlossen. Dadurch haben Sie mehr Zeit, sich auf die Entwicklung von Anwendungen für Ihre Benutzer zu konzentrieren.
* **Verfügbarkeit von 99,999 Prozent:** Sie können mühelos eine [Verfügbarkeit von 99,999 Prozent](../high-availability.md) konfigurieren, um sicherzustellen, dass Ihre Daten stets verfügbar sind.  
* **Kostengünstige, präzise und unbegrenzte Skalierbarkeit:** Im Gegensatz zu anderen MongoDB-Dienstangeboten lassen sich Sammlungen mit Shards auf eine beliebige Größe skalieren. Benutzer der API für MongoDB nutzen aktuell Datenbanken mit mehr als 600 TB Speicher. Dank Skaleneffekten und Ressourcengovernance lässt sich die Cosmos DB-Plattform im Gegensatz zu anderen MongoDB-Dienstangeboten in Schritten ab einem Hundertstel eines virtuellen Computers kosteneffizient skalieren.
* **Serverlose Bereitstellungen:** Im Gegensatz zu MongoDB Atlas ist die API für MongoDB eine native Clouddatenbank mit einem [serverlosen Kapazitätsmodus](../serverless.md). Im [serverlosen Modus](../serverless.md) ist die Abrechnung rein vorgangsbasiert, und sie bezahlen nicht für die Datenbank, wenn Sie sie nicht verwenden.
* **Free-Tarif:** Beim Free-Tarif für Azure Cosmos DB sind die ersten 1.000 RUs/Sek. und 25 GB Speicher in Ihrem Konto dauerhaft kostenlos (angewendet auf der Kontoebene).
* **Upgrades in Sekundenschnelle:** Alle API-Versionen sind in einer einzelnen Codebasis enthalten, was Versionsänderungen auf [Knopfdruck](upgrade-mongodb-version.md) und ganz ohne Ausfallzeiten ermöglicht.
* **Echtzeitanalyse (HTAP) in beliebigem Umfang:** Die API für MongoDB ermöglicht komplexe analytische Abfragen in Echtzeit für Anwendungsfälle wie Business Intelligence auf der Grundlage Ihrer Datenbankdaten ohne Auswirkungen auf Ihre Datenbank. Da hierbei der cloudnative spaltenbasierte Analysespeicher ohne ETL-Pipelines genutzt wird, ist dieses Verfahren schnell und kostengünstig. Weitere Informationen zu Azure Synapse Link finden Sie [hier](../synapse-link.md).

> [!NOTE]
> [Die Azure Cosmos DB-API für MongoDB kann mit dem Free-Tarif kostenlos genutzt werden.](../free-tier.md) Beim Free-Tarif für Azure Cosmos DB erhalten Sie die ersten 1.000 RUs/Sek. und 25 GB Speicher in Ihrem Konto kostenlos, angewendet auf der Kontoebene.


## <a name="how-the-api-works"></a>Funktionsweise der API

Die Azure Cosmos DB-API für MongoDB implementiert das Wire Protocol für MongoDB. Diese Implementierung ermöglicht eine transparente Kompatibilität mit nativen MongoDB-Client-SDKs, -Treibern und -Tools. Azure Cosmos DB hostet nicht die MongoDB-Datenbank-Engine. Von jedem MongoDB-Clienttreiber, der mit der von Ihnen verwendeten API-Version kompatibel ist, sollte ohne spezielle Konfiguration eine Verbindung hergestellt werden können.

MongoDB-Featurekompatibilität:

Die Azure Cosmos DB-API für MongoDB ist mit folgenden MongoDB-Serverversionen kompatibel:
- [Version 4.0](feature-support-40.md)
- [Version 3.6](feature-support-36.md)
- [Version 3.2](feature-support-32.md)

Da allen Versionen der API für MongoDB die gleiche Codebasis zugrunde liegt, können Upgrades problemlos in Sekundenschnelle und ganz ohne Ausfallzeiten durchgeführt werden. Von Azure Cosmos DB werden einfach ein paar Featureflags auf eine andere Version umgestellt.  Die Featureflags ermöglichen auch die weitere Unterstützung älterer API-Versionen wie 3.2 und 3.6. Sie können die Serverversion wählen, die für Sie am besten geeignet ist.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="MongoDB-API von Azure Cosmos DB" border="false":::

## <a name="what-you-need-to-know-to-get-started"></a>Wichtige Informationen für den Einstieg

* Virtuelle Computer in einem Cluster werden Ihnen nicht in Rechnung gestellt. Die [Preise](../how-pricing-works.md) basieren auf dem Durchsatz in Anforderungseinheiten (Request Units, RUs), der pro Datenbank oder pro Sammlung konfiguriert ist. Die ersten 1.000 RUs pro Sekunde sind im [Free-Tarif](../free-tier.md) kostenlos.

* Die Azure Cosmos DB-API für MongoDB kann auf drei Arten bereitgestellt werden:
     * [Bereitgestellter Durchsatz:](../set-throughput.md) Legen Sie einen Wert für RU/s fest, und ändern Sie ihn manuell. Dieses Modell eignet sich am besten für konsistente Workloads.
     * [Autoscale]../(provision-throughput-autoscale.md): Legen Sie eine Obergrenze für den benötigten Durchsatz fest. Der Durchsatz wird sofort gemäß Ihren Anforderungen skaliert. Dieses Modell eignet sich am besten für Workloads, die sich häufig ändern, und optimiert deren Kosten.
     * [Serverlos](../serverless.md): Bezahlen Sie nur für den von Ihnen genutzten Durchsatz. Dieses Modell eignet sich am besten für Entwicklungs-/Testworkloads. 

* Die Leistung von Shardclustern hängt vom Shardschlüssel ab, der beim Erstellen einer Sammlung gewählt wird. Wählen Sie den Shardschlüssel mit Bedacht, um eine gleichmäßige Verteilung Ihrer Daten auf Shards zu gewährleisten.

### <a name="capacity-planning"></a>Kapazitätsplanung

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](../estimate-ru-with-capacity-planner.md)

## <a name="quickstart"></a>Schnellstart

* [Migrieren einer vorhandenen Node.js-MongoDB-Web-App](create-mongodb-nodejs.md)
* [Erstellen einer Web-App mit der Azure Cosmos DB-API für MongoDB und dem .NET SDK](create-mongodb-dotnet.md)
* [Erstellen einer Konsolen-App mit der Azure Cosmos DB-API für MongoDB und dem Java SDK](create-mongodb-java.md)
* [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder vCPUs](../convert-vcore-to-request-unit.md) 
* [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](../estimate-ru-with-capacity-planner.md)

## <a name="next-steps"></a>Nächste Schritte

* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
    * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md) 
    * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbankworkload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md).
* Im Tutorial [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md) erfahren Sie, wie Sie Ihre Kontoverbindungszeichenfolge abrufen.
* Im Tutorial zum [Verwenden von Studio 3T mit Azure Cosmos DB](connect-using-mongochef.md) erfahren Sie, wie Sie eine Verbindung zwischen Ihrer Cosmos-Datenbank und einer MongoDB-App in Studio 3T erstellen.
* Folgen Sie den Anweisungen im Tutorial zum [Importieren von MongoDB-Daten in Azure Cosmos DB](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json), um Ihre vorhandenen Daten in eine Cosmos-Datenbank zu importieren.
* Stellen Sie mithilfe von [Robo 3T](connect-using-robomongo.md) eine Verbindung mit einem Cosmos-Konto her.
* Erfahren Sie, wie Sie [Leseeinstellungen für global verteilte Apps konfigurieren](tutorial-global-distribution-mongodb.md).
* Die Lösungen für häufige Fehler finden Sie in unserem [Leitfaden zur Problembehandlung](error-codes-solutions.md).


<sup>Hinweis: Dieser Artikel beschreibt ein Feature von Azure Cosmos DB, das Wire Protocol-Kompatibilität mit MongoDB-Datenbanken bietet. Microsoft führt keine MongoDB-Datenbanken aus, um diesen Dienst bereitzustellen. Azure Cosmos DB ist kein Partner von MongoDB, Inc.</sup>