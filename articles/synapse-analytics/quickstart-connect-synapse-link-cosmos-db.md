---
title: 'Schnellstart: Herstellen einer Verbindung mit Azure Synapse Link für Azure Cosmos DB'
description: Hier erfahren Sie, wie Sie mithilfe von Synapse Link eine Azure Cosmos DB-Instanz mit einem Synapse-Arbeitsbereich verbinden.
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: synapse-link
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 6d89fd1aa0cb0047057340dd85ec1c0d318016b7
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123106788"
---
# <a name="quickstart-connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Schnellstart: Herstellen einer Verbindung mit Azure Synapse Link für Azure Cosmos DB

In diesem Artikel wird beschrieben, wie Sie über Azure Synapse Analytics Studio mit Synapse Link auf eine Azure Cosmos DB-Datenbank zugreifen. 

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um eine Verbindung zwischen einem Azure Cosmos DB-Konto und Ihrem Arbeitsbereich herstellen zu können:

* Ein vorhandenes Azure Cosmos DB-Konto. Erstellen Sie alternativ anhand [dieser Schnellstartanleitung](../cosmos-db/how-to-manage-database-account.md) ein neues Konto.
* Einen vorhandenen Synapse-Arbeitsbereich. Erstellen Sie alternativ anhand [dieser Schnellstartanleitung](./quickstart-create-workspace.md) einen neuen Arbeitsbereich. 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Aktivieren des Azure Cosmos DB-Analysespeichers

Es empfiehlt sich, Synapse Link für Azure Cosmos DB zu aktivieren, wenn Sie umfangreiche Analysen in Azure Cosmos DB ohne Beeinträchtigung der betrieblichen Leistung ausführen möchten. Diese Funktion stellt HTAP-Funktionen für Container sowie integrierte Unterstützung in Azure Synapse bereit. Befolgen Sie diese Schnellstartanleitung, um Synapse Link für Cosmos DB-Container zu aktivieren.

## <a name="navigate-to-synapse-studio"></a>Navigieren zu Synapse Studio

Wählen Sie in Ihrem Synapse-Arbeitsbereich die Option **Synapse Studio starten** aus. Wählen Sie auf der Synapse Studio-Startseite „Daten“ aus. Dadurch gelangen Sie zum **Datenobjekt-Explorer**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Herstellen einer Verbindung zwischen einer Azure Cosmos DB-Datenbank und einem Synapse-Arbeitsbereich

Die Verbindung mit einer Azure Cosmos DB-Datenbank wird über einen verknüpften Dienst hergestellt. Ein verknüpfter Cosmos DB-Dienst ermöglicht Benutzern das Durchsuchen und Untersuchen von Daten sowie Lese- und Schreibvorgänge von Apache Spark für Azure Synapse Analytics oder SQL in Azure Cosmos DB.

Über den Datenobjekt-Explorer können Sie mithilfe der folgenden Schritte direkt eine Verbindung für eine Azure Cosmos DB-Datenbank herstellen:

1. Wählen Sie neben „Daten“ das Symbol ***+*** aus.
2. Wählen Sie **Connect to external data** (Verbindung mit externen Daten herstellen) aus.
3. Wählen Sie die API aus, mit der Sie eine Verbindung herstellen möchten: SQL oder MongoDB
4. Wählen Sie ***Weiter***.
5. Geben Sie einen Namen für den verknüpften Dienst ein. Der Name wird im Objekt-Explorer angezeigt und von Synapse-Laufzeiten verwendet, um eine Verbindung mit der Datenbank und den Containern herzustellen. Es wird empfohlen, einen Anzeigenamen zu verwenden.
6. Wählen Sie den **Cosmos DB-Kontonamen** und den **Datenbanknamen** aus.
7. (Optional) Wird keine Region angegeben, werden Synapse-Laufzeitvorgänge an die nächstgelegene Region weitergeleitet, in der der Analysespeicher aktiviert ist. Sie können jedoch manuell festlegen, in welcher Region Benutzer auf den Cosmos DB-Analysespeicher zugreifen sollen. Wählen Sie **Additional connection properties** (Zusätzliche Verbindungseigenschaften) und dann **Neu** aus. Geben Sie unter **Eigenschaftenname** den Namen **_PreferredRegions_ *_ ein, und legen Sie unter _* Wert** die gewünschte Region fest (etwa „WestUS2“, ohne Leerstelle zwischen Wort und Zahl).
8. Klicken Sie auf ***Erstellen***

Azure Cosmos DB-Datenbanken werden im Azure Cosmos DB-Abschnitt auf der Registerkarte **Verknüpft** angezeigt. Anhand der folgenden Symbole können Sie einen HTAP-fähigen Azure Cosmos DB-Container von einem reinen OLTP-Container unterscheiden:

**Synapse-Container:**

![HTAP-Container](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

**Reiner OLTP-Container:**

![OLTP-Container](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Schnelles Interagieren mit durch Code generierte Aktionen

Wenn Sie mit der rechten Maustaste in einen Container klicken, wird eine Liste mit Gesten angezeigt, die eine Spark- oder SQL-Laufzeit auslösen. Das Schreiben in einen Container erfolgt über den Transaktionsspeicher von Azure Cosmos DB und verbraucht Anforderungseinheiten.  

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zu den unterstützten Features zwischen Synapse und Azure Cosmos DB](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [Informationen zum Abfragen eines Analysespeichers mit Apache Spark 3 für Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark-3.md)
* [Informationen zum Abfragen eines Analysespeichers mit Apache Spark 2 für Azure Synapse Analytics](synapse-link/how-to-query-analytical-store-spark.md)
