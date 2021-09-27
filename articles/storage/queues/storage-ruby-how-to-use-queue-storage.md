---
title: 'Verwenden von Queue Storage mit Ruby: Azure Storage'
description: Erfahren Sie, wie Sie den Azure Queue Storage zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten verwenden. Die Beispiele wurden in Ruby geschrieben.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 06/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: fd09a951bbc6380a583ec2fec4194b216cca2db1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553258"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Verwenden von Queue Storage mit Ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarios mit dem Microsoft Azure-Warteschlangendienst erläutert. Die Beispiele wurden mit der Ruby Azure-API erstellt. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie unter [Erstellen einer Ruby-Anwendung in App Service unter Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurieren der Anwendung für den Speicherzugriff

Um den Azure Storage zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie PowerShell (Windows), Terminal (Mac) oder Bash (Unix).
2. Geben Sie im Befehlsfenster `gem install azure` ein, um das Gem und Abhängigkeiten zu installieren.

### <a name="import-the-package"></a>Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Einrichten einer Azure Storage-Verbindung

Das Azure-Modul entnimmt den Umgebungsvariablen `AZURE_STORAGE_ACCOUNT` und `AZURE_STORAGE_ACCESS_KEY` die erforderlichen Informationen zum Herstellen einer Verbindung mit Ihrem Azure Storage-Konto. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von `Azure::QueueService` mit dem folgenden Code angeben:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

So rufen Sie diese Werte aus einem klassischen oder Resource Manager-Speicherkonto im Azure-Portal ab

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3. Klicken Sie auf dem Blatt **Einstellungen** auf der rechten Seite auf **Zugriffsschlüssel**.
4. Auf dem angezeigten Blatt **Zugriffsschlüssel** sehen Sie „Zugriffsschlüssel 1“ und „Zugriffsschlüssel 2“. Sie können beide verwenden.
5. Klicken Sie auf das Symbol „Kopieren“, um den Schlüssel in die Zwischenablage zu kopieren.

## <a name="how-to-create-a-queue"></a>Gewusst wie: Erstellen einer Warteschlange

Durch folgenden Code wird ein `Azure::QueueService` -Objekt erstellt, das Ihnen das Arbeiten mit Warteschlangen ermöglicht.

```ruby
azure_queue_service = Azure::QueueService.new
```

Mit der Methode `create_queue()` können Sie eine Warteschlange mit dem angegebenen Namen erstellen.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Gewusst wie: Einfügen einer Nachricht in eine Warteschlange

Zum Einfügen einer Nachricht in eine Warteschlange verwenden Sie die `create_message()`-Methode, um eine neue Nachricht zu erstellen und zur Warteschlange hinzuzufügen.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Gewusst wie: Einsehen der nächsten Nachricht

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die `peek_messages()`-Methode aufrufen. Standardmäßig wird von `peek_messages()` jeweils nur eine Nachricht angeschaut. Sie können jedoch auch angeben, wie viele Nachrichten Sie abrufen möchten.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange

Sie können eine Nachricht in zwei Schritten aus der Warteschlange entfernen.

1. Wenn Sie `list_messages()` aufrufen, wird standardmäßig die nächste Nachricht aus der Warteschlange abgerufen. Sie können jedoch auch angeben, wie viele Nachrichten Sie abrufen möchten. Die für `list_messages()` zurückgegebenen Nachrichten sind für anderen Code, mit dem Nachrichten aus dieser Warteschlange gelesen werden, nicht mehr sichtbar. Sie übergeben das Sichtbarkeits-Zeitlimit in Sekunden als Parameter.
2. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem `delete_message()` aufrufen.

Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In Ihrem Code wird `delete_message()` direkt nach der Verarbeitung der Nachricht aufgerufen.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Im folgenden Codebeispiel wird die Methode `update_message()` verwendet, um eine Nachricht zu aktualisieren. Der Code gibt ein Tupel zurück, das den Pop-Beleg der Warteschlangennachricht und einen `DateTime`-Wert in UTC zurück, der angibt, wann die Nachricht in der Warteschlange sichtbar sein wird.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Vorgehensweise: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können.

1. Sie können auch einen Nachrichtenstapel abrufen.
2. Außerdem können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt.

Im folgenden Codebeispiel wird `list_messages()` verwendet, um 15 Nachrichten mit einem Aufruf abzurufen. Anschließend werden die Nachrichten ausgegeben und gelöscht. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Gewusst wie: Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode `get_queue_metadata()` gibt die ungefähre Anzahl der Nachrichten und andere Metadaten der Warteschlange zurück.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Gewusst wie: Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die `delete_queue()`-Methode für das Warteschlangenobjekt auf.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Queue Storage vertraut gemacht haben, folgen Sie diesen Links, um mehr über komplexere Speicheraufgaben zu erfahren.

- Besuchen Sie den [Blog des Azure-Speicherteams](/archive/blogs/windowsazurestorage/)
- Besuchen Sie das [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) -Repository auf GitHub

Einen Vergleich zwischen Azure Queue Storage, das in diesem Artikel erläutert wird, und den unter [Verwenden von Service Bus-Warteschlangen](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) erörterten Azure Service Bus-Warteschlangen finden Sie unter [Azure Queue Storage und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).
