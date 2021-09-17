---
title: Programmgesteuertes Verwalten von Azure Service Bus-Namespaces und -Entitäten
description: In diesem Artikel wird erläutert, wie Service Bus-Namespaces und -Entitäten dynamisch oder programmgesteuert bereitgestellt werden.
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: b053ff5cce51fbcd0ce56e2bdbfaff39dddb6394
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122356268"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Dynamisches Bereitstellen von Service Bus-Namespaces und -Entitäten 
Azure Service Bus bietet Bibliotheken zur dynamischen Bereitstellung von Service Bus-Namespaces und -Entitäten. Dies ermöglicht komplexe Bereitstellungen und Messagingszenarios sowie eine programmgesteuerte Bestimmung der bereitzustellenden Entitäten.

## <a name="overview"></a>Übersicht
Es gibt zwei Ansätze, um Azure Service Bus-Ressourcen programmgesteuert zu verwalten. Der erste Ansatz besteht darin, die auf [Azure Resource Manager](../azure-resource-manager/management/overview.md) basierenden Bibliotheken zu verwenden, mit denen Sie Namespaces, Warteschlangen, Themen, Abonnements, Regeln und SAS-Richtlinien verwalten können. Azure Resource Manager-basierte Bibliotheken unterstützen die Authentifizierung über Azure Active Directory, aber nicht über Verbindungszeichenfolgen. Der zweite Ansatz besteht darin, dieselben Clientbibliotheken des Diensts zu nutzen, die Sie zum Senden und Empfangen von Nachrichten verwenden. Die Clientbibliotheken stellen auch APIs zur Verfügung, mit deren Hilfe Sie Warteschlangen, Themen, Abonnements und Regeln in einem *vorhandenen* Namespace verwalten können. Sie bieten Unterstützung für die Authentifizierung mit Verbindungszeichenfolgen. Bei der Entscheidung, welchen Ansatz Sie wählen, sollten Sie Folgendes berücksichtigen. 

Die auf Azure Resource Manager basierenden Bibliotheken bieten dieselbe Funktionalität wie das Azure-Portal, die Azure CLI und PowerShell, wenn es um die Verwaltung von Service Bus-Namespaces und -Entitäten wie Warteschlangen, Themen, Abonnements usw. geht. Wenn Sie das Azure-Portal, die Azure CLI oder PowerShell für Ihre Verwaltungsvorgänge verwendet haben und eine dynamische Möglichkeit dafür suchen, dann sind diese Bibliotheken vielleicht besser für Sie geeignet. 

Wenn Sie jedoch bereits eine Clientbibliothek für dienstspezifische Vorgänge wie das Senden und Empfangen von Nachrichten verwenden und auch Service Bus-Entitäten verwalten müssen, dann ist es möglicherweise bequemer, dieselbe Bibliothek zu verwenden. Die Clientbibliotheken verfügen über ein `ServiceBusAdministrationClient` (in älteren Bibliotheken als `ServiceBusManagementClient` bezeichnet), das eine Teilmenge der von den Azure Resource Manager-basierten Bibliotheken bereitgestellten Verwaltungsfunktionen bietet. Es muss betont werden, dass die auf Azure Resource Manager basierenden Bibliotheken es Ihnen ermöglichen, sowohl Service Bus-Namespaces als auch -Entitäten zu verwalten, während die Clientbibliotheken nur die Verwaltung von Entitäten in einem bestehenden Namespace ermöglichen, *nicht* aber die Verwaltung des Namespace selbst.

## <a name="manage-using-azure-resource-manager-based-libraries"></a>Verwalten mit Azure Resource Manager-basierten Bibliotheken

Die auf Azure Resource Manager basierenden Bibliotheken ermöglichen es Ihnen, Namespaces, Warteschlangen, Themen, Abonnements, Regeln und SAS-Richtlinien zu verwalten.  Sie unterstützen *nur* die Authentifizierung mit Azure Active Directory (Azure AD), aber keine Verbindungszeichenfolgen. 

| Sprache | Paket | Dokumentation | Beispiele|
|-|-|-|-|
|.NET | [Microsoft.Azure.Management.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus/) |[API-Referenz für Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus)|[.NET](https://github.com/Azure-Samples/service-bus-dotnet-management/tree/a55185bef30d1763c1a8182a3361dbb548bad436) |
| Java | [azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[API-Referenz für com.azure.resourcemanager.servicebus](/java/api/com.azure.resourcemanager.servicebus)|[Java](https://github.com/Azure-Samples/service-bus-java-manage-publish-subscribe-with-basic-features/tree/e4718a825e8fcfe58e5921770ff8084da67ccd89)|
| JavaScript |[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)|[API-Referenz für @azure/arm-servicebus](/javascript/api/@azure/arm-servicebus/)||
|Python|[azure-mgmt-servicebus](https://pypi.org/project/azure-mgmt-servicebus/)|[API-Referenz für azure-mgmt-servicebus](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus)||


### <a name="fluent-net-and-java-libraries"></a>Fluent .NET- und Java-Bibliotheken
Es gibt eine Fluent-Version der Azure Resource Manager-basierten Bibliotheken. 

|Sprache|Paket|Dokumentation|
|-|-|-|
|.NET|[Microsoft.Azure.Management.ServiceBus.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus.Fluent/) |[API-Referenz für Microsoft.Azure.Management.ServiceBus.Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent) |
| Java|[azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[API-Referenz für com.azure.resourcemanager.servicebus.fluent](/java/api/com.azure.resourcemanager.servicebus.fluent) |

## <a name="manage-using-service-bus-client-libraries"></a>Verwalten mit Service Bus-Clientbibliotheken 

Service Bus-Clientbibliotheken, die für Vorgänge wie das Senden und Empfangen von Nachrichten verwendet werden, können auch zur Verwaltung von Warteschlangen, Themen, Abonnements und Regeln in einem *vorhandenen* Service Bus-Namespace verwendet werden. Dieses Feature ist über `ServiceBusAdministrationClient` in den neuesten Bibliotheken und über `ServiceBusManagementClient` in den älteren Bibliotheken verfügbar. Es wird dringend empfohlen, dass Sie die neuesten Bibliotheken verwenden.

### <a name="latest-service-bus-libraries"></a>Neueste Service Bus-Bibliotheken
|Sprache|Paket|Dokumentation|Beispiele|
|-|-|-|-|
|.NET|  [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus)|[ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)|[.NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)|
|Java|[azure-messaging-servicebus](https://search.maven.org/artifact/com.azure/azure-messaging-servicebus)|[ServiceBusAdministrationAsyncClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationasyncclient), [ServiceBusAdministrationClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient)| [Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)|
|JavaScript|[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)|[ServiceBusAdministrationClient](/javascript/api/@azure/service-bus/servicebusadministrationclient)|[JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)/[TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)|
|Python|[azure-servicebus](https://pypi.org/project/azure-servicebus/)|[ServiceBusAdministrationClient](/python/api/azure-servicebus/azure.servicebus.management.servicebusadministrationclient)|[Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)|

### <a name="legacy-service-bus-libraries"></a>Legacy-Service Bus-Bibliotheken
|Sprache|Paket|Dokumentation|Beispiele|
|-|-|-|-|
|.NET|[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)|[ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient)|[.NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus)|
|Java|[azure-mgmt-servicebus](https://search.maven.org/artifact/com.microsoft.azure/azure-mgmt-servicebus)|[ManagementClientAsync](/java/api/com.microsoft.azure.servicebus.management.managementclientasync), [ManagementClient](/java/api/com.microsoft.azure.servicebus.management.managementclient)|[Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)|


## <a name="next-steps"></a>Nächste Schritte
- Senden von Nachrichten an die Warteschlange und Empfangen von Nachrichten aus der Warteschlange mithilfe der neuesten Service Bus-Bibliothek: [.NET](./service-bus-dotnet-get-started-with-queues.md#send-messages), [Java](./service-bus-java-how-to-use-queues.md), [JavaScript](./service-bus-nodejs-how-to-use-queues.md), [Python](./service-bus-python-how-to-use-queues.md)
- Senden von Nachrichten an ein Thema und empfangen von Nachrichten von einem Abonnement mithilfe der neuesten Service Bus-Bibliothek: [.NET](./service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](./service-bus-java-how-to-use-topics-subscriptions.md), [JavaScript](./service-bus-nodejs-how-to-use-topics-subscriptions.md), [Python](./service-bus-python-how-to-use-topics-subscriptions.md)