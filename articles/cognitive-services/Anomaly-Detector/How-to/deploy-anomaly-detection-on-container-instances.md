---
title: Ausführen eines Containers für die Anomalieerkennung in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Stellen Sie den Container für die Anomalieerkennung in einer Azure Container Instances-Instanz bereit, und testen Sie diesen in einem Webbrowser.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: ec057d625342c2e80478b0555395f6bc5250ee6b
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296773"
---
# <a name="deploy-an-anomaly-detector-univariate-container-to-azure-container-instances"></a>Bereitstellen eines Containers zur Erkennung von univariaten Anomalien in Azure Container Instances

Erfahren Sie, wie Sie Container für die [Anomalieerkennung](../anomaly-detector-container-howto.md) von Cognitive Services für Azure [Container Instances](../../../container-instances/index.yml) bereitstellen. Dieses Verfahren veranschaulicht die Erstellung der Ressource für die Anomalieerkennung. Anschließend wird das Abrufen des dazugehörigen Containerimages erläutert. Abschließend erklären wir, wie Sie die Orchestrierung von Ressource und Image über einen Browser ausführen können. Die Verwendung von Containern kann die Aufmerksamkeit der Entwickler von der Verwaltung der Infrastruktur auf die Anwendungsentwicklung lenken.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Abrufen des Containerimages per Pull und Ausführen des Containers finden Sie unter [Konfigurieren von Containern für die Anomalieerkennung](../anomaly-detector-container-configuration.md).
* Konfigurationseinstellungen finden Sie unter [Konfigurieren von Containern](../anomaly-detector-container-configuration.md).
* [Erfahren Sie mehr über den Anomalieerkennungs-API-Dienst.](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)