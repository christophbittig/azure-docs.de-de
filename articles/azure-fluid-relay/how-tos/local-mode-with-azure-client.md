---
title: Verwenden von AzureClient für lokale Tests
description: Verwenden von AzureClient zum Testen einer Anwendung ohne Dienst
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: f73c5e62471f6c038d7338244ec4756b533fe2d7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661833"
---
# <a name="how-to-use-azureclient-for-local-testing"></a>Verwenden von AzureClient für lokale Tests

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

In diesem Artikel werden die Schritte zum Konfigurieren von **AzureClient** im lokalen Modus und zum lokalen Testen Ihrer Fluid-Anwendung beschrieben.

## <a name="configure-and-create-an-azureclient"></a>Konfigurieren und Erstellen von AzureClient

**AzureClient** kann zur Ausführung für eine lokale Azure Fluid Relay-Instanz konfiguriert werden, indem eine Konfiguration wie die folgende übergeben wird.

```js
    import { AzureClient, AzureConnectionConfig, LOCAL_MODE_TENANT_ID } from "@fluidframework/azure-client";
    import { InsecureTokenProvider } from "@fluidframework/test-client-utils";

    const clientProps = {
        connection: {
            tenantId: LOCAL_MODE_TENANT_ID,
            tokenProvider: new InsecureTokenProvider("", { id: "123", name: "Test User" }),
            orderer: "http://localhost:7070",
            storage: "http://localhost:7070",
        },
    };

    const azureClient = new AzureClient(clientProps);
```

In diesem Beispiel wird der **InsecureTokenProvider** verwendet, um Authentifizierungstoken zu generieren und zu signieren, die vom Azure Fluid Relay-Dienst akzeptiert werden. Wie der Name schon sagt, ist er jedoch unsicher und sollte in Produktionsumgebungen nicht verwendet werden. Weitere Informationen zum InsecureTokenProvider finden Sie unter [Authentifizierung und Autorisierung in Ihrer App](https://fluidframework.com/docs/build/auth/#the-token-provider).

Für die lokale Ausführung konfigurieren Sie zunächst die Orderer- und Speicher-URLs so, dass sie auf die Domäne und den Port verweisen, unter denen die lokale Azure Fluid Relay-Dienstinstanz ausgeführt wird (standardmäßig http://localhost:7070 ). Der letzte Schritt besteht darin, `tenantId` auf `LOCAL_MODE_TENANT_ID` festzulegen. Über all diese Einstellungen wird AzureClient für die Arbeit mit einem lokalen Azure Fluid Relay-Dienst konfiguriert.  

## <a name="enabling-debug-logging"></a>Aktivieren der Debugprotokollierung

Sie können die integrierte Debugprotokollierung über das Fluid-Framework aktivieren, indem Sie die folgende Einstellung in einer Browserkonsole verwenden.

`localStorage.debug = 'fluid:*'`

Für komplexere Szenarien können Sie `logger` an AzureClient übergeben. So können Sie das Protokollierungsverhalten anpassen. Weitere Informationen zu Protokollierung oder Telemetrie finden Sie unter [Protokollierung und Telemetrie](https://fluidframework.com/docs/testing/telemetry/) auf fluidframework.com. 

## <a name="running-azure-fluid-relay-service-locally"></a>Lokales Ausführen des Azure Fluid Relay-Diensts

Um den lokalen Modus von AzureClient zu verwenden, müssen Sie zunächst einen lokalen Server starten. Wenn Sie `npx @fluidframework/azure-local-service@latest` über Ihr Terminalfenster ausführen, wird der lokale Azure Fluid Relay-Server gestartet. Nachdem der Server gestartet wurde, können Sie Ihre Anwendung für den lokalen Dienst ausführen.
