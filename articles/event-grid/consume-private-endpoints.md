---
title: Übermitteln von Ereignissen mithilfe einer privaten Verbindung
description: In diesem Artikel wird beschrieben, wie Sie die Einschränkung umgehen, dass Ereignisse nicht mithilfe einer privaten Verbindung übertragen werden können.
ms.topic: how-to
ms.date: 07/01/2021
ms.openlocfilehash: 0672b2b93cf7413ac9a3d46e8d824354276ba89f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286120"
---
# <a name="deliver-events-using-private-link-service"></a>Übermitteln von Ereignissen mithilfe einer privaten Verbindung
Derzeit ist es nicht möglich, Ereignisse über [private Endpunkte](../private-link/private-endpoint-overview.md) zu übermitteln. Das heißt, es gibt keine Unterstützung, wenn Sie strikte Anforderungen an die Netzwerkisolation haben, bei denen der Datenverkehr der übermittelten Ereignisse den privaten IP-Adressraum nicht verlassen darf. 

## <a name="use-managed-identity"></a>Verwenden der verwalteten Identität
Wenn Ihre Anforderungen jedoch eine sichere Methode zum Senden von Ereignissen über einen verschlüsselten Kanal und eine bekannte Identität des Absenders (in diesem Fall Event Grid) unter Verwendung eines öffentlichen IP-Adressraums erfordern, könnten Sie Ereignisse an Event Hubs, Service Bus oder Azure Storage Service unter Verwendung eines benutzerdefinierten Azure Event Grid-Themas oder einer -Domäne mit vom System verwalteter Identität bereitstellen. Einzelheiten zum Übermitteln von Ereignissen mit einer verwalteten Identität finden Sie unter [Ereignisübermittlung mit einer verwalteten Identität](managed-service-identity.md). 

Anschließend können Sie eine private Verbindung verwenden, die in Azure Functions konfiguriert ist, oder Ihren Webhook, der in Ihrem virtuellen Netzwerk zum Pullen von Ereignissen bereitgestellt wurde. Weitere Informationen finden Sie im Beispiel: [Herstellen einer Verbindung mit privaten Endpunkten mit Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Übermittlung über eine private Verbindung":::


Bei dieser Konfiguration wird der Datenverkehr über die öffentliche IP-Adresse bzw. das Internet aus Event Grid an Event Hubs, Service Bus oder Azure Storage übermittelt. Der Kanal kann jedoch verschlüsselt werden, und eine verwaltete Identität von Event Grid wird verwendet. Wenn Sie Ihre Azure Functions-Funktion oder Ihren Webhook, der in Ihrem virtuellen Netzwerk bereitgestellt wurde, für die Verwendung von Event Hubs, Service Bus oder Azure Storage über eine private Verbindung konfigurieren, verbleibt dieser Teil des Datenverkehrs offensichtlich in Azure.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Übermitteln von Ereignissen an Event Hubs unter Verwendung einer verwalteten Identität
Führen Sie die folgenden Schritte aus, um mithilfe der verwalteten Identität Ereignisse an Event Hubs in Ihrem Event Hubs-Namespace zu übermitteln:

1. Aktivieren der vom System zugewiesenen Identität: [Systemthemen](enable-identity-system-topics.md), [benutzerdefinierte Themen und Domänen](enable-identity-custom-topics-domains.md).  
1. [Fügen Sie die Identität der Rolle **Azure Event Hubs-Datensender** im Event Hubs-Namespace hinzu](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Aktivieren Sie in Ihrem Event Hubs-Namespace die Option **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?**](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services) 
1. [Konfigurieren Sie dann das Ereignisabonnement](managed-service-identity.md#create-event-subscriptions-that-use-an-identity), das einen Event Hub als Endpunkt verwendet, um die vom System zugewiesene Identität zu verwenden.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Übermitteln von Ereignissen an Service Bus unter Verwendung einer verwalteten Identität
Führen Sie die folgenden Schritte aus, um Ereignisse an Service Bus-Warteschlangen oder -Themen in Ihrem Service Bus-Namespace mithilfe der verwalteten Identität zu übermitteln:

1. Aktivieren der vom System zugewiesenen Identität: [Systemthemen](enable-identity-system-topics.md), [benutzerdefinierte Themen und Domänen](enable-identity-custom-topics-domains.md). 
1. [Hinzufügen der Identität der Rolle **Azure Service Bus-Datensender**](../service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) für den Service Bus-Namespace
1. [Aktivieren Sie in Ihrem Service Bus-Namespace die Option **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?**](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services) 
1. [Konfigurieren Sie das Ereignisabonnement](managed-service-identity.md), das eine Service Bus-Warteschlange oder ein -Thema als Endpunkt verwendet, um die vom System zugewiesene Identität zu verwenden.

## <a name="deliver-events-to-storage"></a>Übermitteln von Ereignissen an Storage 
Gehen Sie folgendermaßen vor, um Ereignisse mithilfe der verwalteten Identität an Storage-Warteschlangen zu übermitteln:

1. Aktivieren der vom System zugewiesenen Identität: [Systemthemen](enable-identity-system-topics.md), [benutzerdefinierte Themen und Domänen](enable-identity-custom-topics-domains.md). 
1. [Fügen Sie die Identität der Rolle **Absender der Speicherwarteschlangen-Datennachricht**](../storage/blobs/assign-azure-role-data-access.md) in der Azure Storage-Warteschlange hinzu.
1. [Konfigurieren Sie dann das Ereignisabonnement](managed-service-identity.md#create-event-subscriptions-that-use-an-identity), das eine Storage-Warteschlange als Endpunkt verwendet, um die vom System zugewiesene Identität zu verwenden.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Übermitteln von Ereignissen mit einer verwalteten Identität finden Sie unter [Ereignisübermittlung mit einer verwalteten Identität](managed-service-identity.md).