---
title: Schreiben eines TokenProvider-Elements mit einer Azure-Funktion
description: Hier erfahren Sie, wie Sie einen benutzerdefinierten Tokenanbieter als Azure-Funktion schreiben und bereitstellen.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/tokenproviders/
ms.openlocfilehash: 80524d6ab2da2e805e1107755cef4cfb367f6d2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039447"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>Schreiben eines TokenProvider-Elements mit einer Azure-Funktion

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Im [Fluid Framework](https://fluidframework.com/) sind TokenProvider für das Erstellen und Signieren von Token verantwortlich, die vom `@fluidframework/azure-client` verwendet werden, um Anforderungen an den Azure Fluid Relay-Dienst zu senden. Das Fluid Framework stellt einen einfachen, unsicheren TokenProvider für Entwicklungszwecke bereit, der passend als **InsecureTokenProvider** bezeichnet wird. Jeder Fluid-Dienst muss einen benutzerdefinierten TokenProvider basierend auf den Authentifizierungs- und Sicherheitsaspekten des jeweiligen Diensts implementieren.

Jedem erstellten Azure Fluid Relay-Dienstmandanten werden eine **Mandanten-ID** und ein eigener eindeutiger **geheimer Mandantenschlüssel** zugewiesen. Der geheime Schlüssel ist ein **freigegebener geheimer Schlüssel**. Ihre App/Ihr Dienst und der Azure Fluid Relay-Dienst kennen ihn. TokenProvider müssen den geheimen Schlüssel kennen, um Anforderungen zu signieren, aber der geheime Schlüssel darf nicht im Clientcode enthalten sein.

## <a name="implement-an-azure-function-to-sign-tokens"></a>Implementieren einer Azure-Funktion zum Signieren von Token

Eine Möglichkeit zum Erstellen eines sicheren Tokenanbieters besteht darin, einen HTTPS-Endpunkt sowie eine TokenProvider-Implementierung zu erstellen, die authentifizierte HTTPS-Anforderungen zum Abrufen von Token an diesen Endpunkt sendet. Dadurch können Sie den *geheimen Mandantenschlüssel* an einem sicheren Speicherort speichern – beispielsweise in [Azure Key Vault](../../key-vault/general/overview.md).

Die vollständige Lösung umfasst zwei Komponenten:

1. Einen HTTPS-Endpunkt, der Anforderungen akzeptiert und Azure Fluid Relay-Token zurückgibt
1. Eine ITokenProvider-Implementierung, die eine URL eines Endpunkts akzeptiert und dann Anforderungen zum Abrufen von Token an diesen Endpunkt sendet

### <a name="create-an-endpoint-for-your-tokenprovider-using-azure-functions"></a>Erstellen eines Endpunkts für Ihre TokenProvider-Instanz mithilfe von Azure Functions

Mit [Azure Functions](../../azure-functions/functions-overview.md) können Sie schnell einen solchen HTTPS-Endpunkt erstellen. Im folgenden Beispiel wird dieses Muster in einer Klasse namens **AzureFunctionTokenProvider** implementiert. Sie akzeptiert die URL zu Ihrer Azure-Funktion, `userId` und `userName`. Diese spezifische Implementierung wird auch als Export aus dem `@fluidframework/azure-client`-Paket für Sie bereitgestellt.

In diesem Beispiel wird veranschaulicht, wie Sie eine eigene **HTTPTrigger-Azure-Funktion** erstellen, die das Token abruft, indem Sie Ihren Mandantenschlüssel übergeben.

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

// NOTE: retrieve the key from a secure location.
const key = "myTenantKey";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    // tenantId, documentId, userId and userName are required parameters
    const tenantId = (req.query.tenantId || (req.body && req.body.tenantId)) as string;
    const documentId = (req.query.documentId || (req.body && req.body.documentId)) as string;
    const userId = (req.query.userId || (req.body && req.body.userId)) as string;
    const userName = (req.query.userName || (req.body && req.body.userName)) as string;
    const scopes = (req.query.scopes || (req.body && req.body.scopes)) as ScopeType[];

    if (!tenantId) {
        context.res = {
            status: 400,
            body: "No tenantId provided in query params",
        };
        return;
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
        return;
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
        return;
    }

    let user = { name: userName, id: userId };

    // Will generate the token and returned by an ITokenProvider implementation to use with the AzureClient.
    const token = generateToken(
        tenantId,
        documentId,
        key,
        scopes ?? [ScopeType.DocRead, ScopeType.DocWrite, ScopeType.SummaryWrite],
        user
    );

    context.res = {
        status: 200,
        body: token
    };
};

export default httpTrigger;
```

Die Funktion `generateToken` aus dem Paket `@fluidframework/azure-service-utils` generiert ein Token für den angegebenen Benutzer, das mit dem geheimen Schlüssel des Mandanten signiert wird. Dadurch kann das Token an den Client zurückgegeben werden, ohne das Geheimnis preiszugeben. Stattdessen wird das Token serverseitig unter Verwendung des Geheimnisses generiert, um bereichsbezogenen Zugriff auf das angegebene Dokument zu gewähren. Im ITokenProvider-Beispiel weiter unten werden HTTP-Anforderungen an diese Azure-Funktion gesendet, um Token abzurufen.

### <a name="deploy-the-azure-function"></a>Bereitstellen der Azure-Funktions-App

Azure Functions kann auf verschiedene Arten bereitgestellt werden. Weitere Informationen zum Bereitstellen von Azure Functions finden Sie in der [Azure Functions-Dokumentation](../../azure-functions/functions-continuous-deployment.md) im Abschnitt **Bereitstellen**.

### <a name="implement-the-tokenprovider"></a>Implementieren des Tokenanbieters (TokenProvider)

Tokenanbieter können auf verschiedene Arten implementiert werden. Sie müssen jedoch zwei separate API-Aufrufe implementieren: `fetchOrdererToken` und `fetchStorageToken`. Diese APIs sind für das Abrufen von Token für den Fluid-Orderer und die Speicherdienste zuständig. Beide Funktionen geben `TokenResponse`-Objekte zurück, die den Tokenwert darstellen. Die Fluid Framework-Runtime ruft diese beiden APIs nach Bedarf auf, um Token abzurufen.


Um sicherzustellen, dass der geheime Mandantenschlüssel sicher aufbewahrt wird, wird er an einem sicheren Back-End-Speicherort gespeichert und ist nur innerhalb der Azure-Funktion zugänglich. Zum Abrufen von Token müssen Sie eine Anforderung vom Typ `GET` oder `POST` an Ihre bereitgestellte Azure-Funktion senden und die Mandanten-ID (`tenantID`), die Dokument-ID (`documentId`) und die Benutzer-ID/den Benutzernamen (`userID`/`userName`) angeben. Die Azure-Funktion ist für die Zuordnung zwischen der Mandanten-ID und einem Mandantenschlüsselgeheimnis zuständig, um das Token entsprechend zu generieren und zu signieren.

In der folgenden Beispielimplementierung wird die Bibliothek [axios](https://www.npmjs.com/package/axios) für HTTP-Anforderungen verwendet. Sie können andere Bibliotheken oder Ansätze verwenden, um eine HTTP-Anforderung über Servercode durchzuführen.

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/routerlicious-driver";
import axios from "axios";
import { AzureMember } from "./interfaces";

/**
 * Token Provider implementation for connecting to an Azure Function endpoint for
 * Azure Fluid Relay token resolution.
 */
export class AzureFunctionTokenProvider implements ITokenProvider {
    /**
     * Creates a new instance using configuration parameters.
     * @param azFunctionUrl - URL to Azure Function endpoint
     * @param user - User object
     */
    constructor(
        private readonly azFunctionUrl: string,
        private readonly user?: Pick<AzureMember, "userId" | "userName" | "additionalDetails">,
    ) { }

    public async fetchOrdererToken(tenantId: string, documentId?: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    private async getToken(tenantId: string, documentId: string | undefined): Promise<string> {
        const response = await axios.get(this.azFunctionUrl, {
            params: {
                tenantId,
                documentId,
                userId: this.user?.userId,
                userName: this.user?.userName,
                additionalDetails: this.user?.additionalDetails,
            },
        });
        return response.data as string;
    }
}
```
## <a name="see-also"></a>Siehe auch

- [Hinzufügen benutzerdefinierter Daten zu einem Authentifizierungstoken](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
- [Bereitstellen von Fluid-Anwendungen mit Azure Static Web Apps](deploy-fluid-static-web-apps.md)
