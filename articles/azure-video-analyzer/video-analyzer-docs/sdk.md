---
title: Azure Video Analyzer SDKs und Ressourcen
description: Hier erfahren Sie mehr über die Azure Video Analyzer SDKs.
author: bennage
ms.author: christb
ms.topic: reference
ms.date: 11/04/2021
ms.openlocfilehash: bc85e9b796f948d9fdee5a96555590084dff215c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562046"
---
# <a name="azure-video-analyzer-sdks"></a>Azure Video Analyzer SDKs

Azure Video Analyzer umfasst zwei Gruppen von SDKs. Die Verwaltungs-SDKs werden für die Verwaltung der Azure-Ressource und die Client-SDKs für die Interaktion mit Edgemodulen verwendet.

## <a name="management-sdks"></a>Verwaltungs-SDKs

Mit den Verwaltungs-SDKs können Sie mit den Ressourcen interagieren, die von Azure Resource Manager zur Verfügung gestellt werden. Sie können beispielsweise ein Video Analyzer-Konto erstellen, Bereitstellungstoken für Edgemodule generieren und Zugriffsrichtlinien für Videos verwalten. Die SDKs bauen auf einer zugrunde liegenden [REST-API](/rest/api/videoanalyzer/?branch=video) auf.

Die folgenden Plattformen werden unterstützt:

- [.NET](https://aka.ms/ava/sdk/mgt/net)
- [Java](https://aka.ms/ava/sdk/mgt/java)
- [Python](https://aka.ms/ava/sdk/mgt/python)

## <a name="client-sdks"></a>Client-SDKs

Die Client-SDKs ermöglichen Ihnen, mit den [direkten Methoden][docs-direct-methods] eines Video Analyzer-Edgemoduls zu interagieren. Diese SDKs sind für die Verwendung mit den [Azure IoT Hub SDKs][docs-iot-hub-sdks] konzipiert. Sie unterstützen das Erstellen von Objekten, die die direkten Methoden darstellen, die dann mithilfe der IoT Hub SDKs an das Edgemodul gesendet werden können.

Die folgenden Plattformen werden unterstützt:

- [.NET](https://aka.ms/ava/sdk/client/net)
- [Python](https://aka.ms/ava/sdk/client/python)
- [Java](https://aka.ms/ava/sdk/client/java)

## <a name="see-also"></a>Weitere Informationen

- Weitere Informationen erhalten Sie auch unter [Azure Video Analyzer-APIs](/rest/api/videoanalyzer/)

<!-- links -->
[docs-direct-methods]: direct-methods.md
[docs-iot-hub-sdks]: ../../iot-hub/iot-hub-devguide-sdks.md

[REST API]: https://aka.ms/ava/api/rest
