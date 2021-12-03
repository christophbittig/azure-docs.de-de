---
title: Konfigurieren von Front-End-Frameworks mit Azure Static Web Apps
description: Einstellungen für beliebte Front-End-Frameworks, die für Azure Static Web Apps erforderlich sind
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 95f7c35cc33c1d174cd228bd053dbfea6c850135
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841694"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps"></a>Konfigurieren von Front-End-Frameworks und Bibliotheken mit Azure Static Web Apps

Azure Static Web Apps erfordert, dass Sie die entsprechenden Konfigurationswerte in der [Buildkonfigurationsdatei](build-configuration.md) für Ihr Front-End-Framework oder Ihre Bibliothek besitzen.

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle listet die Einstellungen für eine Reihe von Frameworks und Bibliotheken auf<sup>1</sup>.

Die Absicht der Tabellenspalten wird durch die folgenden Punkte erläutert:

- **Ausgabespeicherort**: Listet den Wert für `output_location` auf, dem [Ordner für erstellte Versionen von Anwendungsdateien](build-configuration.md).

- **Benutzerdefinierter Buildbefehl**: Wenn das Framework einen anderen Befehl als `npm run build` oder `npm run azure:build` erfordert, können Sie einen [benutzerdefinierten Buildbefehl](build-configuration.md#custom-build-commands) definieren.

| Framework | Speicherort für App-Artefakte | Benutzerdefinierter Buildbefehl |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | Nicht zutreffend <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --configuration production` |
| [Angular Universal](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Astro](https://astro.build) | `dist` | – |
| [Aurelia](https://aurelia.io/) | `dist` | – |
| [Backbone.js](https://backbonejs.org/) | `/` | – |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | – |
| [Ember](https://emberjs.com/) | `dist` | – |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | – |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | – |
| [Hyperapp](https://hyperapp.dev/) | `/` | – |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | – |
| [jQuery](https://jquery.com/) | `/` | – |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | – |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | – |
| [Marko](https://markojs.com/) | `public` | – |
| [Meteor](https://www.meteor.com/) | `bundle` | – |
| [Mithril](https://mithril.js.org/) | `dist` | – |
| [Polymer](https://www.polymer-project.org/) | `build/default` | – |
| [Preact](https://preactjs.com/) | `build` | – |
| [React](https://reactjs.org/) | `build` | – |
| [RedwoodJS](https://redwoodjs.com/) | `web/dist` | `yarn rw build` |
| [Stencil](https://stenciljs.com/) | `www` | – |
| [Svelte](https://svelte.dev/) | `public` | – |
| [Three.js](https://threejs.org/) | `/` | – |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | – |
| [Vue.js](https://vuejs.org/) | `dist` | – |

<sup>1</sup> Die obige Tabelle ist nicht als umfassende Liste von Frameworks und Bibliotheken gedacht, die mit Azure Static Web Apps arbeiten.

<sup>2</sup> Nicht zutreffend

## <a name="next-steps"></a>Nächste Schritte

- [Build- und Workflowkonfiguration](build-configuration.md)
