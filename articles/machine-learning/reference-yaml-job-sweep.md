---
title: YAML-Schema für Sweep-Aufträge in der Befehlszeilenschnittstelle (v2)
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema von Sweep-Aufträgen an der Befehlszeilenschnittstelle (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 5c0520b37c98395845ef1adf074e380b264a09d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339717"
---
# <a name="cli-v2-sweep-job-yaml-schema"></a>YAML-Schema für Sweep-Aufträge in der Befehlszeilenschnittstelle (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json. Das Schema wird der Einfachheit halber nachstehend im JSON- und YAML-Format bereitgestellt.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/sweepJob.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/sweepJob.schema.yml":::

---

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml job` können Sie Azure Machine Learning-Aufträge verwalten.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der Befehlszeilenschnittstelle (v2)](how-to-configure-cli.md)
