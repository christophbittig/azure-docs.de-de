---
title: Behandeln von Event Grid-Problemen
description: Dieser Artikel bietet verschiedene Methoden zur Behandlung von Azure Event Grid-Problemen.
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 94370af8a3325d1798c3e2bcb65c2ccb3e54a43b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546346"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Behandlung von Azure Event Grid-Problemen
Dieser Artikel enthält Informationen, die Ihnen bei der Behandlung von Azure Event Grid-Problemen helfen. 

## <a name="diagnostic-logs"></a>Diagnoseprotokolle
Aktivieren Sie Diagnoseeinstellungen für Event Grid-Themen oder -Domänen, um Veröffentlichungs- und Übermittlungsfehlerprotokolle zu erfassen und anzuzeigen. Weitere Informationen finden Sie unter [Diagnoseprotokolle](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Metriken
Sie können Metriken für Event Grid-Themen und -Abonnements anzeigen und Warnungen dazu erstellen. Weitere Informationen finden Sie unter [Event Grid-Metriken](monitor-event-delivery.md).

## <a name="alerts"></a>Alerts
Erstellen Sie Warnungen zu Azure Event Grid-Metriken und -Aktivitätsprotokollvorgängen. Weitere Informationen finden Sie unter [Warnungen zu Event Grid-Metriken und Aktivitätsprotokollen](set-alerts.md).

## <a name="subscription-validation-issues"></a>Abonnementüberprüfungsprobleme
Während der Erstellung eines Ereignisabonnements erhalten Sie möglicherweise eine Fehlermeldung, die besagt, dass die Überprüfung des angegebenen Endpunkts fehlgeschlagen ist. Informationen zur Behandlung von Abonnementüberprüfungsproblemen finden Sie unter [Problembehandlung von Event Grid-Abonnementüberprüfungen](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Probleme mit der Netzwerkkonnektivität
Es gibt verschiedene Gründe dafür, dass Clientanwendungen keine Verbindung mit einem Event Grid-Thema bzw. einer -Domäne herstellen können. Die auftretenden Konnektivitätsprobleme können dauerhaft oder vorübergehend sein. Informationen zum Beheben dieser Probleme finden Sie unter [Beheben von Konnektivitätsproblemen](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Fehlercodes
Wenn Sie Fehlermeldungen mit Fehlercodes wie 400, 409 und 403 erhalten, finden Sie weitere Informationen unter [Beheben von Event Grid-Fehlern](troubleshoot-errors.md). 

## <a name="distributed-tracing"></a>Verteilte Ablaufverfolgung
Die Event Grid-Bibliotheken in .NET, Java, Python und JavaScript unterstützen die Verteilung der Ablaufverfolgung. Um dem [Leitfaden der CloudEvents-Spezifikation](https://github.com/cloudevents/spec/blob/v1.0.1/extensions/distributed-tracing.md) für verteilte Ablaufverfolgung zu genügen, legt die Bibliothek `traceparent` und `tracestate` Attribute einer `CloudEvent`Erweiterung fest, wenn verteilte Ablaufverfolgung aktiviert ist.

Weitere Informationen zum Aktivieren von verteilter Ablaufverfolgung in Ihrer Anwendung finden Sie im Azure SDK in der Dokumentation zur verteilten Ablaufverfolgung.

- [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)
- [Java](/azure/developer/java/sdk/tracing)
- [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/core/azure-core-tracing-opentelemetry)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/core/README.md#tracing)

Um die End-to-End-Ablaufverfolgung für ein [Azure Event Hubs-](handler-event-hubs.md) oder [Azure Service Bus](handler-service-bus.md) Event Grid-Abonnement zu aktivieren, konfigurieren Sie [benutzerdefinierte Übermittlungseigenschaften](delivery-properties.md), um das `traceparent`CloudEvent-Erweiterungsattribut an die `Diagnostic-Id`AMQP-Anwendungseigenschaft weiterzuleiten. 

Hier sehen Sie ein Beispiel für ein Abonnement, bei dem die Eigenschaften für die Ablaufverfolgungszustellung für Event Hubs konfiguriert sind:

```azurecli
az eventgrid event-subscription create --name <event-grid-subscription-name> \
    --source-resource-id <event-grid-resource-id>
    --endpoint-type eventhub \
    --endpoint <event-hubs-endpoint> \
    --delivery-attribute-mapping Diagnostic-Id dynamic traceparent
```

### <a name="sample"></a>Beispiel
Weitere Informationen finden Sie im [Zeilenzählungsbeispiel](/samples/azure/azure-sdk-for-net/line-counter/). Diese Beispiel-App veranschaulicht die Verwendung von Storage, Event Hubs und Event Grid-Clients und zeigt auch ASP.NET Core-Integration, verteilte Ablaufverfolgung und gehostete Dienste. Sie ermöglicht es Benutzern, eine Datei in ein Blob hochzuladen, wodurch ein Event Hubs-Ereignis ausgelöst wird, das den Dateinamen enthält. Der Event Hubs-Prozessor empfängt das Ereignis, die App lädt dann das Blob herunter und zählt die Anzahl der Zeilen in der Datei. Die App zeigt einen Link zu einer Seite an, die die Zeilenanzahl enthält. Wenn auf den Link geklickt wird, wird ein CloudEvent mit dem Namen der Datei mit Event Grid veröffentlicht.

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie weitere Hilfe benötigen, veröffentlichen Sie Ihr Problem im [Stack Overflow-Forum](https://stackoverflow.com/questions/tagged/azure-eventgrid), oder öffnen Sie ein [Supportticket](https://azure.microsoft.com/support/options/). 
