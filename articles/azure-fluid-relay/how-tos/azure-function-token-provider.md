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
ms.openlocfilehash: d6987b4e4592167fcb41a7f6654ff46140a79724
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661795"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>Schreiben eines TokenProvider-Elements mit einer Azure-Funktion

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

Im [Fluid Framework](https://fluidframework.com/) sind TokenProvider für das Erstellen und Signieren von Token verantwortlich, die vom `@fluidframework/azure-client` verwendet werden, um Anforderungen an den Azure Fluid Relay-Dienst zu senden. Das Fluid Framework stellt einen einfachen, unsicheren TokenProvider für Entwicklungszwecke bereit, der passend als **InsecureTokenProvider** bezeichnet wird. Jeder Fluid-Dienst muss einen benutzerdefinierten TokenProvider basierend auf den Authentifizierungs- und Sicherheitsaspekten des jeweiligen Diensts implementieren.

## <a name="implementing-your-own-tokenprovider-class"></a>Implementieren Ihrer eigenen TokenProvider-Klasse

Jedem erstellten Azure Fluid Relay-Dienstmandanten werden eine **Mandanten-ID** und ein eigener eindeutiger **geheimer Mandantenschlüssel** zugewiesen. Der geheime Schlüssel ist ein **freigegebener geheimer Schlüssel**. Ihre App/Ihr Dienst und der Azure Fluid Relay-Dienst kennen ihn. 

TokenProvider müssen den geheimen Schlüssel kennen, um Anforderungen zu signieren, aber der geheime Schlüssel darf nicht im Clientcode enthalten sein. TokenProvider kontaktieren zur Laufzeit den Fluid-Server, um den geheimen Schlüssel sicher abzurufen, ohne ihn für den Client verfügbar zu machen. Dies wird durch zwei separate API-Aufrufe erreicht: `fetchOrdererToken` und `fetchStorageToken`. Sie sind für das Abrufen der Orderer- und Speicher-URLs vom Host verantwortlich. Beide Funktionen geben `TokenResponse`-Objekte zurück, die den Tokenwert darstellen.

## <a name="tokenprovider-class-example"></a>TokenProvider-Klassenbeispiel

Eine Möglichkeit zum Erstellen eines sicheren Tokenanbieters ist das Erstellen einer serverlosen Azure-Funktion und deren Bereitstellung als Tokenanbieter. Dadurch können Sie den *geheimen Mandantenschlüssel* auf einem sicheren Server speichern. Ihre Anwendung würde dann die Azure-Funktion aufrufen, um Token zu generieren.

In diesem Beispiel wird dieses Muster in einer Klasse namens **AzureFunctionTokenProvider** implementiert. Sie akzeptiert die URL zu Ihrer Azure-Funktion, `userId` und `userName`. Diese spezifische Implementierung wird auch als Export aus dem `@fluidframework/azure-client`-Paket für Sie bereitgestellt.

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/azure-client";

export class AzureFunctionTokenProvider implements ITokenProvider {
  constructor(
    private readonly azFunctionUrl: string,
    private readonly userId: string,
    private readonly userName: string,
  );

  public async fetchOrdererToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }
}
```

Um sicherzustellen, dass der geheime Mandantenschlüssel sicher aufbewahrt wird, wird er an einem sicheren Back-End-Speicherort gespeichert und ist nur innerhalb der Azure-Funktion zugänglich. Eine Möglichkeit zum Abrufen eines signierten Tokens ist, eine `GET`-Anforderung an Ihre Azure-Funktion zu richten, indem die `tenantID`, `documentId` und `userID`/`userName` bereitgestellt werden. Die Azure-Funktion ist für die Zuordnung zwischen der Mandanten-ID und einem Mandantenschlüsselgeheimnis verantwortlich, um das Token entsprechend zu generieren und zu signieren, sodass es vom Azure Fluid Relay-Dienst akzeptiert wird.

```typescript
private async getToken(tenantId: string, documentId: string): Promise<string> {
    const params = {
        tenantId,
        documentId,
        userId: this.userId,
        userName: this.userName,
    };
    const token = this.getTokenFromServer(params);
    return token;
}
```

Im folgenden Beispiel wird die [`axios`](https://www.npmjs.com/package/axios)-Bibliothek verwendet, um HTTP-Anforderungen zu erstellen. Sie können andere Bibliotheken oder Ansätze verwenden, um eine HTTP-Anforderung zu erstellen.

```typescript
private async getTokenFromServer(input: any): Promise<string> {
    return axios.get(this.azFunctionUrl, {
        params: input,
    }).then((response) => {
        return response.data as string;
    }).catch((err) => {
        return err as string;
    });
}
```

In diesem Beispiel wird veranschaulicht, wie Sie eine eigene **HTTPTrigger-Azure-Funktion** erstellen, die das Token abruft, indem Sie Ihren Mandantenschlüssel übergeben.

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

//Replace "myTenantKey" with your key here.
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
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
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

Die `generateToken`-Funktion generiert ein Token für den angegebenen Benutzer, das mit dem geheimen Schlüssel des Mandanten signiert wird. So kann das Token an den Client zurückgegeben werden, ohne dass jemals das Geheimnis für ihn verfügbar gemacht wird. Stattdessen wird das Token damit generiert, um Bereichszugriff auf das angegebene Dokument bereitzustellen. Dieses Token kann von einer `ITokenProvider`-Implementierung zur Verwendung mit dem `AzureClient` zurückgegeben werden.

## <a name="see-also"></a>Siehe auch

- [Hinzufügen benutzerdefinierter Daten zu einem Authentifizierungstoken](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
