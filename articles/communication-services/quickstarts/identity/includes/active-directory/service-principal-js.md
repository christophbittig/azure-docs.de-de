---
ms.openlocfilehash: 80522448fa77cb8f07ae89a28f78ce6417edf17d
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113656953"
---
> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/use-managed-Identity).

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir active-directory-authentication-quickstart && cd active-directory-authentication-quickstart
```

Führen Sie `npm init -y` aus, um die Datei **package.json** mit den Standardeinstellungen zu erstellen.

```console
npm init -y
```

### <a name="install-the-sdk-packages"></a>Installieren der SDK-Pakete

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="create-a-new-file"></a>Erstellen einer neuen Datei

Öffnen Sie eine neue Datei mit einem Text-Editor, und speichern Sie sie als `index.js`. Wir platzieren den Code in dieser Datei.

### <a name="use-the-sdk-packages"></a>Verwenden der SDK-Pakete

Fügen Sie am Anfang von `index.js` die folgenden `require`-Anweisungen hinzu, um die Azure Identity- und Azure Storage-SDKs zu verwenden.

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>Erstellen eines DefaultAzureCredential-Standards

Für diese Schnellstartanleitung verwenden wir [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential). Diese Anmeldeinformationen eignen sich für Produktions- und Entwicklungsumgebungen. Da sie für jeden Vorgang benötigt werden, erstellen wir sie in der `index.js`-Datei. 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-service-principals"></a>Erstellen einer Identität und Ausstellen eines Tokens mit Dienstprinzipalen

Als Nächstes schreiben Sie eine Funktion, die eine neue Identität erstellt und ein Token für diese Identität ausstellt. Sie verwenden diese Funktion später, um die Einrichtung des Dienstprinzipals zu testen.

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-service-principals"></a>Senden einer SMS mit Dienstprinzipalen

Schreiben Sie nun eine Funktion, die Dienstprinzipale zum Senden einer SMS verwendet:

```JavaScript
async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}
```

## <a name="write-the-main-function"></a>Schreiben der main-Funktion

Nachdem Sie die Funktionen erstellt haben, können Sie nun eine main-Funktion schreiben, um diese Funktionen aufzurufen und die Verwendung von Dienstprinzipalen zu veranschaulichen:
```JavaScript
async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Service Principals");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

Die endgültige `index.js`-Datei sollte wie folgt aussehen:
```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");

const credential = new DefaultAzureCredential();

async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}

async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}

async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Service Principals");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Service Principals");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Service Principals");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

## <a name="run-the-program"></a>Ausführen des Programms

Wenn alles erledigt ist, können Sie die Datei durch Eingabe von `node index.js` aus dem Verzeichnis Ihres Projekts ausführen. Ist alles in Ordnung, sollte etwa folgende Ausgabe angezeigt werden:

```Bash
    $ node index.js
    Retrieving new Access Token, using Service Principals
    Retrieved Access Token: ey...Q
    Sending SMS using Service Principals
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
