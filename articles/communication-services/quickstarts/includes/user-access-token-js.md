---
title: Datei einfügen
description: Datei einfügen
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: f5b41f98f3d0ce1ca1cab4f8a5c5715d9a10fa64
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677320"
---
> [!NOTE]
> Den fertigen Code für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/access-tokens-quickstart).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/): Active LTS- und Maintenance LTS-Versionen (8.11.1 und 10.14.1 empfohlen).
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../create-communication-resource.md)

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und navigieren Sie zu diesem Verzeichnis.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

Führen Sie `npm init -y` aus, um die Datei **package.json** mit den Standardeinstellungen zu erstellen.

```console
npm init -y
```

### <a name="install-the-package"></a>Installieren des Pakets

Verwenden Sie den Befehl `npm install`, um das Azure Communication Services Identity SDK für JavaScript zu installieren.

```console

npm install @azure/communication-identity --save

```

Durch die Option `--save` wird die Bibliothek als Abhängigkeit in der Datei **package.json** aufgeführt.

## <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen einer neuen Textdatei im Code-Editor
1. Fügen Sie einen Aufruf vom Typ `require` hinzu, um den Kommunikationsidentitätsclient (`CommunicationIdentityClient`) zu laden.
1. Erstellen der Struktur für das Programm, einschließlich einer einfachen Ausnahmebehandlung

Verwenden Sie zum Einstieg den folgenden Code:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');

const main = async () => {
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered an error");
  console.log(error);
})
```

1. Speichern Sie die neue Datei als **issue-access-token.js** im Verzeichnis *access-tokens-quickstart*.

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur [Verwaltung der Verbindungszeichenfolge Ihrer Ressource](../create-communication-resource.md#store-your-connection-string).

Fügen Sie der `main`-Methode folgenden Code hinzu:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

Alternativ dazu können Sie auch den Endpunkt und den Zugriffsschlüssel trennen.
```javascript
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const accessKey = process.env["COMMUNICATION_SERVICES_ACCESSKEY"];
const tokenCredential = new AzureKeyCredential(accessKey);
// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential)
```

Wenn Sie eine Azure Active Directory-Anwendung eingerichtet haben, finden Sie weitere Informationen unter [Verwenden von Dienstprinzipalen](../identity/service-principal.md). Sie können sich auch bei AD authentifizieren.
```javascript
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const tokenCredential = new DefaultAzureCredential();
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Erstellen einer Identität

Von Azure Communication Services wird ein einfaches Identitätsverzeichnis gepflegt. Verwenden Sie die Methode `createUser`, um in dem Verzeichnis einen neuen Eintrag mit einer eindeutigen `Id` zu erstellen. Speichern Sie die empfangene Identität mit einer Zuordnung zu den Benutzern Ihrer Anwendung. Beispielsweise, indem Sie sie in der Datenbank des Anwendungsservers speichern. Die Identität ist später erforderlich, um Zugriffstoken auszustellen.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>Ausstellen von Zugriffstoken

Verwenden Sie die Methode `getToken`, um ein Zugriffstoken für eine bereits vorhandene Communication Services-Identität auszustellen. Der Parameter `scopes` definiert einen Satz primitiver Elemente, die dieses Zugriffstoken autorisieren. Weitere Informationen finden Sie in der [Liste der unterstützten Aktionen](../../concepts/authentication.md). Eine neue Instanz des Parameters `communicationUser` kann basierend auf der Zeichenfolgendarstellung der Azure Communication Service-Identität generiert werden.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Zugriffstoken sind kurzlebige Anmeldeinformationen, die erneut ausgestellt werden müssen. Wenn dies nicht der Fall ist, kann die Benutzerumgebung Ihrer Anwendung unterbrochen werden. Die Antworteigenschaft `expiresOn` gibt die Lebensdauer des Zugriffstokens an.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Erstellen einer Identität und Ausstellen eines Zugriffstokens innerhalb derselben Anforderung

Verwenden Sie die `createUserAndToken`-Methode, um eine Communication Services-Identität zu erstellen und ein Zugriffstoken dafür auszustellen. Der Parameter `scopes` definiert einen Satz primitiver Elemente, die dieses Zugriffstoken autorisieren. Weitere Informationen finden Sie in der [Liste der unterstützten Aktionen](../../concepts/authentication.md).

```javascript
// Issue an identity and an access token with the "voip" scope for the new identity
let identityTokenResponse = await identityClient.createUserAndToken(["voip"]);
const { token, expiresOn, user } = identityTokenResponse;
console.log(`\nCreated an identity with ID: ${user.communicationUserId}`);
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

## <a name="refresh-access-tokens"></a>Zugriffstoken für die Aktualisierung

Das Aktualisieren von Zugriffstoken ist genau so einfach wie das Aufrufen von `getToken` mit der gleichen Identität, die zum Ausstellen der Token verwendet wurde. Außerdem müssen Sie die `scopes`-Elemente der aktualisierten Token angeben.

```javascript
// Value of identityResponse represents the Azure Communication Services identity stored during identity creation and then used to issue the tokens being refreshed
let refreshedTokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Widerrufen von Zugriffstoken

In einigen Fällen können Sie Zugriffstoken explizit widerrufen. Beispielsweise dann, wenn der Benutzer einer Anwendung das Kennwort ändert, das für die Authentifizierung beim Dienst verwendet wird. Die Methode `revokeTokens` erklärt alle aktiven Zugriffstoken für ungültig, die für die Identität ausgestellt wurden.

```javascript
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Löschen einer Identität

Wird eine Identität gelöscht, werden alle aktiven Zugriffstoken widerrufen, und für die Identität können keine Zugriffstoken mehr ausgestellt werden. Außerdem werden alle gespeicherten Inhalte entfernt, die der Identität zugeordnet sind.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie an einer Konsoleneingabeaufforderung zum Verzeichnis mit der Datei *issue-access-token.js*, und führen Sie anschließend den folgenden Befehl vom Typ `node` aus, um die App auszuführen.

```console
node ./issue-access-token.js
```
