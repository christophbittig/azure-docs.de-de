---
title: Konfigurationsübersicht für Azure Static Web Apps
description: Hier erfahren Sie mehr über die verschiedenen Möglichkeiten zur Konfiguration von Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: cshoe
ms.openlocfilehash: 34a93dc1203fb404d0d6c2edfd70954545e26b15
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154924"
---
# <a name="configuration-overview"></a>Konfigurationsübersicht

Die folgenden verschiedenen Konzepte gelten für die Konfiguration einer statischen Web-App.

- [Anwendungskonfiguration](./configuration.md): Definieren Sie Regeln in der Datei `staticwebapp.config.json`, um das Anwendungsverhalten und die Features zu steuern. Verwenden Sie diese Datei, um Routen- und Sicherheitsregeln, benutzerdefinierte Header und Netzwerkeinstellungen zu definieren.

- [Buildkonfiguration](./build-configuration.md): Definieren Sie Einstellungen, die den Buildprozess steuern.

- [Anwendungseinstellungen](./application-settings.md): Legen Sie Einstellungen auf Anwendungsebene und Umgebungsvariablen fest, die von Back-End-APIs verwendet werden können.

## <a name="example-scenarios"></a>Beispielszenarien

| Zweck | Aktion |
|---|---|
| Routingregeln definieren | [Regeln in der Datei staticwebapps.config.json erstellen](./configuration.md) |
| Festlegen, welcher Branch Builds auslöst | [Den Namen des nachverfolgten Branchs in der Buildkonfigurationsdatei aktualisieren](./build-configuration.md)  |
| Definieren, welche Sicherheitsrollen Zugriff auf eine Route haben | [Routen mit Rollen in der Datei „staticwebapps.config.json“ schützen](./configuration.md#securing-routes-with-roles) |
| Festlegen, welche HTML-Datei bedient wird, wenn eine Route nicht mit einer tatsächlichen Datei übereinstimmen soll. | [Eine Fallbackroute in der Datei „staticwebapps.config.json“ definieren](./configuration.md#fallback-routes) |
| Globale Header für HTTP-Anforderungen festlegen | [Globale Header in der Datei „staticwebapps.config.json“ definieren](./configuration.md#global-headers)|
| Einen benutzerdefinierten Buildbefehl definieren | [Einen benutzerdefinierten Buildbefehlwert in der Anwendungskonfigurationsdatei festlegen](./build-configuration.md) |
| Eine Umgebungsvariable für einen Front-End-Build festlegen | [Eine Umgebungsvariable in der Buildkonfigurationsdatei festlegen](./build-configuration.md#environment-variables) |
| Eine Umgebungsvariable für eine API festlegen | [Eine Anwendungseinstellung im Portal festlegen](./application-settings.md) |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Anwendungskonfiguration](configuration.md)
