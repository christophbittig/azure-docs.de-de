---
title: Herstellen einer Verbindung mit GitHub
titleSuffix: Azure Data Factory & Azure Synapse
description: Verwenden von GitHub zum Angeben von Common Data Model-Entitätsverweisen
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: jingwang
ms.openlocfilehash: 659b0883e1b4085c9a8ad61f3512909a9a738a66
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122640982"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Verwenden von GitHub zum Lesen von Common Data Model-Entitätsverweisen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Der GitHub-Connector in Azure Data Factory wird nur zum Empfangen des Entitätsverweisschemas für das [Common Data Model](format-common-data-model.md)-Format im Zuordnungsdatenfluss verwendet.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit GitHub verknüpften Dienst unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft muss auf **GitHub** festgelegt werden. | ja
| userName | GitHub-Benutzername | ja |
| password | GitHub-Kennwort | ja |

## <a name="next-steps"></a>Nächste Schritte

Erstellen eines [Quelldatasets](data-flow-source.md) im Zuordnungsdatenfluss