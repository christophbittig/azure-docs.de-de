---
title: Integrieren Sie den Azure SignalR-Dienst in den Service Connector
description: Integrieren Sie den Azure SignalR Dienst mit Service Connector in Ihre Anwendung
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 688321101923577fb5ef44ac8d7f23a63bf4b08c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021387"
---
# <a name="integrate-azure-signalr-service-with-service-connector"></a>Integrieren Sie den Azure SignalR-Dienst in den Service Connector

Auf dieser Seite werden die unterstützten Authentifizierungstypen und Clienttypen von Azure SignalR Service mithilfe von Service Connector angezeigt. Möglicherweise können Sie in anderen Programmiersprachen weiterhin eine Verbindung mit den Azure SignalR Dienst herstellen, ohne Service Connector zu verwenden. Diese Seite zeigt auch den Standardumgebungsvariablennamen und -wert (oder die Spring Boot-Konfiguration) an, die Sie beim Erstellen der Dienstverbindung erhalten. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .Net | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>Standard-Umgebungsvariablennamen oder Anwendungseigenschaften

### <a name="net"></a>.NET

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | SignalR-Dienstverbindungszeichenfolge | `Endpoint=https://{signalrName}.service.signalr.net;AccessKey={};Version=1.0;` |

**Systemseitig zugewiesene verwaltete Identität**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | SignalR-Dienstverbindungszeichenfolge mit verwalteter Identität | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};Version=1.0;` |

**Benutzerseitig zugewiesene verwaltete Identität**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | SignalR-Dienstverbindungszeichenfolge mit verwalteter Identität | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};Version=1.0;` |

**Dienstprinzipal**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | SignalR-Dienstverbindungszeichenfolge mit Dienstprinzipal | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};ClientSecret={};TenantId={};Version=1.0;` |

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
