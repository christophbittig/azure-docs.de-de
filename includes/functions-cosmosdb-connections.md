---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 11/12/2021
ms.author: mahender
ms.openlocfilehash: 4a78d64e2a66f5d3025407b6d7d7907d085b1fe6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529623"
---
## <a name="connections"></a>Verbindungen

Die Eigenschaften `connectionStringSetting`/`connection` und `leaseConnectionStringSetting`/`leaseConnection` sind Verweise auf eine Umgebungskonfiguration, die angibt, wie sich die App mit Azure Cosmos DB verbinden soll. Damit kann Folgendes festgelegt werden:

- Der Name einer Anwendungseinstellung, die eine [Verbindungszeichenfolge](#connection-string) enthält
- Der Name eines gemeinsamen Präfixes für mehrere Anwendungseinstellungen, die zusammen eine [identitätsbasierte Verbindung](#identity-based-connections) definieren Diese Option ist nur für die `connection`- und `leaseConnection`-Versionen ab [Version 4.x oder eine höhere Version der Erweiterung] verfügbar.

Wenn der konfigurierte Wert sowohl eine genaue Übereinstimmung für eine einzelne Einstellung als auch eine Präfixübereinstimmung für andere Einstellungen ist, wird die genaue Übereinstimmung verwendet.

### <a name="connection-string"></a>Verbindungszeichenfolge

Die Verbindungszeichenfolge für Ihr Datenbankkonto sollte in einer Anwendungseinstellung mit einem Namen gespeichert werden, der dem in der Verbindungseigenschaft der Bindungskonfiguration angegebenen Wert entspricht.

### <a name="identity-based-connections"></a>Identitätsbasierte Verbindungen

Wenn Sie [Version 4.x oder eine höhere Version der Erweiterung] verwenden, können Sie die App anstelle einer Verbindungszeichenfolge mit einem Geheimnis eine [Azure Active Directory-Identität](../articles/active-directory/fundamentals/active-directory-whatis.md) verwenden lassen. Dazu definieren Sie Einstellungen unter einem gemeinsamen Präfix, das der Verbindungseigenschaft in der Trigger- und Bindungskonfiguration zugeordnet ist.

In diesem Modus benötigt die Erweiterung die folgenden Eigenschaften:

| Eigenschaft                  | Vorlage für Umgebungsvariable                       | BESCHREIBUNG                                | Beispielwert                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| Kontoendpunkt | `<CONNECTION_NAME_PREFIX>__accountEndpoint` | Der URI des Azure Cosmos DB-Kontoendpunkts. | https://<database_account_name>.documents.azure.com:443/ |

Es können zusätzliche Eigenschaften festgelegt werden, um die Verbindung anzupassen. Weitere Informationen hierzu finden Sie unter [Allgemeine Eigenschaften für identitätsbasierte Verbindungen](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-cosmos-permissions](./functions-cosmos-permissions.md)]

[Version 4.x oder eine höhere Version der Erweiterung]: ../articles/azure-functions/functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher
