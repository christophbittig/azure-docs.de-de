---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: 9a22117c47032ed931077b601ce24216bdbf3f88
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540029"
---
:::row:::
    :::column span="3":::
        Das Speech SDK für JavaScript ist als npm-Paket verfügbar. Weitere Informationen finden Sie unter <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk </a> und im zugehörigen GitHub-Repository <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Da das Speech SDK für JavaScript als npm-Paket verfügbar ist, können sowohl Node.js als auch Clientwebbrowser es nutzen. Beachten Sie dabei die verschiedenen architektonischen Auswirkungen der einzelnen Umgebungen. Beispielsweise ist das <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Dokumentobjektmodell (DOM) </a> nicht für serverseitige Anwendungen verfügbar, ebenso wie das <a href="https://nodejs.org/api/fs.html" target="_blank">Dateisystem </a> nicht für clientseitige Anwendungen verfügbar ist.

### <a name="nodejs-package-manager-npm"></a>Node.js-Paket-Manager (NPM)

Führen Sie den folgenden Befehl `npm install` aus, um das Speech SDK für JavaScript zu installieren.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

Weitere Informationen finden Sie im <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Schnellstart zum Node.js Speech SDK </a>.
