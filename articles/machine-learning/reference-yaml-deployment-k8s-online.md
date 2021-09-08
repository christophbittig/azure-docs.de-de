---
title: 'CLI (v2): YAML-Schema für Kubernetes-Onlinebereitstellung'
titleSuffix: Azure Machine Learning
description: CLI (v2)-Referenzdokumentation für das YAML-Schema der Kubernetes-Onlinebereitstellung.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 2be11917873944c91895fc44ebb079f37cc26990
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339290"
---
# <a name="cli-v2-kubernetes-online-deployment-yaml-schema"></a>CLI (v2): YAML-Schema für Kubernetes-Onlinebereitstellung

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/k8sOnlineDeployment.schema.json. Das Schema wird der Einfachheit halber nachstehend im JSON- und YAML-Format bereitgestellt.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/k8sOnlineDeployment.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/k8sOnlineDeployment.schema.yml":::

---

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml deployment` können Sie k8sOnlineDeployments von Azure Machine Learning verwalten.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (v2)](how-to-configure-cli.md)
