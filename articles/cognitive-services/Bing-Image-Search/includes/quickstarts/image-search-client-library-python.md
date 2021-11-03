---
title: 'Schnellstart: Python-Clientbibliothek für Bing-Bildersuche'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: c9dd8bdfb9af1d20433a083b48ee1b7e14658438
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253280"
---
Führen Sie mithilfe dieses Schnellstarts Ihre erste Bildersuche mit der Bing-Bildersuche-Clientbibliothek aus, die ein Wrapper für die API ist und die gleichen Funktionen enthält. Diese einfache Python-Anwendung sendet eine Bildersuchabfrage, analysiert die JSON-Antwort und zeigt die URL des ersten zurückgegebenen Bilds an.

Der Quellcode für dieses Beispiel ist auf [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) mit zusätzlichen Fehlerbehandlungen und Anmerkungen verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* [Python 2.7 oder 3.6+](https://www.python.org/).

* Die [Bildersuche-Clientbibliothek in Azure](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) für Python
    * Installation mithilfe von `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Erstellen und Initialisieren der Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE bzw. in einem Editor ein neues Python-Skript und die folgenden Importe:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel und einen Suchbegriff.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Erstellen eines Clients für die Bildersuche

1. Erstellen Sie eine `CognitiveServicesCredentials`-Instanz, und instanziieren Sie damit den Client:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

1. Senden Sie eine Suchabfrage an die Bing-Bildersuche-API:

    ```python
    image_results = client.images.search(query=search_term)
    ```

## <a name="process-and-view-the-results"></a>Lassen Sie die Ergebnisse verarbeiten und anzeigen.

Analysieren Sie die Bildergebnisse, die in der Antwort zurückgegeben werden.

Wenn die Antwort Suchergebnisse enthält, speichern Sie das erste Ergebnis, und drucken Sie die Details aus, z.B. eine Miniaturansichts-URL, die ursprüngliche URL und die Gesamtzahl der zurückgegebenen Bilder.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Einseitige Web-App für die Bing-Bildersuche](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Bing-Bildersuche?](../../overview.md)  
* [Interaktive Onlinedemo testen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Python-Beispiele für das Azure Cognitive Services SDK](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentation zu Azure Cognitive Services](../../../index.yml)
* [Referenz zur Bing-Bildersuche-API](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)