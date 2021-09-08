---
title: Erfassungsclient – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: In diesem Artikel beschreiben wir ein auf GitHub veröffentlichtes Tool, mit dem Kunden Audiodateien einfach und schnell an den Speech-Dienst übertragen können.
services: cognitive-services
author: panosperiorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 069c0b587fd3ff69ca8e883850d44b84ab12486f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346939"
---
# <a name="ingestion-client-for-the-speech-service"></a>Erfassungsclient für den Speech-Dienst

Der Erfassungsclient ist ein auf [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/ingestion) veröffentlichtes Tool, mit dem Kunden Audiodateien über Speech-Dienste schnell und mit wenig oder gar keinem Entwicklungsaufwand transkribieren können. Dazu wird ein dediziertes [Azure Storage](https://azure.microsoft.com/product-categories/storage/)-Konto mit benutzerdefiniertem [Azure Functions](https://azure.microsoft.com/services/functions/) eingerichtet, die entweder die [REST-API](rest-speech-to-text.md) oder das [SDK](speech-sdk.md) auf serverlose Weise verwenden, um Transkriptionsanforderungen an den Dienst weiterzuleiten.  

## <a name="architecture"></a>Aufbau

Das Tool hilft jenen Kunden, die sich ein Bild von der Qualität des Transkripts machen möchten, ohne im Vorfeld Investitionen für die Entwicklung zu tätigen. Das Tool verbindet einige Ressourcen, um Audiodateien zu transkribieren, die im dedizierten [Azure Storage-Container](https://azure.microsoft.com/product-categories/storage/) landen.

Intern verwendet das Tool unsere V3.0 Batch-API oder das SDK und folgt bewährten Methoden zur Handhabung von Hochskalierung, Wiederholungen und Failover. Das folgende Schema beschreibt die Ressourcen und Verbindungen.

:::image type="content" source="media/ingestion-client/architecture-1.png" alt-text="Architektur des Erfassungsclients":::

Im [Leitfaden zu den ersten Schritten für den Erfassungsclient](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/ingestion/ingestion-client/Setup/guide.md) wird beschrieben, wie Sie das Tool einrichten und verwenden.

> [!IMPORTANT]
> Die Preise variieren je nach Betriebsmodus (Batch und Echtzeit) und ausgewählter Azure-Funktions-SKU. Standardmäßig erstellt das Tool eine Azure-Premium-Funktions-SKU, um große Mengen zu verarbeiten. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/functions/).

Sowohl das Microsoft [Speech SDK](speech-sdk.md) als auch die [Spracherkennungs-REST-API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) können verwendet werden, um Transkripts zu erhalten. Die Entscheidung wirkt sich auf die Gesamtkosten aus, wie im Leitfaden erläutert. 

> [!TIP]
> Sie können das Tool und die resultierende Lösung in der Produktionsumgebung verwenden, um eine große Menge von Audiodaten zu verarbeiten.

## <a name="tool-customization"></a>Toolanpassung

Das Tool wurde erstellt, um den Kunden schnell Ergebnisse zu zeigen. Sie können das Tool an die von Ihnen bevorzugten SKUs und Einstellungen anpassen. Die SKUs können über das [Azure-Portal](https://portal.azure.com) bearbeitet werden [und der Code selbst ist auf GitHub verfügbar](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch).

> [!NOTE]
> Wir empfehlen, die Ressourcen in einer eigenen Ressourcengruppe zu erstellen, um die Kosten besser nachvollziehen und nachverfolgen zu können.

## <a name="next-steps"></a>Nächste Schritte

* [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/)
* [Weitere Informationen zum Speech-SDK](./speech-sdk.md)
* [Weitere Informationen zum Speech-Befehlszeilenschnittstellentool](./spx-overview.md)
