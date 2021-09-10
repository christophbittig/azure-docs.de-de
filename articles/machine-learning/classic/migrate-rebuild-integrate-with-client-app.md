---
title: 'ML Studio (Classic): Migrieren zu Azure Machine Learning – Nutzen von Pipelineendpunkten'
description: Integrieren von Pipelineendpunkten in Clientanwendungen in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: c81e0439f9bbde3a24f964e9d7f4d9e3fd2d95c5
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112582796"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Nutzen von Pipelineendpunkten aus Clientanwendungen

In diesem Artikel erfahren Sie, wie Sie Clientanwendungen in [Azure Machine Learning](../index.yml)-Endpunkten integrieren. Weitere Informationen zum Schreiben von Anwendungscode finden Sie unter [Nutzen eines Azure Machine Learning-Endpunkts](../how-to-consume-web-service.md).

Dieser Artikel ist Teil der Reihe zur Migration von Studio (Classic) zu Azure Machine Learning. Weitere Informationen zur Migration zu Azure Machine Learning finden Sie im Artikel mit der [allgemeinen Übersicht über die Migration](migrate-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein Azure Machine Learning-Arbeitsbereich. [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich](../how-to-manage-workspace.md#create-a-workspace).
- Ein [Azure Machine Learning-Echtzeitendpunkt oder -Pipelineendpunkt](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Nutzen eines Echtzeitendpunkts 

Wenn Sie Ihr Modell als **Echtzeitendpunkt** bereitgestellt haben, können Sie den zugehörigen REST-Endpunkt und vorab generierten Code in C#, Python und R wie folgt suchen:

1. Wechseln Sie zu Azure Machine Learning Studio ([ml.azure.com](https://ml.azure.com)).
1. Wechseln Sie zur Registerkarte **Endpunkte**.
1. Wählen Sie den Echtzeitendpunkt aus.
1. Wählen Sie **Nutzen** aus.

> [!NOTE]
> Sie können die Swagger-Spezifikation für ihren Endpunkt auch auf der Registerkarte **Details** finden. Verwenden Sie die Swagger-Definition, um das Endpunktschema zu verstehen. Weitere Informationen zur Swagger-Definition finden Sie in der [offiziellen Swagger-Dokumentation](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Nutzen eines Pipelineendpunkts

Es gibt zwei Möglichkeiten, einen Pipelineendpunkt zu nutzen:

- REST-API-Aufrufe
- Integration in Azure Data Factory

### <a name="use-rest-api-calls"></a>Verwenden von REST-API-Aufrufen

Rufen Sie den REST-Endpunkt über die Clientanwendung auf. Sie können die Swagger-Spezifikation für den Endpunkt verwenden, um das zugehörige Schema zu verstehen:

1. Wechseln Sie zu Azure Machine Learning Studio ([ml.azure.com](https://ml.azure.com)).
1. Wechseln Sie zur Registerkarte **Endpunkte**.
1. Wählen Sie **Pipelineendpunkte** aus.
1. Wählen Sie Ihren Pipelineendpunkt aus.
1. Wählen Sie im Bereich **Endpunkt für Pipeline – Übersicht** den Link unter **Dokumentation zu REST-Endpunkt** aus.

### <a name="use-azure-data-factory"></a>Verwenden von Azure Data Factory

Sie können die Azure Machine Learning-Pipeline als Schritt in einer Azure Data Factory-Pipeline aufrufen. Weitere Informationen finden Sie unter [Ausführen von Azure Machine Learning-Pipelines in Azure Data Factory-Pipelines](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Schema- und Beispielcode für Pipelineendpunkte auffinden. Weitere Informationen zum Nutzen von Endpunkten aus der Clientanwendung finden Sie unter [Nutzen eines Azure Machine Learning-Endpunkts](../how-to-consume-web-service.md).

Weitere Informationen finden Sie in den übrigen Artikeln der Reihe zur Migration zu Azure Machine Learning: 
1. [Übersicht über die Migration](migrate-overview.md).
1. [Migrieren eines Datasets](migrate-register-dataset.md)
1. [Neuerstellen einer Studio (Classic)-Trainingspipeline](migrate-rebuild-experiment.md)
1. [Neuerstellen eines Studio (Classic)-Webdiensts](migrate-rebuild-web-service.md)
1. **Integrieren eines Azure Machine Learning-Webdiensts in Clientanwendungen**
1. [Migrieren von „R-Skript ausführen“-Modulen](migrate-execute-r-script.md)