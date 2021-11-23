---
title: Exportieren von Daten aus Azure IoT Central | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den neuen Datenexport verwenden, um Ihre IoT-Daten nach Azure und benutzerdefinierten Cloudzielen zu exportieren.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/20/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: a4a941d114d233e723d853d12386cb42834d3e19
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493791"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports

In diesem Artikel wird beschrieben, wie Sie den Datenexport in Azure IoT Central verwenden. Verwenden Sie dieses Feature für den fortlaufenden Export von gefilterten und angereicherten IoT-Daten aus Ihrer IoT Central-Anwendung. Beim Datenexport werden Änderungen nahezu in Echtzeit in andere Teile Ihrer Cloudlösung gepusht, damit Sie umsetzbare Einblicke, Analyseergebnisse und Speicherinformationen erhalten.

Beispielsweise können Sie folgende Aktionen ausführen:

- Fortlaufendes Exportieren von Telemetriedaten, Eigenschaftsänderungen, Gerätekonnektivität, Gerätelebenszyklus und Lebenszyklusdaten der Gerätevorlage im JSON-Format nahezu in Echtzeit
- Filtern der Datenströme zum Exportieren von Daten, die die benutzerdefinierten Bedingungen erfüllen.
- Anreichern der Datenströme mit benutzerdefinierten Werten und Eigenschaftswerten aus dem Gerät.
- Transformieren der Datenströme, um deren Form und Inhalt zu ändern.
- Senden der Daten an Ziele wie Azure Event Hubs, Azure Data Explorer, Azure Service Bus, Azure Blob Storage und Webhookendpunkte.

> [!Tip]
> Wenn Sie den Datenexport aktivieren, erhalten Sie nur die Daten ab dem jeweiligen Aktivierungszeitpunkt. Momentan können Daten nicht für Zeiten abgerufen werden, in denen der Datenexport deaktiviert war. Wenn Sie mehr Verlaufsdaten beibehalten möchten, aktivieren Sie den Datenexport frühzeitig.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Datenexportfeatures verwenden können, müssen Sie eine [V3-Anwendung](howto-faq.yml#how-do-i-get-information-about-my-application-) haben und über die Berechtigung [Datenexport](howto-manage-users-roles.md) verfügen.

Wenn Sie eine V2-Anwendung haben, lesen Sie [Migrieren Ihrer V2-IoT Central-Anwendung zu V3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Einrichten des Exportziels

Ihr Exportziel muss vorhanden sein, bevor Sie Ihren Datenexport konfigurieren. Die folgenden Zieltypen sind zurzeit verfügbar:

- Azure Event Hubs
- Azure Service Bus-Warteschlange
- Azure Service Bus-Thema
- Azure Blob Storage
- Azure-Daten-Explorer
- Webhook

### <a name="connection-options"></a>Verbindungsoptionen

Für die Azure-Dienstziele können Sie die Verbindung entweder mit einer *Verbindungszeichenfolge* oder mit einer [verwalteten Identität](../../active-directory/managed-identities-azure-resources/overview.md) konfigurieren. Verwaltete Identitäten sind aus folgenden Gründen sicherer:

- Sie speichern die Anmeldeinformationen für Ihre Ressource nicht in einer Verbindungszeichenfolge in Ihrer IoT Central-Anwendung.
- Die Anmeldeinformationen sind automatisch an die Lebensdauer Ihrer IoT Central-Anwendung gebunden.
- Verwaltete Identitäten rotieren ihre Sicherheitsschlüssel automatisch regelmäßig.

IoT Central verwendet derzeit [systemseitig zugewiesene verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Wenn Sie eine verwaltete Identität konfigurieren, müssen Sie einen *Bereich* und eine *Rolle* festlegen:

- Mit dem Bereich wird definiert, wo die verwaltete Identität verwendet werden kann. Sie können zum Beispiel eine Azure-Ressourcengruppe als Bereich festlegen. In diesem Fall müssen sich die IoT Central-Anwendung und das Ziel in derselben Ressourcengruppe befinden.
- Mit der Rolle wird definiert, über welche Berechtigungen die IoT Central-Anwendung im Zieldienst verfügt. Damit eine IoT Central-Anwendung Daten an einen Event Hub senden kann, muss der verwalteten Identität beispielsweise die Rolle **Azure Event Hubs Data Sender** (Azure Event Hubs-Datensender) zugewiesen sein.

In diesem Artikel wird gezeigt, wie Sie eine verwaltete Identität im Azure-Portal erstellen. Sie können für die Erstellung einer verwalteten Identität auch die Azure CLI verwenden. Weitere Informationen finden Sie unter [Zuweisen des Zugriffs auf eine Ressource für eine verwaltete Identität mithilfe der Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).

### <a name="create-an-event-hubs-destination"></a>Erstellen eines Event Hubs-Ziels

# <a name="connection-string"></a>[Verbindungszeichenfolge](#tab/connection-string)

Wenn Sie keinen vorhandenen Event Hubs-Namespace als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen [neuen Event Hubs-Namespace im Azure-Portal](https://portal.azure.com/#create/Microsoft.EventHub). Weitere Informationen erhalten Sie in der [Azure Event Hubs-Dokumentation](../../event-hubs/event-hubs-create.md).

1. Erstellen Sie einen Event Hub in Ihrem Event Hubs-Namespace. Wechseln Sie zu Ihrem Namespace, und wählen Sie oben **+ Event Hub** aus, um eine Event Hub-Instanz zu erstellen.

1. Generieren Sie einen Schlüssel, der verwendet werden soll, wenn Sie Ihren Datenexport in IoT Central einrichten:

    - Wählen Sie die von Ihnen erstellte Event Hub-Instanz aus.
    - Wählen Sie **Einstellungen > Richtlinien für gemeinsamen Zugriff** aus.
    - Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
    - Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge. Mithilfe dieser Verbindungszeichenfolge richten Sie ein neues Ziel in IoT Central ein.
    - Alternativ können Sie eine Verbindungszeichenfolge für den gesamten Event Hubs-Namespace generieren:
        1. Wechseln Sie im Azure-Portal zu Ihrem Event Hubs-Namespace.
        2. Wählen Sie unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus.
        3. Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
        4. Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge.

# <a name="managed-identity"></a>[Verwaltete Identität](#tab/managed-identity)

Wenn Sie keinen vorhandenen Event Hubs-Namespace als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen [neuen Event Hubs-Namespace im Azure-Portal](https://portal.azure.com/#create/Microsoft.EventHub). Weitere Informationen erhalten Sie in der [Azure Event Hubs-Dokumentation](../../event-hubs/event-hubs-create.md).

1. Erstellen Sie einen Event Hub in Ihrem Event Hubs-Namespace. Wechseln Sie zu Ihrem Namespace, und wählen Sie oben **+ Event Hub** aus, um eine Event Hub-Instanz zu erstellen.

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

So konfigurieren Sie die Berechtigungen:

1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** den Bereich und das Abonnement aus, die Sie verwenden möchten.

    > [!TIP]
    > Wenn sich Ihre IoT Central-Anwendung und Ihr Event Hub in derselben Ressourcengruppe befinden, können Sie für den Bereich **Ressourcengruppe** festlegen und dann die entsprechende Ressourcengruppe auswählen.

1. Wählen Sie als **Rolle** **Azure Event Hubs Data Sender** (Azure Event Hubs-Datensender) aus.

1. Wählen Sie **Speichern** aus. Die verwaltete Identität für Ihre IoT Central-Anwendung ist jetzt konfiguriert.

Informationen dazu, wie Sie Ihren Event Hub besser schützen und nur den Zugriff durch vertrauenswürdige Dienste mit verwalteten Identitäten zulassen können, finden Sie in den folgenden Artikeln:

- [Gewähren des Zugriffs auf Azure Event Hubs-Namespaces über private Endpunkte](../../event-hubs/private-link-service.md)
- [Vertrauenswürdige Microsoft-Dienste](../../event-hubs/private-link-service.md#trusted-microsoft-services)
- [Zulassen des Zugriffs auf Azure Event Hubs-Namespaces aus bestimmten virtuellen Netzwerken](../../event-hubs/event-hubs-service-endpoints.md)

---

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Erstellen eines Ziels für Service Bus-Warteschlangen oder -Themen

# <a name="connection-string"></a>[Verbindungszeichenfolge](#tab/connection-string)

Wenn Sie keinen vorhandenen Service Bus-Namespace als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen [neuen Service Bus-Namespace im Azure-Portal](https://portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Weitere Informationen erhalten Sie in der [Azure Service Bus-Dokumentation](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Wenn Sie eine Warteschlange oder ein Thema als Exportziel erstellen möchten, wechseln Sie zu Ihrem Service Bus-Namespace, und wählen Sie **+ Warteschlange** oder **+ Thema** aus.

1. Generieren Sie einen Schlüssel, der verwendet werden soll, wenn Sie Ihren Datenexport in IoT Central einrichten:

    - Wählen Sie die erstellte Warteschlange oder das erstellte Thema aus.
    - Wählen Sie **Einstellungen/Richtlinien für gemeinsamen Zugriff** aus.
    - Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
    - Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge. Mithilfe dieser Verbindungszeichenfolge richten Sie ein neues Ziel in IoT Central ein.
    - Alternativ können Sie eine Verbindungszeichenfolge für den gesamten Service Bus-Namespace generieren:
        1. Navigieren Sie im Azure-Portal zu Ihrem Service Bus-Namespace.
        2. Wählen Sie unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus.
        3. Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
        4. Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge.

# <a name="managed-identity"></a>[Verwaltete Identität](#tab/managed-identity)

Wenn Sie keinen vorhandenen Service Bus-Namespace als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen [neuen Service Bus-Namespace im Azure-Portal](https://portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Weitere Informationen erhalten Sie in der [Azure Service Bus-Dokumentation](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Wenn Sie eine Warteschlange oder ein Thema als Exportziel erstellen möchten, wechseln Sie zu Ihrem Service Bus-Namespace, und wählen Sie **+ Warteschlange** oder **+ Thema** aus.

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

So konfigurieren Sie die Berechtigungen:

1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** den Bereich und das Abonnement aus, die Sie verwenden möchten.

    > [!TIP]
    > Wenn sich Ihre IoT Central-Anwendung und Ihre Warteschlange oder Ihr Thema in derselben Ressourcengruppe befinden, können Sie für den Bereich **Ressourcengruppe** festlegen und dann die entsprechende Ressourcengruppe auswählen.

1. Wählen Sie als **Rolle** **Azure Service Bus Data Sender** (Azure Service Bus-Datensender) aus.

1. Wählen Sie **Speichern** aus. Die verwaltete Identität für Ihre IoT Central-Anwendung ist jetzt konfiguriert.

Informationen dazu, wie Sie Ihre Warteschlange oder Ihr Thema besser schützen und nur den Zugriff durch vertrauenswürdige Dienste mit verwalteten Identitäten zulassen können, finden Sie in den folgenden Artikeln:

- [Gewähren des Zugriffs auf Azure Service Bus-Namespaces über private Endpunkte](../../service-bus-messaging/private-link-service.md)
- [Vertrauenswürdige Microsoft-Dienste](../../service-bus-messaging/private-link-service.md#trusted-microsoft-services)
- [Zulassen des Zugriffs auf den Azure Service Bus-Namespace aus bestimmten virtuellen Netzwerken](../../service-bus-messaging/service-bus-service-endpoints.md)

---

### <a name="create-an-azure-blob-storage-destination"></a>Erstellen Sie ein Azure Blob Storage-Ziel.

# <a name="connection-string"></a>[Verbindungszeichenfolge](#tab/connection-string)

Wenn es noch kein Azure-Speicherkonto als Exportziel gibt, führen Sie die folgenden Schritte aus:

1. Erstellen Sie ein [neues Speicherkonto im Azure-Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Sie können sich zum Erstellen neuer [Azure Blob Storage-Konten](../../storage/blobs/storage-quickstart-blobs-portal.md) oder neuer [Azure Data Lake Storage v2-Speicherkonten](../../storage/common/storage-account-create.md) genauer informieren. Beim Datenexport können Daten nur in Speicherkonten geschrieben werden, die Blockblobs unterstützen. In der nachstehenden Liste sind die bekannten kompatiblen Speicherkontotypen aufgeführt:

    |Leistungsstufe|Kontotyp|
    |-|-|
    |Standard|Universell v2|
    |Standard|Universell v1|
    |Standard|Blob Storage|
    |Premium|Blockblobspeicher|

1. Wenn Sie einen Container in Ihrem Speicherkonto erstellen möchten, wechseln Sie zu diesem Konto. Wählen Sie unter **Blob-Dienst** die Option **Blobs durchsuchen**. Wählen Sie oben die Option **+ Container** aus, um einen neuen Container zu erstellen.

1. Generieren Sie eine Verbindungszeichenfolge für Ihr Speicherkonto, indem Sie zu **Einstellungen > Zugriffsschlüssel** wechseln. Kopieren Sie eine der beiden Verbindungszeichenfolgen.

# <a name="managed-identity"></a>[Verwaltete Identität](#tab/managed-identity)

Wenn es noch kein Azure-Speicherkonto als Exportziel gibt, führen Sie die folgenden Schritte aus:

1. Erstellen Sie ein [neues Speicherkonto im Azure-Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Sie können sich zum Erstellen neuer [Azure Blob Storage-Konten](../../storage/blobs/storage-quickstart-blobs-portal.md) oder neuer [Azure Data Lake Storage v2-Speicherkonten](../../storage/common/storage-account-create.md) genauer informieren. Beim Datenexport können Daten nur in Speicherkonten geschrieben werden, die Blockblobs unterstützen. In der nachstehenden Liste sind die bekannten kompatiblen Speicherkontotypen aufgeführt:

    |Leistungsstufe|Kontotyp|
    |-|-|
    |Standard|Universell v2|
    |Standard|Universell v1|
    |Standard|Blob Storage|
    |Premium|Blockblobspeicher|

1. Wenn Sie einen Container in Ihrem Speicherkonto erstellen möchten, wechseln Sie zu diesem Konto. Wählen Sie unter **Blob-Dienst** die Option **Blobs durchsuchen**. Wählen Sie oben die Option **+ Container** aus, um einen neuen Container zu erstellen.

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

So konfigurieren Sie die Berechtigungen:

1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** das Abonnement, das Sie verwenden möchten, und **Storage** als Bereich aus. Wählen Sie anschließend Ihr Speicherkonto als Ressource aus.

1. Wählen Sie als **Rolle** **Storage Blob Data Contributor** (Mitwirkender an Storage-Blobdaten) aus.

1. Wählen Sie **Speichern** aus. Die verwaltete Identität für Ihre IoT Central-Anwendung ist jetzt konfiguriert.

    > [!TIP]
    > Diese Rollenzuweisung wird in der Liste auf der Seite **Azure-Rollenzuweisungen** nicht angezeigt.

Informationen dazu, wie Sie Ihren Blobcontainer besser schützen und nur den Zugriff durch vertrauenswürdige Dienste mit verwalteten Identitäten zulassen können, finden Sie in den folgenden Artikeln:

- [Verwenden privater Endpunkte für Azure Storage](../../storage/common/storage-private-endpoints.md)
- [Autorisieren des Zugriffs auf Blobdaten mit verwalteten Identitäten für Azure-Ressourcen](../../storage/blobs/authorize-managed-identity.md)
- [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../../storage/common/storage-network-security.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)

---

### <a name="create-an-azure-data-explorer-destination"></a>Erstellen eines Azure Data Explorer-Ziels

Wenn Sie keinen [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)-Cluster und keine Datenbank als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen neuen Azure Data Explorer-Cluster und eine Datenbank. Weitere Informationen finden Sie im [Azure Data Explorer-Schnellstart](/azure/data-explorer/create-cluster-database-portal). Notieren Sie sich den Namen der Datenbank, die Sie erstellen. Sie benötigen diesen Wert in den folgenden Schritten.

1. Erstellen Sie einen Dienstprinzipal, den Sie verwenden können, um Ihre IoT Central-Anwendung mit Azure Data Explorer zu verbinden. Führen Sie in der Azure Cloud Shell den folgenden Befehl aus:

    ```azurecli
    az ad sp create-for-rbac --skip-assignment --name "My SP for IoT Central"
    ```

    Notieren Sie sich die Werte `appId`, `password` und `tenant` in der Befehlsausgabe. Sie benötigen sie in den folgenden Schritten.

1. Um den Dienstprinzipal der Datenbank hinzuzufügen, navigieren Sie zum Azure Data Explorer-Portal, und führen Sie die folgende Abfrage für Ihre Datenbank aus. Ersetzen Sie die Platzhalter durch die Werte, die Sie sich zuvor notiert haben:

    ```kusto
    .add database <YourDatabaseName> admins ('aadapp=<YourAppId>;<YourTenant>');
    ```

1. Erstellen Sie in Ihrer Datenbank eine Tabelle mit einem geeigneten Schema für die Daten, die Sie exportieren. Die folgende Beispielabfrage erstellt die Tabelle `smartvitalspatch`. Weitere Informationen finden Sie unter [Transformieren von Daten in Ihrer IoT Central-Anwendung für den Export](howto-transform-data-internally.md):

    ```kusto
    .create table smartvitalspatch (
      EnqueuedTime:datetime,
      Message:string,
      Application:string,
      Device:string,
      Simulated:boolean,
      Template:string,
      Module:string,
      Component:string,
      Capability:string,
      Value:dynamic
    )
    ```

1. (Optional) So beschleunigen Sie die Erfassung von Daten in Ihrer Azure Data Explorer-Datenbank:

    1. Navigieren Sie zur Seite **Konfigurationen** für Ihren Azure Data Explorer-Cluster. Aktivieren Sie dann die Option **Streamingerfassung**.
    1. Führen Sie die folgende Abfrage aus, um die Tabellenrichtlinie so zu ändern, dass die Streamingerfassung aktiviert wird:

        ```kusto
        .alter table smartvitalspatch policy streamingingestion enable
        ```

1. Fügen Sie in IoT Central ein Azure Data Explorer-Ziel unter Angabe Ihrer Azure Data Explorer-Cluster-URL, des Datenbanknamens und des Tabellennamens hinzu. In der folgenden Tabelle sind die Dienstprinzipalwerte aufgeführt, die für die Autorisierung zu verwenden sind:

    | Dienstprinzipalwert | Zielkonfiguration |
    | ----------------------- | ------------------------- |
    | appId                   | ClientID                  |
    | tenant                  | Mandanten-ID                 |
    | password                | Geheimer Clientschlüssel             |

    :::image type="content" source="media/howto-export-data/export-destination.png" alt-text="Screenshot des Azure Data Explorer-Exportziels.":::

### <a name="create-a-webhook-endpoint"></a>Erstellen eines Webhookendpunkts

Sie können Daten in einen öffentlich verfügbaren HTTP-Webhookendpunkt exportieren. Sie können einen Webhooktestendpunkt mit [RequestBin](https://requestbin.net/) erstellen. „RequestBin“ drosselt die Anforderung, wenn das Anforderungslimit erreicht wird:

1. Öffnen Sie [RequestBin](https://requestbin.net/).
2. Erstellen Sie ein neues RequestBin-Element, und kopieren Sie die **Bin-URL**. Sie verwenden diese URL beim Testen Ihres Datenexports.

## <a name="set-up-data-export"></a>Einrichten des Datenexports

# <a name="connection-string"></a>[Verbindungszeichenfolge](#tab/connection-string)

[!INCLUDE [iot-central-create-export](../../../includes/iot-central-create-export.md)]

So konfigurieren Sie das Ziel:

1. Fügen Sie ein neues Ziel oder ein Ziel hinzu, das Sie bereits erstellt haben. Wählen Sie den Link **Create a new one** (Neue erstellen) aus, und fügen Sie die folgenden Informationen hinzu:

    - **Zielname:** der Anzeigename des Ziels in IoT Central.
    - **Zieltyp:** Wählen Sie den Typ des Ziels aus. Wenn Sie Ihr Ziel noch nicht eingerichtet haben, finden Sie weitere Informationen unter [Einrichten des Exportziels](#set-up-export-destination).
    - **Autorisierung**: Wählen Sie **Verbindungszeichenfolge** aus.
    - Fügen Sie bei Azure Event Hubs, Azure Service Bus-Warteschlange oder -Thema die Verbindungszeichenfolge für Ihre Ressource ein, und geben Sie bei Bedarf den Namen des Event Hubs, der Warteschlange oder des Themas ein (Groß-/Kleinschreibung beachten).
    - Fügen Sie bei Azure Blob Storage die Verbindungszeichenfolge für Ihre Ressource ein, und geben Sie bei Bedarf den Containernamen ein (Groß-/Kleinschreibung beachten).
    - Bei Webhook fügen Sie die Callback-URL für Ihren Webhookendpunkt ein. Optional können Sie die Webhookautorisierung (OAuth 2.0 und Autorisierungstoken) konfigurieren und benutzerdefinierte Header hinzufügen. 
        - Bei OAuth 2.0 wird nur der Flow für Anmeldeinformationen von Clients unterstützt. Wenn das Ziel gespeichert ist, kommuniziert IoT Central mit Ihrem OAuth-Anbieter, um ein Autorisierungstoken abzurufen. Dieses Token wird bei jeder an dieses Ziel gesendeten Nachricht an den `Authorization`-Header angefügt.
        - Für das Autorisierungstoken können Sie einen Tokenwert angeben, der bei jeder an dieses Ziel gesendeten Nachricht direkt an den `Authorization`-Header angefügt wird.
    - Klicken Sie auf **Erstellen**.

1. Wählen Sie **+ Ziel** und dann ein Ziel aus der Dropdownliste aus. Sie können bis zu fünf Ziele zu einem einzelnen Export hinzufügen.

1. Wenn Sie das Einrichten Ihres Exports abgeschlossen haben, wählen Sie **Speichern** aus. Nach einigen Minuten werden Ihre Daten in Ihren Zielen angezeigt.

# <a name="managed-identity"></a>[Verwaltete Identität](#tab/managed-identity)

[!INCLUDE [iot-central-create-export](../../../includes/iot-central-create-export.md)]

So konfigurieren Sie das Ziel:

1. Fügen Sie ein neues Ziel oder ein Ziel hinzu, das Sie bereits erstellt haben. Wählen Sie den Link **Create a new one** (Neue erstellen) aus, und fügen Sie die folgenden Informationen hinzu:

    - **Zielname:** der Anzeigename des Ziels in IoT Central.
    - **Zieltyp:** Wählen Sie den Typ des Ziels aus. Wenn Sie Ihr Ziel noch nicht eingerichtet haben, finden Sie weitere Informationen unter [Einrichten des Exportziels](#set-up-export-destination).
    - **Autorisierung**: Wählen Sie **Systemseitig zugewiesene verwaltete Identität** aus.
    - Geben Sie bei Azure Event Hubs und Azure Service Bus-Warteschlangen oder -Themen den Hostnamen für Ihre Ressource an. Geben Sie dann den Namen des Event Hubs, der Warteschlange oder des Themas mit der richtigen Groß-/Kleinschreibung ein. Hostnamen sehen wie folgt aus: `contoso-waste.servicebus.windows.net`.
    - Geben Sie beim Azure Blob Storage den Endpunkt-URI für Ihr Speicherkonto und den Containernamen mit der richtigen Groß-/Kleinschreibung ein. Endpunkt-URIs sehen wie folgt aus: `https://contosowaste.blob.core.windows.net`.
    - Bei Webhook fügen Sie die Callback-URL für Ihren Webhookendpunkt ein. Optional können Sie die Webhookautorisierung (OAuth 2.0 und Autorisierungstoken) konfigurieren und benutzerdefinierte Header hinzufügen.
        - Bei OAuth 2.0 wird nur der Flow für Anmeldeinformationen von Clients unterstützt. Wenn das Ziel gespeichert ist, kommuniziert IoT Central mit Ihrem OAuth-Anbieter, um ein Autorisierungstoken abzurufen. Dieses Token wird bei jeder an dieses Ziel gesendeten Nachricht an den `Authorization`-Header angefügt.
        - Für das Autorisierungstoken können Sie einen Tokenwert angeben, der bei jeder an dieses Ziel gesendeten Nachricht direkt an den `Authorization`-Header angefügt wird.
    - Klicken Sie auf **Erstellen**.

1. Wählen Sie **+ Ziel** und dann ein Ziel aus der Dropdownliste aus. Sie können bis zu fünf Ziele zu einem einzelnen Export hinzufügen.

1. Wenn Sie das Einrichten Ihres Exports abgeschlossen haben, wählen Sie **Speichern** aus. Nach einigen Minuten werden Ihre Daten in Ihren Zielen angezeigt.

---

## <a name="monitor-your-export"></a>Überwachen des Exports

Sie können den Status Ihrer Exporte in IoT Central überprüfen. Darüber hinaus können Sie in [Azure Monitor](../../azure-monitor/overview.md) die Datenmenge sehen, die exportiert wird, sowie etwaige Exportfehler. Die Metriken zum Export und zur Geräteintegrität sind über Diagramme im Azure-Portal, eine REST-API sowie Abfragen in PowerShell oder der Azure-Befehlszeilenschnittstelle zugänglich. Derzeit können Sie die folgenden Datenexportmetriken in Azure Monitor überwachen:

- Anzahl der eingehenden Nachrichten für den Export vor dem Anwenden von Filtern
- Anzahl der Nachrichten, die die Filter durchlaufen
- Anzahl von Nachrichten, die erfolgreich an die Ziele exportiert wurden
- Anzahl der aufgetretenen Fehler.

Weitere Informationen finden Sie unter [Überwachen der Anwendungsintegrität](howto-manage-iot-central-from-portal.md#monitor-application-health).

## <a name="destinations"></a>Destinations

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage-Ziel

Daten werden einmal pro Minute exportiert, wobei jede Datei den Batch der Änderungen seit dem vorherigen Export enthält. Exportierte Daten werden im JSON-Format gespeichert. Die Standardpfade zu den exportierten Daten in Ihrem Speicherkonto lauten:

- Telemetriedaten: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Eigenschaftsänderungen: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Wenn Sie die exportierten Dateien im Azure-Portal durchsuchen möchten, navigieren Sie zu der gewünschten Datei, und wählen Sie **Blob bearbeiten** aus.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs- und Azure Service Bus-Ziele

Daten werden nahezu in Echtzeit exportiert. Die Daten befinden sich im Nachrichtentext und liegen im JSON-Format vor, das als UTF-8 codiert ist.

Die Sammlung von Anmerkungen bzw. Systemeigenschaften der Nachricht enthält die Felder `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` und `iotcentral-message-type` mit denselben Werten wie die entsprechenden Felder im Nachrichtentext.

### <a name="azure-data-explorer-destination"></a>Azure Data Explorer-Ziel

Daten werden nahezu in Echtzeit in eine angegebene Datenbanktabelle im Azure Data Explorer-Cluster exportiert. Die Daten befinden sich im Nachrichtentext und liegen im JSON-Format vor, das als UTF-8 codiert ist. Sie können in IoT Central eine [Transformation](howto-transform-data-internally.md) hinzufügen, um Daten zu exportieren, die dem Tabellenschema entsprechen.

Navigieren Sie zum Abfragen der exportierten Daten im Azure Data Explorer-Portal zur Datenbank, und wählen Sie **Abfrage** aus.

### <a name="webhook-destination"></a>Webhookziel

Bei Webhookzielen werden Daten ebenfalls nahezu in Echtzeit exportiert. Die Daten im Nachrichtentext haben dasselbe Format wie bei Event Hubs und Service Bus.

## <a name="telemetry-format"></a>Telemetrieformat

Jede exportierte Nachricht enthält eine normalisierte Form der vollständigen Nachricht, die das Gerät im Nachrichtentext gesendet hat. Die Nachricht ist im JSON-Format und als UTF-8 codiert. Jede Nachricht enthält folgende Informationen:

- `applicationId`: Die ID der IoT Central-Anwendung.
- `messageSource`: Die Quelle für die Nachricht – `telemetry`.
- `deviceId`:  Die ID des Geräts, von dem die Telemetrienachricht gesendet wurde.
- `schema`: Den Namen und die Version des Nutzdatenschemas.
- `templateId`: Die ID der Gerätevorlage, die dem Gerät zugeordnet ist.
- `enqueuedTime`: Der Zeitpunkt, zu dem diese Nachricht von IoT Central empfangen wurde
- `enrichments`: Alle im Export eingerichteten Anreicherungen.
- `module`: Das IoT Edge-Modul, das diese Nachricht gesendet hat. Dieses Feld wird nur angezeigt, wenn die Nachricht aus einem IoT Edge-Modul stammt.
- `component`: Die Komponente, die diese Nachricht gesendet hat. Dieses Feld wird nur angezeigt, wenn die in der Nachricht gesendeten Funktionen als eine Komponente in der Gerätevorlage modelliert wurden.
- `messageProperties`: Hierbei handelt es sich um weitere Eigenschaften, die das Gerät zusammen mit der Nachricht gesendet hat. Diese Eigenschaften werden manchmal auch als *Anwendungseigenschaften* bezeichnet. [Weitere Informationen zu IoT Hub-Dokumenten](../../iot-hub/iot-hub-devguide-messages-construct.md).

Bei Event Hubs und Service Bus exportiert IoT Central eine neue Nachricht schnell, nachdem sie von einem Gerät eingetroffen ist. In die Benutzereigenschaften (auch als „Anwendungseigenschaften“ bezeichnet) jeder Nachricht werden die Eigenschaften `iotcentral-device-id`, `iotcentral-application-id` und `iotcentral-message-source` automatisch einbezogen.

Bei Blob Storage werden Nachrichten im Batch zusammengefasst und einmal pro Minute exportiert.

Das folgende Beispiel zeigt eine exportierte Telemetrienachricht:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "module": "VitalsModule",
    "component": "DeviceComponent",
    "messageProperties": {
      "messageProp": "value"
    }
}
```

### <a name="message-properties"></a>Nachrichteneigenschaften

Telemetrienachrichten verfügen zusätzlich zu den Telemetrienutzdaten über Eigenschaften für Metadaten. Der vorherige Codeausschnitt zeigt Beispiele für Systemmeldungen wie `deviceId` und `enqueuedTime`. Weitere Informationen zu den Eigenschaften von Systemmeldungen finden Sie unter [Systemeigenschaften von D2C-IoT Hub-Nachrichten](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Sie können Telemetrienachrichten Eigenschaften hinzufügen, wenn Sie Ihren Telemetrienachrichten benutzerdefinierte Metadaten hinzufügen möchten. Beispielsweise können Sie einen Zeitstempel für den Zeitpunkt hinzufügen, zu dem das Gerät die Meldung erstellt hat.

Der folgende Codeausschnitt zeigt, wie Sie der Nachricht die `iothub-creation-time-utc`-Eigenschaft hinzufügen, wenn diese auf dem Gerät erstellt wird:

> [!IMPORTANT]
> Das Format dieses Zeitstempels muss UTC ohne Zeitzoneninformationen sein. Beispiel: `2021-04-21T11:30:16Z` ist gültig, `2021-04-21T11:30:16-07:00` ist ungültig.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

Der folgende Codeausschnitt zeigt diese Eigenschaft in der Nachricht, die an Blob Storage exportiert wird:

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Format für Eigenschaftsänderungen

Jede Nachricht oder jeder Datensatz stellt Änderungen an Geräte- und Cloudeigenschaften dar. Die exportierte Nachricht enthält folgende Informationen:

- `applicationId`: Die ID der IoT Central-Anwendung.
- `messageSource`: Die Quelle für die Nachricht – `properties`.
- `messageType`: Entweder `cloudPropertyChange`, `devicePropertyDesiredChange` oder `devicePropertyReportedChange`.
- `deviceId`:  Die ID des Geräts, von dem die Telemetrienachricht gesendet wurde.
- `schema`: Den Namen und die Version des Nutzdatenschemas.
- `enqueuedTime`: Der Zeitpunkt, zu dem diese Änderung von IoT Central erkannt wurde
- `templateId`: Die ID der Gerätevorlage, die dem Gerät zugeordnet ist.
- `properties`: Ein Array von Eigenschaften, die geändert wurden, einschließlich der Namen der geänderten Eigenschaften und Werte. Die Komponenten- und Modulinformationen werden eingeschlossen, wenn die Eigenschaft innerhalb einer Komponente oder eines IoT Edge-Moduls modelliert wird.
- `enrichments`: Alle im Export eingerichteten Anreicherungen.

Bei Event Hubs und Service Bus exportiert IoT Central neue Nachrichtendaten nahezu in Echtzeit an Ihren Event Hub oder Ihre Service Bus-Warteschlange bzw. Ihr Service Bus-Thema. In die Benutzereigenschaften (auch als „Anwendungseigenschaften“ bezeichnet) jeder Nachricht werden die Eigenschaften `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` und `iotcentral-message-type` automatisch einbezogen.

Bei Blob Storage werden Nachrichten im Batch zusammengefasst und einmal pro Minute exportiert.

Das folgende Beispiel zeigt eine exportierte Eigenschaftsänderungsnachricht, die in Azure Blob Storage empfangen wurde.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc",
        "module": "VitalsModule",
        "component": "DeviceComponent"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="device-connectivity-changes-format"></a>Änderungsformat bei der Gerätekonnektivität

Jede Nachricht und jeder Datensatz stellt ein Konnektivitätsereignis von einem einzelnen Gerät dar. Die exportierte Nachricht enthält folgende Informationen:

- `applicationId`: Die ID der IoT Central-Anwendung.
- `messageSource`: Die Quelle für die Nachricht – `deviceConnectivity`.
- `messageType`: Entweder `connected` oder `disconnected`
- `deviceId`: Die ID des Geräts, das geändert wurde
- `schema`: Den Namen und die Version des Nutzdatenschemas.
- `templateId`: Die ID der Gerätevorlage, die dem Gerät zugeordnet ist.
- `enqueuedTime`: Der Zeitpunkt, zu dem diese Änderung in IoT Central aufgetreten ist
- `enrichments`: Alle im Export eingerichteten Anreicherungen.

Bei Event Hubs und Service Bus exportiert IoT Central neue Nachrichtendaten nahezu in Echtzeit an Ihren Event Hub oder Ihre Service Bus-Warteschlange bzw. Ihr Service Bus-Thema. In die Benutzereigenschaften (auch als „Anwendungseigenschaften“ bezeichnet) jeder Nachricht werden die Eigenschaften `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` und `iotcentral-message-type` automatisch einbezogen.

Bei Blob Storage werden Nachrichten im Batch zusammengefasst und einmal pro Minute exportiert.

Das folgende Beispiel zeigt eine exportierte Gerätekonnektivitätsnachricht, die in Azure Blob Storage empfangen wurde.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceConnectivity",
  "messageType": "connected",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-04-05T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}

```

## <a name="device-lifecycle-changes-format"></a>Format der Gerätelebenszyklusänderungen

Jede Nachricht bzw. jeder Datensatz stellt eine Änderung an einem einzelnen Gerät dar. Die exportierte Nachricht enthält folgende Informationen:

- `applicationId`: Die ID der IoT Central-Anwendung.
- `messageSource`: Die Quelle für die Nachricht – `deviceLifecycle`.
- `messageType`: Der Typ des aufgetretenen Fehlers. Einer der Werte `registered`, `deleted`, `provisioned`, `enabled`, `disabled`, `displayNameChanged` oder `deviceTemplateChanged`.
- `deviceId`: Die ID des Geräts, das geändert wurde
- `schema`: Den Namen und die Version des Nutzdatenschemas.
- `templateId`: Die ID der Gerätevorlage, die dem Gerät zugeordnet ist.
- `enqueuedTime`: Der Zeitpunkt, zu dem diese Änderung in IoT Central aufgetreten ist
- `enrichments`: Alle im Export eingerichteten Anreicherungen.

Bei Event Hubs und Service Bus exportiert IoT Central neue Nachrichtendaten nahezu in Echtzeit an Ihren Event Hub oder Ihre Service Bus-Warteschlange bzw. Ihr Service Bus-Thema. In die Benutzereigenschaften (auch als „Anwendungseigenschaften“ bezeichnet) jeder Nachricht werden die Eigenschaften `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` und `iotcentral-message-type` automatisch einbezogen.

Bei Blob Storage werden Nachrichten im Batch zusammengefasst und einmal pro Minute exportiert.

Das folgende Beispiel zeigt eine exportierte Gerätelebenszyklusnachricht, die in Azure Blob Storage empfangen wurde:

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceLifecycle",
  "messageType": "registered",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="device-template-lifecycle-changes-format"></a>Format der Änderungen des Gerätevorlagenlebenszyklus

Jede Nachricht bzw. jeder Datensatz stellt eine Änderung an einer einzelnen veröffentlichten Gerätevorlage dar. Die exportierte Nachricht enthält folgende Informationen:

- `applicationId`: Die ID der IoT Central-Anwendung.
- `messageSource`: Die Quelle für die Nachricht – `deviceTemplateLifecycle`.
- `messageType`: Entweder `created`, `updated` oder `deleted`
- `schema`: Den Namen und die Version des Nutzdatenschemas.
- `templateId`: Die ID der Gerätevorlage, die dem Gerät zugeordnet ist.
- `enqueuedTime`: Der Zeitpunkt, zu dem diese Änderung in IoT Central aufgetreten ist
- `enrichments`: Alle im Export eingerichteten Anreicherungen.

Bei Event Hubs und Service Bus exportiert IoT Central neue Nachrichtendaten nahezu in Echtzeit an Ihren Event Hub oder Ihre Service Bus-Warteschlange bzw. Ihr Service Bus-Thema. In die Benutzereigenschaften (auch als „Anwendungseigenschaften“ bezeichnet) jeder Nachricht werden die Eigenschaften `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` und `iotcentral-message-type` automatisch einbezogen.

Bei Blob Storage werden Nachrichten im Batch zusammengefasst und einmal pro Minute exportiert.

Das folgende Beispiel zeigt eine exportierte Gerätelebenszyklusnachricht, die in Azure Blob Storage empfangen wurde:

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceTemplateLifecycle",
  "messageType": "created",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Vergleich zwischen Legacy-Datenexport und Datenexport

Die folgende Tabelle zeigt die Unterschiede zwischen dem [Legacy-Datenexport](howto-export-data-legacy.md) und den Features des aktuellen Datenexports:

| Funktionen  | Legacy-Datenexport | Neuer Datenexport |
| :------------- | :---------- | :----------- |
| Verfügbare Datentypen | Telemetrie, Geräte, Gerätevorlagen | Telemetriedaten, Eigenschaftsänderungen, Änderungen der Gerätekonnektivität, des Gerätelebenszyklus oder des Gerätevorlagenlebenszyklus |
| Filterung | Keiner | Hängt vom exportierten Datentyp ab. Für Telemetrie, Filtern nach Telemetrie, Nachrichteneigenschaften, Eigenschaftswerte |
| Anreicherungen | Keiner | Anreichern mit einer benutzerdefinierten Zeichenfolge oder einem Eigenschaftswert auf dem Gerät |
| Destinations | Azure Event Hubs, Azure Service Bus-Warteschlangen und -Themen, Azure Blob Storage | Wie bei Legacy-Datenexport plus Webhooks|
| Unterstützte Anwendungsversionen | V2 und V3 | Nur V3 |
| Relevante Grenzwerte | Fünf Exporte pro App, ein Ziel pro Export | 10 Exporte-Ziel-Verbindungen pro App |

## <a name="next-steps"></a>Nächste Schritte

Sie wissen jetzt, wie Sie den Datenexport konfigurieren können. Als Nächstes sollten Sie sich über das [Transformieren von Daten in Ihrer IoT Central-Anwendung für den Export](howto-transform-data-internally.md) informieren.
