---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.custom: devx-track-js
ms.openlocfilehash: 017fb0117f26000d5cc35f4967bf56d8c714de6f
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506504"
---
:::row:::
    :::column span="3":::
        Das Speech-SDK für JavaScript ist als npm-Paket verfügbar. Weitere Informationen finden Sie unter <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk </a> und im zugehörigen GitHub-Repository <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Obwohl das Speech SDK for JavaScript als npm-Paket verfügbar ist und daher sowohl von Clientwebbrowsern als auch von Node.js genutzt werden kann, beachten Sie die verschiedenen Auswirkungen der einzelnen Umgebungen auf die Architektur. Beispielsweise ist das <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dokumentobjektmodell (DOM) </a> nicht für serverseitige Anwendungen verfügbar, ebenso wie das <a href="https://nodejs.org/api/fs.html" target="_blank">Dateisystem </a> nicht für clientseitige Anwendungen verfügbar ist.

### <a name="nodejs-package-manager-npm"></a>Node.js-Paket-Manager (NPM)

Führen Sie den folgenden Befehl `npm install` aus, um das Speech SDK für JavaScript zu installieren.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML-Skripttag

Alternativ dazu können Sie ein `<script>`-Tag direkt im `<head>`-Element des HTML-Codes einfügen, das das <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">**JSDelivr**-NPM-Syndikat</a> nutzt.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Weitere Informationen finden Sie unter <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">Quickstart: Recognize speech in JavaScript on a Web Browser </a> (Schnellstart: Spracherkennung in JavaScript in einem Webbrowser).
