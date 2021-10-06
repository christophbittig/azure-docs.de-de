---
title: Integrieren von Azure-Speicher für Benachrichtigungen und die Modellsicherung
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie Azure-Speicher so integrieren, dass Sie Pushbenachrichtigungen erhalten, wenn Sie Custom Vision-Modelle trainieren oder exportieren. Außerdem können Sie eine Sicherungskopie der exportierten Modelle speichern.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 06/25/2021
ms.author: pafarley
ms.openlocfilehash: d247626607f5fd4b517d7436c21ae33ed5727220
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353321"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Integrieren von Azure-Speicher für Benachrichtigungen und Sicherungen

Sie können Ihr Custom Vision-Projekt in eine Azure-Blobspeicher-Warteschlange integrieren, um Pushbenachrichtigungen zu Trainings- und Exportaktivitäten für Projekte und zu Sicherungskopien von veröffentlichten Modellen zu erhalten. Dieses Feature ist hilfreich, um das ständige Abfragen des Diensts nach Ergebnissen zu vermeiden, wenn lange Vorgänge ausgeführt werden. Stattdessen können Sie die Benachrichtigungen für die Speicherwarteschlange in Ihren Workflow integrieren.

In diesem Leitfaden wird veranschaulicht, wie Sie diese REST-APIs mit cURL verwenden. Sie können auch einen HTTP-Anforderungsdienst wie Postman verwenden, um die Anforderungen auszugeben.

> [!NOTE]
> Pushbenachrichtigungen sind vom optionalen Parameter _notificationQueueUri_ in der **CreateProject**-API abhängig, und für Modellsicherungen müssen Sie zusätzlich den optionalen Parameter _exportModelContainerUri_ verwenden. In diesem Leitfaden wird beides verwendet, damit alle Features vorhanden sind.

## <a name="prerequisites"></a>Voraussetzungen

- Eine Custom Vision-Ressource in Azure. Falls Sie nicht über diese Ressource verfügen, wechseln Sie zum Azure-Portal und [erstellen eine neue Custom Vision-Ressource](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Von diesem Feature wird die Cognitive Service-Ressource (All-in-One-Schlüssel) derzeit nicht unterstützt.
- Ein Azure Storage-Konto mit einem Blobcontainer. Befolgen Sie diese [Storage-Schnellstartanleitung](../../storage/blobs/storage-quickstart-blobs-portal.md), wenn Sie Hilfe zu diesem Schritt benötigen.
- [PowerShell ab Version 6.0](/powershell/scripting/install/installing-powershell-core-on-windows) oder eine ähnliche Befehlszeilenanwendung

## <a name="set-up-azure-storage-integration"></a>Einrichten der Azure-Speicherintegration

Navigieren Sie im Azure-Portal zu Ihrer Custom Vision-Trainingsressource, wählen Sie die Seite **Identität** aus, und aktivieren Sie die systemseitig zugewiesene verwaltete Identität.

Navigieren Sie als Nächstes im Azure-Portal zu Ihrer Speicherressource. Wechseln Sie zur Seite **Zugriffssteuerung (IAM)** , und wählen Sie dann **Rollenzuweisung hinzufügen (Vorschau)** aus. Fügen Sie dann eine Rollenzuweisung für eines der beiden oder für beide Integrationsfeatures hinzu:
* Wenn Sie die Verwendung des Features für die Modellsicherung planen, wählen Sie die Rolle **Mitwirkender an Storage-Blobdaten** aus, und fügen Sie Ihre Custom Vision-Trainingsressource als Mitglied hinzu. Wählen Sie zum Abschließen **Überprüfen + zuweisen** aus.
* Wenn Sie die Verwendung des Features für die Benachrichtigungswarteschlange planen, wählen Sie die Rolle **Mitwirkender an Storage-Warteschlangendaten** aus, und fügen Sie Ihre Custom Vision-Trainingsressource als Mitglied hinzu. Wählen Sie zum Abschließen **Überprüfen + zuweisen** aus.

Hilfe zu Rollenzuweisungen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="get-integration-urls"></a>Abrufen von Integrations-URLs

Als Nächstes rufen Sie die URLs ab, über die Ihre Custom Vision-Ressource auf diese Endpunkte zugreifen kann.

Sie können auf die Integrations-URL für die Benachrichtigungswarteschlange zugreifen, indem Sie zur Seite **Warteschlangen** Ihres Speicherkontos navigieren, eine neue Warteschlange hinzufügen und die zugehörige URL an einem temporären Speicherort speichern.

> [!div class="mx-imgBorder"]
> ![Seite für Azure-Speicherwarteschlange](./media/storage-integration/queue-url.png) 

Sie können auf die Integrations-URL für die Modellsicherung zugreifen, indem Sie zur Seite **Container** Ihres Speicherkontos navigieren und einen neuen Container erstellen. Wählen Sie ihn anschließend aus, und navigieren Sie zur Seite **Eigenschaften**. Kopieren Sie die URL an einen temporären Speicherort.
 
> [!div class="mx-imgBorder"]
> ![Azure-Speicherkonto: Seite mit Containereigenschaften](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Integrieren des Custom Vision-Projekts

Da Sie nun über die Integrations-URLs verfügen, können Sie ein neues Custom Vision-Projekt erstellen, in dem die Azure Storage-Features integriert sind. Sie können auch ein vorhandenes Projekt aktualisieren, um die Features hinzuzufügen.

### <a name="create-new-project"></a>Erstellen eines neuen Projekts

Fügen Sie beim Aufrufen der [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)-API die optionalen Parameter _exportModelContainerUri_ und _notificationQueueUri_ hinzu. Weisen Sie die URL-Werte zu, die Sie im vorherigen Abschnitt erhalten haben. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Wenn Sie die Antwort `200/OK` erhalten, bedeutet dies, dass die Einrichtung der URLs erfolgreich war. In der JSON-Antwort sollten auch URL-Werte angezeigt werden:

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Aktualisieren des vorhandenen Projekts

Rufen Sie zum Aktualisieren eines vorhandenen Projekts mit Integration des Azure-Speicherfeatures die [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1)-API auf, indem Sie die ID des zu aktualisierenden Projekts verwenden. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Legen Sie den Anforderungstext (`body`) auf das folgende JSON-Format fest, und fügen Sie die entsprechenden Werte für _exportModelContainerUri_ und _notificationQueueUri_ ein:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Wenn Sie die Antwort `200/OK` erhalten, bedeutet dies, dass die Einrichtung der URLs erfolgreich war.

## <a name="verify-the-connection"></a>Überprüfen der Verbindung 

Durch Ihren API-Aufruf im vorherigen Abschnitt sollte bereits die Anzeige neuer Informationen in Ihrem Azure-Speicherkonto ausgelöst worden sein. 

In Ihrem angegebenen Container sollte ein Testblob in einem Ordner mit dem Namen **CustomVision-TestPermission** enthalten sein. Dieses Blob ist nur vorübergehend vorhanden.

In Ihrer Benachrichtigungswarteschlange sollte eine Testbenachrichtigung im folgenden Format angezeigt werden:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Abrufen von Ereignisbenachrichtigungen

Gehen Sie wie folgt vor, wenn Sie bereit sind: Rufen Sie die [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1)-API in Ihrem Projekt auf, um einen regulären Trainingsvorgang durchzuführen.

In Ihrer Storage-Benachrichtigungswarteschlange erhalten Sie eine Benachrichtigung, nachdem der Trainingsvorgang abgeschlossen ist:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

Das Feld `"trainingStatus"` kann entweder `"TrainingCompleted"` oder `"TrainingFailed"` enthalten. Das Feld `"iterationId"` ist die ID des trainierten Modells.

## <a name="get-model-export-backups"></a>Abrufen der Sicherungen von Modellexporten

Gehen Sie wie folgt vor, wenn Sie bereit sind: Rufen Sie die [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece)-API auf, um ein trainiertes Modell in eine angegebene Plattform zu exportieren.

Im angegebenen Speichercontainer wird eine Sicherungskopie des exportierten Modells angezeigt. Der Blobname hat das folgende Format:

```
{projectId} - {iterationId}.{platformType}
```

Außerdem erhalten Sie eine Benachrichtigung in Ihrer Warteschlange, nachdem der Exportvorgang abgeschlossen ist. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

Das Feld `"exportStatus"` kann entweder `"ExportCompleted"` oder `"ExportFailed"` enthalten. Das Feld `"modelUri"` enthält die URL des Sicherungsmodells, das in Ihrem Container gespeichert ist. Hierbei wird angenommen, dass Sie zu Beginn Warteschlangenbenachrichtigungen integriert haben. Falls nicht, wird im Feld `"modelUri"` die SAS-URL für Ihr Blob mit dem Custom Vision-Modell angezeigt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden wurde beschrieben, wie Sie ein Projekt zwischen Custom Vision-Ressourcen kopieren und verschieben. Sehen Sie sich als Nächstes die API-Referenzdokumentation an, um sich über weitere Möglichkeiten bei der Verwendung von Custom Vision zu informieren.
* [REST-API-Referenzdokumentation (Training)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [REST-API-Referenzdokumentation (Vorhersage)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)