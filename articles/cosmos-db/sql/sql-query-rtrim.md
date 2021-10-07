---
title: RTRIM in der Abfragesprache für Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Systemfunktion RTRIM in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f1dfaa8de0f6fa6d04fe91618a68a79d4121b7bd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647764"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Gibt einen Zeichenfolgenausdruck zurück, nachdem es Leerzeichen am Ende oder bestimmte Zeichen entfernt.  
  
## <a name="syntax"></a>Syntax
  
```sql
RTRIM(<str_expr1>[, <str_expr2>])  
```  
  
## <a name="arguments"></a>Argumente
  
*str_expr1*  
   Ist ein Zeichenfolgenausdruck

*str_expr2*  
   Ein optionaler Zeichenfolgenausdruck, der aus der str_expr1 gekürzt werden soll. Wenn nicht festgelegt, wird standardmäßig ein Leerzeichen verwendet.  
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel veranschaulicht die Verwendung von `RTRIM` in einer Abfrage.  
  
```sql
SELECT RTRIM("   abc") AS t1, 
RTRIM("   abc   ") AS t2, 
RTRIM("abc   ") AS t3, 
RTRIM("abc") AS t4,
RTRIM("abc", "bc") AS t5,
RTRIM("abc", "abc") AS t6
```  
  
 Hier ist das Resultset.  
  
```json
[
    {
        "t1": "   abc",
        "t2": "   abc",
        "t3": "abc",
        "t4": "abc",
        "t5": "a",
        "t6": ""
    }
]
``` 

## <a name="remarks"></a>Bemerkungen

Der Index wird von dieser Systemfunktion nicht verwendet.

## <a name="next-steps"></a>Nächste Schritte

- [Zeichenfolgenfunktionen in Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](../introduction.md)
