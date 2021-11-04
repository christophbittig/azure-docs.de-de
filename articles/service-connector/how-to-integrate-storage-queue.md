---
title: Integrieren von Azure Queue Storage in Service Connector
description: Integrieren von Azure Queue Storage in Ihre Anwendung mit Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: eec504cadbe4c039219ca58bd23b283c40d3785e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021526"
---
# <a name="integrate-azure-queue-storage-with-service-connector"></a>Integrieren von Azure Queue Storage in Service Connector

Auf dieser Seite werden die unterstützten Authentifizierungstypen und Clienttypen von Azure Queue Storage mithilfe des Service-Connectors angezeigt. Möglicherweise können Sie weiterhin eine Verbindung mit Azure Queue Storage in anderen Programmiersprachen herstellen, ohne den Service-Connector zu verwenden. Auf dieser Seite werden auch der Name und Wert der Standardumgebungsvariablen (oder Spring Boot Konfiguration) angezeigt, die Sie beim Erstellen der Serviceverbindung erhalten. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .Net | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Java | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Java - Spring Boot | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Node.js | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Python | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>Namen von Standard-Umgebungsvariablen oder Anwendungseigenschaften

### <a name="net-java-nodejs-python"></a>.NET, Java, Node.JS, Python

**Geheimnis/ConnectionString**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_CONNECTIONSTRING | Queue Storage-Verbindungszeichenfolge | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |

**Systemseitig zugewiesene verwaltete Identität**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Queue Storage-Endpunkt | `https://{StorageAccountName}.queue.core.windows.net/` |

**Benutzerseitig zugewiesene verwaltete Identität**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Queue Storage-Endpunkt | `https://{storageAccountName}.queue.core.windows.net/` |
| AZURE_STORAGEQUEUE_CLIENTID | Ihre Client-ID | `{yourClientID}` |

**Dienstprinzipal**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | Queue Storage-Endpunkt | `https://{storageAccountName}.queue.core.windows.net/` |
| AZURE_STORAGEQUEUE_CLIENTID | Ihre Client-ID | `{yourClientID}` |
| AZURE_STORAGEQUEUE_CLIENTSECRET | Ihr geheimer Clientschlüssel | `{yourClientSecret}` |
| AZURE_STORAGEQUEUE_TENANTID | Ihre Mandanten-ID | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java - Spring Boot

**Geheimkennwort/Verbindungszeichenfolge**

| Anwendungseigenschaften | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| azure.storage.account-name | Queue storage Kontoname | `{storageAccountName}` |
| azure.storage.account-key | Queue storage Kontoschlüssel | `{yourSecret}` |

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
