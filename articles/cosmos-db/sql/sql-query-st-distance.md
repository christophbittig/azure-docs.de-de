---
title: ST_DISTANCE in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion ST_DISTANCE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b1cf5bdad567a82bd3936f2b2a187375259407ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625341"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Gibt den Abstand zwischen den beiden GeoJSON-Punkt-, Polygon-, MultiPolygon- oder LineString-Ausdrücken zurück. Weitere Informationen finden Sie im Artikel [Räumliche Daten und GeoJSON-Standortdaten in Azure Cosmos DB](sql-query-geospatial-intro.md).
  
## <a name="syntax"></a>Syntax
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumente
  
*spatial_expr*  
   Ist ein beliebiger gültiger GeoJSON-Punkt-, -Polygon- oder -LineString-Objektausdruck.  
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen numerischen Ausdruck zurück, der den Abstand enthält. Dieser wird für das Standardreferenzsystem in Metern angegeben.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel zeigt die Rückgabe aller Familiendokumente, die sich bis zu 30 km vom angegebenen Ort entfernt befinden. Dazu wird die integrierte `ST_DISTANCE`-Funktion verwendet. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Hier ist das Resultset.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Bemerkungen

Diese Systemfunktion profitiert von einem [räumlichen Index](../index-policy.md#spatial-indexes) (außer bei Abfragen mit Aggregaten).

> [!NOTE]
> Die GeoJSON-Spezifikation erfordert, dass die Punkte innerhalb eines Polygons gegen den Uhrzeigersinn angegeben werden. Ein Polygon, das im Uhrzeigersinn angegeben wird, stellt die Umkehrung der darin enthaltenen Region dar.

## <a name="next-steps"></a>Nächste Schritte

- [Räumliche Funktionen in Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](../introduction.md)
