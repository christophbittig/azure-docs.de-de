---
title: 'Anleitung: Konfigurieren eines Ereignishandlers'
description: Leitfaden zu Ereignishandlerkonzepten und Einführung in die Integration bei der Entwicklung mit dem Azure Web PubSub-Dienst
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/31/2021
ms.openlocfilehash: 45ad4d9d6d441584a512f5666245aed4020778cb
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576394"
---
# <a name="event-handler-in-azure-web-pubsub-service"></a>Ereignishandler im Azure Web PubSub-Dienst

Der Ereignishandler verarbeitet die eingehenden Clientereignisse. Ereignishandler werden über das Portal oder die Azure CLI vorab im Dienst registriert und konfiguriert. Sobald ein Clientereignis ausgelöst wird, kann der Dienst erkennen, ob das Ereignis behandelt werden soll oder nicht. Wir unterstützen jetzt den Ereignishandler serverseitig, der einen öffentlichen zugänglichen Endpunkt für den Dienst verfügbar macht, der aufgerufen werden soll, wenn das Ereignis ausgelöst wird. Mit anderen Worten: Er fungiert als **Webhook**. 

Der Dienst übergibt Clientereignisse mithilfe des [CloudEvents-HTTP-Protokolls](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) an den Upstreamwebhook.

Für jedes Ereignis formuliert er eine HTTP-POST-Anforderung an den registrierten Upstream und erwartet eine HTTP-Antwort. 

Die Daten, die vom Dienst an den Server gesendet werden, weisen immer das CloudEvents-Format `binary` auf.

:::image type="content" source="media/howto-develop-eventhandler/event-trigger.png" alt-text="Screenshot des Web PubSub-Dienstereignistriggers":::

## <a name="upstream-and-validation"></a>Upstream und Überprüfung

Beim Konfigurieren des Webhookendpunkts kann die URL den Parameter `{event}` verwenden, um eine URL-Vorlage zu definieren. Der Dienst berechnet den Wert der Webhook-URL dynamisch, sobald er die Clientanforderung empfängt. Beispiel: Wenn die Anforderung `/client/hubs/chat` mit dem konfigurierten Ereignishandler-URL-Muster `http://host.com/api/{event}` für den Hub `chat` empfangen wird, wird beim Herstellen der Clientverbindung zunächst eine Nachricht für diese URL veröffentlicht: `http://host.com/api/connect`. Dies kann nützlich sein, wenn ein PubSub-WebSocket-Client benutzerdefinierte Ereignisse sendet und der Ereignishandler unterschiedliche Ereignisse an verschiedene Upstreams sendet. Beachten Sie, dass der `{event}`-Parameter im URL-Domänennamen nicht zulässig ist.

Beim Einrichten des Ereignishandler-Upstreams über das Azure-Portal oder die Azure CLI überprüft der Dienst den Upstreamwebhook mithilfe des [CloudEvents-Schutzes gegen Missbrauch](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Jede registrierte Upstream-Webhook-URL wird mit diesem Mechanismus überprüft. Der `WebHook-Request-Origin`-Anforderungsheader ist auf den Dienstdomänennamen `xxx.webpubsub.azure.com` festgelegt, und es wird erwartet, dass der Antwortheader `WebHook-Allowed-Origin` diesen Domänennamen oder `*` enthält.

Bei der Überprüfung wird der Parameter `{event}` in `validate` aufgelöst. Beim Versuch, die URL auf `http://host.com/api/{event}` festzulegen, versucht der Dienst z. B., einen **OPTIONS**-Vorgang für eine Anforderung an `http://host.com/api/validate` auszuführen. Nur wenn die Antwort gültig ist, kann die Konfiguration erfolgreich festgelegt werden.

Aktuell werden [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) und [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) nicht unterstützt.

## <a name="authentication-between-service-and-webhook"></a>Authentifizierung zwischen Dienst und Webhook

- Anonymer Modus
- Die einfache Authentifizierung mit `?code=<code>` wird über die konfigurierte Webhook-URL als Abfrageparameter bereitgestellt.
- Azure Active Directory-Authentifizierung 
   - Fügen Sie in den App-Registrierungen von AAD einen geheimen Clientschlüssel hinzu, und stellen Sie den geheimen Clientschlüssel über das Portal oder die CLI für Azure Web PubSub bereit.
   - Bereitstellen der [Identität](../app-service/overview-managed-identity.md?tabs=dotnet) für Azure Web PubSub über das Portal/die CLI

## <a name="configure-event-handler"></a>Konfigurieren eines Ereignishandlers

### <a name="configure-through-azure-portal"></a>Konfigurieren über Azure-Portal

Suchen Sie Ihren Azure Web PubSub-Dienst über **Azure-Portal**. Navigieren Sie zu **Einstellungen**, und geben Sie Ihren hub-name ein. Klicken Sie dann auf **Hinzufügen**, um die serverseitige Webhook-URL zu konfigurieren. Vergessen Sie das **Speichern** nicht, sobald Sie fertig sind.

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="Screenshot: Festlegen des Ereignishandlers":::

### <a name="configure-through-azure-cli"></a>Konfigurieren über Azure CLI

Verwenden Sie die Befehle der Azure CLI-Gruppe [**az webpubsub hub**](/cli/azure/webpubsub/hub), um die Ereignishandlereinstellungen zu aktualisieren.

Befehle | BESCHREIBUNG
--|--
create | Erstellen Sie Hubeinstellungen für den WebPubSub-Dienst.
delete | Löschen Sie Hubeinstellungen für den WebPubSub-Dienst.
list   | Listen Sie Hubeinstellungen für den WebPubSub-Dienst auf.
show   | Zeigen Sie Hubeinstellungen für den WebPubSub-Dienst an.
aktualisieren | Aktualisieren Sie Hubeinstellungen für den WebPubSub-Dienst.

Im Folgenden finden Sie ein Beispiel für das Erstellen von zwei Webhook-URLs für den Hub `MyHub` der `MyWebPubSub`-Ressource.

```azurecli-interactive
az webpubsub hub create -n "MyWebPubSub" -g "MyResourceGroup" --hub-name "MyHub" --event-handler url-template="http://host.com" user-event-pattern="*" --event-handler url-template="http://host2.com" system-event="connected" system-event="disconnected" auth-type="ManagedIdentity" auth-resource="uri://myUri"
```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next step](includes/include-next-step.md)]