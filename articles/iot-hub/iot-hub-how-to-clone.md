---
title: Klonen eines Azure IoT Hubs
description: Klonen eines Azure IoT Hubs
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: lizross
ms.openlocfilehash: 2905e1ad5a5efe4b56b6cd7990fb5d0eaaae34aa
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553385"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Klonen eines Azure IoT Hubs in eine andere Region

In diesem Artikel werden verschiedene Methoden zum Klonen eines IoT Hubs beschrieben und einige Fragen gestellt, die Sie beantworten müssen, bevor Sie mit diesem Vorgang beginnen. Hier sind mehrere Gründe, aus denen Sie einen IoT Hub klonen möchten:
 
* Sie verschieben gerade Ihr Unternehmen von einer Region in eine andere, z.B. von Europa nach Nordamerika (oder umgekehrt), und möchten, dass sich Ihre Ressourcen und Daten in geografischer Nähe zu Ihrem neuen Standort befinden. Deshalb müssen Sie Ihren Hub verschieben.

* Sie richten gerade einen Hub für eine Entwicklungs- und eine Produktionsumgebung ein.

* Sie möchten eine benutzerdefinierte Implementierung einer Hochverfügbarkeit mit mehreren Hubs durchführen. Weitere Informationen dazu finden Sie im [Abschnitt „Erzielen einer regionsübergreifenden Hochverfügbarkeit“ von „Hochverfügbarkeit und Notfallwiederherstellung“](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Sie möchten die Anzahl der für Ihren Hub konfigurierten [Partitionen](iot-hub-scaling.md#partitions) erhöhen. Dies wird bei der ersten Erstellung Ihres Hubs festgelegt und kann nicht geändert werden. Sie können Ihren Hub anhand der Informationen in diesem Artikel klonen und bei der Erstellung des Klons die Anzahl der Partitionen erhöhen.

Zum Klonen eines Hubs benötigen Sie ein Abonnement mit administrativem Zugriff auf den ursprünglichen Hub. Sie können den neuen Hub in eine neue Ressourcengruppe und-Region einfügen, und zwar in demselben Abonnement wie beim ursprünglichen Hub oder sogar in ein neues Abonnement. Allerdings können Sie dabei nicht denselben Namen verwenden, weil der Name des Hubs global eindeutig sein muss.

> [!NOTE]
> Zum aktuellen Zeitpunkt ist kein Feature zum automatischen Klonen eines IoT-Hubs verfügbar. Es handelt es sich vorwiegend um einen manuellen Prozess, der deshalb ziemlich fehleranfällig ist. Die Komplexität des Klonens eines Hubs ist direkt proportional zu dessen Komplexität. So ist beispielsweise das Klonen eines IoT Hubs ohne Nachrichtenrouting ziemlich einfach. Wenn Sie Nachrichtenrouting als nur eine Komplexität hinzufügen, wird das Klonen des Hubs mindestens eine kompliziertere Größenordnung. Wenn Sie außerdem die als Routingendpunkte verwendeten Ressourcen verschieben, ist dies eine weitere kompliziertere Größenordnung. 

## <a name="things-to-consider"></a>Zu beachtende Aspekte

Vor dem Klonen eines IoT Hubs müssen mehrere Aspekte beachtet werden.

* Stellen Sie sicher, dass alle am ursprünglichen Standort verfügbaren Features auch am neuen Standort verfügbar sind. Einige Dienste befinden sich noch in der Vorschauphase, und nicht alle Features stehen überall zur Verfügung.

* Entfernen Sie nicht die ursprünglichen Ressourcen, bevor Sie die geklonte Version erstellt und überprüft haben. Sobald Sie einen Hub entfernt haben, steht er definitiv nicht mehr zur Verfügung, und es gibt keine Möglichkeit zu seiner Wiederherstellung, um die Einstellungen und Daten zu überprüfen und so sicherzustellen, dass der Hub ordnungsgemäß repliziert wird.

* Weil viele Ressourcen global eindeutige Namen erfordern, müssen Sie für die geklonten Versionen andere Namen verwenden. Sie sollten auch für die Ressourcengruppe, zu der der geklonte Hub gehört, einen anderen Namen verwenden. 

* Daten für den ursprünglichen IoT Hub werden nicht migriert. Dazu gehören Telemetrienachrichten, Cloud-zu-Gerät-Befehle (Cloud-to-Device, C2D) und auftragsbezogene Informationen wie Zeitpläne und Verlauf. Auch Metriken und Protokollierungsergebnisse werden nicht migriert. 

* Bei Daten oder Nachrichten, die an Azure Storage weitergeleitet wurden, können Sie die Daten im ursprünglichen Speicherkonto beibehalten, sie in ein neues Speicherkonto in der neuen Region übertragen oder die alten Daten an ihrem Platz belassen und am neuen Standort ein neues Speicherkonto für die neuen Daten erstellen. Weitere Informationen zum Verschieben von Daten in Blob Storage finden Sie unter [Erste Schritte mit AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Daten für Event Hubs sowie für Service Bus-Themen und -Warteschlangen können nicht migriert werden. Dies sind Zeitpunktdaten, die nach Verarbeitung der Nachrichten nicht gespeichert werden.

* Sie müssen Downtime für die Migration planen. Das Klonen der Geräte in den neuen Hub kann dauern. Wenn Sie die Import/Export-Methode verwenden, haben Benchmarktests ergeben, dass es ungefähr zwei Stunden dauern kann, bis 500.000 Geräte verschoben wurden, und dass das Verschieben von einer Million Geräten vier Stunden dauern kann. 

* Sie können die Geräte in den neuen Hub kopieren, ohne die Geräte herunterfahren oder ändern zu müssen. 

    * Wenn die Geräte ursprünglich mithilfe von DPS bereitgestellt wurden, werden die in jedem Gerät gespeicherten Verbindungsinformationen bei einer erneuten Bereitstellung aktualisiert. 
    
    * Andernfalls müssen Sie die Geräte mithilfe der Import/Export-Methode verschieben. Danach müssen die Geräte geändert werden, damit sie den neuen Hub verwenden können. Sie können Ihr Gerät beispielsweise so einrichten, dass der Hostname des IoT Hubs aus den gewünschten Eigenschaften von Gerätezwillingen verwendet wird. Das Gerät übernimmt diesen IoT Hub-Hostnamen, trennt das Gerät vom alten Hub, und verbindet es dann mit dem neuen Hub.
    
* Sie müssen alle verwendeten Zertifikate aktualisieren, damit Sie sie bei den neuen Ressourcen verwenden können. Außerdem haben Sie den Hub wahrscheinlich irgendwo in einer DNS-Tabelle definiert. Diese DNS-Informationen müssen Sie aktualisieren.

## <a name="methodology"></a>Methodik

Dies ist die von uns empfohlene allgemeine Methode zum Verschieben eines IoT Hubs aus einer Region in eine andere. Beim Nachrichtenrouting wird davon ausgegangen, dass die Ressourcen nicht in die neue Region verschoben werden. Weitere Informationen finden Sie im [Abschnitt zum Nachrichtenrouting](#how-to-handle-message-routing).

   1. Exportieren Sie den Hub und dessen Einstellungen in eine Resource Manager-Vorlage. 
   
   1. Nehmen Sie die erforderlichen Änderungen an der Vorlage vor, wie z.B. das Aktualisieren aller Vorkommen des Namens und des Standorts für den geklonten Hub. Aktualisieren Sie für alle Ressourcen in der Vorlage, die für Nachrichtenroutingendpunkte verwendet werden, den Schlüssel in der Vorlage für die jeweilige Ressource.
   
   1. Importieren Sie die Vorlage in eine neue Ressourcengruppe am neuen Standort. Dadurch wird der Klon erstellt.

   1. Debuggen Sie nach Bedarf. 
   
   1. Fügen Sie alles hinzu, was nicht in die Vorlage exportiert wurde. 
   
       So werden beispielsweise Consumergruppen nicht in die Vorlage exportiert. Sie müssen die Consumergruppen der Vorlage entweder manuell hinzufügen oder dazu das [Azure-Portal](https://portal.azure.com) verwenden, nachdem der Hub erstellt wurde. Im Artikel [Verwendung einer Azure Resource Manager-Vorlage zum Konfigurieren des IoT Hub-Nachrichtenroutings](tutorial-routing-config-message-routing-rm-template.md) gibt es ein Beispiel dazu, wie eine Consumergruppe einer Vorlage hinzugefügt wird.
       
   1. Kopieren Sie die Geräte aus dem ursprünglichen Hub in den Klon. Die dazu erforderlichen Schritte finden Sie im Abschnitt [Verwalten der im IoT Hub registrierten Geräte](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Verarbeiten des Nachrichtenroutings

Wenn Ihr Hub [benutzerdefiniertes Routing](iot-hub-devguide-messages-read-custom.md) verwendet, wird die Routingkonfiguration beim Exportieren der Vorlage für den Hub einbezogen. Dies gilt nicht für die Ressourcen. Sie müssen auswählen, ob die Routingressourcen an den neuen Standort verschoben oder am alten Standort beibehalten und unverändert weiterhin verwendet werden sollen. 

Ein Beispiel: Sie haben einen Hub in der Region „USA, Westen“, der Nachrichten an ein Speicherkonto (ebenfalls in „USA, Westen“) weiterleitet, und möchten den Hub in die Region „USA, Osten“ verschieben. Sie können den Hub verschieben und festlegen, dass er weiterhin Nachrichten an das Speicherkonto in „USA, Westen“ weiterleiten soll, oder Sie können den Hub und auch das Speicherkonto verschieben. Beim Weiterleiten von Nachrichten an Endpunktressourcen in einer anderen Region kommt es möglicherweise zu einer geringfügigen Leistungssteigerung.

Sie können einen Hub, der Nachrichtenrouting verwendet, auf ziemlich einfache Weise verschieben, wenn Sie nicht außerdem die für die Routingendpunkte verwendeten Ressourcen verschieben. 

Wenn der Hub Nachrichtenrouting verwendet, haben Sie zwei Wahlmöglichkeiten. 

1. Verschieben Sie die für die Routingendpunkte verwendeten Ressourcen an den neuen Standort.

    * Sie müssen selbst die neuen Ressourcen erstellen – entweder manuell über das [Azure-Portal](https://portal.azure.com) oder unter Verwendung von Resource Manager-Vorlagen. 

    * Wenn Sie die Ressourcen am neuen Standort erstellen, müssen Sie alle umbenennen, da sie global eindeutige Namen haben. 
     
    * Sie müssen die Ressourcennamen und die Ressourcenschlüssel in der Vorlage des neuen Hubs aktualisieren, bevor Sie den neuen Hub erstellen. Die Ressourcen sollten bei der Erstellung des neuen Hubs vorhanden sein.

1. Verschieben Sie nicht die für die Routingendpunkte verwendeten Ressourcen. Verwenden Sie sie „direkt“.

   * In dem Schritt, in dem Sie die Vorlage bearbeiten, müssen Sie die Schlüssel für jede Routingressource abrufen und in der Vorlage ablegen, bevor Sie den neuen Hub erstellen. 

   * Der Hub verweist weiterhin auf die ursprünglichen Routingressourcen und leitet Nachrichten entsprechend der Konfiguration dorthin weiter.

   * Es wird nur eine geringe Leistungssteigerung erzielt, weil sich der Hub und die Routingendpunktressourcen nicht an demselben Standort befinden.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Vorbereitungen zum Migrieren des Hubs in eine andere Region

Dieser Abschnitt enthält spezifische Anleitungen zur Migration des Hubs.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Suchen Sie den ursprünglichen Hub, und exportieren Sie ihn in eine Ressourcenvorlage.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wechseln Sie zu **Ressourcengruppen**, und wählen Sie die Ressourcengruppe aus, die den zu verschiebenden Hub enthält. Sie können auch zu **Ressourcen** wechseln und so den Hub suchen. Wählen Sie den Hub aus.

1. Wählen Sie in der Liste der Eigenschaften und Einstellungen für den Hub **Vorlage exportieren** aus. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-export-template.png" alt-text="Screenshot des Befehls zum Exportieren der Vorlage für den IoT Hub" border="true":::

1. Wählen Sie **Herunterladen** aus, um die Vorlage herunterzuladen. Speichern Sie die Datei an einem Ort, an dem Sie sie wiederfinden können. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-download-template.png" alt-text="Screenshot des Befehls zum Herunterladen der Vorlage für den IoT Hub" border="true":::

### <a name="view-the-template"></a>Vorlage anzeigen 

1. Wechseln Sie zum Ordner „Downloads“ (oder dem Ordner, den Sie beim Exportieren der Vorlage verwendet haben), und suchen Sie nach der ZIP-Datei. Entpacken Sie die Zip-Datei und suchen Sie die Datei mit dem Namen `template.json`. Markieren und kopieren Sie es. Wechseln Sie in einen anderen Ordner und fügen Sie die Vorlagendatei ein (Strg+V). Jetzt können Sie sie bearbeiten.
 
    Das folgende Beispiel zeigt einen generischen Hub ohne Routingkonfiguration. Es ist ein S1-Tier-Hub (mit 1 Einheit) namens **ContosoHub** in der Region **WestUS**. Hier ist die exportierte Vorlage.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoHub_connectionString": {
                "type": "SecureString"
            },
            "IotHubs_ContosoHub_containerName": {
                "type": "SecureString"
            },
            "IotHubs_ContosoHub_name": {
                "defaultValue": "ContosoHub",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2021-07-01",
                "name": "[parameters('IotHubs_ContosoHub_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "identity": {
                    "type": "None"
                },
                "properties": {
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 4
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "[parameters('IotHubs_ContosoHub_connectionString')]",
                            "containerName": "[parameters('IotHubs_ContosoHub_containerName')]"
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None",
                    "disableLocalAuth": false,
                    "allowedFqdnList": []
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>Bearbeiten der Vorlage 

Sie müssen einige Änderungen vornehmen, bevor Sie die Vorlage verwenden können, um den neuen Hub in der neuen Region zu erstellen. Verwenden Sie [VS Code](https://code.visualstudio.com) oder einen Text-Editor zum Bearbeiten der Vorlage.

#### <a name="edit-the-hub-name-and-location"></a>Bearbeiten des Hubnamens und Standorts

1. Entfernen Sie den Abschnitt mit den Parametern für den Containernamen am oberen Rand. **ContosoHub** verfügt nicht über einen zugehörigen Container.

    ``` json
    "parameters": {
      ...
        "IotHubs_ContosoHub_containerName": {
            "type": "SecureString"
        },
      ...
    },
    ```

1. Entfernen Sie die **storageEndpoints**-Eigenschaft.

    ```json
    "properties": {
      ...
        "storageEndpoints": {
        "$default": {
            "sasTtlAsIso8601": "PT1H",
            "connectionString": "[parameters('IotHubs_ContosoHub_connectionString')]",
            "containerName": "[parameters('IotHubs_ContosoHub_containerName')]"
        }
      },
      ...
    
    ```

1. Ändern Sie unter **Ressourcen** den Ort von westUS in eastUS.

    Alte Version:

    ``` json 
    "location": "westus",
    ```
    
    Neue Version: 

    ``` json 
    "location": "eastus",
    ```
#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>Aktualisieren der Schlüssel für die Routingressourcen, die nicht verschoben werden

Wenn Sie die Resource Manager-Vorlage für einen Hub mit konfiguriertem Routing exportieren, werden Sie sehen, dass die Schlüssel für diese Ressourcen in der exportierten Vorlage nicht bereitgestellt werden; der Platz dafür ist durch Sternchen gekennzeichnet. Sie müssen sie ausfüllen, indem Sie im Portal zu diesen Ressourcen wechseln und die Schlüssel abrufen, **bevor** Sie die Vorlage für den neuen Hub importieren und den Hub erstellen. 

1. Rufen Sie die für jede der Routingressourcen erforderlichen Schlüssel ab, und fügen Sie sie in die Vorlage ein. Sie können den/die Schlüssel aus der Ressource im [Azure-Portal](https://portal.azure.com) abrufen. 

   Wenn Sie beispielsweise Nachrichten an einen Speichercontainer weiterleiten, suchen Sie das Speicherkonto im Portal. Wählen Sie im Abschnitt „Einstellungen“ **Zugriffsschlüssel** aus, und kopieren Sie einen der Schlüssel. Beim ersten Export der Vorlage sieht der Schlüssel so aus:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Nachdem Sie den Kontoschlüssel für das Speicherkonto abgerufen haben, fügen Sie ihn in der Vorlage in die Klausel `AccountKey=****` statt der Sternchen ein. 

1. Rufen Sie für Service Bus-Warteschlangen den Schlüssel für den gemeinsamen Zugriff ab, der mit „SharedAccessKeyName“ übereinstimmt. Hier sind der Schlüssel und `SharedAccessKeyName` im JSON-Code:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Dasselbe gilt für „Service Bus-Themen“ und „Event Hub-Verbindungen“.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Erstellen der neuen Routingressourcen am neuen Standort

Dieser Abschnitt gilt nur, wenn Sie die vom Hub verwendeten Ressourcen für die Routingendpunkte verschieben.

Wenn Sie die Routingressourcen verschieben möchten, müssen Sie sie am neuen Standort manuell einrichten. Sie können die Routingressourcen über das [Azure-Portal](https://portal.azure.com) erstellen oder indem Sie die Resource Manager-Vorlage für jede der Ressourcen, die vom Nachrichtenrouting verwendet wird, exportieren, die Ressourcen bearbeiten und dann importieren. Nachdem die Ressourcen eingerichtet wurden, können Sie die Vorlage des Hubs importieren (in der die Routingkonfiguration enthalten ist).

1. Erstellen Sie jede vom Routing verwendete Ressource. Sie können dies manuell über das [Azure-Portal](https://portal.azure.com) erledigen oder die Ressourcen mithilfe von Resource Manager-Vorlagen erstellen. Wenn Sie Vorlagen verwenden möchten, müssen Sie diese Schritte ausführen:

    1. Exportieren Sie jede vom Routing verwendete Ressource in eine Resource Manager-Vorlage.
    
    1. Aktualisieren Sie den Namen und den Standort der Ressource. 

    1. Aktualisieren Sie alle Querverweise zwischen den Ressourcen. Wenn Sie beispielsweise eine Vorlage für ein neues Speicherkonto erstellen, müssen Sie den Namen des Kontos in dieser Vorlage und jeder anderen Vorlage aktualisieren, die darauf verweist. In den meisten Fällen ist der Routingabschnitt in der Vorlage für den Hub die einzige andere Vorlage, die auf die Ressource verweist. 

    1. Importieren Sie jede der Vorlagen, die die jeweilige Ressource bereitstellt.

    Sobald die vom Routing verwendeten Ressourcen eingerichtet wurden und ausgeführt werden, können Sie den Vorgang fortsetzen.

1. Ändern Sie in der Vorlage für den IoT Hub den Namen der einzelnen Routingressourcen in deren neuen Namen, und aktualisieren Sie den Standort bei Bedarf. 

Jetzt haben Sie eine Vorlage zum Erstellen eines neuen Hubs, die fast genauso wie der alte Hub aussieht – je nachdem, wie Sie das Routing durchgeführt haben.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Verschieben – Erstellen des neuen Hubs in der neuen Region durch Laden der Vorlage

Erstellen Sie den neuen Hub am neuen Standort mithilfe der Vorlage. Wenn Sie Routingressourcen haben, die zum Verschieben vorgesehen sind, sollten sie am neuen Standort eingerichtet und die Verweise darauf in der Vorlage entsprechend aktualisiert werden. Wenn Sie die Routingressourcen nicht verschieben werden, sollten sie in der Vorlage mit den aktualisierten Schlüsseln enthalten sein.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Ressource erstellen**. 

1. Geben Sie in das Suchfeld „Vorlagenbereitstellung“ ein und drücken Sie die Eingabetaste.

1. Wählen Sie **Vorlagenbereitstellung (Bereitstellen mit benutzerdefinierten Vorlagen)** aus. Daraufhin wird ein Bildschirm für die Vorlagenbereitstellung geöffnet. Klicken Sie auf **Erstellen**. Sie sollten nun die folgende Anzeige sehen:

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png" alt-text="Screenshot des Befehls zum Erstellen Ihrer eigenen Vorlage":::

1. Wählen Sie **Eigene Vorlage im Editor erstellen** aus, damit Sie Ihre Vorlage aus einer Datei hochladen können. 

1. Wählen Sie **Datei laden** aus.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-upload-file.png" alt-text="Screenshot des Befehls zum Hochladen einer Vorlagendatei":::

1. Suchen Sie nach der von Ihnen bearbeiteten neuen Vorlage, wählen Sie sie und dann **Öffnen** aus. Dadurch wird Ihre Vorlage im Bearbeitungsfenster geladen. Wählen Sie **Speichern** aus. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-uploaded-file.png" alt-text="Screenshot, der das Laden der Vorlage zeigt":::

1. Füllen Sie die folgenden Felder auf der Seite für die benutzerdefinierte Bereitstellung aus.

   **Abonnement**: Wählen Sie das zu verwendende Abonnement aus.

   **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe an einem neuen Ort. Wenn Sie bereits eine eingerichtet haben, können Sie diese auswählen, anstatt eine neue zu erstellen.

   **Region**: Wenn Sie eine bestehende Ressourcengruppe ausgewählt haben, wird die Region für Sie ausgefüllt, damit sie dem Standort der Ressourcengruppe entspricht. Wenn Sie aber eine neue Ressourcengruppe erstellt haben, wird diese zum Standort der Gruppe.

   **Verbindungszeichenfolge**: Geben Sie die Verbindungszeichenfolge für Ihren Hub ein.

   **Hub-Name**: Geben Sie dem neuen Hub in der neuen Region einen Namen.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment-create.png" alt-text="Screenshot der Seite für die benutzerdefinierte Bereitstellung":::

1. Wählen Sie die Schaltfläche **Überprüfen + erstellen** aus.

1. Wählen Sie die Schaltfläche **Erstellen**. Das Portal validiert Ihre Vorlage und stellt Ihren geklonten Hub bereit. Wenn Sie Routingkonfigurationsdaten haben, werden diese zwar in den neuen Hub einbezogen, verweisen aber weiterhin auf die Ressourcen am vorherigen Standort.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment-final.png" alt-text="Screenshot der endgültigen benutzerdefinierten Bereitstellungsseite":::

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Verwalten der Geräte, die beim IoT Hub registriert sind

Nachdem Sie Ihren Klon einsatzbereit gemacht haben, müssen Sie alle Geräte aus dem ursprünglichen Hub in den Klon kopieren. 

Hierfür gibt es mehrere Vorgehensweisen. Ursprünglich haben Sie zum Bereitstellen der Geräte den [Device Provisioning-Dienst (DPS)](../iot-dps/about-iot-dps.md) verwendet oder ihn nicht verwendet. Wenn Sie ihn verwendet haben, ist dies nicht schwierig. Wenn Sie ihn nicht verwendet haben, kann dies sehr kompliziert sein. 

Wenn Sie Ihre Geräte nicht mithilfe von DPS bereitgestellt haben, können Sie den nächsten Abschnitt überspringen und mit [Verwendung von Import/Export zum Verschieben der Geräte in den neuen Hub](#using-import-export-to-move-the-devices-to-the-new-hub) beginnen.

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Verwendung von DPS zum erneuten Bereitstellen der Geräte im neuen Hub

Wenn Sie die Geräte mithilfe von DPS an den neuen Standort verschieben möchten, lesen Sie [Erneute Bereitstellung von Geräten](../iot-dps/how-to-reprovision.md). Wenn Sie damit fertig sind, können Sie die Geräte im [Azure-Portal](https://portal.azure.com) anzeigen und überprüfen, ob sie sich am neuen Standort befinden.

Wechseln Sie über das [Azure-Portal](https://portal.azure.com) zum neuen Hub. Wählen Sie Ihren Hub und dann **IoT-Geräte** aus. Es werden die Geräte angezeigt, die im geklonten Hub erneut bereitgestellt wurden. Sie können auch die Eigenschaften für den geklonten Hub anzeigen. 

Wenn Sie das Routing implementiert haben, vergewissern Sie sich durch Tests, dass Ihre Nachrichten ordnungsgemäß an die Ressourcen weitergeleitet werden.

### <a name="committing-the-changes-after-using-dps"></a>Committen der Änderungen nach Verwendung von DPS

Diese Änderung wurde vom Dienst DPS committet.

### <a name="rolling-back-the-changes-after-using-dps"></a>Ausführen eines Rollbacks der Änderungen nach der Verwendung von DPS 

Wenn Sie ein Rollback der Änderungen ausführen möchten, stellen Sie die Geräte aus dem neuen Hub im alten Hub erneut bereit.

Jetzt haben Sie die Migration Ihres Hubs und seiner Geräte abgeschlossen. Sie können zu [Bereinigung](#clean-up) wechseln.

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Verwendung von Import/Export zum Verschieben der Geräte in den neuen Hub

Das Ziel der Anwendung ist .NET Core, damit Sie sie unter Windows oder Linux ausführen können. Sie können das Beispiel herunterladen, Ihre Verbindungszeichenfolgen abrufen, die Flags für die auszuführenden Bits festlegen, und es ausführen. Bei allen diesen Vorgängen müssen Sie den Code nicht öffnen.

### <a name="downloading-the-sample"></a>Herunterladen des Beispiels

1. Verwenden Sie die IoT-C#-Beispiele auf dieser Seite: [Azure IoT-Beispiele für C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Laden Sie die ZIP-Datei herunter, und entzippen Sie sie auf Ihrem Computer. 

1. Der relevante Code ist in „./iot-hub/Samples/service/ImportExportDevicesSample“ zu finden. Sie müssen den Code nicht anzeigen oder bearbeiten, um die Anwendung ausführen zu können.

1. Geben Sie zum Ausführen der Anwendung drei Verbindungszeichenfolgen und fünf Optionen an. Sie übergeben diese Daten als Befehlszeilenargumente oder verwenden Umgebungsvariablen, oder aber Sie verwenden eine Kombination aus beiden. Wir werden die Optionen als Befehlszeilenargumente und die Verbindungszeichenfolgen als Umgebungsvariablen übergeben. 

   Der Grund dafür: Die Verbindungszeichenfolgen sind lang, bewirken nichts und werden sich wahrscheinlich nicht ändern, doch Sie möchten vielleicht die Optionen ändern und die Anwendung mehrmals ausführen. Wenn Sie den Wert einer Umgebungsvariablen ändern möchten, müssen Sie das Befehlsfenster und Visual Studio oder VS Code schließen – je nachdem, welche dieser Anwendungen Sie verwenden. 

### <a name="options"></a>Optionen

Hier sind die fünf Optionen, die Sie beim Ausführen der Anwendung angeben. Sie werden sie gleich in der Befehlszeile einfügen.

*   **addDevices** (Argument 1) – Legen Sie dafür den Wert „true“ fest, wenn Sie automatisch generierte virtuelle Geräte hinzufügen möchten. Diese Geräte werden dem Quellhub hinzugefügt. Legen Sie außerdem **numToAdd** (Argument 2) fest, um anzugeben, wie viele Geräte Sie hinzufügen möchten. Die maximale Anzahl von Geräten, die Sie bei einem Hub registrieren können, ist eine Million. Der Zweck dieser Option ist das Testen: Sie können eine bestimmte Anzahl von Geräten generieren und dann in einen anderen Hub kopieren.

*   **copyDevices** (Argument 3) – Legen Sie dafür den Wert „true“ fest, damit die Geräte aus einem Hub in einen anderen kopiert werden. 

*   **deleteSourceDevices** (Argument 4) – Legen Sie dafür den Wert „true“ fest, damit alle im Quellhub registrierten Geräte gelöscht werden. Wir empfehlen, vor der Ausführung so lange zu warten, bis Sie sicher sind, dass alle Geräte übertragen wurden. Nachdem Sie die Geräte gelöscht haben, können Sie sie nämlich nicht wiederherstellen.

*   **deleteDestDevices** (Argument 5) – Legen Sie dafür den Wert „true“ fest, damit alle beim Zielhub (dem Klon) registrierten Geräte gelöscht werden. Diese Vorgehensweise empfiehlt sich, wenn Sie die Geräte mehrmals kopieren möchten. 

Der grundlegende Befehl ist *dotnet run*. Er weist .NET an, die lokale „csproj“-Datei zu erstellen und anschließend auszuführen. Sie fügen Ihr Befehlszeilenargument am Ende hinzu, bevor Sie die Datei ausführen. 

Ihre Befehlszeile sollte so wie in diesen Beispielen aussehen:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Verwenden von Umgebungsvariablen für die Verbindungszeichenfolgen

1. Zum Ausführen des Beispiels benötigen Sie die Verbindungszeichenfolgen für den alten und den neuen IoT Hub sowie für ein Speicherkonto, das Sie für temporäre Arbeitsdateien verwenden können. Wir werden die Werte dafür in Umgebungsvariablen speichern.

1. Zum Abrufen der Werte für die Verbindungszeichenfolge melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Fügen Sie die Verbindungszeichenfolgen an einer Stelle ein, an der Sie sie abrufen können, z.B. in NotePad. Wenn Sie Folgendes kopieren, können Sie die Verbindungszeichenfolgen direkt an ihrem Ziel einfügen. Fügen Sie keine Leerzeichen vor und hinter dem Gleichheitszeichen hinzu, weil dadurch der Variablenname geändert wird. Sie benötigen auch keine doppelten Anführungszeichen vor und hinter den Verbindungszeichenfolgen. Wenn Sie Anführungszeichen vor und hinter der Verbindungszeichenfolge für das Speicherkonto einfügen, wird sie nicht funktionieren.

   Unter Windows legen Sie die Umgebungsvariablen folgendermaßen fest:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Unter Linux definieren Sie die Umgebungsvariablen folgendermaßen:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Navigieren Sie für die IoT Hub-Verbindungszeichenfolgen zu jedem einzelnen Hub im Portal. Sie können in **Ressourcen** nach dem Hub suchen. Wenn Sie die Ressourcengruppe wissen, können Sie zu **Ressourcengruppen** wechseln, Ihre Ressourcengruppe und dann den Hub aus der Liste der Ressourcen in dieser Gruppe auswählen. 

1. Wählen Sie in den Einstellungen für den Hub **Freigegebene Zugriffsrichtlinien**, anschließend **iothubowner** aus, und kopieren Sie eine der Verbindungszeichenfolgen. Gehen Sie beim Zielhub genauso vor. Fügen Sie die Verbindungszeichenfolgen den entsprechenden SET-Befehlen hinzu.

1. Suchen Sie für die „Verbindungszeichenfolge für Speicherkonto“ das gewünschte Speicherkonto in **Ressourcen** oder unter seiner **Ressourcengruppe**, und öffnen Sie es. 
   
1. Wählen Sie unter dem Abschnitt „Einstellungen“ die Option **Zugriffsschlüssel** aus, und kopieren Sie eine der Verbindungszeichenfolgen. Fügen Sie die Verbindungszeichenfolge für den entsprechenden SET-Befehl in Ihre Textdatei ein. 

Jetzt haben Sie die Umgebungsvariablen in einer Datei zusammen mit den SET-Befehlen und wissen, wie Ihre Befehlszeilenargumente lauten. Nun führen wir das Beispiel aus.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>Ausführen der Beispielanwendung und Verwenden von Befehlszeilenargumenten

1. Öffnen Sie ein Eingabeaufforderungsfenster. Wählen Sie dazu „Windows“ aus, und geben Sie `command prompt` ein.

1. Kopieren Sie die Befehle zum Festlegen der Umgebungsvariablen jeweils einzeln, fügen Sie sie im Eingabeaufforderungsfenster ein, und drücken Sie die EINGABETASTE. Wenn Sie damit fertig sind, geben Sie im Eingabeaufforderungsfenster `SET` ein, um Ihre Umgebungsvariablen und deren Werte anzuzeigen. Nachdem Sie diese in das Eingabeaufforderungsfenster kopiert haben, müssen Sie sie nicht erneut kopieren – außer wenn Sie ein neues Fenster öffnen.

1. Wechseln Sie im Eingabeaufforderungsfenster die Verzeichnisse, bis das Verzeichnis „./ImportExportDevicesSample“ angezeigt wird (in dem die Datei „ImportExportDevicesSample.csproj“ gespeichert ist). Geben Sie dann Folgendes ein, und beziehen Sie Ihre Befehlszeilenargumente mit ein:

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    Der Befehl „dotnet“ erstellt die Anwendung und führt sie aus. Weil Sie die Optionen beim Ausführen der Anwendung übergeben werden, können Sie deren Werte bei jeder Ausführung ändern. Vielleicht möchten Sie sie beispielsweise einmal ausführen und neue Geräte erstellen, anschließend erneut ausführen und diese Geräte in einen neuen Hub kopieren usw. Sie können auch alle Schritte in derselben Ausführung ausführen. Wir empfehlen jedoch, Geräte erst dann zu löschen, wenn Sie sicher sind, dass der Klonvorgang abgeschlossen ist. Hier ist ein Beispiel, in dem 1.000 Geräte erstellt und dann in den anderen Hub kopiert werden.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Nachdem Sie überprüft haben, ob die Geräte erfolgreich kopiert wurden, können Sie sie folgendermaßen aus dem Quellhub entfernen:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Ausführen der Beispielanwendung mit Visual Studio

1. Wenn Sie die Anwendung in Visual Studio ausführen möchten, ändern Sie Ihr aktuelles Verzeichnis auf den Ordner, in dem sich die Datei „IoTHubServiceSamples.sln“ befindet. Führen Sie dann diesen Befehl im Eingabeaufforderungsfenster aus, um die Projektmappe in Visual Studio zu öffnen. Das muss in demselben Fenster geschehen, in dem Sie die Umgebungsvariablen festlegen, damit diese Variablen bekannt sind.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Klicken Sie mit der rechten Maustaste auf das Projekt *ImportExportDevicesSample*, und wählen Sie **Als Startprojekt festlegen** aus.    
    
1. Legen Sie die Variablen oben in „Program.cs“ im Ordner „ImportExportDevicesSample“ für die fünf Optionen fest.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Drücken Sie F5, um die Anwendung auszuführen. Nach Abschluss der Ausführung können Sie die Ergebnisse anzeigen.

### <a name="view-the-results"></a>Anzeigen der Ergebnisse 

Sie können die Geräte im [Azure-Portal](https://portal.azure.com) anzeigen und überprüfen, ob sie sich am neuen Standort befinden.

1. Wechseln Sie über das [Azure-Portal](https://portal.azure.com) zum neuen Hub. Wählen Sie Ihren Hub und dann **IoT-Geräte** aus. Daraufhin werden die Geräte angezeigt, die Sie gerade aus dem alten Hub in den geklonten Hub kopiert haben. Sie können auch die Eigenschaften für den geklonten Hub anzeigen. 

1. Überprüfen Sie auf Import-/Exportfehler, indem Sie im [Azure-Portal](https://portal.azure.com) zum Azure-Speicherkonto wechseln und im Container `devicefiles` nach der Datei `ImportErrors.log` suchen. Wenn diese Datei leer ist (die Größe „0“ hat), sind keine Fehler aufgetreten. Wenn Sie versuchen, dasselbe Gerät mehrmals zu importieren, wird es beim zweiten Mal abgelehnt und in der Protokolldatei eine Fehlermeldung hinzugefügt.

### <a name="committing-the-changes"></a>Committen der Änderungen 

An diesem Punkt haben Sie Ihren Hub an den neuen Standort kopiert und die Geräte in den neuen Klon migriert. Jetzt müssen Sie entsprechende Änderungen vornehmen, damit die Geräte bei dem geklonten Hub funktionieren.

Zum Committen der Änderungen müssen Sie die folgenden Schritte ausführen: 

* Aktualisieren Sie jedes Gerät, um den IoT Hub-Hostnamen so zu ändern, dass er auf den neuen Hub verweist. Verwenden Sie dafür dieselbe Methode wie bei der ersten Bereitstellung des Geräts.

* Ändern Sie alle Ihre Anwendungen, die auf den alten Hub verweisen so, dass sie auf den neuen Hub verweisen.

* Nachdem Sie diesen Vorgang abgeschlossen haben, sollte der neue Hub einsatzbereit sein. Bei dem alten Hub sollte es keine aktiven Geräte mehr geben, und er sollte deaktiviert sein. 

### <a name="rolling-back-the-changes"></a>Rollback der Änderungen

Wenn Sie ein Rollback der Änderungen ausführen möchten, führen Sie die folgenden Schritte aus:

* Aktualisieren Sie jedes Gerät, um den IoT Hub-Hostnamen so zu ändern, dass er auf den IoT Hub-Hostnamen des alten Hubs verweist. Verwenden Sie dafür dieselbe Methode wie bei der ersten Bereitstellung des Geräts.

* Ändern Sie alle Ihre Anwendungen, die auf den neuen Hub verweisen so, dass sie auf den alten Hub verweisen. Wenn Sie beispielsweise Azure Analytics verwenden, müssen Sie eventuell Ihre [Azure Stream Analytics-Eingabe](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub) neu konfigurieren.

* Löschen Sie den neuen Hub. 

* Wenn Sie Routingressourcen haben, sollte die Konfiguration im alten Hub weiterhin auf die richtige Routingkonfiguration verweisen und nach dem Neustart des Hubs bei diesen Ressourcen funktionieren.

### <a name="checking-the-results"></a>Überprüfen der Ergebnisse 

Wenn Sie die Ergebnisse überprüfen möchten, ändern Sie Ihre IoT-Lösung so, dass sie auf Ihren Hub am neuen Standort verweist, und führen Sie sie aus. Mit anderen Worten: Führen Sie dieselben Aktionen mit dem neuen Hub aus, die Sie mit dem vorherigen Hub ausgeführt haben, und stellen Sie sicher, dass sie ordnungsgemäß funktionieren. 

Wenn Sie das Routing implementiert haben, vergewissern Sie sich durch Tests, dass Ihre Nachrichten ordnungsgemäß an die Ressourcen weitergeleitet werden.

## <a name="clean-up"></a>Bereinigung

Führen Sie eine Bereinigung erst aus, wenn Sie wirklich sicher sind, dass der neue Hub einsatzbereit ist und die Geräte ordnungsgemäß funktionieren. Testen Sie auch unbedingt das Routing, wenn Sie dieses Feature verwenden. Wenn Sie damit fertig sind, bereinigen Sie die alten Ressourcen mit den folgenden Schritten:

* Falls es noch nicht geschehen ist, löschen Sie den alten Hub. So werden alle aktiven Geräte aus dem Hub entfernt.

* Wenn Sie Ihre vorhandenen Routingressourcen an den neuen Standort verschoben haben, können Sie die alten Ressourcen löschen.

## <a name="next-steps"></a>Nächste Schritte

Sie haben einen IoT Hub in einen neuen Hub in einer neuen Region geklont, und zwar vollständig mit den Geräten. Weitere Informationen zum Ausführen von Massenvorgängen für die Identitätsregistrierung in einem IoT Hub finden Sie unter [Importieren und Exportieren von IoT Hub-Geräteidentitäten per Massenvorgang](iot-hub-bulk-identity-mgmt.md).

Weitere Informationen zum IoT Hub und zur Entwicklung für den Hub finden Sie in den folgenden Artikeln:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)

* [Tutorial zum IoT Hub-Routing](tutorial-routing.md)

* [Übersicht über die IoT Hub-Geräteverwaltung](iot-hub-device-management-overview.md)

* Wenn Sie die Beispielanwendung bereitstellen möchten, lesen Sie bitte [Bereitstellung einer .NET Core-Anwendung](/dotnet/core/deploying/index).