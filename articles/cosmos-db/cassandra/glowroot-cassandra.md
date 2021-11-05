---
title: Ausführen von Glowroot in Azure Cosmos DB Cassandra-API (Vorschau)
description: In diesem Artikel wird beschrieben, wie Sie Glowroot in Azure Cosmos DB-Cassandra-API ausführen.
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 1808e3df0b70f61c3cfabf96e61b6675dff6b011
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095919"
---
# <a name="run-glowroot-on-azure-cosmos-db-cassandra-api"></a>Ausführen von Glowroot in Azure Cosmos DB Cassandra-API
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Glowroot ist ein Tool zur Verwaltung der Anwendungsleistung, das zum Optimieren und Überwachen der Leistung Ihrer Anwendungen verwendet wird. In diesem Artikel wird erläutert, wie Sie nun In Azure Cosmos DB Cassandra-API die Leistung Ihrer Anwendung mithilfe von Glowroot überwachen können.

## <a name="prerequisites-and-setup"></a>Voraussetzungen und Setup

* [Erstellen Sie ein Azure Cosmos DB-Cassandra-API-Konto.](manage-data-java.md#create-a-database-account)
* [JAVA (Version 8) für Windows installieren](https://developers.redhat.com/products/openjdk/download)
> [!NOTE]
> Beachten Sie, dass bekannt ist, dass bestimmte Buildziele mit neueren Versionen inkompatibel sind. Wenn Sie bereits über eine neuere Version von JAVA verfügen, können Sie JDK8 trotzdem herunterladen.
> Wenn Sie zusätzlich zu JDK8 neueres JAVA installiert haben: Legen Sie die Variable %JAVA_HOME% in der lokalen Eingabeaufforderung auf JDK8 fest. Dadurch wird nur die Java-Version für die aktuelle Sitzung geändert, und die Einstellungen des globalen Computers bleiben erhalten. 
* [Maven installieren](https://maven.apache.org/download.cgi)
    * Erfolgreiche Installation bestätigen, durch Ausführen von `mvn --version`

## <a name="run-glowroot-central-collector-with-cosmos-db-endpoint"></a>Zentralen Collector von Glowroot mit Cosmos DB-Endpunkt ausführen
Sobald die Endpunktkonfiguration abgeschlossen ist. 
1. [Laden Sie die zentrale Collectorverteilung von Glowroot herunter.](https://github.com/glowroot/glowroot/wiki/Central-Collector-Installation#central-collector-installation)
2. Füllen Sie in der Datei „glowroot-central.properties“ die folgenden Eigenschaften von Ihrem Cosmos DB-Cassandra-API-Endpunkt auf.
    * cassandra.contactPoints
    * cassandra.username
    * cassandra.password
3. Legen Sie die Eigenschaften `cassandra.ssl=true`, `cassandra.gcGraceSeconds=0` und `cassandra.port=10350` fest.
4. Stellen Sie sicher, dass sich die Datei „glowroot-central.properties“ im selben Ordner wie die Datei „glowroot-central.jar“ befindet.
5. Führen Sie `java -jar glowroot-central.jar` aus, um mit der Ausführung von „Glowroot“ zu beginnen.

## <a name="faqs"></a>Häufig gestellte Fragen
Öffnen Sie ein Supportticket, wenn beim Ausführen oder Testen von Glowroot Probleme auftreten. Geben Sie die Abonnement-ID und den Kontonamen an, unter denen der Test von Glowroot ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte
- Erste Schritte mit dem [Erstellen eines Cassandra-API-Kontos, einer Datenbank und einer Tabelle](create-account-java.md) mithilfe einer Java-Anwendung.
- Informieren Sie sich über [unterstützte Features](cassandra-support.md) in der Cassandra-API von Azure Cosmos DB.
