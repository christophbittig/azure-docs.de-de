---
title: Durchsuchen des Internets nach beliebten Videos mithilfe der Bing-Videosuche-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie das Internet mithilfe der Bing-Videosuche-API nach beliebten Videos durchsuchen.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: fca6bfa797e7d2b382e1336359c52f448f94a148
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600663"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Abrufen beliebter Videos mit der Bing-Videosuche-API 

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die Bing-Videosuche-API ermöglicht es Ihnen, die aktuellen Trendvideos aus dem gesamten Web und in verschiedenen Kategorien zu finden. 

## <a name="get-request"></a>GET-Anforderung

Um mit der Bing-Videosuche-API die aktuellen Trendvideos abzurufen, senden Sie die folgenden GET-Anforderung:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Marktunterstützung

Die folgenden Märkte unterstützen beliebte Videos.  
 
-   en-AU (Englisch, Australien)  
-   en-CA (Englisch, Kanada)  
-   En-GB (Englisch, Großbritannien)  
-   en-ID (Englisch, Indonesien)  
-   en-IE (Englisch, Irland)  
-   en-IN (Englisch, Indien)  
-   en-NZ (Englisch, Neuseeland)  
-   en-PH (Englisch, Philippinen)  
-   en-SG (Englisch, Singapur)  
-   en-US (Englisch, USA)  
-   en-WW (English, weltweit aggregierter Code)  
-   en-ZA (Englisch, Südafrika)  
-   zh-CN (Chinesisch, China)

## <a name="example-json-response"></a>JSON-Beispielantwort  

Das folgende Beispiel zeigt eine API-Antwort, die Trendvideos enthält, die nach Kategorie und Unterkategorie aufgelistet sind. Die Antwort enthält auch Bannervideos, das heißt, die beliebtesten Videos, die aus einer oder mehreren Kategorien stammen können.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Gewinnen von Erkenntnissen aus Videos](video-insights.md)