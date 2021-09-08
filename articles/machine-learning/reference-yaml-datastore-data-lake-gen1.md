---
title: CLI (V2) YAML-Schema für Azure Data Lake Gen1-Datenspeicher
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema des Azure Data Lake Gen1-Datenspeichers der CLI (V2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 58b8f0ac3cbdb69d828544cfaf1d095c7784e966
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356045"
---
# <a name="cli-v2-azure-data-lake-gen1-yaml-schema"></a>CLI (V2) YAML-Schema für Azure Data Lake Gen1

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json. Das Schema wird der Einfachheit halber im JSON- und YAML-Format bereitgestellt.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/azureDataLakeGen1.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/azureDataLakeGen1.schema.yml":::

---

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml datastore` können Sie den Azure Machine Learning-Datenspeicher verwalten.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der Befehlszeilenschnittstelle (V2)](how-to-configure-cli.md)
