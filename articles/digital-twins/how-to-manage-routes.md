---
title: Verwalten von Endpunkten und Routen
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie Endpunkte und Ereignisrouten für Azure Digital Twins-Daten einrichten und verwalten.
author: baanders
ms.author: baanders
ms.date: 7/30/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: dd63a04616848acfb3971a97f8363498e6ba4e55
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835742"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Verwalten von Endpunkten und Routen in Azure Digital Twins

In Azure Digital Twins können Sie [Ereignisbenachrichtigungen](concepts-event-notifications.md) an Downstreamdienste oder verbundene Computeressourcen weiterleiten. Dies erfolgt, indem zunächst **Endpunkte** eingerichtet werden, die die Ereignisse empfangen können. Anschließend können Sie [Ereignisrouten](concepts-route-events.md) erstellen, die angeben, welche von Azure Digital Twins generierten Ereignisse an welche Endpunkte übermittelt werden.

In diesem Artikel wird das Erstellen von Endpunkten und Routen mit dem [Azure-Portal](https://portal.azure.com), den [REST-APIs](/rest/api/azure-digitaltwins/), dem [NET (C#) SKD](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) und der [Azure Digital Twins-CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) ausführlich erläutert.

## <a name="prerequisites"></a>Voraussetzungen

* Sie benötigen ein **Azure-Konto**, das Sie [kostenlos einrichten können](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Sie benötigen eine **Azure Digital Twins-Instanz** in Ihrem Azure-Abonnement. Falls Sie noch keine Instanz besitzen, können Sie zum Erstellen die Schritte in [Einrichten einer Instanz und Authentifizierung](how-to-set-up-instance-portal.md) befolgen. Notieren Sie sich die folgenden Werte aus dem Setup, um sie später in diesem Artikel zu verwenden:
    - Instanzname
    - Ressourcengruppe

Nachdem Sie Ihre Instanz eingerichtet haben, können Sie diese Details im [Azure-Portal](https://portal.azure.com) anzeigen. Melden Sie sich beim Portal an, und suchen Sie in der Suchleiste des Portals nach dem Namen Ihrer Instanz.
 
:::image type="content" source="media/how-to-manage-routes/search-field-portal.png" alt-text="Screenshot: Suchleiste im Azure-Portal" lightbox="media/how-to-manage-routes/search-field-portal.png":::

Wählen Sie Ihre Instanz aus den Ergebnissen aus, damit diese Details auf der Seite „Übersicht“ für Ihre Instanz angezeigt werden:

:::image type="content" source="media/how-to-manage-routes/instance-details.png" alt-text="Screenshot der Seite „Übersicht“ für eine Azure Digital Twins-Instanz im Azure-Portal. Der Name und die Ressourcengruppe sind hervorgehoben.":::

Befolgen Sie die nachstehenden Anweisungen, wenn Sie die Azure CLI verwenden möchten, während Sie diesen Leitfaden befolgen.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Erstellen eines Endpunkts für Azure Digital Twins

Dies sind die unterstützten Typen von Endpunkten, die Sie für Ihre Instanz erstellen können:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Weitere Informationen zu den verschiedenen Endpunkttypen finden Sie unter [Wählen zwischen Azure-Messagingdiensten](../event-grid/compare-messaging-services.md).

In diesem Abschnitt wird erläutert, wie diese Endpunkte mithilfe des [Azure-Portals](https://portal.azure.com) oder der [Azure CLI](/cli/azure/dt/endpoint?view=azure-cli-latest&preserve-view=true) erstellt werden. Sie können Endpunkte auch mit den [DigitalTwinsEndpoint-Steuerungsebenen-APIs](/rest/api/digital-twins/controlplane/endpoints) verwalten.

### <a name="prerequisite-create-endpoint-resources"></a>Voraussetzung: Erstellen von Endpunktressourcen

Das für den Endpunkt verwendete Event Grid-Thema, der Event Hub oder das Service Bus-Thema muss bereits vorhanden sein, um einen Endpunkt mit Azure Digital Twins zu verknüpfen.

Verwenden Sie die folgenden Tabelle, um herauszufinden, welche Ressourcen eingerichtet werden sollten, bevor Sie den Endpunkt erstellen.

| Endpunkttyp | Erforderliche Ressourcen (mit Erstellungsanweisungen verknüpft) |
| --- | --- |
| Event Grid-Endpunkt | [Event Grid-Thema](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs-Endpunkt | [Event&nbsp;Hubs-Namespace](../event-hubs/event-hubs-create.md)<br/><br/>[Event Hub](../event-hubs/event-hubs-create.md)<br/><br/>(Optional) [Autorisierungsregel](../event-hubs/authorize-access-shared-access-signature.md) für schlüsselbasierte Authentifizierung | 
| Service Bus-Endpunkt | [Service Bus-Namespace](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus-Topic](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (Optional) [Autorisierungsregel](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) für schlüsselbasierte Authentifizierung|

### <a name="create-the-endpoint"></a>Erstellen des Endpunkts 

Sobald Sie die Endpunktressourcen erstellt haben, können Sie sie für einen Azure Digital Twins-Endpunkt verwenden. 

# <a name="portal"></a>[Portal](#tab/portal) 

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Seite Ihrer Instanz, um einen neuen Endpunkt zu erstellen. Die Instanz finden Sie, indem Sie ihren Namen in die Suchleiste im Portal eingeben.

1. Wählen Sie im Instanzmenü die Option _Endpunkte_ aus. Wählen Sie anschließend auf der darauffolgenden Seite *Endpunkte* die Option *+ Endpunkt erstellen* aus. Dadurch wird die Seite *Endpunkt erstellen* geöffnet. Dort füllen Sie in den nächsten Schritten die Felder aus.

    :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-grid.png" alt-text="Screenshot des Erstellens eines Endpunkts vom Typ „Event Grid“ im Azure-Portal." lightbox="media/how-to-manage-routes/create-endpoint-event-grid.png":::

1. Geben Sie einen **Namen** für Ihren Endpunkt ein, und wählen Sie den **Endpunkttyp**.

1. Geben Sie die restlichen für Ihren Endpunkttyp erforderlichen Informationen an, einschließlich Ihres Abonnements und der [oben](#prerequisite-create-endpoint-resources) beschriebenen Endpunktressourcen.
    1. Nur für Event Hub- und Service Bus-Endpunkte müssen Sie einen **Authentifizierungstyp** auswählen. Sie können die schlüsselbasierte Authentifizierung mit einer vorab erstellten Autorisierungsregel verwenden. Sie können auch die identitätsbasierte Authentifizierung verwenden, wenn Sie den Endpunkt mit einer [verwalteten Identität](concepts-security.md#managed-identity-for-accessing-other-resources-preview) für Ihre Azure Digital Twins-Instanz nutzen. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Screenshot des Erstellens eines Endpunkts vom Typ „Event Hub“ im Azure-Portal." lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Schließen Sie die Erstellung Ihres Endpunkts ab, indem Sie auf _Speichern_ klicken.

>[!IMPORTANT]
> Damit Sie die identitätsbasierte Authentifizierung erfolgreich für Ihren Endpunkt verwenden können, müssen Sie eine verwaltete Identität für Ihre Instanz erstellen, indem Sie die Schritte unter [Routen von Ereignissen mit einer verwalteten Identität](how-to-route-with-managed-identity.md) befolgen.

Nach Erstellung Ihres Endpunkts können Sie überprüfen, ob der Endpunkt erfolgreich erstellt wurde, indem Sie das Benachrichtigungssymbol oben in der Leiste im Azure-Portal überprüfen: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-notifications.png" alt-text="Screenshot der Benachrichtigung zum Überprüfen der Erstellung eines Endpunkts im Azure-Portal.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Falls bei der Erstellung des Endpunkts ein Fehler auftritt, sollten Sie die Fehlermeldung beachten und den Vorgang nach einigen Minuten wiederholen.

Sie können auch den Endpunkt anzeigen, der erstellt wurde, indem Sie auf der Seite *Endpunkte* für Ihre Azure Digital Twins-Instanz nachsehen.

Nun ist das Event Grid-, Event Hub- oder Service Bus-Thema als Endpunkt innerhalb von Azure Digital Twins unter dem Namen verfügbar, den Sie für den Endpunkt ausgewählt haben. Normalerweise verwenden Sie diesen Namen als Ziel einer **Ereignisroute**, die Sie [später in diesem Artikel](#create-an-event-route) erstellen.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli) 

In den folgenden Beispielen wird gezeigt, wie Endpunkte mithilfe des Befehls [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) für die [Azure Digital Twins-CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) erstellt werden. Ersetzen Sie die Platzhalter in den Befehlen durch die Details Ihrer eigenen Ressourcen.

Erstellen eines Event Grid-Endpunkts:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Erstellen eines Event Hubs-Endpunkts (schlüsselbasierte Authentifizierung):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Erstellen eines Service Bus-Themenendpunkts (schlüsselbasierte Authentifizierung):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Nachdem Sie diese Befehle erfolgreich ausgeführt haben, steht das Event Grid-, Event Hub- oder Service Bus-Thema mit dem von Ihnen mit dem `--endpoint-name`-Argument angegebenen Namen als Endpunkt in Azure Digital Twins zur Verfügung. Normalerweise verwenden Sie diesen Namen als Ziel einer **Ereignisroute**, die Sie [später in diesem Artikel](#create-an-event-route) erstellen.

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Erstellen eines Endpunkts mit identitätsbasierter Authentifizierung

Sie können auch einen Endpunkt mit identitätsbasierter Authentifizierung erstellen, um den Endpunkt mit einer [verwalteten Identität](concepts-security.md#managed-identity-for-accessing-other-resources-preview) zu verwenden. Diese Option ist nur für Event Hub- und Service Bus-Endpunkte verfügbar (sie wird nicht für Event Grid unterstützt).

Den CLI-Befehl zum Erstellen dieser Art von Endpunkt finden Sie unten. Sie benötigen die folgenden Werte, um die Platzhalter im Befehl einzuschließen:
* die Azure-Ressourcen-ID Ihrer Azure Digital Twins-Instanz
* einen Endpunktnamen
* einen Endpunkttyp
* den Namespace der Endpunktressource
* den Namen des Event Hub- oder Service Bus-Themas
* den Speicherort Ihrer Azure Digital Twins-Instanz

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

### <a name="create-an-endpoint-with-dead-lettering"></a>Erstellen eines Endpunkts mit unzustellbaren Nachrichten

Wenn ein Endpunkt innerhalb eines bestimmten Zeitraums oder nach einer bestimmten Anzahl von Übermittlungsversuchen nicht übermittelt werden kann, kann Event Grid das nicht übermittelte Ereignis an ein Speicherkonto senden. Dieser Prozess wird als Speicherung **unzustellbarer Nachrichten** bezeichnet.

Sie können die erforderlichen Speicherressourcen mithilfe des [Azure-Portals](https://ms.portal.azure.com/#home) oder der [Azure Digital Twins-CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) einrichten. Endpunkte mit aktivierter Funktion für unzustellbare Nachrichten müssen Sie jedoch mit der [Azure Digital Twins-CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) oder mit [Steuerungsebenen-APIs](concepts-apis-sdks.md#overview-control-plane-apis) einrichten.

Weitere Informationen zu unzustellbaren Nachrichten finden Sie unter [Ereignisrouten](concepts-route-events.md#dead-letter-events). Anweisungen zum Einrichten eines Endpunkts mit unzustellbaren Nachrichten finden Sie im Rest dieses Abschnitts.

#### <a name="set-up-storage-resources"></a>Einrichten von Speicherressourcen

Wenn Sie den Speicherort für unzustellbare Nachrichten festlegen möchten, benötigen Sie ein [Speicherkonto](../storage/common/storage-account-create.md?tabs=azure-portal) mit einem [Containersetup](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) in Ihrem Azure-Konto. 

Später geben Sie den URI für diesen Container an, wenn Sie den Endpunkt erstellen. Die Position der unzustellbaren Nachrichten wird dem Endpunkt als Container-URI mit einem [SAS-Token](../storage/common/storage-sas-overview.md) bereitgestellt. Dieses Token benötigt die `write`-Berechtigung für den Zielcontainer innerhalb des Speicherkontos. Der vollständig formatierte **SAS-URI für unzustellbare Nachrichten** weist das Format `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` auf.

Führen Sie die folgenden Schritte aus, um diese Speicherressourcen in Ihrem Azure-Konto einzurichten, um so die Einrichtung der Endpunktverbindung im nächsten Abschnitt vorzubereiten.

1. Befolgen Sie die Schritte in [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md?tabs=azure-portal), um ein **Speicherkonto** in Ihrem Azure-Abonnement zu erstellen. Notieren Sie den Namen des Speicherkontos zur späteren Verwendung.
1. Befolgen Sie die Schritte in [Erstellen eines Containers](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container), um einen **Container** innerhalb des neuen Speicherkontos zu erstellen. Notieren Sie sich den Containernamen, um ihn später zu verwenden.

##### <a name="create-a-sas-token"></a>Erstellen eines SAS-Token

Erstellen Sie als nächstes ein **SAS-Token** für Ihr Speicherkonto, das der Endpunkt für den Zugriff darauf verwenden kann.

# <a name="portal"></a>[Portal](#tab/portal)

1. Navigieren Sie zunächst zu Ihrem Speicherkonto im [Azure-Portal](https://ms.portal.azure.com/#home) (Sie finden es über die Suchleiste im Portal).
1. Wählen Sie auf der Speicherkontoseite den Link _Shared Access Signature_ in der linken Navigationsleiste, um mit der Einrichtung des SAS-Tokens zu beginnen.

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-1.png" alt-text="Screenshot der Seite „Speicherkonto“ im Azure-Portal." lightbox="./media/how-to-manage-routes/generate-sas-token-1.png":::

1. Wählen Sie auf der Seite *Shared Access Signature* unter *Zulässige Dienste* und *Zulässige Ressourcentypen* die gewünschten Einstellungen aus. Sie müssen mindestens ein Feld in jeder Kategorie auswählen. Wählen Sie unter *Zulässige Berechtigungen* die Option **Schreiben** aus (Sie können auch andere Berechtigungen auswählen).
1. Stellen Sie für die übrigen Einstellungen die gewünschten Werte ein.
1. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche _SAS und Verbindungszeichenfolge generieren_, um das SAS-Token zu generieren. 

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-2.png" alt-text="Screenshot der Seite „Speicherkonto“ im Azure-Portal mit der gesamten Einstellungsauswahl zum Generieren eines SAS-Tokens." lightbox="./media/how-to-manage-routes/generate-sas-token-2.png"::: 

1. Hierdurch werden im unteren Bereich der gleichen Seite unter der Einstellungsauswahl mehrere Werte für die SAS und Verbindungszeichenfolgen generiert. Scrollen Sie nach unten, um die Werte anzuzeigen, und klicken Sie auf das Symbol *In Zwischenablage kopieren*, um den Wert des **SAS-Tokens** zu kopieren. Speichern Sie ihn zur späteren Verwendung.

    :::image type="content" source="./media/how-to-manage-routes/copy-sas-token.png" alt-text="Screenshot der Seite „Speicherkonto“ im Azure-Portal, der hervorhebt, wie das SAS-Token kopiert wird, um es im Geheimnis für unzustellbare Nachrichten zu verwenden." lightbox="./media/how-to-manage-routes/copy-sas-token.png":::

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

1. Rufen Sie Ihre Speicherkontoschlüssel mit dem folgenden Befehl ab, und kopieren Sie den Wert für einen Ihrer Schlüssel:

    ```azurecli
    az storage account keys list --account-name <storage-account-name>
    ```

1. Wählen Sie ein Ablaufdatum aus, und generieren Sie das SAS-Token für Ihr Speicherkonto mit dem folgenden Befehl:

    ```azurecli
    az storage account generate-sas --account-name <storage-account-name> --account-key <storage-account-key> --expiry <expiration-date> --services bfqt --resource-types o --permissions w
    ```

    Die Ausgabe dieses Befehls ist das SAS-Token. Kopieren Sie den SAS-Tokenwert zur späteren Verwendung.

    > [!NOTE]
    > Dieser Befehl enthält die Dienste „**b** lob“, „**f** ile“, „**q** ueue“ und „**t** able“ , *den* Ressourcentyp „**o** bject“ *und* lässt „**w** rite“-*Berechtigungen zu*.
    >
    > Weitere Informationen zum Befehl `az storage account generate-sas` und den zugehörigen Parametern finden Sie in der [Referenz zur Azure-Befehlszeilenschnittstelle](/cli/azure/storage/account?view=azure-cli-latest&preserve-view=true#az_storage_account_generate_sas).

---

#### <a name="create-the-dead-letter-endpoint"></a>Erstellen des Endpunkts für unzustellbare Nachrichten

# <a name="portal"></a>[Portal](#tab/portal)

Zum Erstellen eines Endpunkts mit aktivierten unzustellbaren Nachrichten müssen Sie die [CLI-Befehle](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) oder die [APIs der Steuerungsebene](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) anstelle des Azure-Portals verwenden, um den Endpunkt zu erstellen.

Um Anweisungen dazu zu erhalten, wie Sie dies mit der Azure CLI tun, wechseln Sie zur Registerkarte CLI für diesen Abschnitt.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Fügen Sie die folgenden Parameter für unzustellbare Nachrichten zum Befehl [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) für die [Azure Digital Twins-CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) hinzu, um einen Endpunkt mit aktivierter Funktion für unzustellbare Nachrichten zu erstellen.

Der Wert für den Parameter ist der **SAS-URI für unzustellbare Nachrichten**, der aus dem Speicherkontonamen, dem Containernamen und dem SAS-Token besteht, die Sie im [vorherigen Abschnitt](#set-up-storage-resources) gesammelt haben. Dieser Parameter erstellt den Endpunkt mit der schlüsselbasierten Authentifizierung.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Fügen Sie diesen Parameter am Ende des Befehls zum Erstellen des Endpunkts aus dem Abschnitt [Erstellen des Endpunkts](#create-the-endpoint) hinzu, um einen Endpunkt des gewünschten Typs zu erstellen, für den die Funktion für unzustellbare Nachrichten aktiviert ist.

Alternativ können Sie Endpunkte für unzustellbare Nachrichten mithilfe der [Azure Digital Twins-Steuerungsebenen-APIs](concepts-apis-sdks.md#overview-control-plane-apis) anstelle der CLI verwenden. Lesen Sie hierzu die [DigitalTwinsEndpoint-Dokumentation](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate), um herauszufinden, wie Sie die Anforderung strukturieren und die Parameter für unzustellbare Nachrichten hinzufügen.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Erstellen eines Endpunkts für unzustellbare Nachrichten mit identitätsbasierter Authentifizierung

Sie können auch einen Endpunkt für unzustellbare Nachrichten mit identitätsbasierter Authentifizierung erstellen, um den Endpunkt mit einer [verwalteten Identität](concepts-security.md#managed-identity-for-accessing-other-resources-preview) zu verwenden. Diese Option ist nur für Event Hub- und Service Bus-Endpunkte verfügbar (sie wird nicht für Event Grid unterstützt).

Verwenden Sie zum Erstellen dieser Art von Endpunkt denselben CLI-Befehl wie oben, um einen [Endpunkt mit identitätsbasierter Authentifizierung](#create-an-endpoint-with-identity-based-authentication) mit einem zusätzlichen Feld in den JSON-Nutzdaten für einen `deadLetterUri` zu erstellen.

Im Folgenden finden Sie die Werte, die Sie benötigen, um die Platzhalter im Befehl einzuschließen:
* die Azure-Ressourcen-ID Ihrer Azure Digital Twins-Instanz
* einen Endpunktnamen
* einen Endpunkttyp
* den Namespace der Endpunktressource
* den Namen des Event Hub- oder Service Bus-Themas
* Details zum **SAS-URI für unzustellbare Nachrichten**: Speicherkontoname, Containername
* den Speicherort Ihrer Azure Digital Twins-Instanz

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

#### <a name="message-storage-schema"></a>Nachrichtenspeicherschema

Sobald der Endpunkt für unzustellbare Nachrichten eingerichtet ist, werden unzustellbare Nachrichten im folgenden Format in Ihrem Speicherkonto gespeichert:

`<container>/<endpoint-name>/<year>/<month>/<day>/<hour>/<event-ID>.json`

Unzustellbare Nachrichten entsprechen dem Schema des ursprünglichen Ereignisses, das an den ursprünglichen Endpunkt übermittelt werden sollte.

Im Folgenden finden Sie ein Beispiel für eine unzustellbare Nachricht für eine [Benachrichtigung zur Erstellung eines Zwillings](concepts-event-notifications.md#digital-twin-lifecycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Erstellen einer Ereignisroute

Wenn Sie tatsächlich Daten von Azure Digital Twins an einen Endpunkt senden möchten, müssen Sie eine **Ereignisroute** definieren. Diese Routen ermöglichen es Entwicklern, den Ereignisdatenfluss im gesamten System und zu Downstreamdiensten einzurichten. Eine einzelne Route kann es ermöglichen, dass mehrere Benachrichtigungen und Ereignistypen ausgewählt werden. Weitere Informationen zu Ereignisrouten finden Sie unter [Routing von Azure Digital Twins-Ereignissen](concepts-route-events.md).

**Voraussetzung**: Sie müssen wie weiter oben in diesem Artikel beschrieben Endpunkte erstellen, bevor Sie mit dem Erstellen einer Route fortfahren können. Nachdem die Einrichtung Ihrer Endpunkte abgeschlossen ist, können Sie mit dem Erstellen einer Ereignisroute fortfahren.

>[!NOTE]
>Wenn Sie Ihre Endpunkte gerade erst bereitgestellt haben, vergewissern Sie sich, dass die Bereitstellung abgeschlossen ist, **bevor** Sie versuchen, die Endpunkte für eine neue Ereignisroute zu verwenden. Wenn die Routenbereitstellung fehlschlägt, weil die Endpunkte noch nicht bereit sind, warten Sie einige Minuten, und versuchen Sie es dann erneut.
>
> Wenn Sie eine Skripterstellung für diesen Flow durchführen, sollten Sie dies berücksichtigen, indem Sie eine Wartezeit von zwei bis drei Minuten vorsehen, damit der Endpunktdienst die Bereitstellung abschließen kann, bevor mit der Routeneinrichtung fortgefahren wird.

Eine Routendefinition kann folgende Elemente enthalten:
* Den Routennamen, den Sie verwenden möchten.
* Den Namen des Endpunkts, den Sie verwenden möchten.
* Ein Filter, der definiert, welche Ereignisse an den Endpunkt gesendet werden.
    - Um die Route zu deaktivieren, damit keine Ereignisse gesendet werden, verwenden Sie den Filterwert `false`.
    - Um eine Route zu aktivieren, für die keine bestimmte Filterung gilt, verwenden Sie den Filterwert `true`.
    - Ausführliche Informationen zu allen anderen Filtertypen finden Sie unten im Abschnitt [Filtern von Ereignissen](#filter-events).

Wenn es keinen Routennamen gibt, werden keine Nachrichten außerhalb von Azure Digital Twins weitergeleitet. Wenn es einen Routennamen gibt und der Filter `true` ergibt, werden alle Nachrichten an den Endpunkt weitergeleitet. Wenn es einen Routennamen gibt und ein anderer Filter hinzugefügt wird, werden Nachrichten auf der Grundlage des Filters gefiltert.

Ereignisrouten können mit dem [Azure-Portal](https://portal.azure.com), den [EventRoutes-Datenebenen-APIs](/rest/api/digital-twins/dataplane/eventroutes) oder den [az dt route-CLI-Befehlen](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true) erstellt werden. Der Rest dieses Abschnitts führt Sie durch den Erstellungsprozess.

# <a name="portal"></a>[Portal](#tab/portal2)

Navigieren Sie zum Erstellen einer Ereignisroute im [Azure-Portal](https://portal.azure.com) zur Detailseite für Ihre Azure Digital Twins-Instanz. (Sie können auf die Instanz zugreifen, indem Sie ihren Namen im Portal in der Suchleiste eingeben.)

Wählen Sie im Instanzmenü die Option _Ereignisrouten_ aus. Wählen Sie anschließend auf der Seite *Ereignisrouten* die Option *+ Create an event route* (+ Ereignisroute erstellen) aus. 

Wählen Sie auf der Seite *Create an event route* (Ereignisroute erstellen) mindestens Folgendes aus:
* Einen Namen für Ihre Route im Feld _Name_.
* Den _Endpunkt_, den Sie zum Erstellen der Route verwenden möchten. 

Zum Aktivieren der Route müssen Sie auch einen **Ereignisroutenfilter hinzufügen**, für den mindestens `true` festgelegt ist. (Wenn Sie den Standardwert `false` beibehalten, wird die Route erstellt, aber es werden keine Ereignisse dafür gesendet.) Verwenden Sie hierfür den Umschalter für _Erweiterter Editor_, um ihn zu aktivieren, und geben Sie im Feld *Filter* die Zeichenfolge `true` ein.

:::image type="content" source="media/how-to-manage-routes/create-event-route-no-filter.png" alt-text="Screenshot des Erstellens einer Ereignisroute für Ihre Instanz im Azure-Portal." lightbox="media/how-to-manage-routes/create-event-route-no-filter.png":::

Wählen Sie nach Abschluss des Vorgangs die Schaltfläche _Speichern_ aus, um Ihre Ereignisroute zu erstellen.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli2)

Routen können auch mithilfe der [az dt route](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true)-Befehle für die Azure Digital Twins-CLI verwaltet werden. 

Weitere Informationen zur Verwendung der Befehlszeilenschnittstelle und zu den verfügbaren Befehlen finden Sie unter [Befehlssatz der Azure Digital Twins-Befehlszeilenschnittstelle](concepts-cli.md).

# <a name="net-sdk"></a>[.NET SDK](#tab/sdk2)

In diesem Abschnitt wird gezeigt, wie Sie eine Ereignisroute mit dem [.NET SDK (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) erstellen.

`CreateOrReplaceEventRouteAsync` ist der SDK-Aufruf, der zum Hinzufügen einer Ereignisroute verwendet wird. Hier ist ein Beispiel für seine Verwendung:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Alle SDK-Funktionen sind in synchronen und asynchronen Versionen enthalten.

#### <a name="event-route-sample-sdk-code"></a>Beispielcode für Ereignisroute mit SDK

Die folgende Beispielmethode zeigt, wie Sie eine Ereignisroute mit dem C#-SDK erstellen, auflisten und löschen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

---

## <a name="filter-events"></a>Filtern von Ereignissen

Wie oben beschrieben, verfügen Routen über das Feld **Filter**. Wenn der Filterwert für Ihre Route `false` lautet, werden keine Ereignisse an Ihren Endpunkt gesendet. 

Nachdem mindestens `true` als Filterwert ausgewählt wurde, erhalten Endpunkte verschiedene Ereignisse von Azure Digital Twins:
* Telemetrie, die von [digitalen Zwillingen](concepts-twins-graph.md) unter Verwendung der Azure Digital Twins-Dienst-API ausgelöst wird.
* Änderungsbenachrichtigungen für Zwillingseigenschaften, ausgelöst bei Eigenschaftsänderungen für einen Zwilling in der Azure Digital Twins-Instanz.
* Lebenszyklusereignisse, die ausgelöst werden, wenn Zwillinge oder Beziehungen erstellt oder gelöscht werden.

Sie können die Ereignistypen beschränken, die gesendet werden, indem Sie einen spezifischeren Filter definieren.

>[!NOTE]
> Filter unterscheiden zwischen **Groß- und Kleinschreibung** und müssen mit der Groß-/Kleinschreibung der Nutzlast übereinstimmen. 
>
> Für Telemetriefilter bedeutet dies, dass die Groß-/Kleinschreibung mit der Groß-/Kleinschreibung in der vom Gerät gesendeten Telemetrie übereinstimmen muss und nicht unbedingt mit der im Modell des Zwillings definierten Groß-/Kleinschreibung.

# <a name="portal"></a>[Portal](#tab/portal3)

Verwenden Sie auf der Seite *Create an event route* (Ereignisroute erstellen) den Abschnitt „Add an event route filter“ (Ereignisroutenfilter hinzufügen), um beim Erstellen einer Ereignisroute einen Ereignisfilter hinzuzufügen. 

Sie können entweder eine Auswahl aus einigen grundlegenden allgemeinen Filteroptionen treffen oder die erweiterten Filteroptionen verwenden, um Ihre eigenen benutzerdefinierten Filter zu schreiben.

### <a name="use-the-basic-filters"></a>Verwenden der grundlegenden Filter

Erweitern Sie zur Verwendung der grundlegenden Filter die Option _Ereignistypen_, und wählen Sie die Kontrollkästchen für die Ereignisse aus, die Sie an Ihren Endpunkt senden möchten. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-1.png" alt-text="Screenshot des Erstellens einer Ereignisroute mit einem einfachen Filter im Azure-Portal, der die Kontrollkästchen für die Ereignisse hervorhebt.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Dies führt dazu, dass das Filtertextfeld automatisch mit dem Text des von Ihnen ausgewählten Filters gefüllt wird:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-2.png" alt-text="Screenshot des Erstellens einer Ereignisroute mit einem einfachen Filter im Azure-Portal. Automatisch eingefügter Filtertext nach der Auswahl der Ereignisse.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="use-the-advanced-filters"></a>Verwenden der erweiterten Filter

Alternativ können Sie die erweiterte Filteroption verwenden, um Ihre eigenen benutzerdefinierten Filter zu schreiben.

Aktivieren Sie _Erweiterter Editor_ mit dem Umschalter, um ihn zu aktivieren, wenn Sie eine Ereignisroute mit erweiterten Filteroptionen erstellen möchten. Sie können im Feld *Filter* dann Ihre eigenen Ereignisfilter schreiben:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-advanced.png" alt-text="Screenshot des Erstellens einer Ereignisroute mit einem erweiterten Filter im Azure-Portal.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

# <a name="api"></a>[API](#tab/api)

Sie können die APIs verwenden, um benutzerdefinierte Filter zu schreiben. Um einen Filter hinzuzufügen, können Sie eine PUT-Anforderung für `https://<Your-Azure-Digital-Twins-host-name>/eventRoutes/<event-route-name>?api-version=2020-10-31` mit folgendem Textkörper verwenden:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

---

### <a name="supported-route-filters"></a>Unterstützte Routenfilter

Dies sind die unterstützten Routenfilter.

| Filtername | BESCHREIBUNG | Filtertextschema | Unterstützte Werte | 
| --- | --- | --- | --- |
| True/False | Ermöglicht das Erstellen einer Route ohne Filterung oder das Deaktivieren einer Route, sodass keine Ereignisse gesendet werden. | `<true/false>` | `true` = Route ist ohne Filterung aktiviert. <br> `false` = Route ist deaktiviert. |
| type | Der [Ereignistyp](concepts-route-events.md#types-of-event-messages), der durch Ihre digitale Zwillingsinstanz fließt. | `type = '<event-type>'` | Folgende Werte für Ereignistypen sind möglich: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| `Source` | Der Name der Azure Digital Twins-Instanz. | `source = '<host-name>'`| Dies sind die möglichen Werte für Hostnamen: <br> **Für Benachrichtigungen**: `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net` <br> **Für Telemetrie**: `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net/<twin-ID>`|
| Subject | Eine Beschreibung des Ereignisses im Zusammenhang mit der oben genannten Ereignisquelle. | `subject = '<subject>'` | Folgende Werte für den Betreff sind möglich: <br>**Für Benachrichtigungen**: Der Betreff ist `<twin-ID>` <br> oder ein URI-Format für Betreffelemente, die durch mehrere Teile oder IDs eindeutig identifiziert werden:<br>`<twin-ID>/relationships/<relationship-ID>`<br> **Für Telemetrie**: Der Betreff ist der Komponentenpfad (wenn die Telemetrie von einer Zwillingskomponente ausgegeben wird). Beispiel: `comp1.comp2`. Wenn die Telemetrie nicht von einer Komponente ausgegeben wird, dann ist ihr Betrefffeld leer. |
| Datenschema | DTDL-Modell-ID | `dataschema = '<model-dtmi-ID>'` | **Für Telemetrie**: Das Datenschema ist die Modell-ID des Zwillings oder der Komponente, der bzw. die die Telemetrie ausgibt. Zum Beispiel, `dtmi:example:com:floor4;2` <br>**Für Benachrichtigungen (Erstellen/Löschen)** : Der Zugriff auf das Datenschema kann im Benachrichtigungstext unter `$body.$metadata.$model` erfolgen. <br>**Für Benachrichtigungen (Aktualisieren)** : Der Zugriff auf das Datenschema kann im Benachrichtigungstext unter `$body.modelId` erfolgen.|
| Inhaltstyp | Der Inhaltstyp des Datenwerts. | `datacontenttype = '<content-type>'` | Der Inhaltstyp lautet `application/json`. |
| Spezifikationsversion | Die Version des von Ihnen verwendeten Ereignisschemas. | `specversion = '<version>'` | Die Version muss `1.0` sein. Dies gibt das CloudEvents-Schema, Version 1.0, an. |
| Benachrichtigungstext | Verweisen auf eine beliebige Eigenschaft im Feld `data` einer Benachrichtigung. | `$body.<property>` | Beispiele für Benachrichtigungen finden Sie unter [Ereignisbenachrichtigungen](concepts-event-notifications.md). Auf jede Eigenschaft im Feld `data` kann mit `$body` verwiesen werden.

>[!NOTE]
> Azure Digital Twins unterstützt derzeit das Filtern von Ereignissen basierend auf Feldern innerhalb eines Arrays nicht. Dies schließt auch das Filtern nach Eigenschaften innerhalb eines `patch`-Abschnitts einer [Änderungsbenachrichtigung für digitale Zwillinge](concepts-event-notifications.md#digital-twin-change-notifications) ein.

Die folgenden Datentypen werden als Werte unterstützt, die von Verweisen auf die oben aufgeführten Daten zurückgegeben werden:

| Datentyp | Beispiel |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twin-ID>')`|
|**Integer**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**NULL**|`$body.prop != null`|

Die folgenden Operatoren werden beim Definieren von Routenfiltern unterstützt:

|Familie|Operatoren|Beispiel|
|-|-|-|
|Logisch|AND, OR, ( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Vergleich|<, <=, >, >=, =, !=|`$body.temperature <= 5.5`

Die folgenden Funktionen werden beim Definieren von Routenfiltern unterstützt:

|Funktion|BESCHREIBUNG|Beispiel|
|--|--|--|
|STARTS_WITH(x,y)|Gibt TRUE zurück, wenn der Wert `x` mit der Zeichenfolge `y` beginnt.|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | Gibt TRUE zurück, wenn der Wert `x` mit der Zeichenfolge `y` endet.|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| Gibt TRUE zurück, wenn der Wert `x` die Zeichenfolge `y` enthält.|`CONTAINS(subject, '<twin-ID>')`|

Wenn Sie einen Filter implementieren oder aktualisieren, kann es einige Minuten dauern, bis sich die Änderung in der Datenpipeline widerspiegelt.

## <a name="monitor-event-routes"></a>Überwachen von Ereignisrouten

Routingmetriken wie Anzahl, Wartezeit und Ausfallrate können im [Azure-Portal](https://portal.azure.com/) angezeigt werden. 

Suchen Sie auf der Startseite des Portals nach Ihrer Azure Digital Twins-Instanz, um die Details abzurufen. Wählen Sie links im Navigationsmenü der Azure Digital Twins-Instanz die Option **Metriken** aus, um die Seite *Metriken* aufzurufen.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot der Seite „Metriken“ für Azure Digital Twins":::

Von hier aus können Sie die Metriken für Ihre Instanz anzeigen und benutzerdefinierte Ansichten erstellen.

Weitere Informationen zum Anzeigen von Azure Digital Twins-Metriken finden Sie unter [Anzeigen von Metriken mit Azure Monitor](troubleshoot-metrics.md).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die verschiedenen Arten von Ereignismeldungen, die Sie erhalten können:
* [Ereignisbenachrichtigungen](concepts-event-notifications.md)