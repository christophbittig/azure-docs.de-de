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
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 541cb9049be64977858a10cd486605f9572b3f30
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561345"
---
# <a name="cli-v2-yaml-schemas"></a>YAML-Schemas für CLI (v2)

Die Azure Machine Learning-CLI (v2) ist eine Erweiterung der Azure CLI, die oft YAML-Dateien mit bestimmten Schemas verwendet und diese in manchen Fällen erfordert. In diesem Artikel werden Referenzdokumentationen und das Quellschema für YAML-Dateien aufgeführt. Beispiele sind inline in einzelnen Artikeln enthalten.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="workspace"></a>Arbeitsbereich

| Verweis | URI |
| - | - |
| [Arbeitsbereich](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json |

## <a name="environment"></a>Umgebung

| Verweis | URI |
| - | - |
| [Umgebung](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json |

## <a name="dataset"></a>Dataset

| Verweis | URI |
| - | - |
| [Dataset](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json |

## <a name="model"></a>Modell

| Verweis | URI |
| - | - |
| [Modell](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json |

## <a name="compute"></a>Compute

| Verweis | URI |
| - | - |
| [Computecluster (AmlCompute)](reference-yaml-compute-aml.md) | https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json |
| [Compute-Instanz](reference-yaml-compute-instance.md) | https://azuremlschemas.azureedge.net/latest/computeInstance.schema.json |
| [Angefügter virtueller Computer](reference-yaml-compute-vm.md) | https://azuremlschemas.azureedge.net/latest/vmCompute.schema.json |

## <a name="job"></a>Auftrag

| Verweis | URI |
| - | - |
| [Befehl](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json |
| [Sweep](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json |
| [Pipeline](reference-yaml-job-pipeline.md) | https://azuremlschemas.azureedge.net/latest/pipelineJob.schema.json |

## <a name="datastore"></a>Datenspeicher

| Verweis | URI |
| - | - |
| [Azure-Blob](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json |
| [Azure Files](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json |
| [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json |
| [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json |

## <a name="endpoint"></a>Endpunkt

| Verweis | URI |
| - | - |
| [Onlineverwaltung (Echtzeit)](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json |
| [Batch](reference-yaml-endpoint-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json |

## <a name="deployment"></a>Bereitstellung

| Verweis | URI |
| - | - |
| [Onlineverwaltung (Echtzeit)](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json |
| [Batch](reference-yaml-deployment-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json |

## <a name="component"></a>Komponente

| Verweis | URI |
| - | - |
| [Befehl](reference-yaml-component-command.md) | https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json |

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
