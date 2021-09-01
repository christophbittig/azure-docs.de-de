---
title: Filtern von Ereignissen für Azure Event Grid
description: In diesem Artikel wird beschrieben, wie Sie beim Erstellen eines Event Grid-Abonnements Ereignisse filtern (nach Ereignistyp, Betreff, Operatoren und Daten usw.).
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 666690fc7c0d21c7ae257a20b49949961d89cb60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339930"
---
# <a name="filter-events-for-event-grid"></a>Filtern von Ereignissen für Event Grid

Dieser Artikel zeigt, wie Sie Ereignisse beim Erstellen eines Event Grid-Abonnements filtern können. Weitere Informationen zu den Optionen für die Ereignisfilterung finden Sie unter [Grundlegendes zur Ereignisfilterung für Event Grid-Abonnements](event-filtering.md).

## <a name="filter-by-event-type"></a>Filtern nach Ereignistyp

Beim Erstellen eines Event Grid-Abonnements können Sie angeben, welche [Ereignistypen](event-schema.md) an den Endpunkt gesendet werden sollen. Die Beispiele in diesem Abschnitt erstellen Ereignisabonnements für eine Ressourcengruppe, beschränken jedoch die Ereignisse, die an `Microsoft.Resources.ResourceWriteFailure` und `Microsoft.Resources.ResourceWriteSuccess` gesendet werden. Wenn Sie mehr Flexibilität beim Filtern von Ereignissen nach Ereignistypen benötigen, lesen Sie „Filtern nach erweiterten Operatoren und Datenfeldern“.

### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie für PowerShell den Parameter `-IncludedEventType` beim Erstellen des Abonnements.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

### <a name="azure-cli"></a>Azure CLI
Verwenden Sie für die Azure CLI den Parameter `--included-event-types`. Im folgenden Beispiel wird die Azure-CLI in einer Bash-Shell verwendet:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie auf der Seite **Ereignisabonnement** zur Registerkarte **Filter**. 
1. Wählen Sie **Ereignistyp hinzufügen**  neben **Filtern nach Ereignistypen** aus. 

    :::image type="content" source="./media/how-to-filter-events/add-event-type-button.png" alt-text="Screenshot der Seite „Ereignisabonnement“ mit ausgewählter Schaltfläche „Ereignistyp hinzufügen“.":::    
1. Geben Sie den Ereignistyp ein, und drücken Sie die EINGABETASTE. Im folgenden Beispiel lautet der Ereignistyp `Microsoft.Resources.ResourceWriteSuccess`. 

    :::image type="content" source="./media/how-to-filter-events/sample-event-type.png" alt-text="Screenshot der Seite „Ereignisabonnement“ mit einem Beispielereignistyp.":::    
      
### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage
Verwenden Sie für eine Resource Manager-Vorlage, die Eigenschaft `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

> [!NOTE]
> Weitere Informationen zu diesen Filtern (Ereignistypen, Betreff und Erweitert) finden Sie unter [Grundlegendes zur Ereignisfilterung für Event Grid-Abonnements](event-filtering.md). 

## <a name="filter-by-subject"></a>Filtern nach Betreff

Sie können Ereignisse nach dem Betreff in den Ereignisdaten filtern. Sie können einen Wert angeben, der dem Anfang oder Ende des Betreffs entspricht. Wenn Sie mehr Flexibilität beim Filtern von Ereignissen nach Betreff benötigen, lesen Sie „Filtern nach erweiterten Operatoren und Datenfeldern“.

Im folgenden PowerShell-Beispiel erstellen Sie ein Ereignisabonnement, das nach dem Anfang des Betreffs filtert. Mit dem Parameter `-SubjectBeginsWith` können Sie Ereignisse auf solche für eine bestimmte Ressource beschränken. Sie übergeben die Ressourcen-ID einer Netzwerksicherheitsgruppe.

### <a name="azure-powershell"></a>Azure PowerShell
```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

Im nächste PowerShell-Beispiel wird ein Abonnement für einen Blob Storage erstellt. Es begrenzt die Ereignisse auf die, deren Betreff auf `.jpg` endet.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

### <a name="azure-cli"></a>Azure CLI
Im folgenden Azure CLI-Beispiel erstellen Sie ein Ereignisabonnement, das nach dem Anfang des Betreffs filtert. Mit dem Parameter `--subject-begins-with` können Sie Ereignisse auf solche für eine bestimmte Ressource beschränken. Sie übergeben die Ressourcen-ID einer Netzwerksicherheitsgruppe.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Das nächste Azure CLI-Beispiel erstellt ein Abonnement für einen Blob Storage. Es begrenzt die Ereignisse auf die, deren Betreff auf `.jpg` endet.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie auf der Seite **Ereignisabonnement** die Option **Betrefffilterung aktivieren** aus. 
1. Geben Sie Werte für mindestens eines der folgenden Felder ein: **Betreff beginnt mit** und **Betreff endet mit**. In den folgenden Optionen sind beide Optionen ausgewählt. 

    :::image type="content" source="./media/how-to-filter-events/subject-filter-example.png" alt-text="Screenshot der Seite „Ereignisabonnement“ mit Beispiel für Betrefffilterung.":::
1. Wählen Sie die Option **Betreffabgleich unter Beachtung von Groß-/Kleinschreibung** aus, wenn der Betreff des Ereignisses mit der Groß-/Kleinschreibung der angegebenen Filter übereinstimmen soll. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage
Im folgenden Beispiel für eine Resource Manager-Vorlage erstellen Sie ein Ereignisabonnement, das nach dem Anfang des Betreffs filtert. Mit der Eigenschaft `subjectBeginsWith` können Sie Ereignisse auf solche für eine bestimmte Ressource beschränken. Sie übergeben die Ressourcen-ID einer Netzwerksicherheitsgruppe.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

Das nächste Beispiel für eine Resource Manager-Vorlage erstellt ein Abonnement für einen Blob Storage. Es begrenzt die Ereignisse auf die, deren Betreff auf `.jpg` endet.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

> [!NOTE]
> Weitere Informationen zu diesen Filtern (Ereignistypen, Betreff und Erweitert) finden Sie unter [Grundlegendes zur Ereignisfilterung für Event Grid-Abonnements](event-filtering.md). 

## <a name="filter-by-operators-and-data"></a>Filtern nach Operatoren und Daten

Um bei der Filterung mehr Flexibilität zu erhalten, können Sie Ereignisse nach Operatoren und Dateneigenschaften filtern.

### <a name="subscribe-with-advanced-filters"></a>Abonnieren mit erweiterten Filtern

Weitere Informationen zu den Operatoren und den Schlüsseln, die Sie für die erweiterte Filterung verwenden können, finden Sie unter [Erweiterte Filterung](event-filtering.md#advanced-filtering).

Diese Beispiele erstellen ein benutzerdefiniertes Thema. Sie abonnieren das benutzerdefinierte Thema und filtern nach einem Wert im Datenobjekt. Ereignisse, bei denen die Farbeigenschaft auf „Blau“, „Rot“ oder „Grün“ eingestellt ist, werden an das Abonnement gesendet.

### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie für PowerShell Folgendes:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="azure-cli"></a>Azure CLI

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Beachten Sie, dass für das Abonnement ein [Ablaufdatum](concepts.md#event-subscription-expiration) festgelegt ist.


### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie auf der Seite **Ereignisabonnement** die Option **Neuen Filter hinzufügen** im Abschnitt **ERWEITERTE FILTER** aus. 

    :::image type="content" source="./media/how-to-filter-events/add-new-filter-button.png" alt-text="Screenshot: Seite „Ereignisabonnement“ mit hervorgehobenem Link „Neuen Filter hinzufügen“.":::    
2. Geben Sie einen Schlüssel, einen Operator und Werte an, die verglichen werden sollen. Im folgenden Beispiel wird **data.color** als Schlüssel und **String is in** als Operator angegeben. **blue**, **red** und **green** sind Werte. 

    :::image type="content" source="./media/how-to-filter-events/advanced-filter-example.png" alt-text="Screenshot: Beispiel für einen erweiterten Filter."::: 

    > [!NOTE]
    > Weitere Informationen zu erweiterten Filtern finden Sie unter [Grundlegendes zur Ereignisfilterung für Event Grid-Abonnements](event-filtering.md). 


### <a name="test-the-filter"></a>Testen des Filters
Um den Filter zu testen, senden Sie ein Ereignis, bei dem im Farbfeld „Grün“ festgelegt ist. Da „Grün“ einer der Werte im Filter ist, wird das Ereignis an den Endpunkt übermittelt.

### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie für PowerShell Folgendes:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Um ein Szenario zu testen, in dem das Ereignis nicht gesendet wird, senden Sie ein Ereignis, bei dem das Farbfeld auf „Gelb“ gesetzt ist. Gelb ist nicht einer der im Abonnement angegebenen Werte, sodass das Ereignis nicht an Ihr Abonnement gesendet wird.

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```


### <a name="azure-cli"></a>Azure CLI
Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Um ein Szenario zu testen, in dem das Ereignis nicht gesendet wird, senden Sie ein Ereignis, bei dem das Farbfeld auf „Gelb“ gesetzt ist. Gelb ist nicht einer der im Abonnement angegebenen Werte, sodass das Ereignis nicht an Ihr Abonnement gesendet wird.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Filtern (Ereignistypen, Betreff und Erweitert) finden Sie unter [Grundlegendes zur Ereignisfilterung für Event Grid-Abonnements](event-filtering.md). 

