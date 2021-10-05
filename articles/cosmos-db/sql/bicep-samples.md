---
title: 'Bicep-Beispiele für Azure Cosmos DB: Core-API (SQL)'
description: Verwenden Sie Bicep zum Erstellen und Konfigurieren von Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 8cab03d2b3cd3c6d7b6a2f9bd15c686bea529de9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700392"
---
# <a name="bicep-for-azure-cosmos-db"></a>Bicep für Azure Cosmos DB

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

In diesem Artikel werden Bicep-Beispiele für Core-API-Konten (SQL) gezeigt. Es gibt jedoch auch Bicep-Beispiele für die [Cassandra](../cassandra/manage-with-bicep.md)-, [Gremlin](../graph/manage-with-bicep.md)-, [MongoDB](../mongodb/manage-with-bicep.md)- und [Tabellen](../table/manage-with-bicep.md)-APIs.

## <a name="core-sql-api"></a>Core-API (SQL)

|**Beispiel**|**Beschreibung**|
|---|---|
|[Erstellen eines Azure Cosmos-Kontos, einer Datenbank und eines Containers mit automatisch skaliertem Durchsatz](manage-with-bicep.md#create-autoscale) | Erstellt ein Core-API-Konto (SQL) in zwei Regionen, eine Datenbank und einen Container mit automatisch skaliertem Durchsatz |
|[Erstellen eines Azure Cosmos-Kontos, einer Datenbank und eines Containers mit Analysespeicher](manage-with-bicep.md#create-analytical-store) | Erstellt ein Konto für die Core-API (SQL) in einer Region mit einem Container, für den die analytische Gültigkeitsdauer aktiviert ist. Bietet außerdem die Möglichkeit, die manuelle oder automatische Skalierung des Durchsatzes zu verwenden. |
|[Erstellen eines Azure Cosmos-Kontos, einer Datenbank und eines Containers mit (manuell bereitgestelltem) Standarddurchsatz](manage-with-bicep.md#create-manual) | Erstellt ein Core-API-Konto (SQL) in zwei Regionen, eine Datenbank und einen Container mit Standarddurchsatz |
|[Erstellen eines Azure Cosmos-Kontos, einer Datenbank und eines Containers mit einer gespeicherten Prozedur, einem Trigger und einer UDF](manage-with-bicep.md#create-sproc) | Erstellt ein Core-API-Konto (SQL) in zwei Regionen mit einer gespeicherten Prozedur, einem Trigger und einer benutzerdefinierten Funktion für einen Container |
|[Erstellen eines Azure Cosmos-Kontos mit Azure AD Identität, Rollendefinitionen und Rollenzuweisung](manage-with-bicep.md#create-rbac) | Erstellt ein Core-API-Konto (SQL) mit Azure AD-Identität, Rollendefinitionen und Rollenzuweisung für einen Dienstprinzipal |
|[Erstellen eines Azure Cosmos-Kontos im Free-Tarif](manage-with-bicep.md#free-tier) |  Erstellt ein Azure Cosmos DB-Core-API-Konto (SQL) im Free-Tarif |

## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.

* Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md)
* Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-with-capacity-planner.md)
