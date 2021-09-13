---
title: Übersicht über YAML-Schemas für die Azure Machine Learning-CLI (v2)
titleSuffix: Azure Machine Learning
description: Dieser Artikel bietet eine Übersicht über die YAML-Schemas für die Azure Machine Learning-CLI (v2) und deren Index.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 4d98ebbebbb25e2ab903e06162114f8fa026fbda
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340208"
---
# <a name="cli-v2-yaml-schemas"></a>YAML-Schemas für CLI (v2)

Die Azure Machine Learning-CLI (v2) ist eine Erweiterung der Azure CLI, die oft YAML-Dateien mit bestimmten Schemas verwendet und diese in manchen Fällen erfordert. In diesem Artikel werden Referenzdokumentationen und das Quellschema für YAML-Dateien aufgeführt.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="assets"></a>Objekte

Verweis | URI
- | -
[Arbeitsbereich](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json
[Compute](reference-yaml-compute.md) | https://azuremlschemas.azureedge.net/latest/compute.schema.json
[Umgebung](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json
[Dataset](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json
[Modell](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json

## <a name="datastores"></a>Datenspeicher

Verweis | URI
- | -
[Azure-Blob](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
[Azure Files](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json
[Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json
[Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json

## <a name="jobs"></a>Aufträge

Verweis | URI
- | -
[Befehl](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
[Sweep](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json

## <a name="endpoints"></a>Endpunkte

Verweis | URI
- | -
[Onlineverwaltung (Echtzeit)](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json
[Verwalteter Batch](reference-yaml-endpoint-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json
[Kubernetes-Onlineendpunkt (k8s) (Echtzeit)](reference-yaml-endpoint-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnlineEndpoint.schema.json

## <a name="deployments"></a>Bereitstellungen

Verweis | URI
- | -
[Onlineverwaltung (Echtzeit)](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json
[Verwalteter Batch](reference-yaml-deployment-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json
[Kubernetes-Onlinebereitstellung (k8s) (Echtzeit)](reference-yaml-deployment-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnelineDeployment.schema.json

## <a name="next-steps"></a>Nächste Schritte

- [Installieren, Einrichten und Verwenden der 2.0 CLI (Vorschau)](how-to-configure-cli.md)
