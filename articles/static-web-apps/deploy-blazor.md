---
title: 'Tutorial: Erstellen einer statischen Web-App mit Blazor in Azure Static Web Apps'
description: Erfahren Sie, wie Sie eine Azure Static Web Apps-Website mit Blazor erstellen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: cshoe
ms.openlocfilehash: ac22d5ef720fb0701cb126e6fa0cb627614dd09c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027164"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Tutorial: Erstellen einer statischen Web-App mit Blazor in Azure Static Web Apps

Azure Static Web Apps veröffentlicht eine Website in einer Produktionsumgebung, indem Apps aus einem GitHub-Repository erstellt werden. In diesem Tutorial stellen Sie eine Webanwendung in Azure Static Web Apps über das Azure-Portal bereit.

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/free) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [GitHub](https://github.com) -Konto
- [Azure](https://portal.azure.com)-Konto

## <a name="application-overview"></a>Anwendungsübersicht

Mit Azure Static Web Apps können Sie statische Webanwendungen erstellen, die von einem serverlosen Back-End gestützt werden. Im folgenden Tutorial wird veranschaulicht, wie Sie eine C#-Blazor-WebAssembly-Anwendung bereitstellen, die von einer serverlosen API zurückgegebene Wetterdaten anzeigt.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Fertig gestellte Blazor-App":::

Die in diesem Tutorial gezeigte App setzt sich aus drei verschiedenen Visual Studio-Projekten zusammen:

- **API**: Die Azure Functions-C#-Anwendung, die den API-Endpunkt zur Bereitstellung von Wetterinformationen für die Blazor-WebAssembly-App implementiert. Die Funktion **WeatherForecastFunction** gibt ein Array mit `WeatherForecast`-Objekten zurück.

- **Client**: Das Front-End-Blazor-WebAssembly-Projekt. Eine [Fallbackroute](#fallback-route) wird implementiert, um sicherzustellen, dass das clientseitige Routing funktioniert.

- **Gemeinsam genutzt**: Enthält gemeinsame Klassen, die sowohl von der API als auch von den Clientprojekten referenziert werden, damit Daten vom API-Endpunkt an die Front-End-Web-App übertragen werden können. Die Klasse [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) wird von beiden Apps gemeinsam genutzt.

Diese Projekte werden zum Erstellen einer Blazor-WebAssembly-Anwendung kombiniert, die in dem von einem Azure Functions-API-Back-End unterstützten Browser ausgeführt wird.

## <a name="fallback-route"></a>Fallbackroute

Die Anwendung macht URLs wie _/counter_ und _/fetchdata_ verfügbar, die bestimmten Routen der Anwendung zugeordnet sind. Da diese App als Single-Page-Webanwendung implementiert ist, führt jede Route zur Datei _index.html_. Um sicherzustellen, dass bei Anforderungen für einen beliebigen Pfad die Datei _index.html_ zurückgegeben wird, ist in der Datei _staticwebapp.config.json_ eine [Fallbackroute](./configuration.md#fallback-routes) implementiert. Diese Datei befindet sich im Stammordner des Clientprojekts.

```json
{
  "navigationFallback": {
    "rewrite": "/index.html"
  }
}
```

Mit der obigen Konfiguration wird sichergestellt, dass bei jeglichen Anforderungen an eine beliebige Route in der App die Seite _index.html_ zurückgegeben wird.

## <a name="create-a-repository"></a>Erstellen eines Repositorys

In diesem Artikel wird ein GitHub-Vorlagenrepository verwendet, um Ihnen den Einstieg zu erleichtern. Die Vorlage enthält eine Starter-App, die in Azure Static Web Apps bereitgestellt werden kann.

1. Stellen Sie sicher, dass Sie bei GitHub angemeldet sind, und navigieren Sie zum folgenden Speicherort, um ein neues Repository zu erstellen:
   - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Geben Sie Ihrem Repository den Namen **my-first-static-blazor-app**.

## <a name="create-a-static-web-app"></a>Erstellen einer statischen Web-App

Nachdem das Repository erstellt wurde, erstellen Sie im Azure-Portal eine statische Web-App.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Ressource erstellen**.
1. Suchen Sie nach **Static Web Apps**.
1. Klicken Sie auf **statische Web-Apps**.
1. Klicken Sie auf **Erstellen**.
1. Geben Sie auf der Registerkarte _Grundlagen_ die folgenden Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Abonnement_ | Den Namen Ihres Azure-Abonnements |
    | _Ressourcengruppe_ | **my-blazor-group**  |
    | _Name_ | **my-first-static-blazor-app** |
    | _Plantyp_ | **Free** |
    | _Region für Azure Functions-API und Stagingumgebungen_ | Wählen Sie die Region aus, die Ihnen am nächsten ist. |
    | _Quelle_ | **GitHub** |

1. Wählen Sie **Mit GitHub anmelden** aus, und authentifizieren Sie sich bei GitHub.

1. Geben Sie die folgenden GitHub-Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Organisation_ | Wählen Sie Ihre gewünschte GitHub-Organisation aus. |
    | _Repository_ | Wählen Sie **my-first-static-blazor-app** aus. |
    | _Branch_ | Wählen Sie **main** aus. |

1. Wählen Sie im Abschnitt _Builddetails_ in der Dropdownliste _Buildvoreinstellungen_ die Option **Blazor** aus. Die folgenden Werte werden aufgefüllt:

    | Eigenschaft | Wert | BESCHREIBUNG |
    | --- | --- | --- |
    | App-Speicherort | **Client** | Ordner mit der Blazor-WebAssembly-App |
    | API-Speicherort | **API** | Ordner mit der Azure Functions-App |
    | Ausgabeverzeichnis | **wwwroot** | Ordner in der Buildausgabe mit der veröffentlichten Blazor-WebAssembly-Anwendung |
    
### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Wählen Sie die Schaltfläche **Überprüfen und erstellen** aus, um sicherzustellen, dass alle Details stimmen.

1. Wählen Sie **Erstellen** aus, um mit der Erstellung der statischen App Service-Web-App zu beginnen und einen GitHub Action-Vorgang für die Bereitstellung anzugeben.

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Zu Ressource wechseln**.

1. Wählen Sie **Zu Ressource wechseln** aus.

   :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Schaltfläche „Zu Ressource wechseln“":::

## <a name="view-the-website"></a>Anzeigen der Website

Für die Bereitstellung einer statischen App gelten zwei Aspekte. Der erste Aspekt ist die Bereitstellung der zugrunde liegenden Azure-Ressourcen, aus denen Ihre App besteht. Der zweite ist ein GitHub-Actions-Workflow, mit dem Ihre Anwendung erstellt und veröffentlicht wird.

Bevor Sie zu Ihrer neuen statischen Web-App navigieren können, muss zuerst die Ausführung des Buildvorgangs für die Bereitstellung abgeschlossen sein.

Im Übersichtsfenster von Azure Static Web Apps werden einige Links angezeigt, die Ihnen als Hilfe bei der Interaktion mit Ihrer Web-App dienen.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Übersichtsfenster":::

1. Wenn Sie auf das Banner _Click here to check the status of your GitHub Actions runs_ (Hier klicken, um den Status Ihrer GitHub Actions-Ausführungen zu überprüfen) klicken, gelangen Sie zu den GitHub Actions-Instanzen, die für Ihr Repository ausgeführt werden. Nachdem Sie sich vergewissert haben, dass der Bereitstellungsauftrag abgeschlossen ist, können Sie über die generierte URL zu Ihrer Website navigieren.

2. Nachdem der GitHub Actions-Workflow abgeschlossen ist, können Sie den _URL_-Link auswählen, um die Website auf einer neuen Registerkarte zu öffnen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiter nutzen möchten, können Sie die Azure Static Web Apps-Instanz mit den folgenden Schritten löschen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Suchen Sie in der oberen Suchleiste nach **my-blazor-group**.
1. Wählen Sie den Gruppennamen aus.
1. Klicken Sie auf die Schaltfläche **Löschen**.
1. Klicken Sie zum Bestätigen des Löschvorgangs auf **Ja**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Authentifizierung und Autorisierung](./authentication-authorization.md)
