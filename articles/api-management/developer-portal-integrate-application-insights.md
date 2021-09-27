---
title: Integrieren von Application Insights in das Entwicklerportal
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Sie Application Insights in Ihr verwaltetes oder selbstgehostetes Entwicklerportal integrieren.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: b495a6141e064df80e57c4c96eff67fc928d58c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601688"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Integrieren von Application Insights in das Entwicklerportal

Ein beliebtes Feature von Azure Monitor ist Application Insights. Hierbei handelt es sich um einen erweiterbaren Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM) für Entwickler und DevOps-Profis. Damit können Sie Ihr Entwicklerportal überwachen und Leistungsanomalien erkennen. Application Insights enthält leistungsstarke Analysetools, die Aufschluss über die Aktivitäten der Benutzer Ihres Entwicklerportals geben.

## <a name="add-application-insights-to-your-portal"></a>Hinzufügen von Application Insights zu Ihrem Portal

Gehen Sie wie folgt vor, um Application Insights in Ihr verwaltetes oder selbstgehostetes Entwicklerportal zu integrieren:

> [!IMPORTANT]
> Die ersten beiden Schritte sind für verwaltete Portale nicht erforderlich. Wenn Sie über ein verwaltetes Portal verfügen, fahren Sie mit Schritt 4 fort.

1. Richten Sie eine [lokale Umgebung](developer-portal-self-host.md#step-1-set-up-local-environment) für das aktuelle Release des Entwicklerportals ein.

1. Installieren Sie das **npm-Paket**, um [Paperbits für Azure](https://github.com/paperbits/paperbits-azure) hinzuzufügen:

    ```console
    npm install @paperbits/azure --save
    ```

1. Importieren und registrieren Sie das Application Insights-Modul in der Datei `startup.publish.ts` im Ordner `src`:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. Rufen Sie die Konfiguration des Portals ab:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Erweitern Sie die Websitekonfiguration aus dem vorherigen Schritt mit der Application Insights-Konfiguration:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)
- [Automatisieren von Bereitstellungen im Entwicklerportal](automate-portal-deployments.md)
- [Selbsthosten des API Management-Entwicklerportals](developer-portal-self-host.md)
