---
title: Authentifizieren der Ereignisübermittlung an Ereignishandler (Azure Event Grid)
description: In diesem Artikel werden verschiedene Methoden zum Authentifizieren der Übermittlung an Ereignishandler in Azure Event Grid beschrieben.
ms.topic: conceptual
ms.date: 06/28/2021
ms.openlocfilehash: 01383809e6aab895ff4ed42763c57004a6ee02a8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003231"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Authentifizieren der Ereignisübermittlung an Ereignishandler (Azure Event Grid)
Dieser Artikel enthält Informationen zum Authentifizieren der Ereignisübermittlung an Ereignishandler. 

## <a name="overview"></a>Übersicht
Azure Event Grid verwendet verschiedene Authentifizierungsmethoden, um Ereignisse an Ereignishandler zu übermitteln. `

| Authentifizierungsmethode | Unterstützte Handler | BESCHREIBUNG  |
|--|--|--|
Zugriffsschüssel | <p>Event Hubs</p><p>Service Bus</p><p>Storage-Warteschlangen</p><p>Relay-Hybridverbindungen</p><p>Azure Functions</p><p>Storage-Blobs (Warteschlange für unzustellbare Nachrichten)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p> | Zugriffsschlüssel werden mit den Anmeldeinformationen des Event Grid-Dienstprinzipals abgerufen. Die Berechtigungen werden Event Grid erteilt, wenn Sie den Event Grid-Ressourcenanbieter im jeweiligen Azure-Abonnement registrieren. |  
Verwaltete Systemidentität <br/>&<br/> Rollenbasierte Zugriffssteuerung | <p>Event Hubs</p><p>Service Bus</p><p>Storage-Warteschlangen</p><p>Storage-Blobs (Warteschlange für unzustellbare Nachrichten)</p></li></ul> | Aktivieren Sie die verwaltete Systemidentität für das Thema, und fügen Sie es der entsprechenden Rolle auf dem Ziel hinzu. Ausführliche Informationen finden Sie unter [Verwenden von systemseitig zugewiesenen Identitäten für die Ereignisübermittlung](#use-system-assigned-identities-for-event-delivery).  |
|Bearertokenauthentifizierung mit einem durch Azure AD geschützten Webhook | Webhook | Ausführliche Informationen finden Sie im Abschnitt [Authentifizieren der Ereignisübermittlung an Webhook-Endpunkte](#authenticate-event-delivery-to-webhook-endpoints). |
Geheimer Clientschlüssel als Abfrageparameter | Webhook | Ausführliche Informationen finden Sie im Abschnitt [Mit dem geheimen Clientschlüssel als Abfrageparameter](#using-client-secret-as-a-query-parameter). |

## <a name="use-system-assigned-identities-for-event-delivery"></a>Verwenden von systemseitig zugewiesenen Identitäten für die Ereignisübermittlung
Sie können eine vom System zugewiesene verwaltete Identität für ein Thema oder eine Domäne aktivieren und mit dieser Identität Ereignisse an unterstützte Ziele weiterleiten, z. B. Service Bus-Warteschlangen und -Themen, Event Hubs und Speicherkonten.

Im Folgenden werden die Schritte aufgeführt: 

1. Erstellen Sie ein Thema oder eine Domäne mit einer vom System zugewiesenen Identität, oder aktualisieren Sie ein vorhandenes Thema oder eine Domäne, um die Identität zu aktivieren. Weitere Informationen finden Sie unter [Aktivieren einer verwalteten Identität für ein Systemthema](enable-identity-system-topics.md) oder [Aktivieren einer verwalteten Identität für ein benutzerdefiniertes Thema oder eine benutzerdefinierte Domäne](enable-identity-custom-topics-domains.md).
1. Fügen Sie die Identität einer geeigneten Rolle (z. B. Azure Service Bus-Datensender) für das Ziel (z. B. Service Bus-Warteschlange) zu. Weitere Informationen finden Sie unter [Gewähren des Zugriffs auf ein Event Grid-Ziel für die Identität](add-identity-roles.md).
1. Aktivieren Sie beim Erstellen von Ereignisabonnements die Verwendung der Identität, um Ereignisse an das Ziel zu übermitteln. Weitere Informationen finden Sie unter [Erstellen eines Ereignisabonnements, von dem die Identität verwendet wird](managed-service-identity.md). 

Ausführliche Anweisungen finden Sie unter [Ereignisübermittlung mit einer verwalteten Identität](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Authentifizieren der Ereignisübermittlung an Webhook-Endpunkte
In den folgenden Abschnitten wird beschrieben, wie die Übermittlung von Ereignissen an Webhook-Endpunkte authentifiziert wird. Verwenden Sie unabhängig von der verwendeten Methode einen Mechanismus für einen Überprüfungshandshake. Weitere Informationen finden Sie unter [Webhook-Ereignisbereitstellung](webhook-event-delivery.md). 


### <a name="using-azure-active-directory-azure-ad"></a>Mit Azure Active Directory (Azure AD)
Sie können den Webhook-Endpunkt, der verwendet wird, um Ereignisse von Event Grid zu empfangen, mit Azure AD sichern. Sie müssen eine Azure AD-Anwendung erstellen, eine Rolle und einen Dienstprinzipal zur Autorisierung von Event Grid in Ihrer Anwendung erstellen und das Ereignisabonnement zur Verwendung der Azure AD-Anwendung konfigurieren. Erfahren Sie, wie Sie [Azure Active Directory mit Event Grid konfigurieren](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Mit dem geheimen Clientschlüssel als Abfrageparameter
Sie können auch den Webhook-Endpunkt sichern, indem Sie der Webhook-Ziel-URL, die beim Erstellen eines Ereignisabonnements angegeben wurde, Abfrageparameter hinzufügen. Legen Sie einen der Abfrageparameter als geheimen Clientschlüssel, z. B. ein [Zugriffstoken](https://en.wikipedia.org/wiki/Access_token), oder als gemeinsamen geheimem Schlüssel fest. Der Event Grid-Dienst nimmt alle Abfrageparameter in jede Ereignisbereitstellungsanforderung an den Webhook auf. Der Webhookdienst kann das Geheimnis abrufen und überprüfen. Wenn der geheime Clientschlüssel aktualisiert wird, muss das Ereignisabonnement ebenfalls aktualisiert werden. Damit bei dieser Geheimnisrotation keine Übermittlungsfehler auftreten, lassen Sie den Webhook für einen begrenzten Zeitraum sowohl alte als auch neue Geheimnisse akzeptieren, bevor das Ereignisabonnement mit dem neuen Geheimnis aktualisiert wird. 

Da Abfrageparameter geheime Clientschlüssel enthalten können, werden Sie mit besonderer Sorgfalt behandelt. Sie werden verschlüsselt gespeichert und sind für Dienstoperatoren nicht zugänglich. Sie werden nicht im Rahmen der Dienstprotokollierung/Ablaufverfolgung protokolliert. Beim Abrufen der Ereignisabonnementeigenschaften werden die Zielabfrageparameter standardmäßig nicht zurückgegeben. Beispiel: In der Azure [CLI](/cli/azure) muss der Parameter [--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) verwendet werden.

Weitere Informationen zum Übermitteln von Ereignissen an Webhooks finden Sie unter [Webhook-Ereignisbereitstellung](webhook-event-delivery.md).

> [!IMPORTANT]
> Azure Event Grid unterstützt nur **HTTPS**-Webhook-Endpunkte. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Endpunktüberprüfung mit CloudEvents 1.0
Wenn Sie mit Event Grid bereits vertraut sind, kennen Sie wahrscheinlich auch den Handshake zur Überprüfung von Endpunkten, mit dem Missbrauch verhindert werden soll. CloudEvents v1.0 implementiert eine eigene [Semantik für den Schutz vor Missbrauch](webhook-event-delivery.md) über die **HTTP OPTIONS**-Methode. Weitere Informationen dazu finden Sie unter [HTTP 1.1-Webhooks für die Ereignisbereitstellung – Version 1.0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Wenn Sie das CloudEvents-Schema für die Ausgabe nutzen, verwendet Event Grid anstelle des Event Grid-Mechanismus für Überprüfungsereignisse den Missbrauchsschutz von CloudEvents v1.0. Weitere Informationen finden Sie unter [Verwenden des CloudEvents 1.0-Schemas mit Event Grid](cloudevents-schema.md). 


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Authentifizieren von Clients, die Ereignisse an Themen oder Domänen pushen, finden Sie unter [Authentifizieren von Veröffentlichungsclients](security-authenticate-publishing-clients.md). 
