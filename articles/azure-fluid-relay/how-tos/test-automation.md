---
title: Verwenden der Testautomatisierung mit Azure Fluid Relay
description: Verwenden von Bibliotheken zur Testautomatisierung, um automatisierte Tests für Fluid-Anwendungen zu erstellen
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/testing/testing/
ms.openlocfilehash: 0087be9dccc1b040720ea53f5e6aae6d64f6fa17
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661829"
---
# <a name="how-to-use-test-automation-with-azure-fluid-relay"></a>Verwenden der Testautomatisierung mit Azure Fluid Relay

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Tests und Automatisierung sind entscheidend für die Qualität und Langlebigkeit Ihres Codes. Intern verwendet Fluid eine Reihe von Komponenten- und Integrationstests, die von [Mocha](https://mochajs.org/), [Jest](https://jestjs.io/), [Puppeteer](https://github.com/puppeteer/puppeteer) und [Webpack](https://webpack.js.org/) unterstützt werden.

Sie können Tests mithilfe des lokalen **@fluidframework/azure-local-service** oder eines Testmandanten im Azure Fluid Relay-Dienst durchführen. **AzureClient** kann so konfiguriert werden, dass sowohl eine Verbindung mit einem Remotedienst als auch mit einem lokalen Dienst hergestellt wird, wodurch Sie einen einzelnen Clienttyp zwischen Tests für Live- und lokale Dienstinstanzen verwenden können. Der einzige Unterschied besteht in der Konfiguration, die zum Erstellen des Clients verwendet wird.

## <a name="automation-against-azure-fluid-relay"></a>Automatisierung für Azure Fluid Relay

Ihre Automatisierung kann auf die gleiche Weise wie ein Produktionsmandant eine Verbindung mit einem Testmandanten für Azure Fluid Relay herstellen und benötigt nur die entsprechende Verbindungskonfiguration. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem Azure Fluid Relay-Dienst](connect-fluid-azure-service.md).

## <a name="creating-an-adaptable-test-client"></a>Erstellen eines anpassbaren Testclients

Um einen anpassbaren Testclient zu erstellen, müssen Sie AzureClient je nach Dienstziel unterschiedlich konfigurieren. Die folgende Funktion verwendet eine Umgebungsvariable, um dies zu bestimmen. Sie können die Umgebungsvariable in einem Testskript festlegen, um zu steuern, welcher Dienst als Ziel verwendet wird.

```typescript
function createAzureClient(): AzureClient {
    const useAzure = process.env.FLUID_CLIENT === "azure";
    const tenantKey = useAzure ? process.env.FLUID_TENANTKEY as string : "";
    const user = { id: "userId", name: "Test User" };

    const connectionConfig = useAzure ? {
        tenantId: "myTenantId",
        tokenProvider: new InsecureTokenProvider(tenantKey, user),
        orderer: "https://myOrdererUrl",
        storage: "https://myStorageUrl",
    } : {
        tenantId: LOCAL_MODE_TENANT_ID,
        tokenProvider: new InsecureTokenProvider("", user),
        orderer: "http://localhost:7070",
        storage: "http://localhost:7070",
    };

    const clientProps = {
        connection: config,
    };

    return new AzureClient(clientProps);
}
```

Ihre Tests können diese Funktion aufrufen, um ein AzureClient-Objekt zu erstellen, ohne sich um den zugrunde liegenden Dienst zu kümmern. Der folgende [Mocha](https://mochajs.org/)-Test erstellt den Dienstclient vor dem Ausführen von Tests und verwendet ihn dann zum Ausführen der einzelnen Tests. Es gibt einen einzelnen Test, der den Dienstclient verwendet, um einen Container zu erstellen, der erfolgreich ist, solange keine Fehler ausgelöst werden.

```typescript
describe("ClientTest", () => {
    const client = createAzureClient();
    let documentId: string;

    it("can create Azure container successfully", async () => {
        const schema: ContainerSchema = {
            initialObjects: {
                customMap: SharedMap
            },
        };
        documentId = await container.attach();
        const { container, services } = await azureClient.createContainer(schema);
    });
});

```

## <a name="running-tests"></a>Ausführen von Tests

Sie können die folgenden npm-Skripts in der Datei „package.json“ Ihres Projekts hinzufügen, um Tests auszuführen:

```json
"scripts": {
    "start:local": "npx @fluidframework/azure-local-service@latest > local-service.log 2>&1",
    "test:mocha": "mocha",
    "test:azure": "cross-env process.env.FLUID_CLIENT='\"azure\"' && npm run test:mocha",
    "test:local": "start-server-and-test start:local 7070 test:mocha"
}
```

Zum Installieren der Abhängigkeiten, die für die obigen Skripts erforderlich sind, können Sie den folgenden Befehl verwenden:

```bash
npm install cross-env start-server-and-test mocha
```

Das `test:local`-Skript verwendet die Bibliothek [start-server-and-test](https://www.npmjs.com/package/start-server-and-test), um den lokalen Server zu starten, zu warten, bis Port 7070 (der vom lokalen Server verwendete Standardport) antwortet, das Testskript auszuführen und dann den lokalen Server zu beenden.
