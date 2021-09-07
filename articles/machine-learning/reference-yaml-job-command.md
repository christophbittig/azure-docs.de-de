---
title: YAML-Schema für Befehlsaufträge der CLI (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema für Befehlsaufträge der CLI (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: d3a851628dd56bafe4c82bc9d1da8e44e9a99a90
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355001"
---
# <a name="cli-v2-command-job-yaml-schema"></a>YAML-Schema für Befehlsaufträge der CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/commandJob.schema.json. Das Schema wird der Einfachheit halber im JSON- und YAML-Format bereitgestellt.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/commandJob.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/commandJob.schema.yml":::

---

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml job` können Sie Azure Machine Learning-Aufträge verwalten.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (v2)](how-to-configure-cli.md)
