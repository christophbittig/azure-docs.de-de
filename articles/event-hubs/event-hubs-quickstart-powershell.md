---
title: 'Schnellstart: Erstellen eines Event Hubs mithilfe von PowerShell – Azure Event Hubs'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe von Azure PowerShell einen Event Hub erstellen und anschließend mithilfe des .NET Standard SDK Ereignisse senden und empfangen.
ms.topic: quickstart
ms.date: 09/28/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74ba18e00366aa0df3b28807aaa41f72554a3c99
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218221"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Schnellstart: Erstellen eines Event Hubs mithilfe von Azure PowerShell

Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In dieser Schnellstartanleitung erstellen Sie einen Event Hub mit Azure PowerShell.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Damit Sie dieses Tutorial ausführen können, benötigen Sie folgende Komponenten:

- Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen][], bevor Sie beginnen.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [.NET Core SDK](https://dotnet.microsoft.com/download) Version 2.0 oder höher

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Bei der lokalen Verwendung von PowerShell müssen Sie die aktuelle Version ausführen, um diese Schnellstartanleitung durcharbeiten zu können. Wenn Sie eine Installation oder ein Upgrade durchführen müssen, helfen Ihnen die Informationen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) weiter.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist eine logische Sammlung mit Azure-Ressourcen, und Sie benötigen eine Ressourcengruppe, um einen Event Hub zu erstellen. 

Im folgenden Beispiel wird eine Ressourcengruppe in der Region „East US“ (USA, Osten) erstellt. Ersetzen Sie `myResourceGroup` durch den Namen der Ressourcengruppe, die Sie verwenden möchten:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace

Nachdem die Ressourcengruppe vorhanden ist, können Sie darin einen Event Hubs-Namespace erstellen. Ein Event Hubs-Namespace stellt einen eindeutigen vollqualifizierten Domänennamen bereit, unter dem Sie Ihren Event Hub erstellen können. Ersetzen Sie `namespace_name` durch einen eindeutigen Namen für Ihren Namespace:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Nachdem Sie nun über einen Event Hubs-Namespace verfügen, können Sie darin einen Event Hub erstellen:  
Der zulässige Zeitraum für `MessageRetentionInDays` beträgt ein bis sieben Tage.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Glückwunsch! Sie haben Azure PowerShell verwendet, um einen Event Hubs-Namespace und einen darin enthaltenen Event Hub zu erstellen. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie den Event Hubs-Namespace erstellt und Beispielanwendungen verwendet, um Ereignisse von Ihrem Event Hub zu senden und zu empfangen. Schritt-für-Schritt-Anleitungen zum Senden von Ereignissen an einen Event Hub oder Empfangen von Ereignissen von einem Event Hub finden Sie in den Tutorials zum **Senden und Empfangen von Ereignissen**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (nur senden)](event-hubs-c-getstarted-send.md)
- [Apache Storm (nur empfangen)](event-hubs-storm-getstarted-receive.md)


[kostenloses Konto erstellen]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
