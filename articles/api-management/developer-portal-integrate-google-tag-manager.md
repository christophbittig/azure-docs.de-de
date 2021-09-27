---
title: Integrieren von Google Tag Manager in das Entwicklerportal
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Sie Google Tag Manager in Ihr verwaltetes oder selbstgehostetes Entwicklerportal in Azure API Management integrieren.
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: a3436727470ceb073f5431ef9677fbc17afdda84
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601670"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Integrieren von Google Tag Manager in das API Management-Entwicklerportal

[Google Tag Manager](https://developers.google.com/tag-manager) ist ein von Google erstelltes Tagverwaltungssystem. Es ermöglicht die Verwaltung von JavaScript- und HTML-Tags, die zur Nachverfolgung und Analyse auf Websites eingesetzt werden. Mithilfe von Google Tag Manager können Sie beispielsweise Google Analytics, Wärmebilder oder Chatbots wie LiveChat integrieren.

Führen Sie die in diesem Artikel beschriebenen Schritte aus, um Google Tag Manager in Ihr verwaltetes oder selbstgehostetes Entwicklerportal in Azure API Management zu integrieren.

## <a name="add-google-tag-manager-to-your-portal"></a>Hinzufügen von Google Tag Manager zu Ihrem Portal

Gehen Sie wie folgt vor, um Google Tag Manager in Ihr verwaltetes oder selbstgehostetes Entwicklerportal zu integrieren:

> [!IMPORTANT]
> Die ersten beiden Schritte sind für verwaltete Portale nicht erforderlich. Wenn Sie über ein verwaltetes Portal verfügen, fahren Sie mit Schritt 4 fort.

1. Richten Sie eine [lokale Umgebung](developer-portal-self-host.md#step-1-set-up-local-environment) für das aktuelle Release des Entwicklerportals ein.

1. Installieren Sie das **npm-Paket**, um [Paperbits für Google Tag Manager](https://github.com/paperbits/paperbits-gtm) hinzuzufügen:

    ```sh
    npm install @paperbits/gtm --save
    ```

1. Importieren und registrieren Sie das GTM-Modul in der Datei `startup.publish.ts` im Ordner `src`:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Erweitern Sie die Websitekonfiguration aus dem vorherigen Schritt mit der Google Tag Manager-Konfiguration:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Automatisieren von Bereitstellungen im Entwicklerportal](automate-portal-deployments.md)
- [Selbsthosten des API Management-Entwicklerportals](developer-portal-self-host.md)