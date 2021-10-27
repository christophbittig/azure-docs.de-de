---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 288539542c5b811206bd3e128dbceca317b2a27a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137868"
---
## <a name="connections"></a>Verbindungen

Die `connection`-Eigenschaft ist ein Verweis auf eine Umgebungskonfiguration, die angibt, wie sich die App mit Service Bus verbinden soll. Folgendes kann angegeben werden:

- Der Name einer Anwendungseinstellung, die eine [Verbindungszeichenfolge](#connection-string) enthält
- Der Name eines gemeinsamen Präfixes für mehrere Anwendungseinstellungen, die zusammen eine [identitätsbasierte Verbindung](#identity-based-connections) definieren.

Wenn der konfigurierte Wert sowohl mit einer einzelnen Einstellung komplett als auch mit dem Präfix einer anderen Einstellungen übereinstimmt, wird die komplette Übereinstimmung verwendet.

### <a name="connection-string"></a>Verbindungszeichenfolge

Um die Verbindungszeichenfolge zu erhalten, führen Sie die Schritte unter [Abrufen der Verwaltungsanmeldeinformationen](../articles/service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string) aus. Die Verbindungszeichenfolge muss für einen Service Bus-Namespace gelten und darf nicht auf eine bestimmte Warteschlange oder ein Thema beschränkt sein.

Diese Verbindungszeichenfolge sollte in einer Anwendungseinstellung mit einem Namen gespeichert werden, der dem in der Eigenschaft `connection` der Bindungskonfiguration angegebenen Wert entspricht.

Falls der Name der App-Einstellung mit „AzureWebJobs“ beginnt, können Sie nur den Rest des Namens angeben. Wenn Sie `connection` also beispielsweise auf „MyServiceBus“ festlegen, sucht die Functions-Laufzeit nach einer App-Einstellung namens „AzureWebJobsMyServiceBus“. Ohne Angabe für `connection` verwendet die Functions-Laufzeit die standardmäßige Service Bus-Verbindungszeichenfolge aus der App-Einstellung „AzureWebJobsServiceBus“.

### <a name="identity-based-connections"></a>Identitätsbasierte Verbindungen

Wenn Sie [Version 5.x oder höher der Erweiterung](../articles/azure-functions/functions-bindings-service-bus.md#service-bus-extension-5x-and-higher) verwenden, können Sie anstelle einer Verbindungszeichenfolge mit einem Geheimnis die Anwendung eine [Azure Active Directory-Identität](../articles/active-directory/fundamentals/active-directory-whatis.md) verwenden lassen. Dazu definieren Sie Einstellungen unter einem gemeinsamen Präfix, das der Eigenschaft `connection` in der Trigger- und Bindungskonfiguration zugeordnet ist.

In diesem Modus benötigt die Erweiterung die folgenden Eigenschaften:

| Eigenschaft                  | Vorlage für Umgebungsvariable                       | BESCHREIBUNG                                | Beispielwert |
|---------------------------|-----------------------------------------------------|--------------------------------------------|---------|
| Vollqualifizierter Namespace | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Der vollqualifizierte Service Bus-Namespace. | <service_bus_namespace>.servicebus.windows.net  |

Es können zusätzliche Eigenschaften festgelegt werden, um die Verbindung anzupassen. Siehe [Gemeinsame Eigenschaften für identitätsbasierte Verbindungen](../articles/azure-functions/functions-reference.md#common-properties-for-identity-based-connections).

[!INCLUDE [functions-identity-based-connections-configuration](./functions-identity-based-connections-configuration.md)]

[!INCLUDE [functions-service-bus-permissions](./functions-service-bus-permissions.md)]