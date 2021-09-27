---
title: 'Verwenden von Azure Queue Storage mit PowerShell: Azure Storage'
description: Führen Sie Vorgänge für Azure Queue Storage mit PowerShell aus. Mit Azure Queue Storage können Sie eine große Anzahl von Nachrichten speichern, auf die über HTTP/HTTPS zugegriffen werden kann.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a607d1c494aee2881e623e01e6cd9637cb94dc9d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582286"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>Verwenden von Azure Queue Storage mit PowerShell

Azure Queue Storage ist ein Dienst zur Speicherung einer großen Anzahl von Nachrichten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Weitere Informationen finden Sie unter [Was ist Azure Queue Storage?](storage-queues-introduction.md). In dieser Anleitung werden häufige Vorgänge für Queue Storage behandelt. Folgendes wird vermittelt:

> [!div class="checklist"]
>
> - Erstellen einer Warteschlange
> - Abrufen einer Warteschlange
> - Hinzufügen einer Nachricht
> - Lesen einer Nachricht
> - Löschen einer Nachricht
> - Löschen einer Warteschlange

Für diese Anleitung ist das Azure PowerShell-Modul (`Az`) v0.7 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die derzeit installierte Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu.

Es gibt keine PowerShell-Cmdlets für die Datenebene für Warteschlangen. Sie müssen die .NET-Speicherclientbibliothek wie in PowerShell verwenden, um Vorgänge auf Datenebene durchführen zu können, z. B. Nachricht hinzufügen, Nachricht lesen und Nachricht löschen. Sie erstellen ein Nachrichtenobjekt und können dann Befehle wie `AddMessage` verwenden, um Vorgänge für diese Nachricht auszuführen. In diesem Artikel erfahren Sie, wie Sie dabei vorgehen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Abrufen der Standortliste

Wenn Sie sich nicht sicher sind, welche Region Sie verwenden sollen, können Sie die verfügbaren Regionen auflisten. Sobald die Liste angezeigt wird, wählen Sie die gewünschte Region aus. In dieser Übung wird `eastus` verwendet. Speichern Sie diesen Wert für die zukünftige Verwendung in der Variable `location`.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Speichern Sie den Namen der Ressourcengruppe für die zukünftige Verwendung in einer Variable. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen `howtoqueuesrg` in der Region `eastus` erstellt.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mit dem Befehl [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) ein universelles Standardspeicherkonto mit lokal redundantem Speicher (LRS). Rufen Sie den Kontext des Speicherkontos ab, der das zu verwendende Speicherkonto definiert. Wenn Sie Aktionen für ein Speicherkonto ausführen, verweisen Sie auf den Kontext, anstatt wiederholt die Anmeldeinformationen anzugeben.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Im folgenden Beispiel wird zuerst mit dem Speicherkontokontext, der den Speicherkontonamen und seinen Zugriffsschlüssel umfasst, eine Verbindung mit Azure Storage hergestellt. Anschließend wird das [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue)-Cmdlet aufgerufen, um eine Warteschlange namens `howtoqueue` zu erstellen.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Informationen zu Namenskonventionen für Azure Queue Storage finden Sie unter [Benennen von Warteschlangen und Metadaten](/rest/api/storageservices/naming-queues-and-metadata).

## <a name="retrieve-a-queue"></a>Abrufen einer Warteschlange

Sie können eine bestimmte Warteschlange oder Liste aller Warteschlangen in einem Speicherkonto abfragen und abrufen. Die folgenden Beispiele veranschaulichen das Abrufen aller Warteschlangen im Speicherkonto und einer bestimmten Warteschlange. Beide Befehle verwenden das Cmdlet [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue).

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Hinzufügen von Nachrichten zu einer Warteschlange

Vorgänge, die sich auf die tatsächlichen Nachrichten in der Warteschlange auswirken, verwenden die .NET-Speicherclientbibliothek, wie sie in PowerShell bereitgestellt wird. Um eine Nachricht einer Warteschlange hinzuzufügen, erstellen Sie eine neue Instanz des Message-Objekts, [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage)-Klasse. Rufen Sie anschließend die [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage)-Methode auf. Eine `CloudQueueMessage` kann aus einer Zeichenfolge (im UTF-8-Format) oder einem Bytearray erstellt werden.

Im folgenden Beispiel wird veranschaulicht, wie eine Nachricht zu Ihrer Warteschlange hinzugefügt wird.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Wenn Sie [Azure Storage-Explorer](https://storageexplorer.com) verwenden, können Sie sich mit Ihrem Azure-Konto verbinden, die Warteschlangen im Speicherkonto anzeigen und für eine Warteschlange einen Drilldown durchführen, um die Nachrichten in der Warteschlange anzuzeigen.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Lesen und Löschen einer Nachricht aus der Warteschlange

Nachrichten werden in der Reihenfolge „Best Try“, „First In“, „First Out“ gelesen. Dies ist nicht gewährleistet. Wenn Sie eine Nachricht aus der Warteschlange lesen, wird diese unsichtbar für alle anderen Prozesse, die die Warteschlange betrachten. Dadurch wird sichergestellt, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt.

Dieses **Unsichtbarkeitstimeout** definiert, wie lange die Nachricht unsichtbar bleibt, bevor sie wieder für die Verarbeitung verfügbar ist. Der Standardwert ist 30 Sekunden.

Dieser Code liest eine Nachricht aus der Warteschlange in zwei Schritten. Wenn Sie [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage)-Methode aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für `GetMessage` zurückgegebene Nachricht ist für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Wenn Sie die Nachricht endgültig aus der Warteschlange entfernen möchten, rufen Sie die [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage)-Methode auf.

Im folgenden Beispiel lesen Sie die drei Warteschlangennachrichten durch und warten dann zehn Sekunden (das Unsichtbarkeitstimeout). Dann lesen Sie die drei Nachrichten erneut und löschen diese anschließend, indem Sie `DeleteMessage` aufrufen. Wenn Sie versuchen, die Warteschlange zu lesen, nachdem die Nachrichten gelöscht wurden, wird `$queueMessage` als `$null` zurückgegeben.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie das `Remove-AzStorageQueue`-Cmdlet auf. Das folgende Beispiel zeigt, wie die in dieser Übung verwendete Warteschlange mit dem Cmdlet `Remove-AzStorageQueue` gelöscht wird.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie zum Entfernen aller Ressourcen, die Sie in dieser Übung erstellt haben, die Ressourcengruppe. Dabei werden auch alle in der Gruppe enthaltenen Ressourcen gelöscht. In diesem Fall werden das erstellte Speicherkonto und die Ressourcengruppe selbst entfernt.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie Folgendes über die grundlegende Verwaltung von Queue Storage mit PowerShell gelernt:

> [!div class="checklist"]
>
> - Erstellen einer Warteschlange
> - Abrufen einer Warteschlange
> - Hinzufügen einer Nachricht
> - Lesen der nächsten Nachricht
> - Löschen einer Nachricht
> - Löschen einer Warteschlange

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell-Speichercmdlets

- [Storage PowerShell-Cmdlets](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage-Explorer

- Beim [Microsoft Azure Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) handelt es sich um eine kostenlose eigenständige App von Microsoft, über die Sie ganz einfach visuell mit Azure Storage-Daten arbeiten können – unter Windows, MacOS und Linux.
