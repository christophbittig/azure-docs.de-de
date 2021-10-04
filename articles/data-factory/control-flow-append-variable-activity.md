---
title: Aktivität „Variable anfügen“
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Append Variable-Aktivität so festlegen, dass ein Wert einer vorhandenen Arrayvariablen hinzugefügt wird, die in einer Data Factory- oder Synapse Analytics-Pipeline definiert ist.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 09/09/2021
ms.openlocfilehash: 9d2a081535f571b139a5cb15cdb85b37e2c3af97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811702"
---
# <a name="append-variable-activity-in-azure-data-factory-and-synapse-analytics"></a>Append Variable-Aktivität in Azure Data Factory und Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Verwenden Sie die Append Variable-Aktivität, um einen Wert zu einer vorhandenen Arrayvariablen hinzuzufügen, die in einer Data Factory- oder Synapse Analytics-Pipeline definiert ist.

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
name | Der Name der Aktivität in der Pipeline. | Ja
description | Text, der beschreibt, welche Aktion die Aktivität ausführt. | nein
type | Der Aktivitätstyp ist AppendVariable. | ja
value | Ein Zeichenfolgenliteral- oder Ausdrucksobjektwert, der zum Anfügen an die angegebene Variable verwendet wird. | ja
variableName | Der Name der Variablen, die von der Aktivität geändert wird. Die Variable muss vom Typ „Array“ sein. | ja

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über eine verwandte Ablaufsteuerungsaktivität: 

- [Aktivität „Variable festlegen“](control-flow-set-variable-activity.md)
