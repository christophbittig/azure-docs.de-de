---
title: Herstellen einer Verbindung mit einem Azure Fluid Relay-Dienst
description: Herstellen einer Verbindung mit einem Azure Fluid Relay-Dienst mithilfe der Bibliothek @fluidframework/azure-client
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/deployment/azure-frs/
ms.openlocfilehash: a2093b063e5c2e048bdf0a0a418a8046348032c8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849575"
---
# <a name="how-to-connect-to-an-azure-fluid-relay-service"></a>Herstellen einer Verbindung mit einem Azure Fluid Relay-Dienst

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

In diesem Artikel werden die Schritte erläutert, mit denen Sie Ihren Azure Fluid Relay-Dienst bereitstellen und verwenden können. 

> [!IMPORTANT]
> Bevor Sie Ihre App mit einem Azure Fluid Relay-Server verbinden können, müssen Sie eine [Azure Fluid Relay-Serverressource](provision-fluid-azure-portal.md) in Ihrem Azure-Konto bereitstellen.

Der Azure Fluid Relay-Dienst ist ein in der Cloud gehosteter Fluid-Dienst. Sie können Ihre Fluid-Anwendung mit einer Azure Fluid Relay-Instanz verbinden, indem Sie den im Paket `@fluidframework/azure-client` enthaltenen `AzureClient` verwenden. `AzureClient` übernimmt die Logik beim Herstellen der Verbindung Ihres [Fluid-Containers](https://fluidframework.com/docs/build/containers/) mit dem Dienst, während das Containerobjekt selbst dienstunabhängig bleibt. Sie können eine Instanz dieses Clients für die Verwaltung mehrerer Container verwenden.

In den folgenden Abschnitten wird erläutert, wie Sie `AzureClient` in Ihrer eigenen Anwendung verwenden.

## <a name="connecting-to-the-service"></a>Herstellen einer Verbindung mit dem Dienst

Um eine Verbindung mit einer Azure Fluid Relay-Instanz herzustellen, müssen Sie zunächst ein `AzureClient` erstellen. Sie müssen einige Konfigurationsparameter bereitstellen, darunter die ID des Mandanten, die URLs für Orderer und Speicher sowie einen Tokenanbieter, um das JSON Web Token (JWT) zu generieren, das zur Autorisierung des aktuellen Benutzers für den Dienst verwendet wird. Das `@fluidframework/test-client-utils`-Paket stellt ein `InsecureTokenProvider` bereit, das für Entwicklungszwecke verwendet werden kann.

> [!CAUTION]
> `InsecureTokenProvider` sollte nur für Entwicklungszwecke verwendet werden, da durch die **Verwendung das Mandantenschlüsselgeheimnis in Ihrem clientseitigen Codepaket verfügbar gemacht wird**. Dies muss durch eine Implementierung von `ITokenProvider` ersetzt werden, die das Token von Ihrem eigenen Back-End-Dienst abruft, der für das Signieren mit dem Mandantenschlüssel verantwortlich ist.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new InsecureTokenProvider("myTenantKey", { id: "userId" }),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

Jetzt, da Sie eine Instanz von `AzureClient` besitzen, können Sie diese verwenden, um Fluid-Container zu erstellen oder zu laden!

### <a name="token-providers"></a>Tokenanbieter

[AzureFunctionTokenProvider](https://github.com/microsoft/FluidFramework/blob/main/packages/framework/azure-client/src/AzureFunctionTokenProvider.ts) ist eine Implementierung von `ITokenProvider`, die sicherstellt, dass Ihr Mandantenschlüsselgeheimnis nicht in Ihrem clientseitigen Paketcode verfügbar gemacht wird. Der `AzureFunctionTokenProvider` übernimmt Ihre Azure-Funktions-URL, an die `/api/GetAzureToken` zusammen mit dem aktuellen Benutzerobjekt angefügt wird. Später wird eine `GET`-Anforderung an Ihre Azure-Funktion gestellt, indem „tenantId“, „documentId“ und „userId/userName“ als optionale Parameter übergeben werden.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "userId", userName: "Test User" }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

### <a name="adding-custom-data-to-tokens"></a>Hinzufügen benutzerdefinierter Daten zu Token

Das Benutzerobjekt kann auch optionale zusätzliche Benutzerdetails enthalten. Beispiel:

```javascript
const userDetails = {
  email: "xyz@outlook.com",
  address: "Redmond",
};

const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "UserId", userName: "Test User", additionalDetails: userDetails }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};
```

Ihre Azure-Funktion generiert das Token für den angegebenen Benutzer, der mit dem geheimen Schlüssel des Mandanten signiert wurde, und gibt es an den Client zurück, ohne das Geheimnis selbst verfügbar zu machen.

## <a name="managing-containers"></a>Verwalten von Containern

Die `AzureClient`-API macht `createContainer`- und `getContainer`-Funktionen zum Erstellen bzw. Abrufen von Containern verfügbar. Beide Funktionen verwenden ein _Containerschema_, das das Containerdatenmodell definiert. Für die `getContainer`-Funktion gibt es einen zusätzlichen Parameter für die Container-`id` des Containers, den Sie abrufen möchten.

Im Szenario der Containererstellung können Sie das folgende Muster verwenden:

```javascript
const schema = {
  initialObjects: {
    /* ... */
  },
  dynamicObjectTypes: [
    /*...*/
  ],
};
const azureClient = new AzureClient(clientProps);
const { container, services } = await azureClient.createContainer(
  schema
);
const id = await container.attach();
```

Der `container.attach()`-Aufruf erfolgt, wenn der Container tatsächlich mit dem Dienst verbunden und in seinem Blobspeicher aufgezeichnet wird. Er gibt eine `id` zurück, die der eindeutige Bezeichner für diese Containerinstanz ist.

Jeder Client, der derselben in Zusammenarbeit durchgeführten Sitzung beitreten möchte, muss `getContainer` mit derselben Container-`id` aufrufen.

```javascript
const { container, services } = await azureClient.getContainer(
  id,
  schema
);
```

Weitere Informationen zum Starten der Aufzeichnung von Protokollen, die von Fluid ausgegeben werden, finden Sie unter [Protokollierung und Telemetrie](https://fluidframework.com/docs/testing/telemetry/).

Der zurückgegebene Container enthält die `initialObjects` wie im Containerschema definiert. Weitere Informationen zum Einrichten des Schemas und zum Verwenden des `container`-Objekts finden Sie unter [Data modeling](https://fluidframework.com/docs/build/data-modeling/) (Datenmodellierung) auf „fluidframework.com“.

## <a name="getting-audience-details"></a>Abrufen von Zielgruppendetails

Aufrufe von `createContainer` und `getContainer` geben zwei Werte zurück: einen `container` (oben beschrieben) und ein `services`-Objekt.

Der `container` enthält das Fluid-Datenmodell und ist dienstunabhängig. Jeglicher Code, den Sie für dieses Containerobjekt schreiben, das von `AzureClient` zurückgegeben wird, kann mit dem Client für einen anderen Dienst wiederverwendet werden. Ein Beispiel hierfür ist, wenn Sie mithilfe von `TinyliciousClient` für Ihr Szenario einen Prototypen erstellt haben, dann kann Ihr gesamter Code, der mit den gemeinsam genutzten Objekten innerhalb des Fluid-Containers interagiert, wiederverwendet werden, wenn Sie zur Verwendung von `AzureClient` wechseln.

Das `services`-Objekt enthält Daten, die spezifisch für den Azure Fluid Relay-Dienst sind. Dieses Objekt enthält einen `audience`-Wert, der verwendet werden kann, um die Liste der Benutzer zu verwalten, die derzeit mit dem Container verbunden sind.

Der folgende Code veranschaulicht, wie Sie das `audience`-Objekt verwenden können, um eine aktualisierte Ansicht aller Member zu verwalten, die sich derzeit in einem Container befinden.

```javascript
const { audience } = containerServices;
const audienceDiv = document.createElement("div");

const onAudienceChanged = () => {
  const members = audience.getMembers();
  const self = audience.getMyself();
  const memberStrings = [];
  const useAzure = process.env.FLUID_CLIENT === "azure";

  members.forEach((member) => {
    if (member.userId !== (self ? self.userId : "")) {
      if (useAzure) {
        const memberString = `${member.userName}: {Email: ${member.additionalDetails ? member.additionalDetails.email : ""},
                        Address: ${member.additionalDetails ? member.additionalDetails.address : ""}}`;
        memberStrings.push(memberString);
      } else {
        memberStrings.push(member.userName);
      }
    }
  });
  audienceDiv.innerHTML = `
            Current User: ${self ? self.userName : ""} <br />
            Other Users: ${memberStrings.join(", ")}
        `;
};

onAudienceChanged();
audience.on("membersChanged", onAudienceChanged);
```

`audience` stellt zwei Funktionen bereit, die `AzureMember`-Objekte zurückgeben, die über eine Benutzer-ID und einen Benutzernamen verfügen:

- `getMembers` gibt eine Zuordnung aller Benutzer zurück, die mit dem Container verbunden sind. Diese Werte ändern sich, wenn ein Member dem Container beitritt oder ihn verlässt.
- `getMyself` gibt den aktuellen Benutzer auf diesem Client zurück.

`audience` gibt auch Ereignisse für den Fall aus, dass sich die Liste der Member ändert. `membersChanged` wird für alle Änderungen an der Liste ausgelöst, während `memberAdded` und `memberRemoved` für die jeweiligen Änderungen mit den geänderten `clientId`- und `member`-Werten ausgelöst werden. Nachdem eines dieser Ereignisse ausgelöst wurde, gibt ein neuer Aufruf von `getMembers` die aktualisierte Memberliste zurück.

Ein `AzureMember`-Beispielobjekt sieht wie folgt aus:

```json
{
  "userId": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
  "userName": "Test User",
  "connections": [
    {
      "id": "c699c3d1-a4a0-4e9e-aeb4-b33b00544a71",
      "mode": "write"
    },
    {
      "id": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
      "mode": "write"
    }
  ],
  "additionalDetails": {
    "email": "xyz@outlook.com",
    "address": "Redmond"
  }
}
```

Neben der Benutzer-ID, dem Namen und zusätzlichen Details enthalten `AzureMember`-Objekte auch ein Array von `connections`. Wenn der Benutzer mit nur einem Client bei der Sitzung angemeldet ist, enthält `connections` nur einen Wert mit der ID des Clients und den Status, ob er sich im Lese-/Schreibmodus befindet. Wenn derselbe Benutzer jedoch über mehrere Clients angemeldet ist (d. h. er ist von verschiedenen Geräten angemeldet oder hat mehrere Browserregisterkarten mit demselben Container geöffnet), enthält `connections` hier mehrere Werte für jeden Client. In den Beispieldaten oben sehen wir, dass ein Benutzer mit dem Namen „Test User“ und der ID „0e662aca-9d7d-4ff0-8faf-9f8672b70f15“ den Container derzeit von zwei verschiedenen Clients aus geöffnet hat. Die Werte im Feld `additionalDetails` stimmen mit den Werten überein, die bei der `AzureFunctionTokenProvider`-Tokengenerierung bereitgestellt werden.

Diese Funktionen und Ereignisse können kombiniert werden, um eine Echtzeitansicht der Benutzer in der aktuellen Sitzung darzustellen.

**Glückwunsch!** Sie haben jetzt Ihren Fluid-Container erfolgreich mit dem Azure Fluid Relay-Dienst verbunden und die Benutzerdetails für die Member in Ihrer gemeinsamen Sitzung abgerufen!
