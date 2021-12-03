---
title: Codieren und Streamen von Videodateien mit Node.js
description: Hier wird beschrieben, wie Sie Videodateien mit Node.js streamen. Führen Sie die in diesem Tutorial beschriebenen Schritte aus, um ein neues Azure Media Services-Konto zu erstellen, eine Datei zu codieren und in Azure Media Player zu streamen.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure Media Services, Streamen, Node.js
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: f8eafe88168f908025321d1e3ce6304fa34c03ab
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179946"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Codieren und Streamen von Videodateien mit Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Schnellstart zeigt, wie einfach es ist, zu Codieren und mit dem Streaming von Videos für eine Vielzahl von Browsern und Geräten mit Azure Media Services zu beginnen. Eine Eingabevideodatei kann über HTTPS-URLs, SAS-URLs oder Pfade zu Dateien in Azure Blob Storage angegeben werden.

In dieser Schnellstartanleitung lernen Sie Folgendes:

- Codieren mit Node.js
- Streamen mit Node.js
- Hochladen einer Datei über eine HTTPS-URL mit Node.js
- Verwenden eines HLS- oder DASH-Clientplayers mit Node.js

Im Beispiel in diesem Artikel werden Inhalte codiert, die Sie über eine HTTPS-URL zugänglich machen. Beachten Sie, dass AMS v3 derzeit keine segmentierte Transfercodierung über HTTPS-URLs unterstützt.

![Video abspielen](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Installieren von [Node.js](https://nodejs.org/en/download/)
- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md)<br/>Merken Sie sich die Werte, die Sie für den Namen der Ressourcengruppe und des Media Services-Kontos verwendet haben.
- Führen Sie die Schritte unter [Zugreifen auf die Azure Media Services-API mit der Azure CLI](./access-api-howto.md) aus, und speichern Sie die Anmeldeinformationen. Sie benötigen sie für den Zugriff auf die API.
- Lesen Sie sich zunächst die Anleitung zum [Konfigurieren und Herstellen einer Verbindung mit Node.js](./configure-connect-nodejs-howto.md) durch, um sich mit der Verwendung des Node.js-Client-SDK vertraut zu machen.

## <a name="download-and-configure-the-sample"></a>Herunterladen und Konfigurieren des Beispiels

Klonen Sie mit dem folgenden Befehl ein GitHub-Repository auf Ihren Computer, das das Node.js-Streamingbeispiel enthält:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Das Beispiel befindet sich im Ordner [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Öffnen Sie in Ihrem heruntergeladenen Projekt die Datei [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts). Aktualisieren Sie die Datei *sample.env* im Stammordner mit den Werten und Anmeldeinformationen, die Sie durch das [Zugreifen auf die APIs](./access-api-howto.md) ermittelt haben. Benennen Sie die Datei *sample.env* in *.env* um (nur die Erweiterung ohne Namen).

Mit dem Beispiel werden die folgenden Aktionen durchgeführt:

1. Es wird eine **Transformation** mit einer [Voreinstellung für die inhaltsbezogene Codierung](./encode-content-aware-concept.md) erstellt. Zunächst wird überprüft, ob die angegebene Transformation vorhanden ist.
1. Es wird eine **Ressource** für die Ausgabe erstellt, die vom **Codierungsauftrag** für die Ausgabe genutzt wird.
1. Optional kann mit dem Storage Blob-SDK eine lokale Datei hochgeladen werden.
1. Die Eingabe für den **Auftrag** wird erstellt, die auf einer HTTPS-URL oder einer hochgeladenen Datei basiert.
1. Der **Codierungsauftrag** wird mit der zuvor erstellten Ein- und Ausgabe übermittelt.
1. Überprüfen des Auftragsstatus
1. Die Ausgabe des Codierungsauftrags wird in einen lokalen Ordner heruntergeladen.
1. Ein **Streaminglocator** für die Verwendung im Player wird verwendet.
1. Es werden Streaming-URLs für HLS und DASH erstellt.
1. Gibt den Inhalt in einer Playeranwendung wieder (Azure Media Player).

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Die Anwendung lädt codierte Dateien herunter. Erstellen Sie einen Ordner, in dem die Ausgabedateien abgelegt werden sollen, und aktualisieren Sie in der Datei [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/StreamFilesSample/index.ts#L59) den Wert der Variablen **outputFolder**. Standardmäßig ist der Wert auf „Temp“ festgelegt.
1. Öffnen Sie eine **Eingabeaufforderung**, und navigieren Sie zum Verzeichnis für das Beispiel.
1. Wechseln Sie zum Ordner „AMSv3Samples“.

    ```bash
    cd AMSv3Samples
    ```

1. Installieren Sie die Pakete, die in der Datei *packages.json* verwendet werden.

    ```bash
    npm install 
    ```

1. Wechseln Sie zum Ordner *StreamFilesSample*.

    ```bash
    cd StreamFilesSample
    ```

1. Starten Sie Visual Studio Code aus dem Ordner *AMSv3Samples*. (Dies ist erforderlich, damit das Starten aus dem Ordner erfolgt, in dem sich der Ordner *.vscode* und die *tsconfig.json*-Dateien befinden.)

    ```bash
    cd ..
    code .
    ```

Öffnen Sie den Ordner für *StreamFilesSample*, und öffnen Sie die Datei *index.ts* im Visual Studio Code-Editor.
Drücken Sie bei geöffneter Datei *index.ts* die Taste F5, um den Debugger zu starten.

## <a name="test-with-azure-media-player"></a>Testen mit Azure Media Player

Verwenden Sie Azure Media Player, um den Stream zu testen. Sie können auch einen beliebigen HLS- oder DASH-konformen Player verwenden, z. B. Shaka-Player, HLS.js, Dash.js oder andere.

Sie sollten auf den im Beispiel generierten Link klicken und den Azure Media Player starten können, für den das DASH-Manifest bereits geladen wurde.

> [!NOTE]
> Wenn ein Player auf einer HTTPS-Website gehostet wird, stellen Sie sicher, die URL mit „https“ zu aktualisieren.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Fügen Sie im Feld **URL:** einen der URL-Streamingwerte ein, die Sie bei der Ausführung der Anwendung abgerufen haben. Sie können die URL im HLS-, DASH- oder Smooth-Format einfügen. Azure Media Player wechselt auf Ihrem Gerät dann automatisch zu einem geeigneten Streamingprotokoll für die Wiedergabe.
3. Drücken Sie **Player aktualisieren**.

Azure Media Player kann zum Testen verwendet werden, sollte jedoch nicht in einer Produktionsumgebung zum Einsatz kommen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen in der Ressourcengruppe einschließlich der in diesem Tutorial erstellten Media Services- und Speicherkonten nicht mehr benötigen, löschen Sie die Ressourcengruppe.

Führen Sie den folgenden CLI-Befehl aus:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Weitere Entwicklerdokumentation für Node.js in Azure

- [Azure für JavaScript- und Node.js-Entwickler](/azure/developer/javascript/)
- [Media Services-Quellcode im GitHub-Repository @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentation zum Azure-Paket für Node.js-Entwickler](/javascript/api/overview/azure/)

## <a name="see-also"></a>Weitere Informationen

- [Auftragsfehlercodes](/rest/api/media/jobs/get#joberrorcode)
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure für JavaScript- und Node.js-Entwickler](/azure/developer/javascript/)
- [Media Services-Quellcode im Repository @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Nächste Schritte

> [Media Services-Konzepte](concepts-overview.md)
