---
title: Aufbereiten von HTML-Inhalten für den plastischen Reader
titleSuffix: Azure Applied AI Services
description: Hier erfahren Sie, wie Sie den plastischen Reader mit HTML, JavaScript, Python, Android oder iOS starten. Der plastische Reader verwendet bewährte Verfahren zum Verbessern des Leseverständnisses von Sprachenlernenden, Leseanfängern und Schülern mit Lernschwierigkeiten.
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: 505151c22c095f1168ccad4a84870c430b3dd586
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026522"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>Aufbereiten von HTML-Inhalten für den plastischen Reader

In diesem Artikel erfahren Sie, wie Sie Ihren HTML-Code strukturieren und den Inhalt so abrufen, dass er vom plastischen Reader verwendet werden kann.

## <a name="prepare-the-html-content"></a>Vorbereiten des HTML-Inhalts

Platzieren Sie den Inhalt, den Sie im plastischen Reader innerhalb eines Containerelements rendern möchten. Stellen Sie sicher, dass das Containerelement über eine eindeutige `id` verfügt. Der plastische Reader bietet Unterstützung für grundlegende HTML-Elemente. Weitere Informationen finden Sie in der [Referenz](reference.md#html-support).

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Abrufen des HTML-Inhalts in JavaScript

Verwenden Sie die `id` des Containerelements, um den HTML-Inhalt in Ihren JavaScript-Code zu übernehmen.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Starten des plastischen Readers mit Ihrem HTML-Inhalt

Wenn Sie `ImmersiveReader.launchAsync` aufrufen, legen Sie die `mimeType`-Eigenschaft des Blocks auf `text/html` fest, um das HTML-Rendering zu aktivieren.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die [Referenz zum SDK für den plastischen Reader](reference.md).