---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 3d1ebc666a1d4c6763b080decdd50fd44b3837dc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993308"
---
## <a name="connections"></a>Verbindungen

Die `connection`-Eigenschaft ist ein Verweis auf eine Umgebungskonfiguration, die angibt, wie sich die App mit Event Hubs verbinden soll. Folgendes kann angegeben werden:

- Der Name einer Anwendungseinstellung, die eine [Verbindungszeichenfolge](#connection-string) enthält
- Der Name eines gemeinsamen Präfixes für mehrere Anwendungseinstellungen, die zusammen eine [identitätsbasierte Verbindung](#identity-based-connections) definieren.

Wenn der konfigurierte Wert sowohl eine genaue Übereinstimmung für eine einzelne Einstellung als auch eine Präfixübereinstimmung für andere Einstellungen ist, wird die genaue Übereinstimmung verwendet.

### <a name="connection-string"></a>Verbindungszeichenfolge

Sie erhalten diese Verbindungszeichenfolge, indem Sie für den [Namespace](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) (nicht für den eigentlichen Event Hub) auf die Schaltfläche **Verbindungsinformationen** klicken. Die Verbindungszeichenfolge muss für einen Event Hubs-Namespace und nicht für den Event Hub selbst sein.

Bei Verwendung für Trigger muss die Verbindungszeichenfolge mindestens über Leseberechtigungen verfügen, um die Funktion zu aktivieren. Bei Verwendung für Ausgabebindungen muss die Verbindungszeichenfolge über Sendeberechtigungen verfügen, um Nachrichten an den Ereignisdatenstrom zu senden.

Diese Verbindungszeichenfolge sollte in einer Anwendungseinstellung mit einem Namen gespeichert werden, der dem in der Eigenschaft `connection` der Bindungskonfiguration angegebenen Wert entspricht.

### <a name="identity-based-connections"></a>Identitätsbasierte Verbindungen

Wenn Sie [Version 5.x oder höher der Erweiterung](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) verwenden, können Sie anstelle einer Verbindungszeichenfolge mit einem Geheimnis die Anwendung eine [Azure Active Directory-Identität](../articles/active-directory/fundamentals/active-directory-whatis.md) verwenden lassen. Dazu definieren Sie Einstellungen unter einem gemeinsamen Präfix, das der Eigenschaft `connection` in der Trigger- und Bindungskonfiguration zugeordnet ist.

In diesem Modus benötigt die Erweiterung die folgenden Eigenschaften:

| Eigenschaft                  | Vorlage für Umgebungsvariable                       | BESCHREIBUNG                                | Beispielwert                                        |
|---------------------------|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| Vollqualifizierter Namespace | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Der vollqualifizierte Event Hubs-Namespace. | <event_hubs_namespace>.servicebus.windows.net |

Es können zusätzliche Eigenschaften festgelegt werden, um die Verbindung anzupassen. Siehe [Gemeinsame Eigenschaften für identitätsbasierte Verbindungen](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-event-hubs-permissions](./functions-event-hubs-permissions.md)]
