---
title: 'Tutorial: Konfigurieren des Nachrichtenroutings für Azure IoT Hub mit Azure PowerShell'
description: 'Tutorial: Konfigurieren des Nachrichtenroutings für Azure IoT Hub mithilfe von Azure PowerShell Führen Sie je nach den Eigenschaften in der Nachricht entweder die Weiterleitung an ein Speicherkonto oder an eine Service Bus-Warteschlange durch.'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: lizross
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 8ae8c8a6f8c9be606a95a046eb14149bcfa8a2fb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547315"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Tutorial: Konfigurieren des IoT Hub-Nachrichtenroutings mithilfe von Azure PowerShell

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Herunterladen des Skripts (optional)

Im zweiten Teil dieses Tutorials laden Sie eine Visual Studio-Anwendung herunter und führen sie aus, um Nachrichten an IoT Hub zu senden. Der Download beinhaltet einen Ordner, der die Azure Resource Manager-Vorlage und die Parameterdatei sowie die Azure CLI- und PowerShell-Skripts enthält. 

Wenn Sie sich das fertige Skript ansehen möchten, laden Sie die [Azure IoT-Beispiele für C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip) herunter. Entzippen Sie die Datei „main.zip“. Das Azure CLI-Skript (**iothub_routing_psh.ps1**) finden Sie unter „/iot-hub/Tutorials/Routing/SimulatedDevice/resources/“.

## <a name="create-your-resources"></a>Erstellen Ihrer Ressourcen

Erstellen Sie zunächst die Ressourcen mithilfe von PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Erstellen der grundlegenden Ressourcen mithilfe von PowerShell

Kopieren Sie das folgende Skript, fügen Sie es in Cloud Shell ein, und drücken Sie die EINGABETASTE. Daraufhin wird das Skript Zeile für Zeile ausgeführt. Der erste Abschnitt des Skripts erstellt die grundlegenden Ressourcen für dieses Tutorial (einschließlich Speicherkonto, IoT Hub, Service Bus-Namespace und Service Bus-Warteschlange). Kopieren Sie im weiteren Verlauf des Tutorials jeweils die einzelnen Skriptblöcke, und fügen Sie sie zur Ausführung in Cloud Shell ein.

Einige Ressourcennamen müssen global eindeutig sein. Hierzu zählen beispielsweise der IoT Hub-Name und der Name des Speicherkontos. Zur Vereinfachung wird an diese Ressourcennamen ein alphanumerischer Zufallswert namens *randomValue* angefügt. Der Zufallswert wird einmalig zu Beginn des Skripts generiert und innerhalb des gesamten Skripts nach Bedarf an die Ressourcennamen angefügt. Falls Sie keinen Zufallswert verwenden möchten, können Sie den Wert auf eine leere Zeichenfolge oder auf einen bestimmten Wert festlegen. 

> [!IMPORTANT]
> Die im Ausgangsskript festgelegten Variablen werden auch vom Routingskript verwendet. Führen Sie daher das gesamte Skript in der gleichen Cloud Shell-Sitzung aus. Wenn Sie eine neue Sitzung öffnen, um das Skript zum Einrichten des Routings auszuführen, verfügen einige der Variablen über keine Werte. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Erstellen Sie ein simuliertes Gerät.

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Nachdem die grundlegenden Ressourcen eingerichtet wurden, können Sie das Nachrichtenrouting konfigurieren.

## <a name="set-up-message-routing"></a>Einrichten der Nachrichtenweiterleitung

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Verwenden Sie [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint), um einen Routingendpunkt zu erstellen. Verwenden Sie [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute), um die Messagingroute für den Endpunkt zu erstellen.

### <a name="route-to-a-storage-account"></a>Weiterleiten an ein Speicherkonto 

Richten Sie zuerst den Endpunkt für das Speicherkonto ein, und erstellen Sie dann die Nachrichtenroute.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Hierbei handelt es sich um die Variablen, die vom Skript verwendet werden und in der Cloud Shell-Sitzung festgelegt werden müssen:

**resourceGroup**: Legen Sie beide Vorkommen dieses Felds auf Ihre Ressourcengruppe fest.

**name:** Der Name der IoT Hub-Instanz, für die das Routing gilt.

**endpointName**: Der Name zur Identifizierung des Endpunkts. 

**endpointType**: Der Endpunkttyp. Dieser Wert muss auf `azurestoragecontainer`, `eventhub`, `servicebusqueue` oder `servicebustopic` festgelegt werden. Legen Sie ihn für dieses Tutorial auf `azurestoragecontainer` fest.

**subscriptionID**: Die Abonnement-ID für Ihr Azure-Konto.

**storageConnectionString**: Dieser Wert wird aus dem Speicherkonto abgerufen, das im vorherigen Skript eingerichtet wurde. Er wird beim Routing verwendet, um auf das Speicherkonto zuzugreifen.

**containerName**: Der Name des Containers im Speicherkonto, in den die Daten geschrieben werden.

**Encoding**: Legen Sie dieses Feld auf `AVRO` oder `JSON` fest. Dadurch wird das Format der gespeicherten Daten angegeben. Standardwert: AVRO.

**routeName**: Der Name der Route, die Sie einrichten. 

**condition**: Die Abfrage, mit der nach den Nachrichten gefiltert wird, die an diesen Endpunkt gesendet wurden. Die Abfragebedingung für die Nachrichten, die an den Speicher weitergeleitet werden, lautet `level="storage"`.

**enabled**: Dieses Feld ist standardmäßig auf `true` festgelegt. Dies bedeutet, dass die Nachrichtenroute nach der Erstellung aktiviert werden soll.

Kopieren Sie das folgende Skript, und fügen Sie es in Ihr Cloud Shell-Fenster ein:

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Als Nächstes wird der Routingendpunkt für das Speicherkonto erstellt. Außerdem wird der Container angegeben, in dem die Ergebnisse gespeichert werden sollen. Der Container wurde zusammen mit dem Speicherkonto erstellt.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Erstellen Sie als Nächstes die Nachrichtenroute für den Speicherendpunkt. Die Nachrichtenroute legt fest, wohin die Nachrichten gesendet werden sollen, die der Abfragespezifikation entsprechen.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Weiterleiten an eine Service Bus-Warteschlange

Richten Sie jetzt die Weiterleitung für die Service Bus-Warteschlange ein. Um die Verbindungszeichenfolge für die Service Bus-Warteschlange abrufen zu können, müssen Sie eine Autorisierungsregel mit korrekten Berechtigungen erstellen. Das folgende Skript erstellt eine Autorisierungsregel für die Service Bus-Warteschlange namens `sbauthrule` und legt die Rechte auf `Listen Manage Send` fest. Nach Einrichtung dieser Autorisierungsregel können Sie damit die Verbindungszeichenfolge für die Warteschlange abrufen.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Verwenden Sie nun die Autorisierungsregel, um den Service Bus-Warteschlangenschlüssel abzurufen. Diese Autorisierungsregel wird weiter unten im Skript zum Abrufen der Verbindungszeichenfolge verwendet.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Richten Sie nun den Routingendpunkt und die Nachrichtenroute für die Service Bus-Warteschlange ein. Hierbei handelt es sich um die Variablen, die vom Skript verwendet werden und in der Cloud Shell-Sitzung festgelegt werden müssen:

**endpointName**: Der Name zur Identifizierung des Endpunkts. 

**endpointType**: Der Endpunkttyp. Dieser Wert muss auf `azurestoragecontainer`, `eventhub`, `servicebusqueue` oder `servicebustopic` festgelegt werden. Legen Sie ihn für dieses Tutorial auf `servicebusqueue` fest.

**routeName**: Der Name der Route, die Sie einrichten. 

**condition**: Die Abfrage, mit der nach den Nachrichten gefiltert wird, die an diesen Endpunkt gesendet wurden. Die Abfragebedingung für die Nachrichten, die an die Service Bus-Warteschlange weitergeleitet werden, lautet `level="critical"`.

Hier sehen Sie das Azure PowerShell-Skript für das Nachrichtenrouting für die Service Bus-Warteschlange:

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# If this script fails on the next statement (Add-AzIotHubRoutingEndpoint),
# put the pause in and run it again. Note that if you're running it
# interactively, you can just stop it and then run the rest, because
# you have already set the variables before you get to this point.
#
# Pause for 90 seconds to allow previous steps to complete.
# Then report it to the IoT team here: 
# https://github.com/Azure/azure-powershell/issues
#   pause for 90 seconds and then start again. 
# This way, it if didn't get to finish before it tried to move on, 
#   now it will have time to finish first.
   Start-Sleep -Seconds 90

# This command is the one that sometimes doesn't work. It's as if it doesn't have time to
#   finish before it moves to the next line.
# The error from Add-AzIotHubRoutingEndpoint is "Operation returned an invalid status code 'BadRequest'".
# This command adds the routing endpoint, using the connection string property from the key. 
# This will definitely work if you execute the Sleep command first (it's in the line above).
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Anzeigen des Nachrichtenroutings im Portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Ressourcen eingerichtet und die Nachrichtenrouten konfiguriert haben, können Sie sich nun im nächsten Tutorial darüber informieren, wie Sie Nachrichten an IoT Hub senden, die dann an die verschiedenen Ziele weitergeleitet werden. 

> [!div class="nextstepaction"]
> [Teil 2: Anzeigen der Ergebnisse des Nachrichtenroutings](tutorial-routing-view-message-routing-results.md)
