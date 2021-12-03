---
title: Speicheraspekte für Azure Functions
description: Erfahren Sie über die Speicheranforderungen von Azure Functions und über das Verschlüsseln gespeicherter Daten.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 0e53d2919d8af3f0e8162d4aca9f55f2ec0ab740
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335877"
---
# <a name="storage-considerations-for-azure-functions"></a>Speicheraspekte für Azure Functions

Azure Functions erfordert ein Azure Storage-Konto, wenn Sie eine Funktions-App-Instanz erstellen. Die folgenden Speicherdienste können von ihrer Funktions-App verwendet werden:

|Speicherdienst  | Verwendung in Functions  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Aufrechterhalten von Bindungszustand und Funktionsschlüsseln.  <br/>Wird auch von [Aufgabenhubs in Durable Functions](durable/durable-functions-task-hubs.md) verwendet. |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Dateifreigabe, die zum Speichern und Ausführen Ihres Funktions-App-Codes in einem [Verbrauchstarif](consumption-plan.md) und [Premium-Plan](functions-premium-plan.md) verwendet wird. <br/>Azure Files wird standardmäßig eingerichtet, aber Sie können unter bestimmten Bedingungen eine App ohne [Azure Files](#create-an-app-without-azure-files) erstellen. |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | Wird von [Aufgabenhubs in Durable Functions](durable/durable-functions-task-hubs.md) verwendet.   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  Wird von [Aufgabenhubs in Durable Functions](durable/durable-functions-task-hubs.md) verwendet.       |

> [!IMPORTANT]
> Wenn der verbrauchsbasierte Premium-Hostingplan verwendet wird, werden die Funktionscode- und Bindungskonfigurationsdateien in Azure Files im Hauptspeicherkonto gespeichert. Wenn Sie das Hauptspeicherkonto löschen, wird dieser Inhalt ebenfalls gelöscht und kann nicht wiederhergestellt werden.

## <a name="storage-account-requirements"></a>Anforderungen an das Speicherkonto

Beim Erstellen einer Funktions-App müssen Sie ein allgemeines Azure Storage-Konto erstellen oder verknüpfen, das Blob-, Warteschlangen- und Tabellenspeicher unterstützt. Der Grund hierfür ist, dass Functions für Vorgänge wie das Verwalten von Triggern und das Ausführen von Protokollierfunktionen auf Azure Storage basiert. Einige Speicherkonten unterstützen keine Warteschlangen und Tabellen. Zu diesen Konten zählen reine Blobspeicherkonten und Azure Storage Premium.

Weitere Informationen zu Speicherkontotypen finden Sie unter [Einführung in die Azure Storage-Dienste](../storage/common/storage-introduction.md#core-storage-services). 

Sie können zwar ein vorhandenes Speicherkonto mit ihrer Funktions-App verwenden, aber Sie müssen sicherstellen, dass diese Anforderungen erfüllt werden. Für Speicherkonten, die im Rahmen der Erstellung der Funktions-App im Azure-Portal erstellt werden, ist garantiert, dass sie diese Speicherkontenanforderungen erfüllen. Im Portal werden nicht unterstützte Konten herausgefiltert, wenn Sie beim Erstellen einer Funktions-App ein vorhandenes Speicherkonto auswählen. In diesem Flow dürfen Sie nur vorhandene Speicherkonten in derselben Region wie die von Ihnen erstellte Funktions-App auswählen. Weitere Informationen finden Sie unter [Standort des Speicherkontos](#storage-account-location).

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Speicherkontoanleitung

Jede Funktions-App benötigt für den Betrieb ein Speicherkonto. Wenn dieses Konto gelöscht wird, läuft Ihre Funktions-App nicht. Informationen zur Behandlung speicherbezogener Probleme finden Sie unter [Behandeln speicherbezogener Probleme](functions-recover-storage-account.md). Die folgenden zusätzlichen Überlegungen gelten für das von Funktions-Apps verwendete Speicherkonto.

### <a name="storage-account-location"></a>Standort des Speicherkontos

Für eine optimale Leistung sollte Ihre Funktions-App ein Speicherkonto in derselben Region verwenden, um die Latenz zu verringern. Im Azure-Portal wird diese bewährte Methode erzwungen. Wenn Sie aus irgendeinem Grund ein Speicherkonto in einer anderen Region als Ihre Funktions-App verwenden möchten, müssen Sie Ihre Funktions-App außerhalb des Portals erstellen. 

### <a name="storage-account-connection-setting"></a>Verbindungszeichenfolge für das Speicherkonto

Die Speicherkontoverbindung wird in der Anwendungseinstellung [AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage) verwaltet. 

Die Verbindungszeichenfolgen für das Speicherkonto muss aktualisiert werden, wenn Sie Speicherschlüssel neu generieren. [Erfahren Sie hier mehr über die Verwaltung von Speicherschlüsseln](../storage/common/storage-account-create.md).

### <a name="shared-storage-accounts"></a>Freigegebene Speicherkonten

Es ist möglich, dass mehrere Funktions-Apps dasselbe Speicherkonto ohne Probleme gemeinsam nutzen. Beispielsweise können Sie in Visual Studio mit dem Azure Storage-Emulator mehrere Apps entwickeln. In diesem Fall verhält sich der Emulator wie ein einzelnes Speicherkonto. Dasselbe Speicherkonto, das von Ihrer Funktions-App verwendet wird, kann auch von zum Speichern Ihrer Anwendungsdaten verwendet werden. Dieser Ansatz ist jedoch in einer Produktionsumgebung nicht immer eine gute Idee.

### <a name="lifecycle-management-policy-considerations"></a>Überlegungen zu Richtlinien für die Lebenszyklusverwaltung

Azure Functions verwendet Blob Storage, um wichtige Informationen wie [Funktionszugriffsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys) dauerhaft zu speichern. Wenn Sie eine Richtlinie zur [Lebenszyklusverwaltung](../storage/blobs/lifecycle-management-overview.md) auf Ihr Blob Storage-Konto anwenden, kann die Richtlinie Blobs entfernen, die vom Azure Functions-Host benötigt werden. Aus diesem Grund sollten Sie solche Richtlinien nicht auf das Speicherkonto anwenden, das von Functions verwendet wird. Wenn Sie eine solche Richtlinie anwenden müssen, denken Sie daran, die von Functions verwendeten Container auszuschließen, die in der Regel das Präfix `azure-webjobs` oder `scm` haben.

### <a name="optimize-storage-performance"></a>Optimieren der Speicherleistung

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Speicherdatenverschlüsselung

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Data Residency in der Region

Wenn alle Kundendaten in einer einzelnen Region verbleiben müssen, muss das Speicherkonto, das der Funktions-App zugeordnet ist, eines mit [regionsinterner Redundanz](../storage/common/storage-redundancy.md) sein. Für Speicherkonten mit regionsinterner Redundanz muss auch [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection) verwendet werden.

Andere plattformseitig verwaltete Kundendaten werden nur dann in der Region gespeichert, wenn sie in einer App Service-Umgebung (ASE) mit internem Lastenausgleich gehostet werden. Weitere Informationen finden Sie unter [ASE-Zonenredundanz](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="create-an-app-without-azure-files"></a>Erstellen einer App ohne Azure Files

Azure Files wird standardmäßig für Premium- und Nicht-Linux-Verbrauchspläne eingerichtet, um als freigegebenes Dateisystem in Szenarien mit hoher Skalierung zu dienen. Das Dateisystem wird von der Plattform für einige Features wie Protokollstreaming verwendet, stellt aber in erster Linie die Konsistenz der bereitgestellten Funktionsnutzdaten sicher. Wenn eine App mithilfe einer externen [Paket-URL](./run-functions-from-deployment-package.md) bereitgestellt wird, wird der App-Inhalt von einem separaten schreibgeschützten Dateisystem bereitgestellt, sodass Azure Files auf Wunsch weggelassen werden kann. In solchen Fällen wird ein beschreibbares Dateisystem bereitgestellt, aber es wird nicht garantiert, dass es für alle Funktions-App-Instanzen freigegeben wird.

Wenn Azure Files nicht verwendet wird, müssen Sie Folgendes berücksichtigen:

* Sie müssen die Bereitstellung über eine externe Paket-URL ausführen.
* Ihre App kann sich nicht auf ein freigegebenes beschreibbares Dateisystem verlassen.
* Die App kann runtime v1 von Functions nicht verwenden.
* Für Protokollstreaming in Clients wie dem Azure-Portal werden standardmäßig Dateisystemprotokolle verwendet. Sie sollten stattdessen Application Insights-Protokolle verwenden.

Wenn die oben genannten Punkte ordnungsgemäß berücksichtigt werden, können Sie die App ohne Azure Files erstellen. Erstellen Sie die Funktions-App, ohne die Anwendungseinstellungen `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` und `WEBSITE_CONTENTSHARE` anzugeben. Hierzu können Sie eine ARM-Vorlage für eine Standardbereitstellung generieren, diese beiden Einstellungen entfernen und dann die Vorlage bereitstellen. 

Da Functions Azure Files in Teilen der dynamischen horizontalen Skalierung verwendet, kann Skalierung eingeschränkt werden, wenn die Ausführung ohne Azure Files in Verbrauchs- und Premium-Plänen erfolgt.

## <a name="mount-file-shares"></a>Einbinden von Dateifreigaben

_Diese Funktion ist derzeit nur unter Linux verfügbar._ 

Sie können vorhandene Azure Files-Freigaben in Ihre Linux-Funktions-Apps einbinden. Durch das Einbinden einer Freigabe in Ihre Linux-Funktions-App können Sie vorhandene Machine Learning-Modelle oder andere Daten in Ihren Funktionen nutzen. Sie können den Befehl [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) verwenden, um eine vorhandene Freigabe in Ihre Linux-Funktions-App einzubinden. 

Bei diesem Befehl ist `share-name` der Name der vorhandenen Azure Files-Freigabe, und `custom-id` kann ein beliebige Zeichenfolge sein, mit der die Freigabe bei der Einbindung in die Funktions-App eindeutig definiert wird. `mount-path` ist der Pfad, über den in Ihrer Funktions-App auf die Freigabe zugegriffen wird. `mount-path` muss das Format `/dir-name` haben und darf nicht mit `/home` beginnen.

Ein vollständiges Beispiel finden Sie in den Skripts unter [Einbinden einer Dateifreigabe in eine Python-Funktions-App mithilfe der Azure CLI](scripts/functions-cli-mount-files-storage-linux.md). 

Derzeit wird als `storage-type` nur `AzureFiles` unterstützt. Sie können pro Funktions-App nicht mehr als fünf Freigaben einbinden. Durch die Einbindung einer Dateifreigabe verlängert sich die Kaltstartdauer ggf. um mindestens 200 bis 300 ms. Dieser Wert ist noch höher, wenn sich das Speicherkonto in einer anderen Region befindet.

Die eingebundene Freigabe ist für Ihren Funktionscode unter dem angegebenen `mount-path` verfügbar. Wenn `mount-path` beispielsweise `/path/to/mount` lautet, können Sie wie im folgenden Python-Beispiel über Dateisystem-APIs auf das Zielverzeichnis zugreifen:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Hostingoptionen von Azure Functions.

> [!div class="nextstepaction"]
> [Skalierung und Hosting von Azure Functions](functions-scale.md)
