---
title: Regeln für die Benennung von Azure Data Factory-Entitäten (Version 1)
description: Beschreibt die Benennungsregeln für Data Factory v1-Entitäten.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: e8420bbe9ece67ab8be9b9cf3a3357e9d44cde3e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264140"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Regeln für die Benennung von Azure Data Factory-Entitäten
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Benennungsregeln in Data Factory](../naming-rules.md).

Die folgende Tabelle enthält Benennungsregeln für Data Factory-Artefakte.

| Name | Eindeutigkeit des Namens | Überprüfungen |
|:--- |:--- |:--- |
| Data Factory |Für Microsoft Azure eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt, sodass `MyDF` und `mydf` auf die gleiche Data Factory verweisen. |<ul><li>Jede Data Factory ist an genau ein Azure-Abonnement gebunden.</li><li>Objektnamen müssen mit einem Buchstaben oder einer Zahl beginnen und dürfen nur Buchstaben, Zahlen und Bindestriche (-) enthalten.</li><li>Jedem Bindestrich (-) muss unmittelbar ein Buchstabe oder eine Ziffer vorangestellt werden und er muss von diesen gefolgt sein. Aufeinanderfolgende Bindestriche sind in Containernamen nicht zulässig.</li><li>Namen können 3 bis 63 Zeichen lang sein.</li></ul> |
| Verknüpfte Dienste/Tabellen/Pipelines |Innerhalb einer Data Factory eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. |<ul><li>Maximale Anzahl von Zeichen in einem Tabellennamen: 260.</li><li>Objektnamen müssen mit einem Buchstaben, einer Zahl oder einem Unterstrich (_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\\“</li></ul> |
| Ressourcengruppe |Für Microsoft Azure eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. |<ul><li>Maximale Anzahl von Zeichen: 1000.</li><li>Der Name kann Buchstaben, Ziffern und die folgenden Zeichen enthalten: „-“, „_“, „,“ und „.“</li></ul> |

