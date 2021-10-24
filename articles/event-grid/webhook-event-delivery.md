---
title: Webhook-Ereignisbereitstellung
description: In diesem Artikel werden die Webhook-Ereignisbereitstellung und die Endpunktüberprüfung bei der Verwendung von Webhooks beschrieben.
ms.topic: conceptual
ms.date: 10/13/2021
ms.openlocfilehash: 35b088f18b4261760d7908a8e779dbc1bda56501
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062992"
---
# <a name="webhook-event-delivery"></a>Webhook-Ereignisbereitstellung
Ein Webhook ist eine der vielen Möglichkeiten, um Ereignisse aus Azure Event Grid zu empfangen. Wenn ein neues Ereignis bereit ist, sendet der Event Grid-Dienst per POST-Vorgang eine HTTP-Anforderung an den konfigurierten Endpunkt, wobei das Ereignis im Anforderungstext enthalten ist.

Wie viele andere Dienste, die Webhooks unterstützen, müssen Sie bei Event Grid nachweisen, das Sie im Besitz Ihres Webhookendpunkts sind. Vorher wird mit dem Bereitstellen von Ereignissen für diesen Endpunkt nicht begonnen. Diese Anforderung verhindert, dass ein böswilliger Benutzer Ihren Endpunkt mit Ereignissen überschwemmt. Wenn Sie einen der drei unten angegebenen Azure-Dienste verwenden, wird diese Überprüfung automatisch von der Azure-Infrastruktur durchgeführt:

- Azure Logic Apps mit [Event Grid-Connector](/connectors/azureeventgrid/)
- Azure Automation über [Webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions mit [Event Grid-Trigger](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Endpunktüberprüfung mit Event Grid-Ereignissen

Falls Sie einen anderen Typ von Endpunkt nutzen, z. B. eine auf einem HTTP-Trigger basierende Azure-Funktion, muss Ihr Endpunktcode an einem Überprüfungshandshake mit Event Grid beteiligt sein. Event Grid unterstützt zwei Methoden zur Überprüfung des Abonnements.

- **Synchroner Handshake**: Zum Zeitpunkt der Erstellung des Ereignisabonnements sendet Event Grid ein Ereignis zur Überprüfung des Abonnements an Ihren Endpunkt. Das Schema dieses Ereignisses ähnelt dem aller anderen Event Grid-Ereignisse. Der Datenteil dieses Ereignisses umfasst eine `validationCode`-Eigenschaft. Ihre Anwendung überprüft, ob es sich bei der Überprüfungsanforderung um ein erwartetes Ereignisabonnement handelt, und gibt den Überprüfungscode synchron in der Antwort zurück. Dieser Handshakemechanismus wird in allen Event Grid-Versionen unterstützt.

- **Asynchroner Handshake**: In bestimmten Fällen können Sie den Überprüfungscode nicht synchron in der Antwort zurückgeben. Wenn Sie beispielsweise einen Drittanbieterdienst nutzen (z. B. [`Zapier`](https://zapier.com) oder [IFTTT](https://ifttt.com/)), können Sie unter Umständen nicht programmgesteuert mit dem Überprüfungscode antworten.

   Ab Version 2018-05-01-preview unterstützt Event Grid einen manuellen Überprüfungshandshake. Wenn Sie ein Ereignisabonnement mit einem SDK oder Tool erstellen, für die diese neue API-Version (2018-05-01-preview oder höher) verwendet wird, sendet Event Grid im Datenteil des Abonnementüberprüfungsereignisses eine `validationUrl`-Eigenschaft. Um den Handshake abzuschließen, suchen Sie diese URL in den Ereignisdaten und senden ihr eine GET-Anforderung. Sie können entweder einen REST-Client oder Ihren Webbrowser verwenden.

   Die angegebene URL ist **5 Minuten** lang gültig. Während dieser Zeit lautet der Bereitstellungsstatus des Ereignisabonnements `AwaitingManualAction`. Wenn Sie die manuelle Überprüfung nicht innerhalb von 5 Minuten abschließen, wird der Bereitstellungsstatus auf `Failed` eingestellt. Sie müssen das Ereignisabonnement erneut erstellen, bevor Sie mit der manuellen Überprüfung beginnen.

   Dieser Authentifizierungsmechanismus erfordert auch, dass der Webhookendpunkt einen HTTP-Statuscode von 200 zurückgibt, um sicherzustellen, dass das POST für das Überprüfungsereignis akzeptiert wurde, bevor er in den manuellen Überprüfungsmodus gewechselt wird. Mit anderen Worten: Wenn der Endpunkt 200 zurückgibt, aber nicht synchron eine Überprüfungsantwort zurückgibt, wird der Modus in den manuellen Überprüfungsmodus überführt. Wenn innerhalb von fünf Minuten ein GET-Vorgang auf die Überprüfungs-URL folgt, gilt der Überprüfungshandshake als erfolgreich.

> [!NOTE]
> Die Verwendung von selbstsignierten Zertifikaten wird nicht unterstützt. Verwenden Sie stattdessen ein signiertes Zertifikat von einer kommerziellen Zertifizierungsstelle.

### <a name="validation-details"></a>Überprüfungsdetails

- Zum Zeitpunkt der Erstellung/Aktualisierung des Ereignisabonnements sendet Event Grid ein Abonnementüberprüfungsereignis an den Zielendpunkt.
- Das Ereignis enthält einen `aeg-event-type: SubscriptionValidation`-Headerwert.
- Der Hauptteil des Ereignisses weist dasselbe Schema wie andere Event Grid-Ereignisse auf.
- Die `eventType`-Eigenschaft des Ereignisses lautet `Microsoft.EventGrid.SubscriptionValidationEvent`.
- Die `data`-Eigenschaft des Ereignisses enthält eine `validationCode`-Eigenschaft mit einer zufällig generierten Zeichenfolge. Beispiel: `validationCode: acb13…`.
- Die Ereignisdaten enthalten auch die `validationUrl`-Eigenschaft mit einer URL für die manuelle Überprüfung des Abonnements.
- Das Array enthält ausschließlich das Validierungsereignis. Andere Ereignisse werden in einer separaten Anforderung gesendet, nachdem Sie den Validierungscode zurückgegeben haben.
- Die EventGrid-SDKs der Datenebene verfügen über Klassen, die den Daten des Abonnementüberprüfungsereignisses und der Abonnementüberprüfungsantwort entsprechen.

Ein Beispiel für „SubscriptionValidationEvent“ finden Sie im folgenden Beispiel:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/myeventsub/validate?id=0000000000-0000-0000-0000-00000000000000&t=2021-09-01T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2021-00-01T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Senden Sie wie im folgenden Beispiel gezeigt den Validierungscode zurück an die Eigenschaft `validationResponse`, um den Besitz des Endpunkts nachzuweisen:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Sie müssen den Antwortstatuscode **HTTP 200 OK** zurückgeben. **HTTP 202 Accepted** wird nicht als gültige Antwort zur Überprüfung des Event Grid-Abonnements erkannt. Die HTTP-Anforderung muss innerhalb von 30 Sekunden abgeschlossen werden. Wenn der Vorgang nicht innerhalb von 30 Sekunden abgeschlossen wird, wird er abgebrochen und kann nach 5 Sekunden wiederholt werden. Wenn alle Versuche zu Fehlern führen, wird dies als Handshake-Validierungsfehler gewertet.

Alternativ können Sie das Abonnement manuell überprüfen, indem Sie eine GET-Anforderung an die Überprüfungs-URL senden. Das Ereignisabonnement bleibt im Status „Ausstehend“, bis es überprüft wurde. Die Überprüfungs-URL verwendet den Port 553. Wenn die Firewallregeln Port 553 blockieren, müssen die Regeln für einen erfolgreichen manuellen Handshake möglicherweise angepasst werden.

Ein Beispiel für die Handhabung des Handshakes zur Abonnementüberprüfung finden Sie in einem [C#-Beispiel](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Endpunktüberprüfung mit CloudEvents 1.0
CloudEvents 1.0 implementiert eine eigene [Semantik für den Schutz vor Missbrauch](webhook-event-delivery.md) über die **HTTP OPTIONS**-Methode. Weitere Informationen dazu finden Sie [hier](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Wenn das CloudEvents-Schema für die Ausgabe verwendet wird, verwendet Event Grid anstelle des Event Grid-Mechanismus für Überprüfungsereignisse den Missbrauchschutz von CloudEvents 1.0.

## <a name="event-schema-compatibility"></a>Ereignisschemakompatibilität
Wenn ein Thema erstellt wird, wird ein Schema für eingehende Ereignisse definiert. Wenn ein Abonnement erstellt wird, wird ein Schema für ausgehende Ereignisse definiert. Die folgende Tabelle zeigt die Kompatibilität, die beim Erstellen eines Abonnements zulässig ist. 

| Schema für eingehende Ereignisse | Schema für ausgehende Ereignisse | Unterstützt |
| ---- | ---- | ---- |
| Event Grid-Schema | Event Grid-Schema | Yes |
| | Cloud Events v1.0-Schema | Yes |
| | Benutzerdefiniertes Eingabeschema | No |
| Cloud Events v1.0-Schema | Event Grid-Schema | No |
| | Cloud Events v1.0-Schema | Yes |
| | Benutzerdefiniertes Eingabeschema | No |
| Benutzerdefiniertes Eingabeschema | Event Grid-Schema | Yes |
| | Cloud Events v1.0-Schema | Yes |
| | Benutzerdefiniertes Eingabeschema | Ja |

## <a name="next-steps"></a>Nächste Schritte
Im folgenden Artikel finden Sie Informationen zur Behandlung von Problemen bei der Überprüfung von Ereignisabonnements: 

[Problembehandlung bei Fehlern in Azure Event Grid](troubleshoot-subscription-validation.md).
