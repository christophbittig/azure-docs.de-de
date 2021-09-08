---
title: 'CLI (v2): YAML-Schema für Kubernetes-Onlineendpunkt'
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema des Kubernetes-Onlineendpunkts (CLI v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: ad1946ca0460257365cdb589af21ee9f37e6f16f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339289"
---
# <a name="cli-v2-kubernetes-online-endpoint-yaml-schema"></a>CLI (v2): YAML-Schema für Kubernetes-Onlineendpunkt

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/k8sOnlineEndpoint.schema.json. Das Schema wird unten der Einfachheit halber im JSON- und YAML-Format bereitgestellt.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/k8sOnlineEndpoint.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/k8sOnlineEndpoint.schema.yml":::

---

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml endpoint` können Sie Azure Machine Learning-Endpunkte verwalten.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
