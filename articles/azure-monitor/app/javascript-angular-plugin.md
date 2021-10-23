---
title: Angular-Plug-in für das Application Insights-JavaScript-SDK
description: Hier erfahren Sie, wie Sie das Angular-Plug-In für das Application Insights-JavaScript-SDK installieren und verwenden.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: dc5b7e3f1e452bbbf4e65442cbf683b4cb1a8816
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129812"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Angular-Plug-in für das Application Insights-JavaScript-SDK

Das Angular-Plug-In für das Application Insights-JavaScript-SDK bietet folgende Möglichkeiten:

- Nachverfolgung von Routeränderungen
- Nachverfolgung nicht abgefangener Ausnahmen

> [!WARNING]
> Das Angular-Plug-In ist nicht kompatibel mit ECMAScript 3 (ES3).

## <a name="getting-started"></a>Erste Schritte

Installieren Sie das npm-Paket:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Grundlegende Verwendung

Richten Sie eine Application Insights-Instanz in der Eintragskomponente in Ihrer App ein:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

Zum Nachverfolgen nicht abgefangener Ausnahmen richten Sie „ApplicationinsightsAngularpluginErrorService“ in `app.module.ts` ein:

```js
import { ApplicationinsightsAngularpluginErrorService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
  ...
  providers: [
    {
      provide: ErrorHandler,
      useClass: ApplicationinsightsAngularpluginErrorService
    }
  ]
  ...
})
export class AppModule { }
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum JavaScript SDK finden Sie in der [Dokumentation zum Application Insights JavaScript SDK](javascript.md).
- [Angular-Plug-in auf GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
