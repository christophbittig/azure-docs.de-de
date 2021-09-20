---
title: 'Azure Event Grid: Festlegen benutzerdefinierter Header für übermittelte Ereignisse'
description: Hier erfahren Sie, wie Sie benutzerdefinierte Header (oder Übermittlungseigenschaften) für übermittelte Ereignisse festlegen.
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 3600d74d91ad218f3fcab99002762d605fba3139
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831350"
---
# <a name="custom-delivery-properties"></a>Benutzerdefinierte Übermittlungseigenschaften
Mit Ereignisabonnements können Sie HTTP-Header einrichten, die in übermittelte Ereignisse eingeschlossen werden. Diese Funktion ermöglicht es Ihnen, benutzerdefinierte Header festzulegen, die für ein Ziel erforderlich sind. Beim Erstellen eines Ereignisabonnements können bis zu zehn Header festgelegt werden. Die einzelnen Headerwert dürfen nicht größer als 4.096 Bytes (4K) sein.

Sie können benutzerdefinierte Header für die Ereignisse festlegen, die an die folgenden Ziele übermittelt werden:

- webhooks
- Azure Service Bus-Themen und -Warteschlangen
- Azure Event Hubs
- Relay-Hybridverbindungen

Wenn Sie ein Ereignisabonnement im Azure-Portal erstellen, können Sie die Registerkarte **Übermittlungseigenschaften** verwenden, um benutzerdefinierte HTTP-Header festzulegen. Auf dieser Seite können feste und dynamische Headerwerte festgelegt werden.

## <a name="setting-static-header-values"></a>Festlegen statischer Headerwerte
Wenn Sie Header mit einem festen Wert festlegen möchten, geben Sie den Namen des Headers und dessen Wert in die entsprechenden Felder ein:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Übermittlungseigenschaften: statisch":::

Bei Angabe vertraulicher Daten empfiehlt es sich gegebenenfalls, das Kontrollkästchen **Geheim?** zu aktivieren. Vertrauliche Daten werden nicht im Azure-Portal angezeigt. 

## <a name="setting-dynamic-header-values"></a>Festlegen dynamischer Headerwerte
Sie können den Wert eines Headers auf der Grundlage einer Eigenschaft in einem eingehenden Ereignis festlegen. Verwenden Sie die JsonPath-Syntax, um auf den Eigenschaftswert eines eingehenden Ereignisses zu verweisen, der als Wert für einen Header in ausgehenden Anforderungen verwendet werden soll. Wenn Sie beispielsweise in den Ereignisdaten den Wert eines Headers namens **Channel** mit dem Wert der Eigenschaft **system** eines eingehenden Ereignisses festlegen möchten, können Sie Ihr Ereignisabonnement wie folgt konfigurieren:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Übermittlungseigenschaften: dynamisch":::

## <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle
Verwenden Sie den `--delivery-attribute-mapping`-Parameter, wenn Sie ein Abonnement mit dem `az eventgrid event-subscription create`-Befehl erstellen. Hier sehen Sie ein Beispiel:

```azurecli
az eventgrid event-subscription create -n es1 \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1
    --endpoint-type storagequeue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/sa1/queueservices/default/queues/q1 \
    --enable-advanced-filtering-on-arrays true
    --delivery-attribute-mapping staticproperty1 static somestaticvalue2 true 
    --delivery-attribute-mapping staticproperty2 static somestaticvalue3 false 
    --delivery-attribute-mapping dynamicproperty1 dynamic data.key1
```

## <a name="examples"></a>Beispiele
Dieser Abschnitt enthält ein paar Beispiele für die Verwendung von Übermittlungseigenschaften.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Festlegen des Autorisierungsheaders mit einem Bearertoken (nicht normatives Beispiel)

Legen Sie einen Wert für einen Autorisierungsheader fest, um die Anforderung mit Ihrem Webhook-Handler zu identifizieren. Ein Autorisierungsheader kann festgelegt werden, wenn Sie nicht [Ihren Webhook mit Azure Active Directory schützen](secure-webhook-delivery.md).

| Headername   | Headertyp | Headerwert |
| :--           | :--         | :--            |
|`Authorization` | statischen | `BEARER SlAV32hkKG...`|

Ausgehende Anforderungen sollten jetzt den für das Ereignisabonnement festgelegten Header enthalten:

```console
POST /home.html HTTP/1.1
Host: acme.com

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Das Definieren von Autorisierungsheadern ist sinnvoll, wenn es sich bei dem Ziel um einen Webhook handelt. Für [mit einer Ressourcen-ID abonnierte Funktionen](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs und Hybridverbindungen sollte diese Option nicht verwendet werden, da diese Ziele bei Verwendung mit Event Grid ihre eigenen Authentifizierungsschemas unterstützen.

### <a name="service-bus-example"></a>Service Bus-Beispiel
Von Azure Service Bus wird die Verwendung der folgenden Nachrichteneigenschaften beim Senden einzelner Nachrichten unterstützt. 

| Headername | Headertyp |
| :-- | :-- |
| `MessageId` | Dynamisch |  
| `PartitionKey` | Statisch oder dynamisch |
| `SessionId` | Statisch oder dynamisch |
| `CorrelationId` | Statisch oder dynamisch |
| `Label` | Statisch oder dynamisch |
| `ReplyTo` | Statisch oder dynamisch | 
| `ReplyToSessionId` | Statisch oder dynamisch |
| `To` |Statisch oder dynamisch |
| `ViaPartitionKey` | Statisch oder dynamisch |

> [!NOTE]
> - Der Standardwert von `MessageId` ist die interne ID des Event Grid Ereignisses. Sie kann außer Kraft gesetzt werden. Beispielsweise `data.field`.
> - Sie können nur `SessionId` oder `MessageId` festlegen. 

### <a name="event-hubs-example"></a>Event Hubs-Beispiel

Wenn Sie Ereignisse für eine bestimmte Partition innerhalb eines Event Hub veröffentlichen müssen, legen Sie die `ParitionKey`-Eigenschaft für Ihr Ereignisabonnement fest, um den Partitionsschlüssel zur Identifizierung der Event Hub-Zielpartition anzugeben.

| Headername | Headertyp |
| :-- | :-- |
|`PartitionKey` | statischen |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Konfigurieren der Gültigkeitsdauer für ausgehende Ereignisse in Azure Storage-Warteschlangen
Für das Azure Storage-Warteschlangenziel können Sie nur die Gültigkeitsdauer der ausgehenden Nachricht nach Übermittlung an eine Azure Storage-Warteschlange konfigurieren. Ohne Zeitangabe beträgt die Gültigkeitsdauer der Nachricht standardmäßig sieben Tage. Sie können auch festlegen, dass das Ereignis nie ablaufen soll.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Übermittlungseigenschaften: Speicherwarteschlange":::

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Ereignisübermittlung finden Sie in den folgenden Artikeln:

- [Übermittlung und Wiederholung](delivery-and-retry.md)
- [Webhook-Ereignisbereitstellung](webhook-event-delivery.md)
- [Ereignisfilterung](event-filtering.md)
