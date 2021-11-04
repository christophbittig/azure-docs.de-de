---
title: HTTP-Erweiterungsprotokoll – Azure
description: Azure Video Analyzer ermöglicht es Ihnen, seine Verarbeitungsfunktionen mittels eines Pipeline-Erweiterungsknotens zu erweitern. Der HTTP-Erweiterungsprozessor ermöglicht Erweiterungsszenarien mit dem HTTP-Protokoll, bei denen es nicht in erster Linie auf Leistung und/oder optimale Ressourcenverwendung ankommt.
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 217848284743b0bcf9e5f805d04b0deb43a587f4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563186"
---
# <a name="use-the-http-extension-protocol"></a>Verwenden Sie das HTTP-Erweiterungsprotokoll 

[!INCLUDE [header](includes/edge-env.md)]

Mit Azure Video Analyzer ermöglicht es Ihnen, die Verarbeitungsfunktionen mittels eines [Pipeline-Erweiterungsknotens](../pipeline-extension.md) zu erweitern. Der HTTP-Erweiterungsprozessor ermöglicht Erweiterungsszenarien anhand des HTTP-Protokolls, bei denen es nicht in erster Linie auf Leistung und/oder optimale Ressourcenverwendung ankommt. In diesem Artikel erfahren Sie, wie Sie dieses Protokoll zum Senden von Nachrichten zwischen dem Video Analyzer und einem HTTP-REST-Endpunkt verwenden, der typischerweise um einen KI-Inferenzserver herum aufgebaut sein würde.

Der HTTP-Vertrag wird zwischen den folgenden beiden Komponenten definiert:

* HTTP-Server
* Das Video-Analyzer-Modul fungiert als HTTP-Client

## <a name="http-contract"></a>HTTP-Vertrag

### <a name="request"></a>Anforderung

Die Anforderungen des Video Analyzer-Moduls an Ihren HTTP-Server würden wie folgt aussehen:

|Schlüssel|Wert|
|---|---|
|POST|https://hostname/optional-path?optional-query|
|Akzeptieren|Anwendung/json|
|Authorization| Basic, Digest, Bearer (durch Unterstützung benutzerdefinierter Header)|
|Content-Type|  image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length (Textlänge in Bytes)   ||
|User-Agent |Azure Media Services|
|Body|  Bildbytes, binär kodiert in einem der unterstützten Inhaltstypen.|
```

### Example

```html
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Antwort

Die Antworten von Ihrem Inferenzserver an das Video Analyzer-Modul sollten wie folgt aussehen:

|Schlüssel|   Wert|
|---|----|
|Statuscodes|  200 OK. Rückschlussergebnisse gefunden<br/>204 Kein Inhalt – Kein Ergebnis vom AI aufgefunden<br/>400 Ungültige Anforderung. Nicht erwartet.<br/>500 Interner Serverfehler. Nicht erwartet.<br/>503 Server ausgelastet – Der Video Analyzer wich aus, basierend auf dem „Erneut versuchen-Nach“ Header, oder, falls der Header nicht vorhanden ist, basierend auf einer Standardzeitspanne.|
|Content-Type   |Anwendung/json|
|Content-Length |Textlänge in Bytes|
|Body|  JSON-Objekt mit einzelner Eigenschaft vom Typ „inferences“|

### <a name="example"></a>Beispiel

```html
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2021 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Es wird empfohlen, dass Antworten mithilfe gültiger JSON-Dokumente zurückgegeben werden, die dem gemäß dem [Schema-Objektmodell für Rückschlussmetadaten](inference-metadata-schema.md) vorab-definierten Schema entsprechen. Die Konformität mit dem Schema gewährleistet die Interoperabilität mit anderen Komponenten im Video Analyzer, z. B. die Möglichkeit, Objekte in Live-Videos zu verfolgen und die Inferenz-Metadaten während der Wiedergabe über das Video zu überlagern, wie [hier](record-stream-inference-data-with-video.md) demonstriert.

Wenn Ihr Modul eine Antwort zurückgibt, bei welcher der Inhaltstyp nicht „application/json“ lautet, codiert Live Video Analytics die Nachricht als Base64-Inhalt und serialisiert sie als transparente JSON-Nutzlast.

Wenn Ihr Modul eine Antwort mit dem Inhaltstyp „application/json“ zurückgibt, das JSON-Schema jedoch nicht dem oben beschriebenen Inferenz-Metadatenschema folgt, wird die Nachrichtennutzlast über die Pipeline weitergeleitet, die Interoperabilität wird jedoch verringert.

> [!NOTE]
> Wenn Ihr Inferenzserver kein Ergebnis für eine bestimmte Abbildung liefert, muss er den HTTP 204 Status Code (Kein Inhalt) mit einem leeren Antwortkörper zurückgeben. Live Video Analytics wird dies als leeres Ergebnis interpretieren und das Ereignis nicht über die gesamte Pipeline weiterleiten.

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen zum gRPC-Erweiterungsprotokoll](grpc-extension-protocol.md)
