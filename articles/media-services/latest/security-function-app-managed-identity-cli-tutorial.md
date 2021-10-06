---
title: Gewähren von Zugriff auf ein Media Services-Konto für eine Azure-Funktions-App
description: Angenommen, Sie möchten für Ihr Sendestudio einen Hinweis mit der Aufschrift „On Air“ erstellen. Mithilfe der Media Services-API können Sie zwar ermitteln, wann Media Services-Liveereignisse ausgeführt werden, der dazu nötige Aufruf kann über eingebettete Geräte jedoch schwierig sein. Alternativ können Sie mithilfe von Azure Functions eine HTTP-API für Ihr eingebettetes Gerät verfügbar machen. Daraufhin kann Media Services von Azure Functions aufgerufen werden, um den Status des Liveereignisses abzurufen.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: inhenkel
ms.openlocfilehash: dc05d6488978004eebee68b901214ab71f0fffd4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656643"
---
# <a name="tutorial-give-an-azure-function-app-access-to-a-media-services-account"></a>Tutorial: Gewähren von Zugriff auf ein Media Services-Konto für eine Azure-Funktions-App

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Angenommen, Sie möchten Besucher Ihrer Website oder Anwendung darauf hinweisen, dass Sie in Ihrem Sendestudio auf Sendung (On Air) sind. Mithilfe der Media Services-API können Sie zwar ermitteln, wann Media Services-Liveereignisse ausgeführt werden, der dazu nötige Aufruf kann über eingebettete Geräte jedoch schwierig sein. Alternativ können Sie mithilfe von Azure Functions eine HTTP-API für Ihr eingebettetes Gerät verfügbar machen. Daraufhin kann Media Services von Azure Functions aufgerufen werden, um den Status des Liveereignisses abzurufen.

:::image type="content" source="media/diagrams/managed-identities-scenario-function-app-access-media-services-account.svg" alt-text="Gewähren des Zugriffs auf ein Media Services-Konto für eine Funktions-App mithilfe verwalteter Identitäten":::

In diesem Tutorial wird die Media Services-API 2020-05-01 verwendet.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Um die Befehle in diesem Artikel verwenden zu können, müssen Sie bei dem Abonnement angemeldet sein, das Sie verwenden möchten.

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>Festlegen des Abonnements

Verwenden Sie den folgenden Befehl, um das Abonnement festzulegen, mit dem Sie arbeiten möchten.

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Es empfiehlt sich sehr, vor diesem Tutorial zunächst die [Schnellstartanleitung zum Erstellen einer C#-Funktion über die Befehlszeile in Azure](../../azure-functions/create-first-function-cli-csharp.md) durchzuarbeiten.  Die dort angegebenen Einrichtungsschritte sind nämlich die gleichen, die auch hier erforderlich sind.  Außerdem können Sie so mit einem einfachen Beispiel arbeiten, auf dem dieses Tutorial basiert.

## <a name="resource-names"></a>Ressourcennamen

Überlegen Sie sich zunächst, wie die von Ihnen erstellten Ressourcen heißen sollen.  Sie sollten problemlos als Gruppe identifizierbar sein – insbesondere, wenn Sie nicht vorhaben, sie nach dem Testen weiter zu verwenden. Da sich Benennungsregeln für viele Ressourcentypen unterscheiden, empfiehlt es sich, nur Kleinbuchstaben zu verwenden. Beispiel: „mediatest1rg“ für den Namen Ihrer Ressourcengruppe und „mediatest1stor“ für den Namen Ihres Speicherkontos. Verwenden Sie für jeden Schritt in diesem Artikel die gleichen Namen.

Auf diese Namen wird in den Befehlen weiter unten verwiesen.  Sie benötigen Namen für folgende Ressourcen:

- „your-resource-group-name“ (Name Ihrer Ressourcengruppe)
- „your-storage-account-name“ (Name Ihres Speicherkontos)
- „your-media-services-account-name“ (Name Ihres Media Services-Kontos)
- „your-region“ (Ihre Region)
- „your-function-name“ (Name Ihrer Funktion): Verwenden Sie „OnAir“.
- „your-live-event-name“ (Name Ihres Liveereignisses): Verwenden Sie „live1“.
- „your-ip-address“ (Ihre IP-Adresse): Verwenden Sie „0.0.0./32“.

> [!NOTE]
> Die Bindestriche dienen lediglich zur besseren Lesbarkeit. Verwenden Sie aufgrund der Inkonsistenz bei der Benennung von Ressourcen in Azure-Diensten keine Bindestriche in Ihren Ressourcennamen.<br/><br/>
> Bei allem, was durch „00000000-0000-0000-0000000000“ dargestellt wird, handelt es sich um den eindeutigen Bezeichner der Ressource.  Dieser Wert wird in der Regel von einer JSON-Antwort zurückgegeben. Es empfiehlt sich außerdem, die JSON-Antworten zu kopieren und in Editor oder in einen anderen Text-Editor einzufügen, da diese Antworten Werte enthalten, die für spätere CLI-Befehle benötigt werden.<br/><br/>
> Der Regionsname wird außerdem nicht von Ihnen erstellt,  sondern von Azure bestimmt.

### <a name="list-azure-regions"></a>Auflisten der Azure-Regionen

Falls Sie nicht genau wissen, welchen Regionsnamen Sie verwenden sollen, können Sie mithilfe des folgenden Befehls eine Liste abrufen:

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>Sequenz

Die folgenden Schritte werden in einer bestimmten Reihenfolge ausgeführt, da jeweils einzelne oder mehrere Werte aus den JSON-Antworten im nächsten Schritt der Sequenz verwendet werden.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Die von Ihnen erstellten Ressourcen müssen einer Ressourcengruppe angehören. Erstellen Sie zuerst die Ressourcengruppe. Für die Erstellung des Media Services-Kontos und in nachfolgenden Schritten wird `your-resource-group-name` verwendet.

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Erstellen eines Speicherkontos

Dem von Ihnen erstellten Media Services-Konto muss ein Speicherkonto zugeordnet sein. Erstellen Sie zuerst das Speicherkonto für das Media Services-Konto. In nachfolgenden Schritten wird `your-storage-account-name` verwendet.

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Erstellen eines Media Services-Kontos

Erstellen Sie nun das Media Services-Konto: Suchen Sie nach „`“.

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-cli.md)]

## <a name="set-up-the-azure-function"></a>Einrichten der Azure-Funktion

In diesem Abschnitt wird die Azure-Funktion eingerichtet.

### <a name="get-the-code"></a>Abrufen des Codes

Verwenden Sie Azure Functions, um Ihr Funktionsprojekt zu erstellen und den Code aus der HTTP-Vorlage abzurufen.

```azurecli-interactive
func init MediaServicesLiveMonitor –dotnet
```

### <a name="change-directory"></a>Wechseln des Verzeichnisses

Achten Sie darauf, dass Ihr Arbeitsverzeichnis dem Projektverzeichnis entspricht.  Andernfalls treten Fehler auf.

```azurecli-interactive
cd .\MediaServicesLiveMonitor\
```

### <a name="name-your-function"></a>Benennen Ihrer Funktion

```azurecli-interactive
func new --name OnAir --template "HTTP trigger" --authlevel "anonymous"
```

## <a name="configure-the-functions-project"></a>Konfigurieren des Functions-Projekts

### <a name="install-media-services-and-other-extensions"></a>Installieren von Media Services und anderen Erweiterungen

Führen Sie den Befehl dotnet add package im Terminalfenster aus, um die Erweiterungspakete zu installieren, die Sie in Ihrem Projekt benötigen. Mit dem folgenden Befehl werden die Pakete Media Services und Azure Identity installiert.

```bash
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

### <a name="edit-the-onaircs-code"></a>Bearbeiten des Codes in der Datei „OnAir.cs“

Ändern Sie die Datei `OnAir.cs`. Ändern Sie die Variablen `subscriptionId`, `resourceGroup` und `mediaServicesAccountName` in die Werte, für die Sie sich zuvor entschieden haben.

```csharp
using Azure.Core;
using Azure.Identity;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Rest;
using System.Threading.Tasks;

namespace MediaServicesLiveMonitor
{
    public static class OnAir
    {
        [FunctionName("OnAir")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            if (string.IsNullOrWhiteSpace(name))
            {
                return new BadRequestObjectResult("Missing 'name' URL parameter");
            }

            var credential = new ManagedIdentityCredential();
            var accessTokenRequest = await credential.GetTokenAsync(
                new TokenRequestContext(
                    scopes: new string[] { "https://management.core.windows.net" + "/.default" }
                    )
                );
            ServiceClientCredentials credentials = new TokenCredentials(accessTokenRequest.Token, "Bearer");

            var subscriptionId = "00000000-0000-0000-000000000000";                 // Update
            var resourceGroup = "<your-resource-group-name>";                       // Update
            var mediaServicesAccountName = "<your-media-services-account-name>";    // Update

            var mediaServices = new AzureMediaServicesClient(credentials)
            {
                SubscriptionId = subscriptionId
            };

            var liveEvent = await mediaServices.LiveEvents.GetAsync(resourceGroup, mediaServicesAccountName, name);

            if (liveEvent == null)
            {
                return new NotFoundResult();
            }

            return new OkObjectResult(liveEvent.ResourceState == LiveEventResourceState.Running ? "On air" : "Off air");
        }
    }
}
```

## <a name="create-the-function-app"></a>Erstellen der Funktions-App

Erstellen Sie die Funktions-App, die als Host für die Funktion fungiert. Der Name entspricht dem des vorherigen Downloads (`MediaServicesLiveMonitorApp`).

```azurecli-interactive

az functionapp create --resource-group <your-resource-group-name> --consumption-plan-location your-region --runtime dotnet --functions-version 3 --name MediaServicesLiveMonitorApp --storage-account mediatest3store --assign-identity "[system]"

```

Suchen Sie in der JSON-Antwort nach `principalId`:

```json
{
...  
"identity": {
//Note the principalId value for the following step
    "principalId": "00000000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-000000000000",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  }
...
```

## <a name="grant-the-function-app-access-to-the-media-services-account-resource"></a>Gewähren von Zugriff auf die Media Services-Kontoressource für die Funktions-App

Für diese Anforderung gilt Folgendes:

- `assignee` ist die Prinzipal-ID (`principalId`) aus der JSON-Antwort von `az functionapp create`.
- `scope` ist die ID (`id`) aus der JSON-Antwort von `az ams account create`.  Sehen Sie sich zur Referenz die obige JSON-Beispielantwort an.

```azurecli-interactive
az role assignment create --assignee 00000000-0000-0000-000000000000 --role "Media Services Account Administrator" --scope "/subscriptions/<the-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.Media/mediaservices/<your-media-services-account-name>"
```

## <a name="publish-the-function"></a>Veröffentlichen der Funktion

```azurecli-interactive
func azure functionapp publish MediaServicesLiveMonitorApp
```

## <a name="validation"></a>Überprüfung

Navigieren Sie in einem Browser zur Funktions-URL. Beispiel:

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

Daraufhin sollte ein Fehler vom Typ 404 (Nicht gefunden) zurückgeben werden, da das Liveereignis noch nicht vorhanden ist.

### <a name="create-a-live-event"></a>Erstellen eines Liveereignisses

```azurecli-interactive
az ams live-event create --resource-group test3 --account-name mediatest3 --name live1 --streaming-protocol RTMP
```

Navigieren Sie in einem Browser zur Funktions-URL. Beispiel:

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

Daraufhin sollte „Off Air“ (Nicht auf Sendung) angezeigt werden.

### <a name="start-the-live-event"></a>Starten des Liveereignisses

Wenn Sie das Liveereignis starten, sollte von der Funktion „On Air“ (Auf Sendung) zurückgegeben werden.

```azurecli-interactive
az ams live-event start live1
```

Diese Funktion ermöglicht Zugriff für alle. Der Schutz des Zugriffs auf die Azure-Funktion und die Einrichtung eines On Air-Hinweises werden in diesem Dokument nicht behandelt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die von Ihnen erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe.

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
