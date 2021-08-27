---
title: Aktivität „Variable anfügen“ in Azure Data Factory
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Aktivität „Variable anfügen“ so festlegen, dass ein Wert einer vorhandenen Arrayvariablen hinzugefügt wird, die in einer Data Factory-Pipeline definiert ist.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 4157ad2fc1cf3619b1fa4c55709f8828415f0dca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122640634"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Aktivität „Variable anfügen“ in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Verwenden Sie die Aktivität „Variable anfügen“ zum Hinzufügen eines Werts zu einer vorhandenen Arrayvariablen, die in einer Data Factory-Pipeline definiert ist.

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
name | Der Name der Aktivität in der Pipeline. | Ja
description | Text, der beschreibt, welche Aktion die Aktivität ausführt. | nein
type | Der Aktivitätstyp ist AppendVariable. | ja
value | Ein Zeichenfolgenliteral- oder Ausdrucksobjektwert, der zum Anfügen an die angegebene Variable verwendet wird. | ja
variableName | Der Name der Variablen, die von der Aktivität geändert wird. Die Variable muss vom Typ „Array“ sein. | ja

## <a name="next-steps"></a>Nächste Schritte
Machen Sie sich mit einer verwandten Ablaufsteuerungsaktivität vertraut, die von Data Factory unterstützt wird: 

- [Aktivität „Variable festlegen“](control-flow-set-variable-activity.md)
