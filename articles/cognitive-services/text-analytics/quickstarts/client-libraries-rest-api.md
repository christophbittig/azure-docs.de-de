---
title: 'Schnellstart: Textmining mithilfe der Textanalyse-Clientbibliothek'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diese Schnellstartanleitung, um Stimmungsanalyse und mehr mithilfe der Textanalyse-API aus Azure Cognitive Services durchzuführen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/25/2021
ms.author: aahi
keywords: Textmining, Stimmungsanalyse, Textanalyse
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 8eeac06c4db34a7aa2452ae9fda51890ac1b91a1
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864854"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Schnellstart: Verwenden der Textanalyse-Clientbibliothek und der REST-API

Dieser Artikel hilft Ihnen beim Einstieg in die Verwendung der Textanalyse-Clientbibliothek und der REST-API. Befolgen Sie diese Schritte, um die Codebeispiele für das Text Mining zu testen.


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * In dieser Schnellstartanleitung werden nur die folgenden Versionen der API behandelt: v3.1 und v3.2-preview.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Die neueste stabile Version der Textanalyse-API ist `3.1`.
> * Im Code dieses Artikels werden der Einfachheit halber synchrone Methoden und ein ungeschützter Anmeldeinformationsspeicher verwendet. Für Produktionsszenarien wird aus Leistungs- und Skalierbarkeitsgründen die Verwendung der asynchronen Batchmethoden empfohlen. Informationen finden Sie in der Referenzdokumentation weiter unten.
> * Sie können auch die neueste Vorschauversion der Clientbibliothek für die extraktive Zusammenfassung verwenden. Sehen Sie sich die folgenden Beispiele auf [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java) an:


[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Die neueste stabile Version der Textanalyse-API ist `3.1`.
>    * Befolgen Sie nur die Anweisungen für die von Ihnen verwendete Version.
> * Im Code dieses Artikels werden der Einfachheit halber synchrone Methoden und ein ungeschützter Anmeldeinformationsspeicher verwendet. Für Produktionsszenarien wird aus Leistungs- und Skalierbarkeitsgründen die Verwendung der asynchronen Batchmethoden empfohlen. Informationen finden Sie in der Referenzdokumentation weiter unten.
> * Diese Version der Textanalyse-Clientbibliothek können Sie auch [in Ihrem Browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md) ausführen.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * In dieser Schnellstartanleitung werden nur die folgenden Versionen der API behandelt: v3.1 und v3.2-preview.

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Die neueste stabile Version der Textanalyse-API ist `3.1`.
>    * Befolgen Sie nur die Anweisungen für die von Ihnen verwendete Version.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lösung erkunden](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Übersicht über die Textanalyse](../overview.md)
* [Standpunktanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitätserkennung](../how-tos/text-analytics-how-to-entity-linking.md)
* [Sprache erkennen](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Spracherkennung](../how-tos/text-analytics-how-to-language-detection.md)
