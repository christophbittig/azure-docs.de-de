---
title: Azure Event Grid – Übermittlung und Wiederholung
description: Beschreibt, wie Azure Event Grid Ereignisse übermittelt und wie nicht übermittelte Nachrichten verarbeitet werden.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a6055a99e717dd379dc6bd43411c73456bdaede8
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730329"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid – Übermittlung und Wiederholung von Nachrichten
Event Grid bietet permanente Übermittlung. Die Anwendung versucht, für jedes übereinstimmende Abonnement jede Nachricht **mindestens ein Mal** sofort zu übermitteln. Wenn der Endpunkt eines Abonnenten den Empfang eines Ereignisses nicht bestätigt, oder wenn ein Fehler auftritt, versucht Event Grid die Übermittlung erneut auf Grundlage eines festen [Wiederholungszeitplans](#retry-schedule) und einer festen [Wiederholungsrichtlinie](#retry-policy). Standardmäßig übermittelt das Event Grid-Modul immer nur ein Ereignis gleichzeitig an den Abonnenten. Die Nutzlast ist jedoch ein Array mit einem einzelnen Ereignis.

> [!NOTE]
> Bei der Ereignisübermittlung durch Event Grid wird unter Umständen die Reihenfolge nicht eingehalten, sodass Abonnenten die Ereignisse ggf. nicht in der richtigen Reihenfolge erhalten. 

## <a name="retry-schedule"></a>Wiederholungszeitplan
Wenn EventGrid einen Fehler für einen Ereignisbereitstellungsversuch erhält, entscheidet EventGrid je nach Art des Fehlers, ob die Bereitstellung erneut versucht oder ob das Ereignis als unzustellbar markiert oder gelöscht werden soll. 

Wenn es sich bei dem vom abonnierten Endpunkt zurückgegebenen Fehler um einen konfigurationsbedingten Fehler handelt, der nicht durch Wiederholungsversuche behoben werden kann (z. B. wenn der Endpunkt gelöscht wird), markiert EventGrid das Ereignis als unzustellbare Nachricht oder löscht das Ereignis, wenn die Warteschlange für unzustellbare Nachrichten nicht konfiguriert ist.

In der folgenden Tabelle sind die Endpunkt- und Fehlertypen beschrieben, für die kein Wiederholungsversuch ausgeführt wird:

| Endpunkttyp | Fehlercodes |
| --------------| -----------|
| Azure-Ressourcen | 400 – Ungültige Anforderung, 413 – Anforderungsentität zu groß, 403 – Unzulässig | 
| Webhook | 400 – Ungültige Anforderung, 413 – Anforderungsentität zu groß, 403 – Unzulässig, 404 – Nicht gefunden, 401 – Nicht autorisiert |
 
> [!NOTE]
> Wenn die Warteschlange für unzustellbare Nachrichten nicht für den Endpunkt konfiguriert ist, werden Ereignisse gelöscht, wenn die oben genannten Fehler auftreten. Es empfiehlt sich, die Warteschlange für unzustellbare Nachrichten zu konfigurieren, wenn Sie nicht möchten, dass diese Arten von Ereignissen gelöscht werden.

Wenn der vom abonnierten Endpunkt zurückgegebene Fehler nicht in der obigen Liste enthalten ist, führt EventGrid die Wiederholung mithilfe der unten beschriebenen Richtlinien durch:

Event Grid wartet nach der Zustellung einer Nachricht 30 Sekunden auf eine Antwort. Nach 30 Sekunden, wenn der Endpunkt nicht geantwortet hat, wird die Nachricht zur Wiederholung in die Warteschlange eingereiht. Event Grid verwendet exponentiell ansteigende Wartezeiten für Wiederholungsversuche für die Ereignisübermittlung. Event Grid wiederholt die Zustellung nach folgendem Zeitplan auf Basis der besten Leistung:

- 10 Sekunden
- 30 Sekunden
- 1 Minute
- 5 Minuten
- 10 Minuten
- 30 Minuten
- 1 Stunde
- 3 Stunden
- 6 Stunden
- Alle 12 Stunden bis zu 24 Stundenlang


Wenn der Endpunkt innerhalb von 3 Minuten antwortet, versucht Event Grid, das Ereignis aus der Wiederholungswarteschlange auf bestmögliche Weise zu entfernen, aber es können dennoch weiterhin Duplikate empfangen werden.

Event Grid fügt allen Wiederholungsschritten eine geringfügige Randomisierung hinzu und kann opportunistisch bestimmte Wiederholungen überspringen, wenn ein Endpunkt konsistent fehlerhaft ist, für einen längeren Zeitraum ausgefallen ist oder überlastet zu sein scheint.

## <a name="retry-policy"></a>Wiederholungsrichtlinie
Sie können die Wiederholungsrichtlinie beim Erstellen eines Ereignisabonnements anpassen, indem Sie die folgenden beiden Konfigurationen verwenden. Ein Ereignis wird gelöscht, wenn eins dieser Limits der Wiederholungsrichtlinie erreicht wird. 

- **Maximale Anzahl von Versuchen**: Der Wert muss eine ganze Zahl zwischen 1 und 30 sein. Der Standardwert ist 30.
- **TTL des Ereignisses**: Der Wert muss eine ganze Zahl zwischen 1 und 1.440 sein. Der Standardwert beträgt 1.440 Minuten.

Beispiele für CLI- und PowerShell-Befehle zum Konfigurieren dieser Einstellungen finden Sie unter [Festlegen der Wiederholungsrichtlinie](manage-event-delivery.md#set-retry-policy).

## <a name="output-batching"></a>Ausgabebatches 
Event Grid sendet jedes Ereignis standardmäßig einzeln an Abonnenten. Der Abonnent empfängt ein Array mit einem einzelnen Ereignis. Sie können Event Grid zum Zusammenstellen von Ereignissen für die Übermittlung konfigurieren, um die HTTP-Leistung in Szenarios mit hohen Durchsätzen zu verbessern. Die Batchverarbeitung ist standardmäßig deaktiviert und kann für einzelne Abonnements aktiviert werden.

### <a name="batching-policy"></a>Richtlinie für die Batchverarbeitung
Es gibt zwei Einstellungen für die Batchübermittlung:

* **Maximale Anzahl von Ereignissen pro Batch** bezeichnet die maximale Anzahl der Ereignisse, die Event Grid pro Batch übermittelt. Diese Zahl wird nie überschritten, jedoch können weniger Ereignisse übermittelt werden, wenn keine anderen Ereignisse zum Zeitpunkt der Veröffentlichung verfügbar sind. Event Grid verzögert Ereignisse nicht, um einen Batch zu erstellen, wenn weniger Ereignisse verfügbar sind. Der Wert muss zwischen 1 und 5.000 liegen.
* **Bevorzugte Batchgröße in KB** bezeichnet die Obergrenze für die Batchgröße in Kilobyte. Ähnlich wie bei der maximalen Anzahl von Ereignissen kann die Batchgröße kleiner sein, wenn nicht mehr Ereignisse zum Zeitpunkt der Veröffentlichung vorhanden sind. Es ist möglich, dass ein Batch die bevorzugte Batchgröße überschreitet, *wenn* ein einzelnes Ereignis größer als die bevorzugte Batchgröße ist. Wenn die bevorzugte Größe beispielsweise 4 KB ist und ein Ereignis mit 10 KB an Event Grid gepusht wird, wird das Ereignis mit 10 KB dennoch in einem eigenen Batch übermittelt, anstatt gelöscht zu werden.

Die Batchübermittlung wird pro Ereignisabonnement über das Portal, eine Befehlszeilenschnittstelle, PowerShell oder über SDKs konfiguriert.

### <a name="batching-behavior"></a>Verhalten der Batchverarbeitung

* Alle oder keine

  Event Grid baut auf einer Alle-oder-keine-Semantik auf. Ein teilweiser Erfolg einer Übermittlung im Batch wird nicht unterstützt. Abonnenten sollten darauf achten, nur so viele Ereignisse pro Batch anzufordern, wie Sie nach sinnvollem Ermessen in 60 Sekunden tatsächlich verarbeiten können.

* Optimistische Batchverarbeitung

  Die Richtlinieneinstellungen für die Batchverarbeitung sind keine strengen Vorgaben für das Batchverarbeitungsverhalten, sie werden jeweils im Hinblick auf bestmögliche Leistung eingehalten. Bei niedrigen Ereignisraten stellen Sie häufig fest, dass die Batchgröße unter der angeforderten maximalen Anzahl von Ereignissen pro Batch liegt.

* Standardwert ist AUS

  Standardmäßig fügt Event Grid jeder Übermittlungsanforderung nur ein Ereignis hinzu. Zum Aktivieren der Batchverarbeitung müssen Sie eine der zuvor in diesem Artikel beschriebenen Einstellungen im JSON-Code des Ereignisabonnements festlegen.

* Standardwerte

  Beim Erstellen eines Ereignisabonnements müssen nicht beide Einstellungen („Maximale Anzahl von Ereignissen pro Batch“ und „Bevorzugte Batchgröße in KB“) angegeben werden. Wenn nur eine Einstellung festgelegt ist, verwendet Event Grid (konfigurierbare) Standardwerte. In den folgenden Abschnitten finden Sie die Standardwerte und es wird beschrieben, wie dieser außer Kraft gesetzt werden können.

### <a name="azure-portal"></a>Azure-Portal: 
![Einstellungen der Batchübermittlung](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Wenn Sie ein Ereignisabonnement erstellen, verwenden Sie die folgenden Parameter: 

- **max-events-per-batch**: maximale Anzahl von Ereignissen in einem Batch. Muss eine Zahl im Bereich 1 bis 5.000 sein.
- **preferred-batch-size-in-kilobytes**: bevorzugte Batchgröße in Kilobyte. Muss eine Zahl im Bereich 1 bis 1.024 sein.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Weitere Informationen zur Verwendung von Azure CLI mit Event Grid finden Sie unter [Weiterleiten von Speicherereignissen an den Webendpunkt mit Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).


## <a name="delayed-delivery"></a>Verzögerte Übermittlung
Wenn bei einem Endpunkt Übermittlungsfehler auftreten, beginnt Event Grid, die Übermittlung von Ereignissen an diesen Endpunkt und erneute Versuche zu verzögern. Wenn beispielsweise bei den ersten zehn an einem Endpunkt veröffentlichten Ereignissen Fehler auftreten, geht Event Grid davon aus, dass am Endpunkt Probleme auftreten, und verzögert alle nachfolgenden Wiederholungsversuche *und neuen* Übermittlungen für einige Zeit – in einigen Fällen bis zu mehreren Stunden.

Der funktionale Zweck der verzögerten Übermittlung besteht darin, sowohl fehlerhafte Endpunkte als auch das Event Grid-System zu schützen. Ohne Backoff und Verzögerung der Übermittlung an fehlerhafte Endpunkte können die Wiederholungsrichtlinie und Volumefunktionen von Event Grid ein System leicht überfordern.

## <a name="dead-letter-events"></a>„Unzustellbare Nachrichten“-Ereignisse
Wenn ein Ereignis innerhalb eines bestimmten Zeitraums oder nach einer bestimmten Anzahl von Übermittlungsversuchen nicht übermittelt werden kann, kann Event Grid das nicht übermittelte Ereignis an ein Speicherkonto senden. Dieser Prozess wird als Speicherung **unzustellbarer Nachrichten** bezeichnet. Von Event Grid werden unzustellbare Nachrichten gespeichert, wenn **eine der folgenden** Bedingungen erfüllt ist. 

- Das Ereignis wird nicht innerhalb der **Gültigkeitsdauer** übermittelt. 
- Die **Anzahl der Versuche** hat den Grenzwert überschritten.

Wenn eine der Bedingungen erfüllt ist, wird das Ereignis gelöscht oder als unzustellbare Nachricht gespeichert.  Die Speicherung unzustellbarer Nachrichten ist standardmäßig nicht aktiviert. Wenn Sie das Feature aktivieren möchten, müssen Sie bei der Erstellung des Ereignisabonnements ein Speicherkonto zum Speichern nicht übermittelter Ereignisse angeben. Ereignisse werden aus diesem Speicherkonto gepullt, um Übermittlungsprobleme zu beheben.

Event Grid sendet ein Ereignis an den Speicherort für unzustellbare Nachrichten, wenn alle Wiederholungsversuche ausgeführt wurden. Wenn Event Grid den Antwortcode 400 (Ungültige Anforderung) oder 413 (Anforderungsentität zu groß) empfängt, wird das Ereignis sofort für die Warteschlange für unzustellbare Nachrichten geplant. Diese Antwortcodes geben an, dass die Übermittlung des Ereignisses nie erfolgreich ausgeführt wird.

Der Ablauf der Gültigkeitsdauer wird ERST beim nächsten geplanten Übermittlungsversuch geprüft. Aus diesem Grund gilt Folgendes: Auch wenn die Gültigkeitsdauer vor dem nächsten geplanten Übermittlungsversuch abläuft, wird der Ablauf eines Ereignisses erst zum Zeitpunkt der nächsten Übermittlung überprüft. Anschließend wird das Ereignis als unzustellbar markiert. 

Es gibt eine fünfminütige Verzögerung zwischen dem letzten Versuch, ein Ereignis zu übermitteln, und der Übermittlung an den Speicherort für unzustellbare Nachrichten. Diese Verzögerung dient dazu, die Anzahl der Blob Storage-Vorgänge zu reduzieren. Wenn der Speicherort für unzustellbare Nachrichten vier Stunden lang nicht verfügbar ist, wird das Ereignis gelöscht.

Wenn Sie den Speicherort für unzustellbare Nachrichten festlegen möchten, benötigen Sie ein Speicherkonto mit einem Container. Der Endpunkt für diesen Container wird bei der Erstellung des Ereignisabonnements angegeben. (Format des Endpunkts: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`)

Es wird empfohlen, sich benachrichtigen zu lassen, wenn ein Ereignis an den Speicherort für unzustellbare Nachrichten gesendet wurde. Wenn Event Grid auf nicht übermittelte Ereignisse reagieren soll, [erstellen Sie ein Ereignisabonnement](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) für den Blobspeicher für unzustellbare Nachrichten. Sobald bei Ihrem Blobspeicher für unzustellbare Nachrichten ein nicht übermitteltes Ereignis eingeht, informiert Event Grid Ihren Handler. Der Handler reagiert mit den gewünschten Aktionen zur Abstimmung nicht übermittelter Ereignisse. Ein Beispiel für das Einrichten eines Speicherorts für unzustellbare Nachrichten und Wiederholungsrichtlinien finden Sie unter [Unzustellbare Nachrichten und Wiederholungsrichtlinien](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Übermittlungsereignisformate
Dieser Abschnitt enthält Beispiele für Ereignisse und Ereignisse für unzustellbare Nachrichten in verschiedenen Übermittlungsschemaformaten (Event Grid-Schema, CloudEvents 1.0-Schema und benutzerdefiniertes Schema). Weitere Informationen zu diesen Formaten finden Sie in den Artikeln [Event Grid-Schema](event-schema.md) und [Cloud Events 1.0-Schema](cloud-event-schema.md). 

### <a name="event-grid-schema"></a>Event Grid-Schema

#### <a name="event"></a>Ereignis 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Ereignis für unzustellbare Nachrichten

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1.0-Schema

#### <a name="event"></a>Ereignis

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Ereignis für unzustellbare Nachrichten

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Benutzerdefiniertes Schema

#### <a name="event"></a>Ereignis

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Ereignis für unzustellbare Nachrichten
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Nachrichtenübermittlungsstatus

Event Grid verwendet HTTP-Antwortcodes zum Bestätigen des Eingangs von Ereignissen. 

### <a name="success-codes"></a>Erfolgscodes

Event Grid berücksichtigt **nur** die folgenden HTTP-Antwortcodes als erfolgreiche Übermittlungen. Alle anderen Statuscodes gelten als Übermittlungen, bei denen Fehler aufgetreten sind, und die Übermittlungen werden entsprechend neu versucht oder als unzustellbar behandelt. Nach dem Empfang eines erfolgreichen Statuscodes betrachtet Event Grid die Übermittlung als abgeschlossen.

- 200 – OK
- 201 – Erstellt
- 202 – Akzeptiert
- 203 Keine Autorisierungsinformationen
- 204 Kein Inhalt

### <a name="failure-codes"></a>Fehlercodes

Alle anderen Codes, die nicht zur obigen Gruppe (200-204) gehören, werden als Fehler angesehen, und es wird (bei Bedarf) ein neuer Versuch unternommen. Für einige gelten spezifische Wiederholungsrichtlinien, die im Folgenden beschrieben werden, alle anderen folgen dem standardmäßigen exponentiellen Backoffmodell. Es ist wichtig zu beachten, dass aufgrund der stark parallelisierten Architektur von Event Grid das Wiederholungsverhalten nicht deterministisch ist. 

| Statuscode | Wiederholungsverhalten |
| ------------|----------------|
| 400 – Ungültige Anforderung | Nicht erneut versucht |
| 401 – Nicht autorisiert | Wiederholung nach fünf Minuten oder mehr für Azure-Ressourcenendpunkte |
| 403 Verboten | Nicht erneut versucht |
| 404 – Nicht gefunden | Wiederholung nach fünf Minuten oder mehr für Azure-Ressourcenendpunkte |
| 408 Anforderungstimeout | Wiederholen Sie den Vorgang nach mindestens 2 Minuten. |
| 413 – Anforderungsentität zu groß | Nicht erneut versucht |
| 503 Dienst nicht verfügbar | Wiederholen Sie den Vorgang nach mindestens 30 Sekunden. |
| Alle anderen | Wiederholen Sie den Vorgang nach mindestens 10 Sekunden. |

## <a name="custom-delivery-properties"></a>Benutzerdefinierte Übermittlungseigenschaften
Mit Ereignisabonnements können Sie HTTP-Header einrichten, die in übermittelte Ereignisse eingeschlossen werden. Diese Funktion ermöglicht es Ihnen, benutzerdefinierte Header festzulegen, die für ein Ziel erforderlich sind. Beim Erstellen eines Ereignisabonnements können bis zu zehn Header festgelegt werden. Die einzelnen Headerwert dürfen nicht größer als 4.096 Bytes (4K) sein. Sie können benutzerdefinierte Header für die Ereignisse festlegen, die an die folgenden Ziele übermittelt werden:

- webhooks
- Azure Service Bus-Themen und -Warteschlangen
- Azure Event Hubs
- Relay-Hybridverbindungen

Weitere Informationen finden Sie unter [Benutzerdefinierte Übermittlungseigenschaften](delivery-properties.md). 

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Anzeigen des Status von Ereignisübermittlungen finden Sie unter [Überwachen der Event Grid-Nachrichtenübermittlung](monitor-event-delivery.md).
* Um Optionen für die Ereignisübermittlung anzupassen, lesen Sie [Unzustellbare Nachrichten und Wiederholungsrichtlinien](manage-event-delivery.md).
