---
title: 'Schnellstart: Azure Blob Storage-Clientbibliothek v10 für JavaScript'
description: Erstellen, Hochladen und Löschen von Blobs und Containern in Node.js mit der Azure Storage-Clientbibliothek v10 für JavaScript
author: normesta
ms.author: normesta
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: be5e67eeec220e2bbb8fa3cb525bb8ec9f224b06
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599067"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>Schnellstart: Verwalten von Blobs per JavaScript v10 SDK in Node.js

In dieser Schnellstartanleitung erfahren Sie, wie Sie Blobs mithilfe von Node.js verwalten. Bei Blobs handelt es sich um Objekte, die große Mengen von Text oder Binärdaten enthalten können, z. B. Bilder, Dokumente, Streamingmedien und Archivdaten. Hier wird gezeigt, wie Sie Blobs hochladen, herunterladen, auflisten und löschen und wie Sie Container verwalten.

> [!NOTE]
> In dieser Schnellstartanleitung wird eine Legacy-Version der Azure Blob Storage-Clientbibliothek verwendet. Informationen zu den ersten Schritten mit der neuesten Version finden Sie unter [Schnellstart: Verwalten von Blobs per JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage-Konto [Informationen zu Azure-Speicherkonten](../common/storage-account-create.md)
- [Node.js](https://nodejs.org/en/download/).

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

Die in diesem Schnellstart verwendete [Beispielanwendung](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) ist eine einfache Node.js-Konsolenanwendung. Klonen Sie zunächst das Repository auf Ihren Computer, indem Sie den folgenden Befehl verwenden:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Als Nächstes ändern Sie Ordner für die Anwendung:

```bash
cd azure-storage-js-v10-quickstart
```

Öffnen Sie nun den Ordner in Ihrer bevorzugten Codebearbeitungsumgebung.

## <a name="configure-your-storage-credentials"></a>Konfigurieren Ihre Speicheranmeldeinformationen

Vor dem Ausführen der Anwendung müssen Sie die Sicherheitsanmeldeinformationen für Ihr Speicherkonto angeben. Das Beispielrepository enthält eine Datei mit dem Namen *.env.example*. Benennen Sie diese Datei um, indem Sie die Erweiterung *.example* entfernen, sodass die Datei nur noch *.env* heißt. Fügen Sie in der *.env*-Datei Ihren Kontonamen und Zugriffsschlüsselwerte hinter den Schlüsseln *AZURE_STORAGE_ACCOUNT_NAME* und *AZURE_STORAGE_ACCOUNT_ACCESS_KEY* hinzu.

## <a name="install-required-packages"></a>Installieren erforderlicher Pakete

Führen Sie im Anwendungsverzeichnis *npm install* aus, um die erforderlichen Pakete für die Anwendung zu installieren.

```bash
npm install
```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Nachdem nun die Abhängigkeiten installiert wurden, können Sie das Beispiel ausführen, indem Sie den folgenden Befehl ausgeben:

```bash
npm start
```

Die Ausgabe der App sollte ungefähr wie folgt aussehen:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Bei Verwendung eines neuen Speicherkontos für diesen Schnellstart wird unter der Bezeichnung „*Container:* “ unter Umständen nur der Container *demo* aufgeführt.

## <a name="understanding-the-code"></a>Grundlegendes zum Code

Das Beispiel beginnt mit dem Importieren einer Reihe von Klassen und Funktionen aus dem Azure Blob Storage-Namespace. Jedes der importierten Elemente wird im Kontext besprochen, wie es in diesem Beispiel verwendet wird.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

Anmeldeinformationen werden aus Umgebungsvariablen gelesen, basierend auf dem entsprechenden Kontext.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

Das Modul *dotenv* lädt Umgebungsvariablen, wenn die App lokal zum Debuggen ausgeführt wird. Werte werden in der Datei *.env* definiert und in den aktuellen Ausführungskontext geladen. In der Produktion stellt die Serverkonfiguration diese Werte zur Verfügung, weshalb dieser Code nur ausgeführt wird, wenn das Skript *nicht* in einer „Produktions“umgebung ausgeführt wird.

Der nächste Block von Modulen wird importiert, um beim Herstellen von Schnittstellen mit dem Dateisystem zu helfen.

```javascript
const fs = require('fs');
const path = require('path');
```

Diese Module haben den folgenden Zweck:

- *fs* ist das native Node.js-Modul, das zum Arbeiten mit dem Dateisystem verwendet wird.

- *path* ist erforderlich, um den absoluten Pfad der Datei zu ermitteln, der zum Hochladen einer Datei in den Blobspeicher verwendet wird.

Als Nächstes werden Umgebungsvariablenwerte gelesen und in Konstanten abgelegt.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```

Die nächste Gruppe von Konstanten hilft dabei, den Zweck von Dateigrößenberechnungen während Uploadvorgängen zu verdeutlichen.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

Für Anforderungen der API kann festgelegt werden, dass nach einem bestimmten Intervall ein Timeout auftreten soll. Die [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy&preserve-view=true) Klasse ist zuständig für das Verwalten, wie bei Anforderungen ein Timeout erfolgt, und die folgende Konstante wird verwendet, um in diesem Beispiel verwendete Timeouts zu definieren.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Aufrufen von Code

Um die *async/await*-Syntax von JavaScript zu unterstützen, wird der gesamte Aufrufcode in eine Funktion namens *execute* eingebettet. Dann wird *execute* aufgerufen und als Zusage (promise) behandelt.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

Der gesamte folgende Code wird innerhalb der Funktion „execute“ ausgeführt, in der der Kommentar `// commands...` platziert ist.

Zunächst werden die relevanten Variablen deklariert, um Namen, Beispielinhalt zuzuweisen und auf die lokale Datei zu verweisen, die in den Blobspeicher hochgeladen wird.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

Kontoanmeldeinformationen werden zum Erstellen einer Pipeline verwendet, die für die Verwaltung verantwortlich ist, wie Anforderungen an die REST-API gesendet werden. Pipelines sind threadsicher und geben die Logik für Wiederholungsrichtlinien, Protokollierung, Deserialisierungsregeln für HTTP-Antworten und mehr an.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

Die folgenden Klassen werden in diesem Codeblock verwendet:

- Die [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy&preserve-view=true)-Klasse ist verantwortlich für das Einbetten von Anmeldeinformationen für das Speicherkonto, um sie einer Anforderungspipeline bereitzustellen.

- Die [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy&preserve-view=true)-Klasse ist für das Erstellen einer neuen Pipeline verantwortlich.

- Die [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy&preserve-view=true) modelliert eine in der REST-API verwendete URL. Instanzen dieser Klasse gestatten Ihnen die Ausführung von Aktionen wie das Auflisten von Containern und bieten Kontextinformationen zum Generieren von Container-URLs.

Die Instanz von *ServiceURL* wird zusammen mit den Instanzen von [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy&preserve-view=true) und [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy&preserve-view=true) verwendet, um Container und Blobs in Ihrem Speicherkonto zu verwalten.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

Die Variablen *containerURL* und *blockBlobURL* werden im gesamten Beispiel wiederverwendet, um das Speicherkonto zu manipulieren.

Zu diesem Zeitpunkt ist der Container im Speicherkonto nicht vorhanden. Die Instanz von *ContainerURL* stellt eine URL dar, die Sie verwenden können. Mithilfe dieser Instanz können Sie den Container erstellen und löschen. Der Speicherort dieses Containers entspricht einem Speicherort wie diesem:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

Die *blockBlobURL* dient dem Verwalten einzelner Blobs, sodass Sie Blobinhalte hochladen, herunterladen und löschen können. Die hier dargestellte URL ähnelt diesem Speicherort:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Wie bei dem Container ist der Blockblob noch nicht vorhanden. Die Variable *blockBlobURL* wird später verwendet, um das Blob durch Hochladen von Inhalt zu erstellen.

### <a name="using-the-aborter-class"></a>Verwenden der Aborter-Klasse

Für Anforderungen der API kann festgelegt werden, dass nach einem bestimmten Intervall ein Timeout auftreten soll. Die *Aborter*-Klasse ist zuständig für das Verwalten, wie ein Timeout von Anforderungen erfolgt. Der folgende Code erstellt einen Kontext, in dem eine Gruppe von Anforderungen 30 Minuten Zeit zur Ausführung erhält.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Aborter verleihen Ihnen die Kontrolle über Anforderungen, indem sie Ihnen Folgendes gestatten:

- Festlegen des Zeitraums, der einem Batch von Anforderungen gewährt wird
- Festlegen, wie lange eine einzelne Anforderung im Batch ausgeführt werden kann
- Abbrechen von Anforderungen
- Verwenden des statischen *Aborter.none*-Members, um ein Timeout aller Ihrer Anforderungen zu unterbinden

### <a name="create-a-container"></a>Erstellen eines Containers

Zum Erstellen eines Containers wird die *create*-Methode der *ContainerURL* verwendet.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Da der Name des Containers beim Aufrufen von *ContainerURL.fromServiceURL (serviceURL, containerName)* definiert wird, ist zum Erstellen des Containers lediglich das Aufrufen der *create*-Methode erforderlich.

### <a name="show-container-names"></a>Anzeigen von Containernamen

Konten können eine große Anzahl von Containern speichern. Der folgende Code veranschaulicht, wie sich Container segmentiert auflisten lassen, wodurch Sie eine große Anzahl von Containern durchlaufen können. Der *showContainerNames*-Funktion werden Instanzen von *ServiceURL* und *Aborter* übergeben.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

Die *showContainerNames*-Funktion verwendet die *listContainersSegment*-Methode, um Batches von Containernamen aus dem Speicherkonto anzufordern.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

Wenn die Antwort zurückgegeben wird, werden die *containerItems* durchlaufen, um die Namen in der Konsole zu protokollieren.

### <a name="upload-text"></a>Hochladen von Text

Um Text in das Blob hochzuladen, verwenden Sie die *upload*-Methode.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

Hier werden der Text und seine Länge an die Methode übergeben.

### <a name="upload-a-local-file"></a>Hochladen einer lokalen Datei

Um eine lokale Datei in den Container hochzuladen, benötigen Sie eine Container-URL und den Pfad zu der Datei.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

Die *uploadLocalFile*-Funktion ruft die *uploadFileToBlockBlob*-Funktion auf, die den Dateipfad und eine Instanz des Ziels der Blockblobs als Argumente übernimmt.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Hochladen eines Datenstroms

Das Hochladen von Datenströmen wird ebenfalls unterstützt. In diesem Beispiel wird eine lokale Datei als Datenstrom geöffnet, der an die upload-Methode übergeben werden soll.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

Die *uploadStream*-Funktion ruft *uploadStreamToBlockBlob* auf, um den Datenstrom in den Speichercontainer hochzuladen.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

Während eines Uploads ordnet *uploadStreamToBlockBlob* Puffer zu, um Daten aus dem Datenstrom zwischenzuspeichern, falls eine Wiederholung erforderlich ist. Der Wert *maxBuffers* legt fest, wie viele Puffer höchstens verwendet werden, da jeder Puffer eine gesonderte Uploadanforderung erstellt. Im Idealfall ergeben mehr Puffer höhere Geschwindigkeiten, aber auf Kosten einer höheren Arbeitsspeicherauslastung. Die Uploadgeschwindigkeit stagniert, wenn die Anzahl der Puffer hoch genug ist, dass der Engpass auf das Netzwerk oder den Datenträger übergeht, anstatt auf den Client.

### <a name="show-blob-names"></a>Anzeigen von Blobnamen

Ebenso wie Konten viele Container enthalten können, kann jeder Container potenziell eine große Menge von Blobs enthalten. Der Zugriff auf jedes Blob in einem Container steht durch eine Instanz der *ContainerURL*-Klasse zur Verfügung.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

Die Funktion *showBlobNames* ruft *listBlobFlatSegment* auf, um Batches von Blobs aus dem Container anzufordern.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Herunterladen eines Blobs

Nachdem ein Blob erstellt wurde, können Sie den Inhalt mithilfe der *download*-Methode herunterladen.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

Die Antwort wird als Datenstrom zurückgegeben. In diesem Beispiel wird der Stream mithilfe der folgenden Hilfsfunktion *streamToString* in eine Zeichenfolge konvertiert:

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Löschen eines BLOBs

Die *delete*-Methode aus einer *BlockBlobURL*-Instanz löscht ein Blob aus dem Container.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Löschen eines Containers

Die *delete*-Methode aus einer *ContainerURL*-Instanz löscht einen Container aus dem Speicherkonto.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Alle in das Speicherkonto geschriebenen Daten werden am Ende des Codebeispiels automatisch gelöscht.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie Blobs und Container in Azure Blob Storage mit Node.js verwalten. Weitere Informationen zur Arbeit mit diesem SDK finden Sie im GitHub-Repository.

> [!div class="nextstepaction"]
> [Azure Storage v10 SDK für JavaScript-Repository](https://github.com/Azure/azure-storage-js)
> [Azure Storage-JavaScript-API-Referenz](/javascript/api/overview/azure/storage-overview)
