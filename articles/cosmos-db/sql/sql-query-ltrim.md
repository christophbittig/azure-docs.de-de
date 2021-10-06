---
title: LTRIM in der Abfragesprache für Azure Cosmos DB
description: Hier erfahren Sie mehr über die SQL-Systemfunktion LTRIM in Azure Cosmos DB, die einen Zeichenfolgenausdruck zurückgibt, nachdem vorangestellte Leerzeichen entfernt wurden.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dca2a6354c19031c2b55b7a61d69273a06c6f49
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661258"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Gibt einen Zeichenfolgenausdruck zurück, nachdem er führende Leerzeichen oder angegebene Zeichen entfernt hat.   
  
## <a name="syntax"></a>Syntax
  
```sql
LTRIM(<str_expr1>[, <str_expr2>])  
```  
  
## <a name="arguments"></a>Argumente
  
*str_expr1*  
   Ist ein Zeichenfolgenausdruck

*str_expr2*  
   Ist ein optionaler Zeichenfolgenausdruck, der von str_expr1 zugeschnitten werden soll. Ist er nicht gesetzt, wird standardmäßig ein Leerzeichen verwendet.
  
## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
## <a name="examples"></a>Beispiele
  
  Das folgende Beispiel veranschaulicht die Verwendung von `LTRIM` in einer Abfrage.  
  
```sql
SELECT LTRIM("   abc") AS t1, 
LTRIM("   abc   ") AS t2, 
LTRIM("abc   ") AS t3, 
LTRIM("abc") AS t4,
LTRIM("abc", "ab") AS t5,
LTRIM("abc", "abc") AS t6
```  
  
 Hier ist das Resultset.  
  
```json
[
    {
        "t1": "abc",
        "t2": "abc   ",
        "t3": "abc   ",
        "t4": "abc",
        "t5": "c",
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
