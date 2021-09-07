---
title: YAML-Schema des Azure Files-Datenspeichers der CLI (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation zum YAML-Schema des Azure Files-Datenspeichers der CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: f00bc52d7992034eb080892d0859cd2449807651
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339291"
---
# <a name="cli-v2-azure-files-datastore-yaml-schema"></a>YAML-Schema des Azure Files-Datenspeichers der CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/azureFile.schema.json. Das Schema wird der Einfachheit halber nachstehend im JSON- und YAML-Format bereitgestellt.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/azureFile.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/azureFile.schema.yml":::

---

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml datastore` können Sie Azure Machine Learning-Datenspeicher verwalten.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (v2)](how-to-configure-cli.md)
