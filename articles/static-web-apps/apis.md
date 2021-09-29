---
title: API-Unterstützung in Azure Static Web Apps mit Azure Functions
description: Hier erfahren Sie, welche API-Features von Azure Static Web Apps unterstützt werden.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: cshoe
ms.openlocfilehash: d7716c6d91ac0f7c8c699d14f265e00f5535a00d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730409"
---
# <a name="api-support-in-azure-static-web-apps-with-azure-functions"></a>API-Unterstützung in Azure Static Web Apps mit Azure Functions

Von Azure Static Web Apps werden serverlose API-Endpunkte über [Azure Functions](../azure-functions/functions-overview.md) bereitgestellt. Durch die Verwendung von Azure Functions werden APIs bedarfsgerecht dynamisch skaliert und bieten folgende Features:

- **Integrierte Sicherheit** mit direktem Zugriff auf Benutzerdaten für die [Authentifizierung und rollenbasierte Autorisierung](user-information.md)

- **Nahtloses Routing**, durch das die _API-Route_ ganz ohne benutzerdefinierte CORS-Regeln auf sichere Weise für die Web-App verfügbar gemacht wird

Azure Static Web Apps-APIs werden von zwei möglichen Konfigurationen unterstützt:

- **Verwaltete Funktionen**: Standardmäßig ist die API einer statischen Web-App eine Azure Functions-Anwendung, die von Azure Static Web Apps verwaltet und bereitgestellt wird, was mit ein paar Einschränkungen einhergeht.

- **Bring Your Own Functions (BYOF)** (Nutzung eigener Funktionen): Optional können Sie [eine vorhandene Azure Functions-Anwendung eines beliebigen Plantyps bereitstellen](functions-bring-your-own.md), die über alle Features von Azure Functions verfügt. Mit dieser Konfiguration sind Sie dafür verantwortlich, eine separate Bereitstellung für die Functions-App zu verarbeiten.

Die folgende Tabelle vergleicht die Unterschiede zwischen der Verwendung verwalteter und vorhandener Funktionen.

| Feature | Verwaltete Funktionen | Nutzung eigener Funktionen (BYOF) |
| --- | --- | --- |
| Zugriff auf Azure Functions-[Trigger](../azure-functions/functions-triggers-bindings.md#supported-bindings) | Nur HTTP | Alle |
| Unterstützte Azure Functions-[Runtimes](../azure-functions/supported-languages.md#languages-by-runtime-version) | Node.js 12<br>.NET Core 3.1<br>Python 3.8 | Alle |
| Unterstützte [Hostingpläne](../azure-functions/functions-scale.md) für Azure Functions | Nutzung | Nutzung<br>Premium<br>Dediziert |
| [Integrierte Sicherheit](user-information.md) mit direktem Zugriff auf Benutzerdaten für die Authentifizierung und rollenbasierte Autorisierung | ✔ | ✔ |
| [Routingintegration](./configuration.md?#routes), durch die die _API-Route_ ganz ohne benutzerdefinierte CORS-Regeln auf sichere Weise für die Web-App verfügbar gemacht wird. | ✔ | ✔ |
| [Durable Functions](../azure-functions/durable/durable-functions-overview.md)-Programmiermodell | ✕ | ✔ |
| [Verwaltete Identität](../app-service/overview-managed-identity.md) | ✕ | ✔ |
| Tokenverwaltung mithilfe der [Authentifizierungs- und Autorisierungsfunktionen von Azure App Service](../app-service/configure-authentication-provider-aad.md) | ✕ | ✔ |
| Außerhalb von Azure Static Web Apps verfügbare API-Funktionen | ✕ | ✔ |
| In Azure Key Vault gespeicherte Anwendungseinstellungen | ✕ | ✔ |

## <a name="configuration"></a>Konfiguration

API-Endpunkte sind für die Web-App über die _API-Route_ verfügbar.

| Verwaltete Funktionen | Nutzung eigener Funktionen |
| --- | --- |
| Während die _API_-Route festgelegt ist, haben Sie die Kontrolle über den Ordnerspeicherort der verwalteten Functions-App. Dieser Ort kann durch [Bearbeiten der YAML-Datei des Workflows](build-configuration.md) geändert werden, die sich im Ordner _.github/workflows_ Ihres Repositorys befindet. | Anforderungen an die _API_-Route werden an Ihre vorhandene Azure Functions-App gesendet. |

## <a name="troubleshooting-and-logs"></a>Problembehandlung und Protokolle

Protokolle sind nur verfügbar, wenn Sie [Application Insights](monitor.md) hinzufügen.

| Verwaltete Funktionen | Nutzung eigener Funktionen |
| --- | --- |
| Aktivieren Sie die Protokollierung, indem Sie Application Insights für Ihre statische Web-App aktivieren. | Aktivieren Sie die Protokollierung, indem Sie Application Insights für Ihre Azure Functions-App aktivieren. |

## <a name="constraints"></a>Einschränkungen

- Das API-Routenpräfix muss _api_ sein.
- Routenregeln für API-Funktionen unterstützen nur [Umleitungen](configuration.md#defining-routes) und das [Sichern von Routen mit Rollen](configuration.md#securing-routes-with-roles).

| Verwaltete Funktionen | Nutzung eigener Funktionen |
| --- | --- |
| <ul><li>Trigger sind auf [HTTP](../azure-functions/functions-bindings-http-webhook.md) beschränkt.</li><li>Die Azure Functions-App muss entweder in Node.js 12, .NET Core 3.1 oder Python 3.8 geschrieben sein.</li><li>Einige Anwendungseinstellungen werden vom Dienst verwaltet, weshalb die folgenden Präfixe von der Runtime reserviert sind:<ul><li>*APPSETTING\_, AZUREBLOBSTORAGE\_, AZUREFILESSTORAGE\_, AZURE_FUNCTION\_, CONTAINER\_, DIAGNOSTICS\_, DOCKER\_, FUNCTIONS\_, IDENTITY\_, MACHINEKEY\_, MAINSITE\_, MSDEPLOY\_, SCMSITE\_, SCM\_, WEBSITES\_, WEBSITE\_, WEBSOCKET\_, AzureWeb*</li></ul></li></ul> | <ul><li>Sie sind für die Verwaltung der Bereitstellung der Functions-App verantwortlich.</li></ul> |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer API zu Azure Static Web Apps (Vorschauversion) mit Azure Functions](add-api.md)
